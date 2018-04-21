# TODO: these need to be organized

## Core Principles

TODO reconcile and reference "Sovereign Privacy by Design" document.
1. The Identity Owner is in control
    1. Distributed/Decentralized
1. Privacy by design
1. Security by design
1. Accessible for all
    1. Financially accessible to all (basic identity should be free or very low cost)
    1. Functionally accessible (great experience for all users, including low latency)
    1. Scales to work for every person, org, or thing


One of the core principles of SSI is the issuer has minimal coordination with relying parties. The more flexibility an Identity Owner has to prove things about him/herself the more useful those credentials will be.

Sovrin credentials allow the identity holder to prove arbitrary predicates. My driver license can contain my birthdate, and I can prove that I am over 18, or over 19, or over 27.5, or under 65.

Even for (especially for) service providers.

###### True Selective Disclosure prohibits sharing identifiers and signatures across relationships
This concept is also known as Correlation Privacy.

Using the same identifier for multiple relationships allows the other partyies in those relationships to share information with each other in a very efficient way. If I share my identifier and attribute x with party A, and I share that same identifier and attribute y and z with party B, then A and B have an easy way to horse-trade data about me, create a collective identity record for me. This shadow identity record would be under their control, not mine.

Zero-knowledge proofs mentioned above are also required to combat another correlation point. Hashes and traditional digital signatures are by design universally unique. If I use a digital signature from an Issuer to prove attribute w and x to relying party A, and I use the same digital signature to prove attribute x and y to relying party B, then that signature serves as a unique identifier. ZKP allows for proving attributes without sharing the unique digital signatures from the issuers.

Facebook is a centralized service, but even they realized this correlation risk in their SSO product and shifted to using unique identifiers for you when you authenticated for different Relying Parties.

Some may argue that Relying Parties will simply ask for enough information to uniquely identify you anyway, so its no use. But until a system that allows for true selective disclosure and privacy from correlation is in use, legislatures won't prescribe its concepts, and people won't know its possible. Sovrin credentials come with this built in. When Relying Parties realize they can have strong authentication without collecting excessive toxic PII, they'll stop asking for so much of it. When people get used to sharing less PII with so many Relying Parties, they will become warry of those Relying Parties that ask for too much.

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

## The Sovrin Sub-protocols
The Sovrin Sub-protocols include:
1. Issuer Setup
1. Connecting
1. Issuing
1. Proving
1. Revoking

### Issuer Setup
Issuers of Credentials need three basic Public Objects before they can issue Credentials:
1. A public DID and Document
1. Credential Definition
1. Revocation Registry

#### Public DID and DID Document
An Issuer is a well-known entity in the Sovrin ecosystem. They have a public DID that is referenced by their Credential Definitions and Revocation Registries.

#### Credential Definition
An Issuer issues Credentials against a Credential type, called a Credential Definition. A Credential Definition includes two major parts: (1) a reference to a Schema, and (2) public keys specific to the credentials issued under that Credential Definition.

The Schema is a template that defines the fields and data types and semantics of the credentials issued under that Credential Definitions. A Credential Definition can reference any public Schema created by any party. This allows for public reuse of well-designed Schema, which should drive consolidation and a form of standardization over time.

The public keys are used by Relying Parties to verify that a person really has a Credential from that Issuer. The issuer generates special private and public keys for a specific Credential Definition, securely storing the private keys, and publishing the public key in the Credential Definition on the ledger.

These terms are abstract, so an example may be helpful. The California State Driver License Division is an Issuer. They have a number of fields on their standard driver license. They create a Schema (if an existing one is not adequate) with those fields they use on their driver license today. They call this Schema "Bear Driver License" and give it version "0.1.0". They generate the keys and publish it in a Credential Definition they call "California Driver License" and give it version "1".

#### Revocation Registry
Holders of Credentials need the ability to prove the credential they have is valid without that Proof being linkable back to the Issuer or another Relying Party. The Revocation Registry is a mechanism that allows a Credential holder to prove in zero knowledge that the credential they hold has not been revoked.

During setup, an Issuer creates one or more Revocation Registries to support this ability with its Credential holders. The credentials hold a special serial number that allows the holder to prove theirs is not revoked.

Issuers can issue and revoke credentials in the Revocation Registry in a way that doesn't disclose which credential was revoked. If an issuer does not add the credential to the Registry, or revokes a credential, the next time a Credential holder tries to prove their credential is not revoked, they will be unable to do so.

#### Pre-issuing the Revocation Registry
A credential needs to have its serial number in the Registry in order for its holder to prove it has not been revoked. Because Issuance and Revocation are done with a public Registry, there are some timing attacks that are possible if a Relying Party knows exactly when a Credential is issued. To guard against this, we recommend Issues pre-issue their revocation registry. This means every Credential they issue will already be part of the Registry, and only revocations need to be written to the public Registry.

This has a side benefit of reducing the number writes to the public ledger, which also removes the chance of race conditions from using a credential too quickly after issuance. Of course more advanced schemes could be employed to limit timing attacks, but pre-issuance appears to be a simple solution.

### Connecting Sub-Protocol
TODO

### Issuing Sub-Protocol
The Issuing sub-protocol describes the interaction between the Issuer and Identity Owner. This interaction follows the Negotiation Pattern. Here is the mapping to the pattern:
* The **Identity Owner** assumes the *Acquirer* role in the pattern. 
* The **Issuer** assumes the *Producer* role.
* The **Credential Offer** maps to the *Offer*.
* The **Credential Request** maps to the *Request*.
* The **Credential** maps to the *Matter* in the pattern.

If an Issuer is pre-issuing its Revocation Registry (recommended), then no ledger write is required for Issuance. Otherwise, a single transaction to update the Revocation Registry is required before the Credential can be used.

Updates to a Revocation Registry can be batched. If an Issuer issues and revokes a large number of Credentials in a short period of time, these issuances and revocations can be combined in one efficient transaction.


### Proving Sub-Protocol
The Proving sub-protocol describes the interaction between the Relying Party and Identity Owner. This interaction also follows the Negotiation Pattern with one caveat: there would not be a leading Proof Offer. The Authentication Sub-Protocol would start with a Proof Request. If a Credential holder could not or wished not to provide Proof as requested by the Proof Request, then the credential holder could send a Proof Offer that described the proof it was willing to share. The Authentication sub-protocol lists guidelines that the Relying Party could follow while constructing the Proof Request such that the Identity Owner can ensure that requested information respects its privacy. 

Here is the mapping to the pattern:
* The **Relying Party** assumes the *Acquirer* role in the pattern.
* The **Identity Owner** assumes the *Producer* role.
* The **Proof Offer** maps to the *Offer*.
* The **Proof Request** maps to the *Request*.
* The **Proof** maps to the *Matter* in the pattern.

### Revoking Sub-Protocol
Revocations are accomplished with a Ledger write to the Revocation Registry. The Issuer should also communicate with the Credential holder directly that their Credential was revoked using a CRED_NOTICE message.


## An independent protocol for Self-Sovereign Identity
Any public ledger that is capable of storing and maintaining the core Public Objects (DIDs/DID docs, Schema, Credential Defs, Revocation Registries, and Anchors) could provide the Public Ledger requirements of the protocol, as long as the consumers of those objects trusted that particular ledger.

### Interoperability Requirements

### Ledger Requirements
Generating a new Credential does not require writing to the ledger if the Issuer pre-issues the Revocation Registry. Authentication does not require a write to the ledger either; it requires a read though. 

Proving a Credential is not revoked, and verification of a Proof both require reading from the ledger. Therefore the ledger should have good read performance.
