# TODOs
This document describes the various items that need to be flushed out or reconciled with different documents in this repository. 
Creating github issues from them is an option too.

1. Reconcile the following with [Self-Sovereign Privacy By Design](self_sovereign_privacy_by_design_v1.md)
```
## Core Principles

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
```