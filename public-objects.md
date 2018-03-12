# Public Objects
The public objects are stored on the ledger as transactions. [This](https://github.com/hyperledger/indy-node/blob/master/docs/transactions.md) document describes the transactions. The objects described below are going to be changes in the near future as described.
## Claim Definition
```json
{
  "schema_ref": "<schema id>",
  "primary": {
    "sig_type": "<primary claim signature scheme>",
    "verification_key": { <scheme-specific public key material> }
  },
  "revocation": {
    "sig_type": "<revocation signature scheme>",
    "verification_key": { <scheme-specific public key material> }
  }
}
```
<span style="color:yellow">TODO: Needs Issuer reference</span>

# Signature Schemes
## Primary Claim Signature Schemes
This is the value of the "primary" element in a Claim Definition.

There is currently only one scheme in use today: CL.
### The "CL" Primary Claim Signature Scheme
```json
{
  "sig_type": "CL",
  "verification_key": {
    "rctxt": "<large integer>",
    "rms": "<large integer>",
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

## Revocation Signature Schemes
This is the value of the "revocation" element in a Claim Definition.

There is currently only one scheme in use today: CL(TODO).
### The "CL(TODO)" Revocation Signature Scheme
```json
{
  "sig_type": "(TODO: what do we all our revocation sig scheme?)",
  "verification_key": {
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

## Code references to ledger objects
1. [NYM](https://github.com/hyperledger/indy-sdk/blob/778a38d92234080bb77c6dd469a8ff298d9b7154/libindy/src/services/ledger/types.rs#L43)
1. [ATTRIB](https://github.com/hyperledger/indy-sdk/blob/778a38d92234080bb77c6dd469a8ff298d9b7154/libindy/src/services/ledger/types.rs#L88)
1. [SCHEMA](https://github.com/hyperledger/indy-sdk/blob/778a38d92234080bb77c6dd469a8ff298d9b7154/libindy/src/services/ledger/types.rs#L144)
1. [CLAIM_DEF](https://github.com/hyperledger/indy-sdk/blob/778a38d92234080bb77c6dd469a8ff298d9b7154/libindy/src/services/ledger/types.rs#L234)
1. [NODE](https://github.com/hyperledger/indy-sdk/blob/778a38d92234080bb77c6dd469a8ff298d9b7154/libindy/src/services/ledger/types.rs#L300)
1. [POOL_CONFIG](https://github.com/hyperledger/indy-sdk/blob/778a38d92234080bb77c6dd469a8ff298d9b7154/libindy/src/services/ledger/types.rs#L457)
1. [POOL_UPGRADE](https://github.com/hyperledger/indy-sdk/blob/778a38d92234080bb77c6dd469a8ff298d9b7154/libindy/src/services/ledger/types.rs#L477)