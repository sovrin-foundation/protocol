# Motivation
Attributes describe the characteristics of an identity, name, birthdate, location, current employer, are all attributes (some attributes may change over time). A credential is a collection of attributes of an identity. When the credential is attested (using a digital signature) by another entity (usually an authority of some sort), the credential is called a **verifiable credential** and each attribute of the credential is called a **verifiable attribute**.  
Verifiable attributes allow individuals to use the relationship they have with trusted institutions to establish new trusted relationships with others, by sharing those attributes. The following objects are involved in Sovrin's attribute exchange protocol.

__Taken from [Schema Elements doc](https://docs.google.com/document/d/1VT8myB5XcCJIrIU2xEE3Vgxpfa47aFswh1qDCFCBtIg/edit#). Reference this in that doc__
1. [Schema](schema.md#Schema)
2. [Schema Elements](schema.md#Schema-Elements)
3. [Encodings](schema.md#Encodings)
4. [Subjects](schema.md#Subjects)
5. Holder  
This is the object that possesses the credential. It might or might not be the subject of the credential, like a father possessing the birth certificate credential is the holder of the credential, the child being the subject.
6. Issuer   
The object issuing the credential to a holder. This usually is an organisation like some government authority or even a private organisation. This object also possese some secret cryptographic data that is used to sign the credentials at the time of issuance.
7. Relying party  
The object that requests (from the holder) and receives verifiable attributes or some predicates over those. It then validates the signature over the verifiable attributes.
8. [Proof request](proof-request.md)
9. Proof  
The holders shares the verifiable attributes and any predicates over those with the relying party in the form of a proof. 
It consists of any revealed attribute values and additional cryptographic data that is sufficient for the relying party to verify the issuer's signature.
More details can be found [here](proof.md)
*Note: The request and generation of proof are using ZkLang which involves more objects, see [here](zklang.md) for details.*
10. [Credential Definition](cred-def.md#Credential-Definition) 
11. [Credential Offer](cred-offer.md)
12. [Credential Request](cred-request.md)  
13. Revocation Authority  
This is entity responsible for managing the revocation of credentials. This might or might not be the issuer of the credential.
14. [Revocation registry definition](cred-def.md#Revocation-Registry-Definition) 
15. [Revocation registry entry](cred-def.md#Revocation-Registry-Entry)  
