# Credential
**TODO: Define**

## Locked Credentials
Proofs generated from locked credentials are also locked, and these cannot be verified without being unlocked. The Relying Party can only unlock such a proof with the help of the Issuer. 

The privacy requirements are still in effect, in that the Relying Party and only knows what the ID Owner shares, and the Issuer knows nothing about the Proof or Credential except the Credential Pricing Class.

### Issuing a Locked Credential
An Issuer can issue a **Locked Credential**, which is a Credential that can only be used to create Proofs that are locked. The Issuer locks that credential with a special key, called a **Locking Secret**. This Locking Secret will be needed to unlock Proofs during the Proving protocol.

### Creating a Proof
A Credential holder that has a Locked Credential can generate a **Locked Proof**. A Locked Proof cannot be verified until it is unlocked. That proof contains a unique untraceable number called a **Locked Token**.

### Unlocking a Locked Proof
The Relying Party sends the Locked Token in the form of an **Unlock Request** to the Issuer. The Unlock Request can come with a payment, which the Issuer may require in order to unlock the proof. In this case, the Unlock Request also includes proof of the Credential's **Pricing Class**, which is used to calculate the unlocking fee. 

The Issuer performs a calculation with both the Locked Token and the Locking Secret, to produce an **Unlocked Token**. The Issuer sends this back to the Relying Party. 

The Relying Party uses this Unlock Token to complete the Verification step. At this point, the Relying Party knows whatever is proven in the Proof, as if the Credential were never locked in the first place.

### Payments
TODO: payments must be private to maintain privacy guarantees.

### Unlocking Coupons
TODO: describe the Unlocking Coupons concept.