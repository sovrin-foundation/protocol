# Agent Authorization Policy

## Version 2

Each Identity Owner creates a Policy on the ledger, identified by an address **_I_**. Each agent has an agent policy keypair that will be used with **_I_**. The policy allows a key to have some combination of four authorizations:

* **0: PROVE**: agents with this authorization can create Proofs from Credentials

* **1: PROVE_GRANT**: agents with this authorization can grant the PROVE authorization to other agents

* **2: PROVE_REVOKE**: agents with this authorization can revoke the PROVE authorization from other agents

* **3: PROVE_ADMIN**: keys with this authorization can modify the provision or revoke prove authorization policy, i.e., grant and revoke the PROVE_GRANT authorization
    * PROVE_GRANT_GRANT
    * PROVE_GRANT_REVOKE

Each authorization is either on or off. Therefore, an array of authorizations can be represented in a bitmap using the well known position for each authorization. For example in big endian, 0001 represents [PROVE], and 1000 would represent [PROVE_ADMIN].

This is a public record, but no information in this public record is ever shared with any other party. Its purpose is to allow for key management of devices in a flexible way, while allowing for entities to prove in zero knowledge that they are using an agent that is authorized by the entity. This ZKP is possible because the ledger maintains a global accumulator for all keys with the PROVE authorization, called the Prover Registry. When a key is added to a Policy, and that key is given the PROVE authorization, the ledger adds a commitment to the Prover Registry. When a key loses its PROVE authorization, the ledger removes the associated commitment from the Prover Registry.

* A can revoke A, B, and C, and where B, can only revoke B and C

* This is a deep subtopic

### Setup

1. Trusted setup

    1.1. There is a global accumulator needed to store commitments to the keys with PROVE authorizations. 
    From this accumulator, a key can prove in zero knowledge that it is authorized to prove. This requires a trusted setup.

#### ID Owner's First Agent

1. Agent creates policy management keypair (A<sub>p</sub><sup>pk</sup>, A<sub>p</sub><sup>sk</sup>) and a secret S

2. Agent creates policy address **_I_** on the ledger and adds A<sub>p</sub><sup>pk</sup>  as a key and ensures that key has all authorizations (111).

3. Agent adds K = Comm(S, r0) to the PROVE authorization.

    1. What kind of accumulator to use?

4. The ledger adds commitment Comm(K, **_I_**) to the global accumulator **_A_**. The only commitments added to the global accumulator are those values with the PROVE authorization.

#### ID Owner's Subsequent Agents

1. One authorized agent (or multiple agents in a multisig scenario) provisions a new agent. 

2. The new Agent creates policy management keypair (A<sub>p</sub><sup>pk</sup>, A<sub>p</sub><sup>sk</sup>) and S. 

3. New agent sends A<sub>p</sub><sup>pk</sup> and K= Comm(S, r0) back to the provisioning agent(s).

4. Provisioning agent(s) adds authorization for A<sub>p</sub><sup>pk</sup> and K to the policy.

#### Lifecycle

 As agents are granted PROVE authorization in address **_I_****_, _**the ledger adds or removes the commitments to the accumulator. Agents can be added to be provisioners and revokers by admin agents. The provision or revoke policy can be changed to require more than one agent to agree on a change like 2 of 3. The ledger will enforce these rules by requiring multiple signatures complete the transaction.

### Claim Lifecycle

#### Issuance

1. Claim Receiver sends a Claim Request, which contains a blinded link secret and blinded address **_I_**.

2. Issuer selects an index i for the claim from his non-revocation accumulator A<sub>I</sub>.

3. Issuer generates claim C using A<sub>I</sub>.

4. Issuer sends C and A<sub>I</sub> to Claim Receiver.

5. Issuer adds i to his non-revocation accumulator A<sub>I</sub>.

6. Claim Receiver provides C and A<sub>I</sub> to Identity Owner.

7. Identity Owner gives C, and A<sub>I</sub> to Proof Presenter. 

#### Proof Presentation

1. Claim Presenter refreshes the revocation data from the non-revocation accumulator and Prover Registry.

2. Claim Presenter requests access from a Verifier.

3. Verifier sends a Proof Request: what must be proven and which attributes must be disclosed.

