# Message Packaging

TODO: Write about composable primitives.

## Typed Messages
Messages are not self describing by themselves. They need some metadata to help describe what they are.

This is done with a `type` object, which has two members. One for the type name, and another for the version.

Example:
```json
{
  "name": "CLAIM",
  "version": "0.1"
}
```

A message can be "typed" in one of two methods. One in which the original message is unchanged, but wrapped in an object, and the other where special `@type` member is added to the message.

### Wrapping method
In this method, the original message is wrapped as the `msg` element of a new object, along with a `type` object:

```json
{
  "type": { <type object> },
  "msg": { <message> }
}
```

### Annotating method
A `@type` member is included in the message object itself, as a peer to the root members of the object.
```json
{
  "@type": {...},
  <other members>
}
```

Notice the annotation method uses a prepended `@` to indicate it is an annotation, that is a member that is not part of the original object.

## Signed Message
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

## Topical Messages
Messages are usually part of interactions.

### Topic ID (`tid`)
Because multiple interactions can happen simultaneously, it's important to differentiate between them. This is done with a Topic ID or `tid`.

The first message in an interaction should set a `tid`
(128-bit random number) that the two parties use to keep 
track of an interaction.

### Message ID (`mid`)
Each message in an interaction needs a way to be uniquely 
identified. This is done with Message ID (`mid`). The first 
message from each party has a `mid` of 0, 
the second message sent from each party is 1, and 
so forth. A message is uniquely identified in an 
interaction by its `tid`, the sender DID, and the `mid`.

### Last Message ID (`lmid`)
To aid in understanding the last message the other party received, a Last Message ID or `lmid` can be included.

### Example
As an example, Alice is an issuer and she offers a claim to Bob.

* Alice establishes a Topic ID, 7.
* Alice sends a CLAIM_OFFER, `tid`=7, `mid`=0. 
* Bob responded with a CLAIM_REQUEST, `tid`=7, `mid`=0, `lmid`=0.
* Alice sends a CLAIM, `tid`=7, `mid`=1, `lmid`=0.
* Bob responds with an ACK, `tid`=7, `mid`=1, `lmid`=1.

### Nested interactions
Sometimes there are interactions that need to occur with the same party, while an existing interaction is in-flight.

When an interaction is nested within another, the initiator of a new interaction can include a Parent Topic ID (`ptid`). This signals to the other party that this is a topic that is branching off of an existing interaction.

### Nested Example
As before, Alice is an issuer and she offers a claim to Bob. This time, she wants a bit more information before she is comfortable providing a claim.

* Alice establishes a Topic ID, 7.
* Alice sends a CLAIM_OFFER, `tid`=7, `mid`=0. 
* Bob responded with a CLAIM_REQUEST, `tid`=7, `mid`=0, `lmid`=0.
* **Alice sends a PROOF_REQUEST, `tid`=11, `ptid`=7, `mid`=0.**
* **Bob sends a PROOF, `tid`=11,`mid`=0, `lmid`=0.**
* Alice sends a CLAIM, `tid`=7, `mid`=1, `lmid`=0.
* Bob responds with an ACK, `tid`=7, `mid`=1, `lmid`=1.

All of the steps are the same, except the two bolded steps that are part of a nested interaction.

### Topic object
A topic object has the following fields discussed above:

* `tid`: A mutually agreed identifier for the interaction.  
* `mid`: A message ID unique to the `tid` and sender.  
* `ptid`: An optional parent `tid`. Used when branching or nesting a new interaction off of an existing one.  
* `lmid`: A reference to the last message the sender received from the receiver. (Missing if it is the first message in an interaction.)  

The topic object can be associated with a message in one of two way.

### Wrapping Method
In this method, the original message is wrapped as the `msg` element of a new object, along with a `topic` object:

```json
{
  "topic": { <topic object> },
  "msg": { <message> }
}
```

### Annotating method
A `@topic` member is included in the message object itself, as a peer to the root members of the object.
```json
{
  "@topic": {...},
  <other msg members>
}
```

