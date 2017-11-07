# The Sovrin Protocol
The Sovrin Network is a pioneer in Self-Sovereign Identity (SSI). This style of digital identity is designed to be private, secure and in control of the owner of the Identity. SSI enables trusted interactions between individuals, organizations, and things. These interactions often need to be bidirectional in ways that differ fundamentally from assumptions made for the traditional Client-server model. These interactions need to be initiated by both parties. These interactions need to be verifiable by both parties. These interactions need to be private.

## Asynchronous Messaging
The framework provides the construct that both parties in an interaction could require human input. Additionally, either party may require time to consider either party's terms. These terms could be complex or simple. These terms could have short or long term implications. Because of this, it's important that either party have time to consider what is being negotiated. Asynchronous message allows either party time to respond to messages.

## Guiding Principles of SSI
TODO reconcile and reference "Sovereign Privacy by Design" document.
1. The Identity Owner is in control
    1. Distributed/Decentralized
1. Privacy by design
1. Security by design
1. Accessible for all
    1. Financially accessible to all (basic identity should be free or very low cost)
    1. Functionally accessible (great experience for all users, including low latency)
    1. Scales to work for every person, org, or thing

## Guiding Principles of this Protocol
### Distributed/Decentralized
### The Identity Owner is in control
TODO: They hold the claims. They don't have to go back to the issuer to tweak their claims to be able to prove things on their claims.

No coordination needed between Issuer and Relying Parties.

### Privacy by design
##### Encrypted communications
TODO: Encrypted only for the recipient.
cover use of PKAE
When signing should and should not be used

##### Selective Disclosure
###### True Selective Disclosure requires ZKP
There are levels of selective disclosure. An example of selective disclosure might be to share just my birthdate from my driver license when I need to prove I'm over the age of 18. My birthdate can be used with other information about me shared or inferred to strongly identify me. We can do better.

Better would be to share just my birth year, which would work for most people. If I turned 18 this year, then I'd have to fall back to showing birth month, and possibly birth day as well. But it's still better.

Better might be to ask the Issuer to include a field just for "over 18". Then I can prove I'm over 18 without sharing my birthdate or birth year. But what if I'm a smoker and I travel to a state in the US that has a 19 year-old smoking statute? What if I'm over 18 in the UK, and I travel to the US and want to prove I'm over 21 (US drinking age). 

One of the core principles of SSI is the issuer has minimal coordination with relying parties. The more flexibility an Identity Owner has to prove things about him/herself the more useful those claims will be.

Sovrin claims allow the identity holder to prove arbitrary predicates. My driver license can contain my birthdate, and I can prove that I am over 18, or over 19, or over 27.5, or under 65.

Even for (especially for) service providers.

###### True Selective Disclosure prohibits sharing identifiers and signatures across relationships
This concept is also known as Correlation Privacy.

Using the same identifier for multiple relationships allows the other partyies in those relationships to share information with each other in a very efficient way. If I share my identifier and attribute x with party A, and I share that same identifier and attribute y and z with party B, then A and B have an easy way to horse-trade data about me, create a collective identity record for me. This shadow identity record would be under their control, not mine.

Zero-knowledge proofs mentioned above are also required to combat another correlation point. Hashes and traditional digital signatures are by design universally unique. If I use a digital signature from an Issuer to prove attribute w and x to relying party A, and I use the same digital signature to prove attribute x and y to relying party B, then that signature serves as a unique identifier. ZKP allows for proving attributes without sharing the unique digital signatures from the issuers.

Facebook is a centralized service, but even they realized this correlation risk in their SSO product and shifted to using unique identifiers for you when you authenticated for different Relying Parties.

Some may argue that Relying Parties will simply ask for enough information to uniquely identify you anyway, so its no use. But until a system that allows for true selective disclosure and privacy from correlation is in use, legislatures won't prescribe its concepts, and people won't know its possible. Sovrin claims come with this built in. When Relying Parties realize they can have strong authentication without collecting excessive toxic PII, they'll stop asking for so much of it. When people get used to sharing less PII with so many Relying Parties, they will become warry of those Relying Parties that ask for too much.

##### Minimize dependency on the ledger.
### Security by design
##### Keys at the edge
##### Private keys never shared
##### Every actor is authorized and every communication verified (zero-trust)
##### Principle of least privilege/authorization (zero-trust)
##### Minimal risk and impact of impersonation (service providers)
### Financially accessible to all
##### Minimize writes to the ledger.
### Functionally accessible
##### Allow for third party app and service providers while maintaining other security and privacy principles.
### Scalable
##### Minimize dependency on the ledger, especially writes.

## Basic Description of What's Possible with Sovrin


## What's on the Ledger?
The protocol stores the following Public Objects on the ledger:
1. Public DIDs and DID Documents (including public keys and addressable endpoints)
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
Issuers of Claims need three basic Public Objects before they can issue Claims:
1. A public DID and Document
1. Claim Definition
1. Revocation Registry

#### Public DID and DID Document
An Issuer is a well-known entity in the Sovrin ecosystem. They have a public DID that is referenced by their Claim Definitions and Revocation Registries.

#### Claim Definition
An Issuer issues Claims against a Claim type, called a Claim Definition. A Claim Definition includes two major parts: (1) a reference to a Schema, and (2) public keys specific to the claims issued under that Claim Definition. 

