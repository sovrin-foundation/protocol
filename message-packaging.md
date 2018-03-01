# Message Packaging

Message delivery is complex. The hops in message delivery between two parties might vary according to whether agents are used, how much they are trusted, and how functional edge devices are. Likewise, the way encryption is applied may need dynamic adjustment.

For this reason, the protocol does not prescribe a single encryption, a single format, a single routing strategy. Instead, it defines some primitives and some standard patterns for how these primitives are combined. By composing these primitives according to circumstances, a wide variety of use cases can be addressed by a sender, with predictable and modest handling effort by the receiver.

# Message Packaging Primitives

### Typed Messages
Messages are not self-describing by themselves. They need some metadata to help describe what they are.

This is done with a `type` object, which has three members. One for the type name, one for the version and one for the format of the message.

Generally, the version should conform to Semantic Versioning principles and the format should make use of mime-types.

Example:
```json
{
  "name": "CLAIM",
  "version": "0.1",
  "format": "application/msgpack"
}
```

A message can be "typed" in one of two methods. One in which the original message is unchanged, but wrapped in an object, and the other where special `@type` member is added to the message.

#### Wrapping method
In this method, the original message is wrapped as the `msg` element of a new object, along with a `type` object:

```json
{
  "type": { <type object> },
  "msg": { <message> }
}
```

#### Annotating method
A `@type` member is included in the message object itself, as a peer to the root members of the object.
```json
{
  "@type": {...},
  <other members>
}
```

Notice the annotation method uses a prepended `@` to indicate it is an annotation, that is a member that is not part of the original object.

### Signed Message
Signing is only done when the use case requires it, for several reasons: 
1. Authentication can be accomplished as part of the Public Key Authenticated Encryption (PKAE). 
2. The Non-Repudiation offered by signing (DSA, ECDSA, EDDSA) is not always desired, as in the interrim steps of a negotiation. Signatures make a message non-repudiable. 
3. Signatures are somewhat more expensive to compute and verify than other ways to authenticate, like PKAE.    

If a signature is required, the original message is encoded as BASE64URL and added to a new object with two other signature fields: 

```json
{
  "signed": "<string encoded message>",
  "sig": "<string encoded signature>",
  "sig_type": "<algorithm used to sign>"
}
```

The `sig_type` tells us how the original message was encoded, what algorithm was used to create the signature, and how the signature is encoded.

For example, the `ED25519_B64U` signature scheme has the following characteristics:
* Pre-conditions: **None**
* Message encoding: **BASE64URL**
* Signing algorithm: **ed25519**
* Signature encoding: **BASE64URL**

More advanced signature schemes can be used. For example, the CL_CLAIM_1 algorithm has the following characteristics.
* Pre-conditions: **message is a typed CLAIM version 0.1 through 0.2, JSON**
* Message encoding: **none**
* Signing algorithm: **CL-RSA**
* Signature encoding: **none**

### Topical Messages
Messages are usually part of interactions.

#### Topic ID (`tid`)
Because multiple interactions can happen simultaneously, it's important to differentiate between them. This is done with a Topic ID or `tid`.

The first message in an interaction should set a `tid`
(128-bit random number) that the two parties use to keep 
track of an interaction.

#### Message ID (`mid`)
Each message in an interaction needs a way to be uniquely 
identified. This is done with Message ID (`mid`). The first 
message from each party has a `mid` of 0, 
the second message sent from each party is 1, and 
so forth. A message is uniquely identified in an 
interaction by its `tid`, the sender DID, and the `mid`. The combination of those three parts would be a way to uniquely identify a message.

#### Last Message ID (`lmid`)
In an interaction, it may be useful for the recipient of a message to know if their last message was received. A Last Message ID or `lmid` can be included to help detect missing messages.

#### Example
As an example, Alice is an issuer and she offers a claim to Bob.

* Alice establishes a Topic ID, 7.
* Alice sends a CLAIM_OFFER, `tid`=7, `mid`=0. 
* Bob responded with a CLAIM_REQUEST, `tid`=7, `mid`=0, `lmid`=0.
* Alice sends a CLAIM, `tid`=7, `mid`=1, `lmid`=0.
* Bob responds with an ACK, `tid`=7, `mid`=1, `lmid`=1.

#### Nested interactions
Sometimes there are interactions that need to occur with the same party, while an existing interaction is in-flight.

When an interaction is nested within another, the initiator of a new interaction can include a Parent Topic ID (`ptid`). This signals to the other party that this is a topic that is branching off of an existing interaction.

#### Nested Example
As before, Alice is an issuer and she offers a claim to Bob. This time, she wants a bit more information before she is comfortable providing a claim.

* Alice establishes a Topic ID, 7.
* Alice sends a CLAIM_OFFER, `tid`=7, `mid`=0. 
* Bob responded with a CLAIM_REQUEST, `tid`=7, `mid`=0, `lmid`=0.
* **Alice sends a PROOF_REQUEST, `tid`=11, `ptid`=7, `mid`=0.**
* **Bob sends a PROOF, `tid`=11,`mid`=0, `lmid`=0.**
* Alice sends a CLAIM, `tid`=7, `mid`=1, `lmid`=0.
* Bob responds with an ACK, `tid`=7, `mid`=1, `lmid`=1.

