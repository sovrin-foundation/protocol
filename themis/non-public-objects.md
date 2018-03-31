# Non Public Objects

## CLAIM_OFFER
```json
{
  "schema_seq_no": "<a reference to the schema, like sequence number of schema on the ledger>",
  "issuer_did": "<reference to the issuer, like the issuer’s DID on the ledger>"
}
``` 


## Claim Request
```json
{
  "data": {
    "issuer_did": "<reference to the issuer, like the issuer’s DID on the ledger>",
    "schema_seq_no": "<a reference to the schema, like sequence number of schema on the ledger>",
    "blinded_ms": {
      "ur": "<revocation part>",
      "u": "<primary part>"
    },
    "prover_did": "<does not have to be the prover's DID, but some unique identifier by which the issuer knows the prover>"
  },
  "nonce": "<a nonce acting as request id>"
}
```

## Proof Request
```json
{
  "name": "<name of the proof request>",
  "version": "<version of the proof request>",
  "attributes": {
      "<attribute name, like age>": "<attribute type, like number>",
      "<attribute name, like ssn>": "<attribute type, like string>",
      "<attribute name, like status>": "<attribute type, like enum>"
  },
  "verifiableAttributes": "<a list of attribute names for which the proof has to be presented>",
  "nonce": "<a nonce acting as request id>"
}
```

## Claim Notice
When an issuer is making a change that affects its claim holders, it should let them know of the change. This could be because that the Issuer is updating its Claims to a new schema, or rotating its public keys, or some breach was detected and claims issued between a certain date period need to be reissued, or a particular claim was revoked (e.g, driver privileges suspended due to court order). This is done by sending the claim holder(s) a Claim Notice message.
