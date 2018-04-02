# Credential Definition
*Note:The objects described below are the desired format, the current format will soon be changed to the one mentioned below. Also in the actual ledger transaction, the format described below will be wrapped in a `data` section and there will be one more section called `reqMetadata` that will contain metadata like the author of transaction, nonce for the transaction, etc.*  

A credential definition associates an issuer and their public issuance
keys with a particular schema and revocation strategy. Credential
definitions are published on the ledger. 

```json
  "schemaId":21,      // txn no of the schema
  "sigType": "CL",
  "publicKeys": {
    // One or more public keys, one is described below below
  },
```

**Public key**:
```json
{
  "rctxt": "<large integer>",
  "z": "<large integer>",
  "n": "<large integer>",
  "s": "<large integer>",
  "r": {
    "<field1>": "<large integer>",
    "<field2>": "<large integer>",
    ...
  }
}
```

## Revocation Registry Definition
```json
{
  "revocDefType":"type-3-pairing",
  "credDefId":"<reference to the credential definition>",
  "issuanceType": "<issued by default or not>",
  "maxCredNum": 1000000,
  "publicKeys": {
      // One or more public keys, one is described below below
  },
  "tailsHash": "<SHA256 hash>",
  "tailsLocation": "<URL>"
}
```

**Public key**:
```json
{
  "y": "<large random number in G2>",
  "pk": "<large random number in G1>",
  "h": "<large random number in G1>",
  "h0": "<large random number in G1>",
  "h1": "<large random number in G1>",
  "h2": "<large random number in G1>",
  "htilde": "<large random number in G1>",
  "hhat": "<large random number in G2>",
  "g": "<large random number in G1>",
  "gprime": "<large random number in G2>",
  "u": "<large random number in G2>",
  "qr": "<large number specifying the order of the pairing group>",
}
```
## Revocation Registry Entry
```json
{
  "revocRegDefId": "reference to the revocation registry definition",
  "revocDefType":"type-3-pairing",
  "prevAccum":"<prev_accum_value>",
  "accum":"<accum_value>",
  "issued": [], (optional)
  "revoked": [],
}
```