All of the steps are the same, except the two bolded steps that are part of a nested interaction.

#### Topic object
A topic object has the following fields discussed above:

* `tid`: A mutually agreed identifier for the interaction.  
* `mid`: A message ID unique to the `tid` and sender.  
* `ptid`: An optional parent `tid`. Used when branching or nesting a new interaction off of an existing one.  
* `lmid`: A reference to the last message the sender received from the receiver. (Missing if it is the first message in an interaction.)  

The topic object can be associated with a message in one of two way.

#### Wrapping Method
In this method, the original message is wrapped as the `msg` element of a new object, along with a `topic` object:

```json
{
  "topic": { <topic object> },
  "msg": { <message> }
}
```

#### Annotating method
A `@topic` member is included in the message object itself, as a peer to the root members of the object.
```json
{
  "@topic": {...},
  <other msg members>
}
```

### Forward Messages
A wrapper that includes an original message, plus a specific DID that is the intended recipient.

Structure:
```json
{
  "fwd": <DID>,
  "msg": <message>
}
```

### Bundled Messages
Sometimes, it's useful to send a collection of related messages.

Structure:
```json
{
  "bundled": [msg1, msg2],
}
```

This can be useful when the recipient of a message is to forward a message on, but needs to have some information about the message. This is a common requirement with Agents, where the Agent needs to know that the message is a Claim Offer, but doesn't need to know the details of the Claim itself.

Example:
```json
{
  "bundled": [
    <msg for cloud agent>, 
    {
      "fwd": <DID>,
      "msg": <msg for edge agent>
    }
  ]
}
```

### Encrypted Messages
There are two basic types of encrypted messages.
1. `anoncrypt`: public key anonymous encryption
1. `authcrypt`: public key authenticated encryption

#### Public Key Authenticated Encryption
Public Key Authenticated Encryption (PKAE) is a method where an elliptic curve diffie-hellman key exchange is used along with a nonce to compute a shared secret. This shared secret is used as a symmetric encryption key. The ciphertext can be decrypted, but only if the public key of the sender and the nonce used are communicated. The recipient knows the message is authentic because the symmetric key could only be known by the recipient and the sender. Authenticated Encryption is repudiable, meaning the recipient cannot prove to another party that the sender sent it. Because extra information is required to decrypt, this does not result in a self-contained message.

#### Anoncrypt Messages
`anoncrypt` messages use PKAE in conjunction with an ephemeral sender key pair to strongly encrypt a message to an intended recipient.

The anoncrypt function takes the msg and the recipient's verkey as arguments. A nonce is used, but because the ephemeral key is used only once, it is deterministic.

```
anoncrypt(msg, recipient_verkey)
```

Anoncrypt messages can be decrypted.
```
anondecrypt(msg, recipient_private_key)
```

#### Authcrypt Messages
`authcrypt` messages are like `anoncrypt` in that they are constructed with an ephemeral key pair. The difference is there are two attributes included with the message that make it possible to authenticate the sender: (1) sender's ver_key, and (2) signature over sender's ephemeral pub_key using the sender's sig_key (private counterpart to the ver_key). The sender's ver_key cannot be trusted. In order to trust the ver_key, the recipient must verify the signature. The signature proves to the recipient that the sender (the holder of the private counterpart to the ver_key) sent the message. This encryption retains the repudiable characteristic, in that the recipient cannot prove that the sender sent the message. They can only prove the sender once used the ephemeral key, which doesn't prove anything of any importance. 

Internal message structure:
```json
{
  "from": "<sender's ver_key>",
  "sig": "<sender's ephemeral pub_key signed by sender>",
  "msg": "<msg>"
}
```

#### Multiplexing Authcrypt Messages
In cases where two different edge agents need to receive the same message, but the message is being sent through the a common cloud agent, one solution is simply to send two separate authcrypt messages. 

If the message is large, this can result in encrypting the message twice, and having to send it twice. It may be desireable to encrypt it and send it only once, while maintaining the requirement that both edge agents still be able to authenticate the message to the source.
 
This can be done by a process called **Multiplexing**. 

In Multiplexing, the Authcrypt message is split into two parts. The **Meta** and the **Elemental**. At a high level, the Authcrypt Meta looks like a normal Authcrypt message, except the base message is moved into the Authcrypt Elemental.

The Authcrypt Meta message holds the hash and the key of the base message, but the base and the 

The sender generates a one-time random symmetric key, and encrypts the base message with this key. This is the Elemental.

Then the sender constructs an Authcrypt Meta message for each recipient. It substitutes a hash of the Elemental and the symmetric key for the base message.

Internal message structure (for each recipient):
```json
{
  "from": "<sender's ver_key>",
  "sig": "<sender's ephemeral pub_key signed by sender>",
  "msg_hash": "<msg_hash>",
  "msg_key": "<msg_key>"
}
```

