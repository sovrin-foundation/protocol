<!-- TODO: Add link to this doc from here: https://wiki.hyperledger.org/projects/indy/documentation -->

# The Sovrin Protocol

The Sovrin Protocol is a set of standards, patterns, and tools that facilitate
trusted interactions. People, institutions, and things can use this
protocol to interact with one another in many different ways--but
always as peers, in patterns that are easy, powerful, secure, and private.

The protocol adapts to many different circumstances. it can be used on the
internet, over sneakernet, or even with snail mail.

The protocol is rooted in principles of Self-Sovereign Identity (SSI). Instead of
having powerful institutions grant marginalized people identities based
on siloed data, SSI puts identity owners in charge. They create their
own identity, manage keys, and share personal data however they want,
without constraints or approval from others. Nobody can take their identity
away.

## Safe and Empowered Digital Interactions

Alice wants to interact with many different parties.

She wants to interact with minimal reliance on third parties. She wants to control her interactions such that another party cannot interfere with her interactions.

Alice's digital interactions should be safe. Safe from prying eyes. Safe from services that track her without her knowledge and consent. Safe from unwanted correlation. Compromised third parties should not be able to impersonate her, or impersonate others to her. She should be safe from apps that exploit their positions of trust. Safe from disclosing more information about herself than is needed. 

Alice's digital interactions should be easy. Being secure should not be harder than not being secure. She shouldn't have to make a decision to be private; she should be private by default. She should have good information about the intent of interactions. Her apps should help her make good decisions about what to share and who to trust. 

Alice should be able to establish trust with another party with minimal reliance on third parties and minimal exchange of personal information. She should be able to make informed decisions about who is trustworthy. She should be able to share her reputation and attributes across contexts without revealing private information. Alice should have strong assurances in her digital interactions without the need for exchanging secrets and personal information.

Alice's digital interactions should be essentially free, regardless of her circumstances and regardless of where she lives in the world. The cost of interactions should not be an inhibitor to interaction. Alice should not feel compelled to give up privacy in exchange for the ability to interact digitally, or compelled to exchange her personal information for services.

