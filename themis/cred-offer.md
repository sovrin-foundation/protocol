# Credential Offer (CRED_OFFER)

A credential offer is sent from an issuer to a potential holder. It
communicates the type of credential on offer, and possibly its proposed
contents. It may also communicate a price that must be paid before the
credential is issued.

```json
{
  "schema_seq_no": "<a reference to the schema, like sequence number of schema on the ledger>",
  "issuer_did": "<reference to the issuer, like the issuer’s DID on the ledger>"
}
```

Credential offers sent to a potential holder are typically followed by
[credential request](cred-request.md) that flow the other direction.
