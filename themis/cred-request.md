# Credential Request

A credential request is sent from a potential credential holder to an
issuer, asking that a credential be issued. This message is a necessary
step in issuance, because the potential holder must provide a blinded
_link secret_ that will be used to bind the credential exclusively
to them.

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

## Credential Notice
When an issuer is making a change that affects its credential holders, it should let them know of the change. This could be because that the Issuer is updating its Credentials to a new schema, or rotating its public keys, or some breach was detected and credentials issued between a certain date period need to be reissued, or a particular credential was revoked (e.g, driver privileges suspended due to court order). This is done by sending the credential holder(s) a Credential Notice message.
