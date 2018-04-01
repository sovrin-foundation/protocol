![Janus](Janus_coin.png)

# Janus

Janus is the current codename for a protocol that enables secure
communication between peers. Its name refers to the two-faced figure
from Greek mythology, emphasizing a two-way flow of information.
(This also feels appropriate for the classic crypto communication
stories that feature Alice and Bob.)

Janus is a subset or subprotocol of the overarching protocol for self-sovereign
identity. It assumes the existence of certain ecosystem features such as
agents and keys, and is thus dependent on a larger context.

Janus partially overlaps the mandate of other secure communications
protocols like Signal. The implications of this overlap are discussed
elsewhere.

## Requirements

At the highest level, Janus aims to:

1. Provide highly secure comm channels, with correct basis for trust.
2. Provide strong privacy guarantees as demanded by [Self-Sovereign
   Privacy by Design](
   https://docs.google.com/document/d/1fKAw9p8eJI-7hAZuQ8orMDGYnq7WWpE6QYs1YOTWgjA/edit).
3. Facilitate interoperability (be usable from many programming languages,
   many blockchain technologies, many vendor stacks, many OSes and
   hardware platforms).
4. Be [transport-agnostic](message-transport.md) (work over http, raw tcp, smtp, snail mail,
   carrier pigeon...).
5. Be [composable](message-packaging.md) and [extensible](message-inventory.md).

## Agents and Keys

People and institutions can't speak raw bytes on a wire; they use software
that works on their behalf. Each independent, key-weilding piece of software
that represents an identity owner is called an agent.

There are two agent types:

 * Edge. Run on hardware directly controlled by identity owner: mobile
   devices, laptops, on-prem servers (where identity owner = institution).
   High trust. Manage the most privileged keys.
 * Cloud. Run on hardware controlled by someone else: agents as a service.
   Trust is somewhat tempered. Manage delegated, limited-use keys.

Every participant in secure communication has at least one agent.
Each agent needs its own keys. Keys are never copied. (For deep
background on the theory and principles of key management espoused
by Janus, see [this recent research report](https://drive.google.com/open?id=1OEOl5cv69dEidK_Efx8blcYwgyPiaU_c).)

The distinction between identity owners and their keys and agents, and
the trust differences between technologies in the cloud and at the
edge, imply a [model of the ecosystem that is layered and nuanced](message-layers.md).

## Messages

At its heart, Janus thinks about communication as an asynchronous exchange
of messages over any untrusted, unreliable transport. To whatever transport
is used in a given context, Janus adds features such as security, privacy,
reliability, and synchronous patterns like http's familiar request/response.
This allows Janus to provide value in classic RESTful paradigms as well as:

* Broadcasts, pub/sub
* Occasionally connected agents
* Fancy routing (store-and-forward, onion, delivery delay)
* Other protocols (e.g., snail mail)
* Semi-trusted agents (progressive/muted auth, limited proxies)

Janus message exchange can also negotiate an alternate,
non-message-oriented communication mode with trust and security rooted
in Janus's standard guarantees (e.g., video streaming over a channel
built with Janus messages).

The [inventory of Janus message types](message-inventory.md) is open; anybody can add their own.

## Composability and Transformations

In snail mail, the content of a message and its packaging are orthogonal.
You can send a bill, a photo, or a love letter--or another envelope--in the
same envelope.

Janus messages are similar. Core messages (what you put inside the
envelope) can be packaged and repackaged per circumstance, with
[_transformations_](message-packaging.md).

## Case Studies

Perhaps the best way to understand Janus is to observe it in action.
See the following:

* [Love Letters](love-letters.md)