4. Claim Presenter sends disclosed claim attributes and other proofs (in zero-knowledge) and a zero-knowledge proof that

    4.1. ![image alt text](aap_0.png)

    4.2. ![image alt text](aap_1.png)

    4.3. ![image alt text](aap_2.png)

    4.4. ![image alt text](aap_3.png)

    4.5. ![image alt text](aap_4.png)

    4.6. 4.6.1. ![alt text](https://latex.codecogs.com/png.latex?%5Cinline%20%5Csmall%20%5Cmathcal%7BA%7D%5Bu%2CN%5D%28b_1%2Cb_2%2C%5Cldots%2Cb_n%29) is defined for prime <img src="https://latex.codecogs.com/png.latex?\inline&space;\small&space;b_i\in[B;B^2-1]" title="\small b_i\in[B;B^2-1]" />

    4.6.1. ![image alt text](aap_7.png) is defined for prime ![image alt text](aap_8.png)

    4.6.2. ![image alt text](aap_9.png)

    4.6.3. We say that ![image alt text](aap_10.png) for ![image alt text](aap_11.png).

    4.6.4. It must hold that ![image alt text](aap_12.png)

    4.7. ![image alt text](aap_13.png)

    4.8. Our intention is to prove the hierarchy of commitments: ![image alt text](aap_14.png) is a commitment to K, which is itself a commitment to S. We want to prove the knowledge of S by putting it in another commitment ![image alt text](aap_15.png).

    4.9. All values not enclosed in ()’s are assumed to be known to the verifier. 

    4.10. We want to prove  ![image alt text](zpok.png)

    4.11. For this we need additional commitments C2,C3,C4.

    4.12. ![image alt text](proof.png)

    4.13. The proof can be prepared as concatenation of

        4.13.1. NIZK{ (r4,C1): (C1 in A) && C4 = Comm(C1,r4) } = CommAcc proof

        4.13.2. NIZK{ (K,I,r2): Credential(I) && C3 =Comm(K,I,r2) } = Claim+commitment proof

        4.13.3. NIZK{ (K,I,r2,r4): C3 = Comm(K,I,r2) && C4 = Comm(Comm(K,I),r4) }        = DComm proof

        4.13.4. NIZK{ (S,r0,r1,r2,I): C2 = Comm(S,r1) && C3 =  Comm(Comm(S,r0),I,r2)}  = DComm proof

    These 4 proofs can be united into a single one with some reduction in size.

    4.14. Claim index i has not been revoked yet.

5. Claim Presenter generates ![image alt text](aap_18.png)

6. Claim Presenter computes

    6.1. ![image alt text](aap_19.png)

    6.2. ![image alt text](aap_20.png)

    6.3. ![image alt text](aap_21.png)

    6.4. ![image alt text](aap_22.png)

    6.5. ![image alt text](aap_23.png)

7. Claim Presenter sends ![image alt text](aap_24.png) to Verifier

8. Verifier validates the proof

    8.1. Computes ![image alt text](aap_25.png) and ![image alt text](aap_26.png)

    8.2. Verifies that ![image alt text](aap_27.png)

"I am a valid claim presenter"<=

<="I am part of the claim policy"<=

<="My public key is part of the claim policy"<=

<="I know the private key of the public key of the claim policy"<=

<="I know a private key and a public key which are part of the policy that is part of the claim."<=

<="I know S which is private key of a public key which is part of the policy that is mentioned in the claim”<=

<="I know K and S s.t. K is a commitment to S, and K is part of the policy that is mentioned in the claim”<=

<="I know K and S s.t. K is a commitment to S, and K is part of policy I, and I is the policy in the claim”

<="I know K and S s.t. K is a commitment to S, and (K,I) belong to the key-policy accumulator in the claim, and I is the policy in the claim”<=

<="Here is C1 which is a commitment to K which is a commitment to S, s.t. (K,I) belong to the key-policy accumulator, and I is the policy in the claim”

### Potential attacks

1. Inspecting the ledger reveals all of the agents that are associated with an entity. Hence even if Appk is revealed for one agent, agent is correlated to the entity, all agents of that entity are correlated.

    1. The security is provided by the fact that Appk cannot be derived from or correlated to the proof.

2. An agency that is malicious or compromised (all storage is encrypted). Kinds of compromises:

    2. Agency router logs are leaked, but the router is still in control of agency.

    3. Adversary is able to get into Agency machines and eavesdrop on all events (server logs, read disk files, etc) but still not manipulate agency machines.

    4. Active attack (partial control): The adversary controls and is able to manipulate some part of the agency.   

3. It will become obvious to third parties how secure a given address (person or organization) is. Does this encourage an attacker?

4. Correlation by network inspection (source/destination IP address, inference of packet timing and size)

    5. An agency router can act as a privacy-enhancing proxy. 

        1. Reason 1, by going through a proxy, the recipient would see the proxy's IP address, not the sender's IP address. Because many are using the same proxy, this masks the sender somewhat.

        2. Reason 2, an attacker could correlate based on the timing of messages or the size of messages sent through a router. A Router could hold messages for a random period of time before forwarding, and add random number of bytes of garbage to a message to thwart these types of inference attacks. 

        3. Note: this could motivate an attack on a major agency for the purposes of eavesdropping.

    6. With this solution, isn't It now *more *imperative that we employ a mix network? Or did we have the same risk with correlation of IP addresses?

## Version 1

TO ADD: agents can be linkable within the same relationship (DID-DID), but not across relationships.

UPDATE: [Simplification](agent_authorization_policy.md#4.%20Simplification) with scheme

### 0. Introduction

This protocol supports:

* Unique Identity Owner;

* Multiple Indistinguishable Claim Receivers;

* Multiple Distinguishable Claim Presenters (Agents);

* Unique Updateable Agent Revoker;

* Unique Non-updateable Provisioner.

### 1. Table of assets and roles

<table>
  <tr>
    <td>Asset\Role</td>
    <td>Identity Owner</td>
    <td>Provisioner</td>
    <td>Claim Receiver</td>
    <td>Claim Presenter</td>
    <td>Claim Revoker</td>
    <td>Agent Revoker</td>
    <td>Verifier</td>
    <td>Claim Issuer</td>
  </tr>
  <tr>
    <td>Agent key a<sub>ag</sub></td>
    <td></td>
    <td></td>
    <td></td>
    <td>O</td>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td>Agent revocation key a<sub>ar</sub></td>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
    <td>O</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td>Agent ID V<sub>ag</sub></td>
    <td></td>
    <td>KM</td>
    <td></td>
    <td>O</td>
    <td></td>
    <td>KM</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td>Agent Revoker ID V<sub>ar</sub></td>
    <td></td>
    <td>K</td>
    <td></td>
    <td></td>
    <td></td>
    <td>O</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td>Provisioning key a<sub>pr</sub></td>
    <td></td>
    <td>O</td>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td>Provisioning ID V<sub>pr</sub></td>
    <td></td>
    <td>O</td>
    <td>K</td>
    <td>K</td>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td>Agent certificate S<sub>a</sub></td>
    <td></td>
    <td>KM</td>
    <td></td>
    <td>O</td>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td>Link secret a<sub>ls</sub></td>
    <td>O</td>
    <td></td>
    <td>K</td>
    <td>K</td>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td>Claim revocation key a<sub>cr</sub></td>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
    <td>O</td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td>Claim revocation ID V<sub>cr</sub></td>
    <td></td>
    <td></td>
    <td>K</td>
    <td>K</td>
    <td>K</td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td>Owner defined attributes A<sub>O</sub></td>
    <td>O</td>
    <td></td>
    <td>K</td>
    <td>K</td>
    <td></td>
    <td></td>
    <td>K</td>
    <td></td>
  </tr>
  <tr>
    <td>Issuer defined attributes A<sub>I</sub></td>
    <td></td>
    <td></td>
    <td>K</td>
    <td>K</td>
    <td></td>
    <td></td>
    <td>K</td>
    <td>O</td>
  </tr>
</table>


#### Legend

O: owns

K: knows

KM: knows multiple values

### 2. Construction

* (a<sub>ag</sub>, V<sub>ag</sub>) - private-public signature keypair, unique for agent.

* (a<sub>ar</sub>,V<sub>ar</sub>) - private-public signature keypair;

* (a<sub>pr</sub>,V<sub>pr</sub>) - private-public signature keypair;

* S<sub>a</sub> = Sig<sub>a_pr</sub>(V<sub>ar</sub>,V<sub>ag</sub>)

* (a<sub>cr</sub>,V<sub>cr</sub>) - private-public signature keypair;

* Claim attributes signed by Issuer: V<sub>pr</sub>,a<sub>ls</sub>, identity attributes

### 3. Use cases

#### 3.1. Setup

##### 3.1.1 Identity Owner

1. Identity Owner generates link secret als as a random value

2. Identity Owner defines owner-defined attributes A<sub>O</sub>;

3. Identity Owner selects an agent accumulator: his own one or a global one.

##### 3.1.2 Provisioner

1. Provisioner generates provisioning key a<sub>pr</sub>;

2. Provisioner computes provisioning ID V<sub>pr</sub>;

3. Provisioner tells V<sub>pr</sub> to Identity Owner.

##### 3.1.3 Claim Receiver

1. Claim Receiver gets V<sub>pr</sub> from Identity Owner.

##### 3.1.4 Agent Revoker

1. Agent Revoker generates agent revocation key aar and agent revocation ID V<sub>ar</sub>.

2. Agent Revoker submits V<sub>ar</sub> to Provisioner.

##### 3.1.5 Claim Presenter

1. Claim Presenter generates agent key aag and agent ID V<sub>ag</sub>.

2. Claim Presenter submits V<sub>ag</sub> to Provisioner.

3. Provisioner creates agent certificate S<sub>a</sub> for Claim Presenter.

4. Provisioner adds S<sub>a</sub> to the agent accumulator and sends S<sub>a</sub> to Agent Revoker.

5. Claim Presenter stores S<sub>a</sub>.

#### 3.2 Claim Lifecycle

##### 3.2.1 Issuance

8. Claim Receiver gets A<sub>O</sub>, als from Identity Owner;

9. Claim Receiver contacts the Issuer and submits A<sub>O</sub>, a<sub>ls</sub>, and V<sub>pr</sub> in the blinded form.

10. Issuer selects an index i for the claim and adds it to A<sub>I</sub>.

11. Issuer generates claim C using A<sub>I</sub>.

12. Issuer sends C and A<sub>I</sub> to Claim Receiver.

13. Issues adds i to his non-revocation accumulator.

14. Claim Receiver provides C and A<sub>I</sub> to Identity Owner.

15. Identity Owner gives C, A<sub>O</sub>, a<sub>ls</sub>,A<sub>I</sub>, and V<sub>pr</sub> to Claim Presenter.

##### 3.2.2 Presentation

9. Claim Presenter refreshes the revocation data from the non-revocation accumulator and agent accumulator.

10. Claim Presenter contacts Verifier.

11. Verifier provides a presentation policy: what attributes must be disclosed.

12. Claim Presenter presents claim attributes (in zero-knowledge) and a zero-knowledge proof that

    23. He knows Vpr contained in the claim;

    24. He knows (V<sub>ar</sub>,V<sub>ag</sub>),S<sub>a</sub>, where S<sub>a</sub> is a signature of (V<sub>ar</sub>,V<sub>ag</sub>) on V<sub>pr</sub>;

    25. He knows private key corresponding to V<sub>ag</sub>;

    26. Vag has not been revoked yet.

    27. Claim index i has not been revoked yet.

13. Verifier checks the proof.

##### 3.2.3 Revocation

1. Issuer selects the index i to revoke.

2. Issuer removes i from the non-revocation accumulator

#### 3.3 Agent Revocation

1. Agent Revoker selects the agent he revokes and retrieves his agent ID Vag and certificate S<sub>a</sub>.

2. Agent Revoker approaches the agent accumulator and provides S<sub>a</sub> and a zero knowledge proof that

    1. S<sub>a</sub> is a signature on some key on some (V<sub>ar</sub>,V<sub>ag</sub>).

    2. He knows the private key from V<sub>ar</sub>.

#### 3.4 Agent Revoker Rotation

1. Generate new Agent Revoker ID.

2. Issue new agent certificates using the new Agent Revoker ID.

3. Replace the old agent certificates with the old ones.

4. Revoke the old agent certificates/

### 4. Simplification

The process can be simplified if we assume that the Identity Owner has a software/hardware vault, where he keeps all most valuable secrets and runs protected code. Then the Provisioner code may run there, and needs only occasional interaction with the outer world to publish his ID, provision new agents, and revoke them.

![image alt text](aap_28.png)

