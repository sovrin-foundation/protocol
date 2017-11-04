# Basic Message Packaging
Messages are not self describing by themselves. They need a header to describe what they are.

Example header value:
```json
{
    "type": "CLAIM",
    "type_version": "0.1"
}
```

Messages need to be packaged with their headers so recipients know how to understand them. For this reason, 
the message is included as the "payload" of a new object, along with its header:


```json
{
    "header": {...},
    "payload": {...}
}
```
This object is called an **Qualified Message**

##Signing
Signing is only done when the use case requires it, for several reasons: 
1. Authentication can be accomplished as part of the Public Key Authenticated Encryption (PKAE). 
2. The Non-Repudiation offered by signing (DSA, ECDSA, EDDSA) is not always desired, as in the interrim steps of a negotiation. Signatures make a message non-repudiable. 
3. Signatures are somewhat more expensive to compute and verify than other ways to authenticate, like PKAE.    

If a signature is required, the header and payload need to be encoded as BASE64URL and two signature fields added to the object: 

```json
{
    "header": <JSON header value encoded as BASE64URL>,
    "payload": <arbitrary message encoded as BASE64URL>,
    "sig": "<BASE64URL encoding of sig of header + payload>"
    "sig_type": "ED25519_HMAC_SHA256",
    }
}
```

# Protocol Message Packaging
Messages are usually part of interactions.

Multiple interactions can happen simultaneously, and so an interaction identifier (IID) is needed to keep things straight.

The first message in an interaction should set an IID 
(128-bit random number) that the two parties use to keep 
track of an interaction.

Each message in an interaction needs a way to be uniquely 
identified. This is done with Message ID (mid). The first 
message from each party has an mid of 0, 
the second message sent from each party is 1, and 
so forth. A message is uniquely identified in an 
interaction by its IID, the sender DID, and the MID.

There can be nested interaction, as in the case of a Claim Request. 
Alice sends Bob a Claim Request. 
Bob wants Alice to provide some additional proof first, so he sends her a Proof Request.
Alice sends a Proof.
Bob sends the Claim.
This is actually a Proving Interaction nested within an Issuing Interaction.

```json
{
  "iid": "",
  "mid": 0,
  "msg": <packaged message>
}
```

To keep track of nesting, the initiator of an interaction can also reference an optional parent iid.

iid: A mutually agreed identifier for the Interaction.
mid: A message ID unique to the IID and sender.
piid: A parent iid used when branching or nesting a new interaction off of an existing one.
lmid: A reference to the last message the sender received from the receiver. Missing if it is the first message in an interaction.


to_did: who the message is to. This did should stay constant throughout the negotiation.
from_did: who the message is from. This did should stay constant throughout the negotiation.

```json
{
  "iid": "<128 random bits encoded as base64url>",
  "mid": <sequential id>,
  "msg": <packaged message>
}
```

Example:

Bob sends to Alice:
```json
{
  "iid": "Gh4...y4p",
  "mid": 0,
  "msg": {
    "header": BASE64URL({
      "type": "CLAIM_OFFER",
      "type_version": "0.1"
    }),
    "payload": BASE64URL(<claim request>)
    }
}
```


Alice sends to Bob:
```json
{
  "iid": "Gh4...y4p",
  "mid": 0,
  "lmid": 0,
  "msg": {
    "header": BASE64URL({
      "type": "CLAIM_REQUEST",
      "type_version": "0.1"
    }),
    "payload": BASE64URL(<claim request>)
    }
}
```

Bob sends to Alice:
```json
{
  "iid": "X1j...Kk2",
  "piid": "Gh4...y4p",
  "mid": 0,
  "msg": {
    "header": BASE64URL({
      "type": "PROOF_REQUEST",
      "type_version": "0.1"
    }),
    "payload": BASE64URL(<proof request>)
    }
}
```
This is the first message in a new nested interaction!


Alice sends to Bob:
```json
{
  "iid": "X1j...Kk2",
  "mid": 0,
  "lmid": 0,
  "msg": {
    "header": BASE64URL({
      "type": "PROOF",
      "type_version": "0.1"
    }),
    "payload": BASE64URL(<proof>)
    }
}
```
This is the second message in the nested interaction.

Bob sends to Alice:
```json
{
  "iid": "Gh4...y4p",
  "mid": 1,
  "lmid": 0,
  "msg": {
    "header": BASE64URL({
      "type": "CLAIM",
      "type_version": "0.1"
    }),
    "payload": BASE64URL(<claim>)
    }
}
```
This is the third message in the original interaction.

Bob sends to Alice:
```json
{
  "iid": "Gh4...y4p",
  "mid": 1,
  "lmid": 1,
  "msg": {
    "header": BASE64URL({
      "type": "ACK",
      "type_version": "0.1"
    }),
    "payload": BASE64URL(<ack>)
    }
}
```
This is the fourth and last message in the original interaction.
