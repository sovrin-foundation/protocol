# The Sovrin Protocol
The Sovrin Network is a pioneer in Self-Sovereign Identity (SSI). This style of digital identity is designed to be private, secure and in control of the owner of the Identity. SSI enables trusted interactions between individuals, organizations, and things. These interactions often need to be bidirectional in ways that differ fundamentally from assumptions made for the traditional Client-server model. These interactions need to be initiated by both parties. These interactions need to be verifiable by both parties. These interactions need to be private.

## Asynchronous Messaging
The framework provides the construct that both parties in an interaction could require human input. Additionally, either party may require time to consider either party's terms. These terms could be complex or simple. These terms could have short or long term implications. Because of this, it's important that either party have time to consider what is being negotiated. Asynchronous message allows either party time to respond to messages.

## Guiding Principles of SSI
1. Distributed/Decentralized
1. Privacy by design
1. Security by design
1. Financially accessible to all (basic identity should be free or very low cost)
1. Functionally accessible (great experience for all users, including low latency)
1. Scales to work for every person, org, or thing

## Guiding Principles of this Protocol
### Distributed/Decentralized
### Privacy by design
##### Encrypted communications
##### Selective disclosure
##### Non-correlating
##### Minimize dependency on the ledger.
### Security by design
##### Keys at the edge
##### Private keys never shared
##### Every actor is authorized
##### Minimal authorization of keys
##### Minimal risk of impersonation by service providers
##### Zero-trust architecture
### Financially accessible to all
##### Minimize writes to the ledger.
### Functionally accessible
##### Allow for third party app and service providers while maintaining other security and privacy principles.
### Scalable
##### Minimize dependency on the ledger, especially writes.

## Basic Description of What's Possible with Sovrin


## What's on the Ledger?
The protocol stores the following Ledger Objects on the ledger:
1. Public DIDs and DID Documents (inluding public keys and addressable endpoints)
2. Schema
3. Claim Definitions (references a Schema and includes an Issuer's public keys)
4. Revocation Registries (references a Claim Definition)
5. Anchors (root hashes of other ledgers, private or public, to aid in proof of existence)

## What's not on the Ledger?
1. Private keys, symmetric keys, 
1. Personally identifiable information (PII), including hashes of PII
1. Claims
1. Proofs
1. Authentication or Verification transactions

## The Sovrin Sub-protocols
The Sovrin Sub-protocols include:
1. Issuer Setup
1. Connecting
1. Issuing
1. Proving
1. Revoking

### Issuer Setup
Issuers of Claims need four basic Ledger Objects before they can issue Claims:
1. A public DID and Document
3. Claim Definition
4. Revocation Registry

#### Public DID and DID Document
An Issuer is a well-known entity in the Sovrin ecosystem. They have a public DID that is referenced by their Claim Definitions and Revocation Registries.

#### Claim Definition
An Issuer issues Claims against a Claim type, called a Claim Definition. A Claim Definition includes two major parts: (1) a reference to a Schema, and (2) public keys specific to the claims issued under that Claim Definition. 

The Schema is a template that defines the fields and data types and semmantics of the claims issued under that Claim Definitions. A Claim Definition can reference any public Schema created by any party. This allows for public reuse of well-designed Schema, which should drive consolidation and a form of standardization over time.

The public keys are used by Relying Parties to verify that a person really has a Claim from that Issuer. The issuer generates special private and public keys for a specific Claim Definition, securely storing the private keys, and publishing the public key in the Claim Definition on the ledger.

These terms are abstract, so an example may be helpful. The California State Driver License Division is an Issuer. They have a number of fields on their standard driver license. They create a Schema (if an existing one is not adequate) with those fields they use on their driver license today. They call this Schema "Bear Driver License" and give it version "0.1.0". They generate the keys and publish it in a Claim Definition they call "California Driver License" and give it version "1".

#### Revocation Registry
Holders of Claims need the ability to prove the claim they have is valid without 

In order to support anonymous revocation ()

Because the Sovrin protocol seeks to minimize dependencies on the ledger where possible, many interactions do not require direct ledger writes.

### Agents
Agents are dedicated to supporting one entity (person or organization), which is perpendicular to modern web infrastructure and horizontal architectures in general. An agent is aligned to one and only one entity, and everything it does is in direct support of that one entity.

Agents come in two basic flavors: (1) Edge Agents and (2) Cloud Agents.

####
Edge Agents, also known as Local Agents, are Sovrin-aware code (perhaps in the form of an SDK) running on a device in direct control of the owner. Devices include mobile phones, tablets, PCs, and servers controlled by enterprises. 

####
Cloud Agents are any agents running on hardware physically controlled by another party.

## An independent protocol for Self-Sovereign Identity
Any public ledger that is capable of storing and maintaining the core Ledger Objects (DIDs/DID docs, Schema, Claim Defs, Revocation Registries, and Anchors) could provide the Public Ledger requirements of the protocol, as long as the consumers of those objects trusted that particular ledger.

### Interoperability Requirements

### Ledger Requirements
