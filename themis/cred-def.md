# Credential Definition
*Note: The objects described below are the desired format, the current format will soon be changed to the one mentioned below. The format below is only for the `data` part of the ledger transaction.*

A credential definition associates an issuer and their public issuance
keys with a particular schema and revocation strategy. Credential
definitions are published on the ledger. 

```json
  "schemaId": "<schema id>", // In Sovrin, it is the txn no of the schema
  "publicKeys": {
    "primary": "<issuance public key>", // described below
    "revocation": "<revocation public key>" // described below, this can be empty if the credential is non-revocable.
  },
```

**Issuance Public key**:
```json
{
  "sigType": "<credential issuance signature scheme>", // In Sovrin, there is only 1 as of now called CL,
  "publicKey": {
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
}
```

**Revocation Public key**:
```json
{
  "sigType": "<credential issuance signature scheme>", // In Sovrin, there is only 1 as of now called type-3-pairing
  "publicKey": {
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
}
```

## Revocation Registry Definition
```json
{
  "revocDefType": "<credential revocation signature scheme>",  // In Sovrin, there is only 1 as of now called type-3-pairing
  "credDefId": "<reference to the credential definition>",
  "issuanceType": "<issued by default or not>",
  "maxCredNum": "<maximum number of credentials that can be issued>",
  "publicKey": "public key for the revocation registry", // described belwo
  "tailsHash": "<SHA256 hash>",
  "tailsLocation": "<URL>"
}
```

**Revocation Registry**:
```json
{
  "z": "<the field FP_12>"
}
```

## Revocation Registry Entry
```json
{
  "revocRegDefId": "reference to the revocation registry definition",
  "revocDefType": "type-3-pairing",
  "prevAccum": "<prev_accum_value>",
  "accum": "<accum_value>",
  "issued": [<contains 0 or more entries>], (optional)
  "revoked": [<contains 0 or more entries>], (optional)
}
```

## Code references to ledger objects
1. [CLAIM_DEF](https://github.com/hyperledger/indy-sdk/blob/778a38d92234080bb77c6dd469a8ff298d9b7154/libindy/src/services/ledger/types.rs#L234)