## Addressed Messages
A wrapper that includes an original message, plus a specific DID that is the intended recipient.

Structure:
```json
{
  "to": <DID>,
  "msg": <message>
}
```

## Shared Messages

Structure:
```json
{
  disclosed: <msg for recipient in plaintext>,
  forwarded: <encrypted msg for edge>
}
```
This is useful when the recipient of a message is to forward a message on, but needs to have some information about the message. This is a common requirement with Agents, where the Agent needs to know that the message is a Claim Offer, but doesn't need to know the details of the Claim itself.

## Encrypted Messages
There are three basic types of encrypted messages.
1. authenticated encryption: `pkae` 
1. anonymous encryption: `anoncrypt`
1. self-contained authenticated encryption: `authcrypt` 

### Authenticated Encryption
Public Key Authenticated Encryption (PKAE) is a method where an elliptic curve diffie-hellman key exchange is used along with a nonce to compute a shared secret. This shared secret is used as a symmetric encryption key. The ciphertext can be decrypted, but only if the public key of the sender and the nonce used are communicated. Because extra information is required to decrypt, this does not result in a self-contained message.

The PKAE function takes the recipient's verkey and a one-time nonce:
```
pkae_encrypt(msg, recipient_verkey, nonce)
```

### Anoncrypt Messages
`anoncrypt` messages use PKAE in conjunction with an ephemeral sender key pair to strongly encrypt a message to the intended recipient. The message is self-contained, in that the recipient can take an anoncrypt message and decrypt it without any other information.

The anoncrypt function takes the recipient's verkey:
```
anoncrypt(msg, recipient_verkey)
```

### Authcrypt Messages
`authcrypt` messages combine PKAE with anoncrypt to make self-contained authenticated encryption. The public key and nonce needed to decrypt PKAE are supplied in an anoncrypt message. This allows an Authcrypt message to be sent over an untrusted network and still maintain the privacy requirements (i.e., the public key is not known).

Structure:
```json
{
  "hint": base64url(
            anoncrypt(
              HINT
              recipient_verkey)),
  "msg": base64url(
           pkae_encrypt(
             MSG,
             recipient_verkey,
             <nonce1>))
}
```
...where `HINT` is...
```json
{
  "from": <sender verkey>,
  "nonce": <nonce1>
}
```

To open a combo_box:
1. Decode the Base64URL hint.
1. Decrypt the 
decrypt the sealedbox, and use the from and nonce from the sealed_box to decrypt the pkae_box:

pkae_decrypt(ciphertext, from, nonce)


## Alternate construction

Internal message structure:
```json
{
  "from": "<sender's ver_key>",
  "sig":"<sender's ephemeral pub_key signed by sender>",
  "msg":"msg"
}
```

### Construction
Sender...
1. Generates a new ephemeral keypair.
1. Signs the ephmeral public key.
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
1. Encodes the ciphertext in BASE64URL and adds it to a JSON object along with the the ephemeral public key.
    ```json
    {
      "lock_box_key": "<BASE64URL(sender's ephemeral pub_key)>",
      "ciphertext": "<BASE64URL(ciphertext)>"
    }
    ```

### Deconstruction
Recipient...
1. Decodes the lock_box_key.
1. Decodes the ciphertext.
1. Uses the lock_box_key and its own private key to decrypt the ciphertext.
1. Verifies the signature is a signature over the `lock_box_key` using the `from` ver_key in the "from".

At this point, the recipient has a message it knows was encrypted by the sender and was not tampered with. The message is still repudiable.


#### Issues to overcome with libsodium
Sealed_box generates the keypair in the encryption operation. The contents of the message contains the ephemeral public key, so this won't work for us. We could deconstruct the sealed_box logic, but this would defeat one of the purposes of sealed_box.
 
Crypto_box could work, but it requires a nonce. A nonce is not needed with an ephemeral key authenticated encryption, because the ephemeral key is used only once. Maybe we could use crypto_box with a static nonce, say 0x01. But using the nonce is extra work that's not necessary.

So neither are great for what we want to do. but I would think Crypto_box with a static nonce is the easiest approach.


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


