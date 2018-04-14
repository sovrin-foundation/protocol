# Credential Offer

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

## Code reference (some of below might be different from above mentioned structure, the above structure should be considered the correct representation)
1. [Credential Offer](https://github.com/hyperledger/indy-sdk/blob/ad77c94a1116d2540bc6365cdb4f22ebfc899401/libindy/src/domain/credential_offer.rs#L7)
