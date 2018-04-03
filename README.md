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

For a more detailed discussion on these principles, see [Self-Sovereign Privacy By Design - V1](self_sovereign_privacy_by_design_v1.md).

## Three Dimensions of Self Sovereign Identity

There are three orthogonal dimensions to Alice's digital identity: (1) her _**relationships**_, (2) her _**attributes**_, and (3) her _**agents**_. Separating these three dimensions allows for a clear understanding of how they interact. Conflating them confuses the proper role of actors and systems and weakens security.


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
1.  A privacy-respecting ability to assert things about others and prove assertions about oneself
1.  Control all her things (e.g., agent provisioning, authorization, revocation)
1.  Privacy respecting value transfer that is compliant with regulation
1.  Semantic Interoperability


### Secure peer-to-peer communications
In order for Alice to interact securely with others, she'll need a transport-agnostic protocol that encrypts all information and ensures authentication at every level. It should allow for multi-hop communications without needing to trust intermediaries or share more information than is minimally necessary to accomplish the task. [Janus](janus/README.md) is the working name for a protocol that meets these requirements. Janus uses pairwise [DIDs](did.md) as mentioned above. 

Alice also needs a way to bootstrap trust and ensure she's still interacting with who she thinks she is. The Sovrin network acts as a public registry for DIDs, public keys, and endpoints.

### Privacy-respecting issuing and proving

Alice needs a way to receive credentials from issuers. She also needs a way to prove that she holds a credential from a certain issuer with certain attributes or characteristics. She needs to do this in a way that doesn't share any more information than she intends. And her sharing of attributes should not result in disclosing correlation handles. [Themis](themis/README.md) is the working name for a protocol that accomplishes these requirements.

The Sovrin network acts as a public registry for Credential Definitions, Schema definitions, and Issuer public keys. Issuer public keys are used to verify proofs from credentials issued.

An issuer of a credential should also be able to revoke that credential. Alice should be able to prove she posesses a credential that is not revoked, without strongly identifying _which_ credential she holds. Privacy-respecting Revocation is a key to any credentialing system. 

The Sovrin network also acts as a public registry for Credential revocation.

### Control
Alice needs to be able to exercise control over her keys and agents without interference and without permission from a third party. * [DIDs and DID Documents](did.md) provide much of that functionality. The [Relationship State Machine](relationship-state-machine.md) is a concept that allows for pairwise relationships to be established securely without an initial write to Sovrin. Microledgers are an implementation of the the Relationship State Machine concept.

With Sovrin, reliance on a centralized third party is removed by design. This moves key management to the edge. It increases the responsibility of applications to properly manage keys and recovery. The [Decentralized Key Management (DKMS)](dkms/README.md) protocol outlines ways in which Alice can significantly reduce reliance on centralized third parties, while still being able to interact safely and securely.

Alice needs a way to provision new agents, and revoke them remotely if they are lost or compromised, and do this without communicating with them. Agent Authorization Policies, outlined in the DKMS documents, help to ensure only trusted devices are able to prove certain things on behalf of Alice. The Sovrin network is used to store a sophisticated agent provisioning policy along with a mechanism for proving in a privacy-respecting way that the agent used is authorized.


### Privacy-respecting value transfer
Alice needs to be able to send payment in interactions with others. For example, paying a fee for a Driver License. She needs a privacy respecting way to pay for transactions on the Sovrin ledger. Issuers need a way to recover some of the costs of issuing. Holders can pay Issuers, as in the case of Alice paying a fee for her driver license. Relying parties can also pay Issuers, as in the case of credit reporting agencies. All this should be done in a privacy respecting way.

<!--TODO: content needed.-->

### Semantic interoperability
Issuers and Relying Parties should not need to have strong agreements in place, or build expensive integrations. Sovrin [Schemas](themis/schema.md) are published on the Sovrin ledger by Sovrin users. Anyone can publish a schema.

Because they are public, others can see what kinds of attributes are being issued in credentials. Relying parties can adjust to those schemas, or suggest improvements to the Issuers.
 
Issuers can see what other issuers in their ecosystem are publishing and what relying parties are accepting. They can adjust to make it easier for relying parties to accept proofs of their credentials.

We believe there will be a natural and organic convergence around the most useful schemas.

<!--TODO: formalize the Sovrin type system.-->

<!--TODO: 

* Proof Request Proposal
  * [Concept: Schema Elements](https://docs.google.com/document/d/1VT8myB5XcCJIrIU2xEE3Vgxpfa47aFswh1qDCFCBtIg)
  * [Sovrin.org/schema](https://drive.google.com/open?id=130GRcmmLwDgCY9bgf9bYHarymkDLXO-R)

-->

<!--TODO: content needed: Must work for individuals and institutions alike.-->

<!--TODO: content needed: Using Sovrin as the Public Registry and the Public Ledger.-->

<!--TODO: directory of concepts mentioned above

## Key components of the Sovrin Protocol include:

* [Janus](janus/README.md) (a protocol for secure and private communication)
* [Themis](themis/README.md) (a subprotocol for issuing digital credentials and proving things with them in a privacy-preserving way)
* [Hermes](hermes/README.md) (a subprotocol for exchanging economic value)

-->

