# Decentralized Key Management

## Introduction
A decentralized key management system is an approach to cryptographic key
management where there is no central authority. DKMS leverages the security,
immutability, availability, and resiliency properties of distributed ledgers
to provide highly scalable key distribution, verification, and recovery.

The lack of any central authority shifts a significant measure of responsibility
for key management directly to the participating entity.
Distributed ledger technology can make it much easier to share and verify public keys.
Each entity manages its own authoritative key material without requiring approval from
other parties. DKMS uses distributed ledgers with a combination of DIDs for decentralized
identification.

Sovrin supports an ecosystem that enables DKMS. Agents and agencies built on Sovrin must follow DKMS
architecture and principles.

## Key Management
DKMS adheres to the principle of key separation where keys for different purposes should be cryptographically
separated. This avoids use of the same key for multiple purposes. Keys are classified based on usage and the
nature of information being protected. Any change to a key requires that the relevant DID method ensure that
the change comes from the identity owner or her authorized delegate.

DKMS architecture addresses what keys are needed, how they are used, where they should be stored and
protected, how long they should live, and how they are revoked or recovered when lost or compromised.

### Key Types
A key management system should specify and define each key type used. The following key layering and policies
can be applied.

1. **Master keys**: Keys that are not cryptographically protected. They are distributed manually or
initially installed and protected by procedural controls and physical or electronic isolation.
2. **Key encrypting keys**: Symmetric or public keys used for key transport or storage of other keys.
3. **Data keys**: Used to provide cryptographic operations on user data (e.g., encryption, authentication).

The keys at one level are used to protect items at a lower level. Consequently, special measures
are used to protect master keys, including severely limiting access and use, hardware protection,
and providing access to the key only under shared control.

The following list includes the initial key types required by the Sovrin DID Method Spec and the Sovrin
protocol for verifiable credential exchange:

1. **Link secret**: (one per entity) A high-entropy 256-bit integer included in every credential in blinded form.
2. **DID keys**: (one per relationship per agent) Ed25519 keys used for non-repudiation signing and verification
for DIDs.
3. **Authorization policy keys**: (one per agent) Key pairs used with the authorization policy registry.
4. **Agent recovery keys**: (a fraction per recovery trustee) The public key is stored by the agent
and used for encrypting backups. The private key is saved to an offline medium or split into shares and
given to trustees.
5. **Wallet encryption keys**: (one per wallet segment) Symmetric keys for encrypting wallets and
backups.

### Multi-Device Management
All keys except for the link secret are unique per device. This allows for per relationship control of
authorized devices and remote revocation.

### Key Portability and Migration
Portability of DKMS wallets and keys is an important requirement. Migration of edge and cloud agents to
another agency (including self-hosting) must be supported.

## Protocol
### Microledger Transactions

DKMS architecture uses microledgers to represent the state of the authorized keys in a relationship.
They are used between two parties where each party signs transactions using DID keys.

### Authorization Policy
Each of the Identity Owner’s agents has a key-pair that will be used with an authorization policy on the ledger.
Its purpose is to allow for key management of devices in a flexible way, while allowing
for agents to prove in zero knowledge that they are using an agent that is authorized by the owner.

### Authenticated Encryption
The use of DIDs and microledgers allows communication between agents to use authenticated encryption.
Agents use their DID verification keys for authenticating each other whenever a communication channel is
established. Microledgers allow DID keys to have rooted mutual authentication for any two parties with a DID.

# Recovery
In decentralized identity management, recovery is important since identity owners have no “higher authority”
to turn to for recovery.

1. Offline recovery uses physical media or removable digital media to store recovery keys.

2. Social recovery employs “trustees” who store encrypted recovery data on an identity owners behalf—typically
in the trustees own agent(s).

These methods are not exclusive.

### Key Loss
Key loss means the owner can assume there is no further risk of compromise. For example devices unable to
function due to water, electricity, breaking, fire, hardware failure, acts of God, etc.

### Compromise
Key compromise means that private keys and/or master keys have become or can become known either passively
or actively.

Passively means the identity owner is not aware of the compromise. An attacker may be eavesdropping
or have remote communication with the agent but has not provided direct evidence of intrusion or malicious
activity, such as impersonating the identity owner or committing fraud.

Actively means the identity owner knows her keys have been exposed. For example, the owner is locked out
of her own device, or becomes aware of abuse or fraud.
To protect from either, there are techniques available: rotation, revocation, and quick recovery. Rotation
helps to limit a passive compromise, while revocation and quick recovery help to limit an active one.

#### Key Rotation
Keys SHOULD be changed periodically to limit tampering. When keys are rotated, the previous keys are
revoked and new ones are added.

#### Key Revocation
DKMS keys MUST be revocable. Verifiers MUST be able to determine the revocation status of a DKMS key.
