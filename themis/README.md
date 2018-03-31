![Themis](themis.jpg)

# Themis

Themis is a protocol that enables powerful proving, so identity
owners can know what they need to know about one another, trust that information,
but also respect one another's privacy. Its name refers to a titan in
Greek mythology who embodied judgment, impartiality, and fairness; she
is often shown blindfolded and holding scales, which resonates with the
anonymous, attribute-based credentials and the zero-knowledge proofs
at the heart of Themis technology.

Themis is a subset or subprotocol of the overarching protocol for self-sovereign
identity. It assumes the existence of certain ecosystem features such as
agents and keys, and is thus dependent on a larger context.


## Guiding Principles of this Protocol
### Distributed/Decentralized
### The Identity Owner is in control
TODO: They hold the claims. They don't have to go back to the issuer to tweak their claims to be able to prove things on their claims.

No coordination needed between Issuer and Relying Parties.

##### Selective Disclosure
###### True Selective Disclosure requires ZKP
There are levels of selective disclosure. An example of selective disclosure might be to share just my birthdate from my driver license when I need to prove I'm over the age of 18. My birthdate can be used with other information about me shared or inferred to strongly identify me. We can do better.

Better would be to share just my birth year, which would work for most people. If I turned 18 this year, then I'd have to fall back to showing birth month, and possibly birth day as well. But it's still better.

Better might be to ask the Issuer to include a field just for "over 18". Then I can prove I'm over 18 without sharing my birthdate or birth year. But what if I'm a smoker and I travel to a state in the US that has a 19 year-old smoking statute? What if I'm over 18 in the UK, and I travel to the US and want to prove I'm over 21 (US drinking age).
