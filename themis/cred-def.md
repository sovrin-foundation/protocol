# Credential Definition
*Note: The objects described below are the desired format, the current format will soon be changed to the one mentioned below. The format below is only for the `data` part of the ledger transaction.*

For the holder to create a proof from a credential and the relying party to 
verify the proof generated from a credential, they need the issuer's public key. These public keys correspond to the secret keys the issuer used to sign the credential. 
A credential definition associates an issuer and their public issuance
keys with a particular schema and revocation strategy. The credential definition 
also specifies the signature scheme used by the issuer and references the schema for the credential.
Credential definitions are published on the ledger. 

```json
{
  "schemaId": "<schema id>", // In Sovrin, it is the txn no of the schema
  "primary": {
    "sigType": "<primary credential signature scheme>",
    "publicKey": { <scheme-specific public key material> }
  },
  "revocation": {
    "sigType": "<revocation signature scheme>",
    "publicKey": { <scheme-specific public key material> }
  } // described below, this can be empty if the credential is not revocable.
}
```

**Example of primary publicKey**:
There is currently only one scheme in use today, "CL". This is the structure for that scheme.
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

**Example of revocation public key**:
There is currently only one revocation scheme in use today, "CKS" (named after its authors Jan Camenisch, Markulf Kohlweiss and Claudio Soriente, here is the [paper](https://www.microsoft.com/en-us/research/wp-content/uploads/2016/02/main-51.pdf)).   
This is the structure for that scheme.
```json
{
  "y": "<large number in G2>",
  "pk": "<large number in G1>",
  "h": "<large number in G1>",
  "h0": "<large number in G1>",
  "h1": "<large number in G1>",
  "h2": "<large number in G1>",
  "htilde": "<large number in G1>",
  "hhat": "<large number in G2>",
  "g": "<large number in G1>",
  "gprime": "<large number in G2>",
  "u": "<large number in G2>",
  "qr": "<large number specifying the order of the pairing group>",
}
```

## Revocation Registry Definition
This describes the kind of revocation mechanism used for managing the revocation and/or issuance of credentials. 
The kind of data structure used, keys to manage that data structure and other data needed to compute the witness is mentioned here.
```json
{
  "revocDefType": "<revocation registry scheme>",  // In Sovrin, there is only 1 as of now called CL_ACCUM
  "credDefId": "<reference to the credential definition>",
  "issuanceType": "<issued by default or not>",
  "maxCredNum": "<maximum number of credentials that can be issued>",
  "publicKey": "<public data for the revocation registry>", // described below
  "tailsHash": "<SHA256 hash>",
  "tailsLocation": "<URL>"
}
```

**Revocation Registry Public Data**:
```json
{
  "z": "<the field FP_12>"
}
```

## Revocation Registry Entry
When claims are issued and/or revoked, the revocation registry needs to be updated. An update looks like this:
```json
{
  "revocRegDefId": "<reference to the revocation registry definition>",
  "prevAccum": "<prev_accum_value>",
  "accum": "<accum_value>",
  "issued": [<0 or more entries>],
  "revoked": [<0 or more entries>],
}
```

## Code references to ledger objects (some of below might be different from above mentioned structure, the above structure should be considered the correct representation)
1. [CLAIM_DEF](https://github.com/hyperledger/indy-sdk/blob/778a38d92234080bb77c6dd469a8ff298d9b7154/libindy/src/services/ledger/types.rs#L234)
2. [Primary public key](https://github.com/hyperledger/indy-crypto/blob/fc078a014a6b72ede838b79696258d3ee56f87d4/libindy-crypto/src/cl/mod.rs#L161)
3. [Revocation public key](https://github.com/hyperledger/indy-crypto/blob/fc078a014a6b72ede838b79696258d3ee56f87d4/libindy-crypto/src/cl/mod.rs#L202)
4. [Revocation Registry Definition](https://github.com/hyperledger/indy-crypto/blob/fc078a014a6b72ede838b79696258d3ee56f87d4/libindy-crypto/src/cl/mod.rs#L229)
5. [Revocation Registry Entry](https://github.com/hyperledger/indy-crypto/blob/fc078a014a6b72ede838b79696258d3ee56f87d4/libindy-crypto/src/cl/mod.rs#L249)