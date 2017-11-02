# Basic Message Packaging
Messages are not self describing by themselves. They need to be packaged so handlers know what to do with them. A basic message package looks like this:

```json
{
    "header": <JSON header value encoded as BASE64URL>,
    "payload": <arbitrary message encoded as BASE64URL>,
    "sig": "<BASE64URL encoding of sig of header + payload>"
    "sig_type": "ED25519_HMAC_SHA256",
    }
}
```

Example header value:
```json
{
    "type": "CLAIM",
    "type_version": "0.1"
}
```

# Protocol Message Packaging
Messages are usually part of interactions.

Multiple interactions can happen simultaneously, and so an interaction identifier (IID) is needed to keep things straight.

The first message in an interaction should set an IID (128-bit random number) that the two parties use to keep track of an interaction.
Each message in an interaction needs a way to be uniquely identified. The first message from each party is always 0, the second message sent from each party is always 1, and so forth. A message is uniquely identified in an interaction by its IID, the sender DID, and the MID.

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

To keep track of nesting, the initiator of an interaction can also reference an optional parent IID.

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