The Schema is a template that defines the fields and data types and semmantics of the claims issued under that Claim Definitions. A Claim Definition can reference any public Schema created by any party. This allows for public reuse of well-designed Schema, which should drive consolidation and a form of standardization over time.

The public keys are used by Relying Parties to verify that a person really has a Claim from that Issuer. The issuer generates special private and public keys for a specific Claim Definition, securely storing the private keys, and publishing the public key in the Claim Definition on the ledger.

These terms are abstract, so an example may be helpful. The California State Driver License Division is an Issuer. They have a number of fields on their standard driver license. They create a Schema (if an existing one is not adequate) with those fields they use on their driver license today. They call this Schema "Bear Driver License" and give it version "0.1.0". They generate the keys and publish it in a Claim Definition they call "California Driver License" and give it version "1".

#### Revocation Registry
Holders of Claims need the ability to prove the claim they have is valid without that Proof being linkable back to the Issuer or another Relying Party. The Revocation Registry is a mechanism that allows a Claim holder to prove in zero knowledge that the claim they hold has not been revoked.

During setup, an Issuer creates one or more Revocation Registries to support this ability with its Claim holders. The claims hold a special serial number that allows the holder to prove theirs is not revoked.

Issuers can issue and revoke claims in the Revocation Registry in a way that doesn't disclose which claim was revoked. If an issuer does not add the claim to the Registry, or revokes a claim, the next time a Claim holder tries to prove their claim is not revoked, they will be unable to do so.

#### Pre-issuing the Revocation Registry
A claim needs to have its serial number in the Registry in order for its holder to prove it has not been revoked. Because Issuance and Revocation are done with a public Registry, there are some timing attacks that are possible if a Relying Party knows exactly when a Claim is issued. To guard against this, we recommend Issues pre-issue their revocation registry. This means every Claim they issue will already be part of the Registry, and only revocations need to be written to the public Registry.

This has a side benefit of reducing the number writes to the public ledger, which also removes the chance of race conditions from using a claim too quickly after issuance. Of course more advanced schemes could be employed to limit timing attacks, but pre-issuance appears to be a simple solution.

### Connecting Sub-Protocol
TODO

### Issuing Sub-Protocol
Issuing a Claim follows the Negotiation Pattern. Here is the mapping to the pattern:
* The **Identity Owner** assumes the *Acquirer* role in the pattern. 
* The **Issuer** assumes the *Producer* role.
* The **Claim Offer** maps to the *Offer*.
* The **Claim Request** maps to the *Request*.
* The **Claim** maps to the *Matter* in the pattern.

If an Issuer is pre-issuing its Revocation Registry (recommended), then no ledger write is required for Issuance. Otherwise, a single transaction to update the Revocation Registry is required before the Claim can be used.

Updates to a Revocation Registry can be batched. If an Issuer issues and revokes a large number of Claims in a short period of time, these issuances and revocations can be combined in one efficient transaction.
### Proving Sub-Protocol
Proof presentation and verification also follows the Negotiation Pattern, . The sub-protocol would start with a 

Proving also follows the Negotiation Pattern with one caveat: there would not be a leading Proof Offer. The Proving Sub-Protocol would start with a Proof Request. If a Claim holder could not or wished not to provide Proof as requested by the Proof Request, then the claim holder could send a Proof Offer that described the proof it was willing to share.

Here is the mapping to the pattern:
* The **Relying Party** assumes the *Acquirer* role in the pattern. 
* The **Identity Owner** assumes the *Producer* role.
* The **Proof Offer** maps to the *Offer*.
* The **Proof Request** maps to the *Request*.
* The **Proof** maps to the *Matter* in the pattern.

### Revoking Sub-Protocol
Revocations are accomplished with a Ledger write to the Revocation Registry. The Issuer should also communicate with the Claim holder directly that their Claim was revoked using a CLAIM_NOTICE message.

## Agents
Agents are dedicated to supporting one *Owner* (person or organization), which is perpendicular to modern web infrastructure and horizontal architectures in general. An agent is aligned to one and only one Owner, and everything it does is in direct support of that one Owner.

Agents come in two basic flavors: (1) Edge Agents and (2) Cloud Agents.

### Edge Agents
An owner's Edge Agents, also known as Local Agents, are Sovrin-aware code (perhaps in the form of an SDK) running on a device in direct control of the owner. Devices include mobile phones, tablets, PCs, and servers controlled by enterprises. 

### Cloud Agents
An owner's Cloud Agents are any of its agents running on hardware physically controlled by another party.

## An independent protocol for Self-Sovereign Identity
Any public ledger that is capable of storing and maintaining the core Public Objects (DIDs/DID docs, Schema, Claim Defs, Revocation Registries, and Anchors) could provide the Public Ledger requirements of the protocol, as long as the consumers of those objects trusted that particular ledger.

### Interoperability Requirements

### Ledger Requirements
Generating a new Claim does not require writing to the ledger if the Issuer pre-issues the Revocation Registry. Proving does not require a write to the ledger either. 

Proving a Claim is not revoked, and verification of a Proof both require reading from the ledger. Therefore the ledger should have good read performance.