The Meta message's internal structure can be the same for each recipient, including using the same ephemeral keypair. However, each message is encrypted _for_ each recipient using X25519 just like any other message.

The sender can then send a collection of Authcrypt Meta messages, one for each recipient, and one Authcrypt Elemental Message. The Cloud Agent can disperse the appropriate Authcrypt Meta message to each recipient, along with the Elemental Message.

The recipients decrypt the Authcrypt Meta messages and verify it like any other Authcrypt message. Then it hashes Elemental and verifies it matches the hash in the Meta. Then it decrypts Elemental.

##### Encryption
To encrypt, the sender calls...
```
authcrypt(msg, recipient_verkey, sender_ver_key, sender_sig_key)
```

The authcrypt function...
1. Generates a new ephemeral keypair.
1. Signs the ephmeral public key using `sender_sig_key`.
1. Constructs a JSON object like so:
    ```json
    {
      "from": "<sender's ver_key>",
      "sig": "<signature over sender's ephemeral pub_key>",
      "msg": "<msg>"
    }
    ```
1. Encrypts the object using the ephemeral private key.
1. Vigorously discards the ephemeral private key as it should only be used once.
1. Appends the ephemeral public key to the ciphertext

##### Decryption
To decrypt, the recipient calls...
```
authdecrypt(msg, recipient_private_key)
```

The authdecrypt function...

1. Removes the appended ephemeral public key
1. Uses the ephemeral public key and its own private key to decrypt the ciphertext.
1. Verifies that the signature over the ephemeral public key is correct using the `sender's ver_key`.

##### Implementation using libsodium
Anoncrypt is simply the sealed_box in libsodium. Authcrypt is similar, in that it uses the same deterministic nonce, and appends the ephemeral public key to the ciphertext. The main difference is the ephemeral public key must be signed in the message. Authcrypt uses a libsodium crypto_box with a deterministic nonce = hash(ephemeral pub key, recipient pub key), the same approach that sealed_box takes.

## Message Composition Patterns
### Routing Methods
#### Just in Time Routing
Just-in-time routing is a method where the sender sends a message to the next hop with enough information for that next hop to be able to send it to the hop after that.

#### Onion Routing
Onion routing is a method where the sender encapsulates all of the routing information to get the message to the end recipient. Each party in the path is able to decrypt one more layer to understand its part. 

### Common Routes
#### Alice Edge to Bob Edge
This example assumes Just in Time Routing.

Alice first constructs an "edge message" for bob, and encrypts it.
```
fwd( to=bob_did,
  authcrypt( to=bob_edge_ver_key, 
    typed(
      MSG
    )
  ) 
)
```
This will be referred to as "edge_msg" below.

Alice packages it for--and sends to--Bob's Agency's Router.
```
anoncrypt( to=bob's_agency's_router,
  <edge_msg>
)
```

Bob's Agency's Router decrypts it, sees the FWD message, looks up the DID, and forwards to Bob's Cloud Agent.
```
anoncrypt( to=bob's_cloud_agent,
  <edge_msg>
)
```

Bob's Cloud Agent decrypts it, sees the FWD message, looks up the DID, and forwards to Bob's Edge Agent.
```
authcrypt( to=bob's_edge_agent,
  <edge_msg>
)
```

Bob's Edge Agent decrypts it and processes it.

#### Alice Edge to Bob Edge though Cloud Agents
Same as above, except the first hop goes to Alice's Agency's Router, then Alice's Cloud Agent, and then picks up with Bob's Agency's Router and so on.

# Scratchpad below this point
Bob wants Alice to share the topic info and type info with his Agent.



Result: you have an authenticated message.

Agency router gets 
sealed({to: DID, msg: <encrypted>})


Alice's Edge to Bob's Router to Bob's Cloud to Bob's Edge
Alice's Edge to Alice’s Router to Alice's Cloud to Bob's Router to Bob's Cloud to Bob's Edge


Secure Message to another party's Cloud:

```
anoncrypt( encrypted for router
  routed(
    authcrypt( encrypted for cloud
      topical(
        signed( CL_CLAIM
          typed(
            CLAIM
          )
        )
      )
    )
  )
)
```

Secure Message to another party's Edge:

```
anoncrypt( encrypted for router
  routed(
    authcrypt( for cloud agent
      topical(
        shared( with claim_headers
          authcrypt( for edge agent
            signed( CL_CLAIM
              typed(
                CLAIM
              )
            )
          )
        )
      )
    )
  )
)
```

Scenarios
Msg to edge
Msg to edge with part of headers revealed
Msg to edge with headers revealed
Msg to edge with headers and part of msg revealed 
Msg to edge with headers and msg revealed 
Msg to cloud (all revealed)


Bob's router unseals the message, sees the DID,and forwards combo'ed to Bob's Cloud.





Open question: 

Duck typing or wrap everywhere, or something in between.

Candidates for duck typing: topic, 

@topic: {
  
}