For a more detailed discussion on these principles, see [Self-Sovereign Privacy By Design - V1](https://docs.google.com/document/d/1fKAw9p8eJI-7hAZuQ8orMDGYnq7WWpE6QYs1YOTWgjA).

## Three Dimensions of Self Sovereign Identity

There are three orthogonal dimensions to Alice's digital identity: (1) her _relationships_, (2) her _attributes_, and (3) her _agents_. Separating these three dimensions allows for a clear understanding of how they interact. Conflating them confuses the proper role of actors and systems and weakens security.


### Relationships

In Sovrin, identifiers are not shared across contexts. This helps protect privacy by limiting unwanted or cross-context correlation. Identifiers can be thought of as correlation handles. Correlation handles, like identifiers, are very useful within a context. Because identifiers are not shared across contexts, Alice uses a different identifier for every relationship she has. Because each relationship, or "pairing," has a different identifier, you can say that Sovrin uses pairwise identifiers.

Sovrin's identifiers are DIDs. DID stands for Decentralized Identifier. (For more information, see the DID specification here: https://w3c-ccg.github.io/did-spec/).

Sovrin supports groups. It can be said that a pair is just another group, a group of two. There are cases where an identifier is well known, as in the case of a public corporation. The corporation can have one or more "public" DIDs. This holds true to the constraint that identifiers are not shared across contexts, because the context of these DIDs is a public one. This is an exceptional case. Even though a person is free to create a public DID, it's important not to attach to that DID a reputation that must be carried into other contexts.


### Attributes

In Sovrin, a person has two types of attributes. The first type is assertions about oneself, like "my name is Alice," or, "my favorite color is yellow." The second are asserted by others. For example, a driver license with my address on it. Essentially, this is an assertion made by the State of California that I live at this address.

The party asserting some attributes about another is called an **Issuer**. When Alice holds a driver license, she can share that with Bob and Bob can examine it. If Bob believes that that driver license is authentic, and Bob trusts the State of California in its address verification processes, Bob can rely on the fact that Alice lives at 123 Oak Street.

A **Credential** is a generic term to refer to a collection of attributes. A driver license is a credential. A credential allows Alice to prove to Bob that another party asserts some attributes about Alice.


### Agents

In the physical world, we can speak with our mouths, sign documents with our hands, hear with our ears. In the digital world, we have software and hardware that we use to accomplish the digital equivalent. These bits of running code that operate on our behalf are called, Agents.

My mobile phone can run an agent. My notebook computer can run an agent. I can ask a service provider to host an agent in the cloud.

The important thing to note is an agent is owned by one person or entity. Even if I have an agent hosted by a service provider, it's still my agent.

Some agents have endpoints. These agents can serve as simple message proxies or perform more sophisticated actions.

There are two types of agents: Edge Agents, and Cloud Agents. 

<!--TODO: reconcile with description found elsewhere--> 

Agents hold keys which are authorized for certain types of activity. This allows the Agent owner to assign low privileges to lower trust Cloud Agents, and higher privileges to higher trust Edge Agents. Some privileges may require multiple keys held by different agents, including agents of trusted associates, to perform certain types of activities. For example, perhaps a funds transfer of greater than $10,000 USD would require two of four keys (phone, tablet, notebook, and Alice's partner, Bob).

Reference diagrams found here: [DIDs, Credentials and Agents slides](https://docs.google.com/presentation/d/1oz1uB7y4J6GuqlmzyRmqrAwNiQQaWXs1LuHfFayAnwI) 

<!--TODO: pull diagrams into this doc.--> 

## The Intersections of Dimensions


### The Relationship-Attribute Plane

Because Relationships and Attributes are orthogonal, an Issuer does not issue credentials to a particular DID. Remember DIDs are contextual to a relationship, and Credentials should be usable in different relationships without sharing correlation handles across relationships or contexts.

Sovrin Credentials allow for an individual to dynamically generate proofs from one or more credentials as needed for any relationship, without correlation handles. This is not to say that cross-context correlation can't be done with shared attributes, but the control is in a person's hands, and the protocol does not leak correlation handles.


### The Relationship-Agent Plane

Because a person or entity has multiple relationships (represented by a pair of DIDs), an agent must support multiple DIDs. 

Not every agent needs to be used in every relationship. The Relationship-Agent plane is represented by a "Relationship State Machine," which is implemented with a replicated microledger. Agents are authorized within the microledger so the other party (or parties) in the relationship know how to regard each agent (identified by public key). For example, a cloud agent may _not_ be authorized to sign on behalf of a person, but may be authorized to communicate GPS coordinates or be a message relay.

<!--TODO: RSM reference above is probably too much detail for this section-->

Note, an agent does not have its own DID. DIDs are orthogonal to Agents. Agents can be identified by a local name, or by the public key used in a particular relationship. 

Each DID/Agent combination requires a separate key. An endpoint must be unique per relationship, so an agent must support multiple endpoints. 

Agents are authorized for certain types of activity _for each relationship_. This allows the Agent owner to use different agents in different contexts.


### The Agent-Attribute Plane

The keys Agents have are stored in their own wallets. Wallets can hold credentials (verifiable collections of attributes).

Agents have special keys that allow them to be able to generate proofs about credentials they hold. The Agent Authorization Policy is a combination of a ledger smart contract and a cryptographic accumulator that allows for proving in zero knowledge that a device is an authorized device. This allows a person or organization to have a sophisticated recovery policy and to be able to revoke a compromised agent.

A person may selectively copy credentials to different agents, effectively limiting which credentials are provable from which agents.

For more details about how these dimensions work together, see [How DIDs, Keys, Credentials, and Agents Work Together in Sovrin](https://docs.google.com/document/d/1hnQPEdfmAG-DnXGrDXowjc5J571pK7Ub4bWkUlzrH1Y).

## General Requirements

In order to achieve the conceptual model, we need the following:
1.  A Secure and private way to connect to and communicate with peers
1.  A privacy-respecting ability to prove things about oneself and assert things about others
1.  Control all her things (e.g., agent provisioning, authorization, revocation)
1.  Privacy respecting value transfer that is compliant with regulation
1.  Semantic Interop (intra-ecosystem)


### Secure peer-to-peer communications

**TODO: introduce these concepts**
*   DID specification
*   Janus Protocol
*   Public Registry for DIDs, public keys, endpoints
*   Microledgers


### Privacy-respecting issuing and proving

**TODO: introduce these concepts**
* Privacy-respecting attribute exchange protocol
  * TODO: https://github.com/evernym/protocol/blob/changes/attribute-exchange.md
* Public Registry for Issuer public keys and Signature types
* Public Registry for Credential Revocation


### Control

**TODO: introduce these concepts**
* (e.g., agent provisioning, authorization, revocation) (**3DIM**)
  * [Microledgers](https://github.com/evernym/protocol/blob/aff664fff9184d66fcb496c19398350ee4077db6/relationship-state-machine.md)
* Decentralized Key Management protocol
  * [DID / DID Document](https://github.com/evernym/protocol/blob/aff664fff9184d66fcb496c19398350ee4077db6/did.md)
  * **TODO: DKMS reference**
* Public Registry for Agent Authorization Policy
  * [Agent Authorization Policy](https://github.com/evernym/protocol/blob/aff664fff9184d66fcb496c19398350ee4077db6/dkms/overview.md)


### Privacy-respecting value transfer

**TODO: introduce these concepts**
*   Public Ledger for payments
TODO: Summarize and reference 


### Semantic interoperability

**TODO: introduce these concepts**
* Public Registry for Schema
* The Sovrin Type System
* Proof Request Proposal
  * [Concept: Schema Elements](https://docs.google.com/document/d/1VT8myB5XcCJIrIU2xEE3Vgxpfa47aFswh1qDCFCBtIg)
  * [Sovrin.org/schema](https://drive.google.com/open?id=130GRcmmLwDgCY9bgf9bYHarymkDLXO-R)

<!--TODO: content needed: Must work for individuals and institutions alike.-->

<!--TODO: content needed: Using Sovrin as the Public Registry and the Public Ledger.-->

##Key components of the Sovrin Protocol include:

* [Janus](janus/README.md) (a protocol for secure and private communication)
* [Themis](themis/README.md) (a subprotocol for issuing digital credentials and proving things with them in a privacy-preserving way)
* [Hermes](hermes/README.md) (a subprotocol for exchanging economic value)
