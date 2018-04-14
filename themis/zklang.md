# ZKLang
A typical interaction between an identity owner and relying party (which has asked for proof) looks like this.
1. Relying party asks for proof using Proof Request.
2. Identity owner examines the proof request and decides what credential definitions need to be used for creating the proof. The resulting object is called a proof resolution.
3. The identity owner then generates a ZKL ProofSpec defining which statement should be proven. It contains only public information (since the verifier also gets this object), and contains everything required to verify a proof, so it includes e.g. the public keys of issuers.
4. The identity owner takes his private data like values of all the attributes, credentials, link secret that is used in the chosen credentials to create a ZKL Witness.
5. The identity owner then uses the ProofSpec and Witness data to create a Proof object.
6. The identity owner then sends the Proof and the ProofSpec to the relying party.
7. The relying party then checks that the ProofSpec is compatible with the sent Proof Request and then verifies the Proof.