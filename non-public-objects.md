# Non Public Objects

## Claim Offer
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
  "name": "Job-Application-2",
  "version": "0.3",
  "attributes": {
      "ssn": "string",
      "last_name": "string",
      "status": "string",
      "first_name": "string",
      "degree": "string",
      "phone_number": "string"
  },
  "verifiableAttributes": ["status", "degree"]
  "nonce": "<a nonce acting as request id>"
}
```