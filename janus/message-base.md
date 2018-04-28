# SSI Base Message Format

> *Note: This is a rough dodcument, and contains inconsistancies, missing sections, etc. These will iron out as work on this document progresses.*

The role of the Self-Soverign Identity Message Format is to act as a foundation for the message types that will power the SSI messaging ecosystem.
This format is intended to be as simple as possible, providing just enough structure to facilitate compatible communication.

Goals:
* Broad compatibility across SSI ecosystem
* Allow for innovation in message types
* Support communication from clients with commonly available libraries

## Base Envelope

The base message format is a JWT Payload. All JWT standard attributes are reserved. Additional attributes to messages are included based on the type specified. (See Message Types.)

Example:
```javascript=
{
    '@type': 'example.com/basemessage',
    'iss': 'did:exa:11111111111',
    'aud': 'did:exa:22222222222',
    'iat': 'timestamp',
}
```
### Applicable JWT attributes

iss (Issuer) - from DID
sub (Subject)
aud (Audience) - to DID
exp (Expiration Time) - ts
nbf (Not Before) - ts
iat (Issued At) - ts
jti (JWT ID) - unique id for JWT to prevent replay

Additional JOSE Headers (for JWS/JWE):

typ (Type) - Should these be used as type designators?
cty (Content Type)


## Message Types

Each message should have a type, specified by the top level @type attribute. The attribute value should be a URI that uniquely identifies the message type, and should be resolvable into a resource (ie, webpage) with details for message type use.

Each type indicates a particular version and format. 

```javascript=
{
  '@type': 'example.com/examplemessage/v1'
}
```

## Message Encoding

Messages can arrive encoded and packaged in several different formats as described here. It is up to the agent to decide if and how to handle these various forms depending on the security requirements and considering the required uses.

### Full Encryption
Messages will full encryption are packaged as described in this document.
### Common Crypto
Messages are encrypted or signed using only commonly available crypto libraries. The security may not be as good as Full Encryption, but allows integration with limited systems. This serves both Enterprise Integration and advanced IOT applications.

Messages received with common crypto should be marked as such for internal processing. See the document about Security Context.
### No Crypto
There may be use cases for which crypto of any kind is difficult, such as simple IOT devices. Messages in this form send the basic payload unencoded, unsigned, and unencrypted. It is possible to include a shared secret as a bearer token.

Messages received with no crypto should be marked as such for internal processing, and the actions taken intentionally limited. See the document about Security Context.

## Encryption / Signing

The SSI Protocol uses elliptic curve cryptography to achieve message availability,
confidentiality, and integrity. It offers three crypto primitives:

```anon_crypt(msg, recipient_verkey)```
<blockquote>Encrypt only for recipient. Sender is anonymous. Parties can be unknown
to each other. The message is tamper evident.</blockquote>

```auth_crypt(msg, recipient_verkey, sender_sigkey)```
<blockquote>Encrypt only for recipient. Recipient learns sender’s verkey but can’t
prove to anybody else who the sender is (making the message repudiable).
Parties can be unknown to each other. The message is tamper evident.</blockquote>

```sign(msg, sender_sigkey)```
<blockquote>Associate a non-repudiable signature with a message.
Note that messages are not required to use non-repudiable signatures.</blockquote>

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

More advanced signature schemes can be used. For example, the CL_CRED_1 algorithm has the following characteristics.
* Pre-conditions: **message is a typed CRED version 0.1 through 0.2, JSON**
* Message encoding: **none**
* Signing algorithm: **CL-RSA**
* Signature encoding: **none**

### Topical Messages
Messages are usually part of interactions.

> TODO: How does this fit in with the JWT headers?

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
As an example, Alice is an issuer and she offers a credential to Bob.

* Alice establishes a Topic ID, 7.
* Alice sends a CRED_OFFER, `tid`=7, `mid`=0. 
* Bob responded with a CRED_REQUEST, `tid`=7, `mid`=0, `lmid`=0.
* Alice sends a CRED, `tid`=7, `mid`=1, `lmid`=0.
* Bob responds with an ACK, `tid`=7, `mid`=1, `lmid`=1.

#### Nested interactions
Sometimes there are interactions that need to occur with the same party, while an existing interaction is in-flight.

When an interaction is nested within another, the initiator of a new interaction can include a Parent Topic ID (`ptid`). This signals to the other party that this is a topic that is branching off of an existing interaction.

#### Nested Example
As before, Alice is an issuer and she offers a credential to Bob. This time, she wants a bit more information before she is comfortable providing a credential.

* Alice establishes a Topic ID, 7.
* Alice sends a CRED_OFFER, `tid`=7, `mid`=0. 
* Bob responded with a CRED_REQUEST, `tid`=7, `mid`=0, `lmid`=0.
* **Alice sends a PROOF_REQUEST, `tid`=11, `ptid`=7, `mid`=0.**
* **Bob sends a PROOF, `tid`=11,`mid`=0, `lmid`=0.**
* Alice sends a CRED, `tid`=7, `mid`=1, `lmid`=0.
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
In cases where two different edge agents need to receive the same message, but the message is being sent through a common cloud agent, one solution is simply to send two separate authcrypt messages. 

If the message is large, this results in encrypting the message twice, and having to send it twice. It may be desireable to encrypt it and send it only once, while maintaining the requirement that both edge agents still be able to authenticate the message to the source.
 
This can be done by a process called **Multiplexing**. 

In Multiplexing, the Authcrypt message is split into two parts. The **Meta** and the **Elemental**. At a high level, the Authcrypt Meta looks like a normal Authcrypt message, except the base message is moved into the Authcrypt Elemental.

The Authcrypt **Meta** message holds the hash and the key of the base message, but the base is held encrypted in the **Elemental**.

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

The recipients decrypt the Authcrypt Meta messages and verify it like any other Authcrypt message. Then it hashes Elemental and verifies it matches the hash in the Meta. Then it decrypts Elemental using the key in Meta.

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
