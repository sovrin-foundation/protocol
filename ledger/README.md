# Ledger
A public, tamper resistant data structure that is capable of storing and maintaining the core Public Objects 
(DIDs/DID docs, Schema, Credential Defs, Revocation Registries, and Anchors) could provide the Public Ledger 
requirements of the protocol, as long as the consumers of those objects trusted that particular ledger.

## What's on the Ledger?
The protocol stores the following Public Objects on the ledger:  
*Note:  The public objects on the ledger are created/updated through events called transactions. Each transaction has 2 sections, a payload section called `data` and a meta section called `reqMetadata` that will contain metadata like the author of transaction, nonce for the transaction, etc.*
1. Public DIDs and DID Documents (including public keys and addressable endpoints)
2. Schema
3. Credential Definitions (references a Schema and includes public keys created by the Issuer for that type of Credential)
4. Revocation Registries (references a Credential Definition)
5. Anchors (root hashes of other ledgers, private or public, to aid in proof of existence)

## What's not on the Ledger?
1. Private keys, symmetric keys,
1. Personally identifiable information (PII), including hashes of PII
1. Credentials
1. Proofs
1. Authentication or Verification transactions

### Requirements
Generating a new Credential does not require writing to the ledger if the Issuer pre-issues the Revocation Registry. Authentication does not require a write to the ledger either; it requires a read though. 

Proving a Credential is not revoked, and verification of a Proof both require reading from the ledger. Therefore the ledger should have good read performance.

#### Interoperability Requirements
**TODO**