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
1. Distributed/Decentralized
1. Privacy by design
    1. Encrypted communications
    1. Selective disclosure
    1. Non-correlating
    1. Minimize dependency on the ledger.
1. Security by design
    1. Keys at the edge
    1. Private keys never shared
    1. Every actor is authorized
    1. Minimal authorization of keys
    1. Minimal risk of impersonation by service providers
    1. Zero-trust architecture
1. Financially accessible to all
    1. Minimize writes to the ledger.
1. Functionally accessible
    1. Allow for third party app and service providers while maintaining other security and privacy principles.
1. Scalable
    1. Minimize dependency on the ledger, especially writes.

## Basic Description of What's Possible with Sovrin


## What's on the Ledger?
The protocol stores the following on the ledger:
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

## Orchestrated Interactions
The Sovrin protocol has two rough classifications of communications, (1) Ledger and (2) Peer.

### Ledger Writes and Reads
Ledger communications include the writes and reads to the Sovrin Ledger. Because the Sovrin protocol seeks to minimize dependencies on the ledger where possible, many interactions are peer interactions.

### Peer Interactions
Peer interactions are the heart of the Sovrin protocol.

### Agents
Agents are dedicated to supporting one entity (person or organization), which is perpendicular to modern web infrastructure and horizontal architectures in general. An agent is aligned to one and only one entity, and everything it does is in direct support of that one entity.

Agents come in two basic flavors: (1) Edge Agents and (2) Cloud Agents.

####
Edge Agents, also known as Local Agents, are Sovrin-aware code running on a device in direct control of the owner. Devices include mobile phones, tablets, PCs, and servers controlled by enterprises. 

####
Cloud Agents are any agents running on hardware physically controlled by another party.

