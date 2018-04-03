# Index
Attributes describe the characteristics of an identity, name, birthdate, location, current employer, are all attributes (some attributes may change over time). A credential is a collection of attributes of an identity. When the credential is attested (using a digital signature) by another entity (usually an authority of some sort), the credential is called a **verifiable credential** and each attribute of the credential is called a **verifiable attribute**.  
Verifiable attributes allow individuals to use the relationship they have with trusted institutions to establish new trusted relationships with others, by sharing those attributes. Sovrin's attribute exchange protocol is described below

## Objects:
__Taken from [Schema Elements doc](https://docs.google.com/document/d/1VT8myB5XcCJIrIU2xEE3Vgxpfa47aFswh1qDCFCBtIg/edit#). Reference this in that doc__
1. Schema  
Describes the various attributes present in a credential. Contains a field called *name* and an ordered array of locally named references to Schema Elements (see below) and other Schemas. Every member of a schema must have a unique name. It can optionally contains a field called *spec* that describes the schema.
Note: When a field’s "name" attribute is missing, it inherits its name from the schema or schema element that named. [Examples](public-objects.md#Schema) 

2. Schema Elements  
These are the low-level reusable elements that can be composed into Schema. [Examples](public-objects.md#Schema%20Elements)

3. Encodings  
Data must be encoded in order to prove things about it. Cryptographic methods work with large integer in finite fields, so we must encode elements of a credential in integers. [Examples](public-objects.md#Encodings)  

    In the case where only one encoding is used for both proving and revealing, the encoding attribute can be a simple reference to an encoding.  
    
    `"encoding": "length_mm"`
    
    When different encodings are used for proving and revealing, the encoding attribute can be a value with "prove" and "reveal" attributes.
    
    `"encoding": {"prove": "utf8_sha256", "reveal": "utf8"}`
    
    When multiple encodings are included for an attribute, the encoding attribute can be an array. These encodings can be used for both proving and revealing.
    
    `"encoding": ["length_mm", "length_meters"]`
    
    If multiple encodings are used for proving, but a different encoding is used for revealing, then the encoding attribute can be an object where the "prove" attribute is an array of encodings.
    
    `"encoding": {"prove": ["utf8_sha256", "D–M Soundex"], "reveal": "utf8"}`
    
    **Units of Measure**
    
    Units of measure are encapsulated in encodings. For example:
    
    `length_half_inches: {spec: "length in whole number of half inches"}`
    
    `length_mm: {spec: "length in whole number of millimeters"}`
    
    **Code Lists**
    
    Code lists are also encapsulated in encodings, for example: 
    
    `hair_color_codes: {spec: "color of hair; bald -> 'BA', black -> 'BL', blonde -> 'BE', brown -> 'BR', gray -> 'GR', red/auburn -> 'RA', sandy -> 'SA', unknown -> 'UN', white -> 'WH'}`

4. Subjects  
    Fields can have a subject, which communicates who or what the attribute is about.  
    Example:
      ```
      {
        name: "person"
        "items": [
          {schema: <name>},
          {schema: <address>},
          {schema_element: <birthdate>, encoding: <since_1870>},
        ]
      }
    
      {
        "name": "birth_certificate",
        "items": [
          {subject: "father", schema: <person>, flatten: true},
          {subject: "mother", schema: <person>, flatten: true},
          {subject: "child", schema: <person>, flatten: true},
          {subject: "father", schema_element: <bloodtype>},
          {subject: "mother", schema_element: <bloodtype>},
        ]
      }
      ```

5. Holder  
This is the object that possesses the credential. It might or might not be the subject of the credential, like a father possessing the birth certificate credential is the holder of the credential, the child being the subject.

6. Issuer   
The object issuing the credential to a holder. This usually is an organisation like some government authority or even a private organisation. This object also possese some secret cryptographic data that is used to sign the credentials at the time of issuance.

7. Relying party  
The object that requests (from the holder) and receives verifiable attributes or some predicates over those. It then validates the signature over the verifiable attributes.

8. Proof  
The holders shares the verifiable attributes and any predicates over those with the relying party in the form of a proof. It consists of any revealed attribute values and additional cryptographic data that is sufficient for the relying party to verify the issuer's signature.
A proof is a JSON document that derives from one or more credentials. It demonstrates in a cryptographic/mathematical way that attributes within credentials held by the prover satisfy constraints requested by a verifier.
Proof formats are likely to evolve over time, as the ecosystem's requirements for proving become more sophisticated, and as claims become richer. 
The format documented [here](non-public-objects.md#Proof) is a good starting point, and should be sufficient for most high-priority use cases for the next year or two. 
*Note: The request and generation of proof are using ZkLang which involves more objects, see [here](zklang.md) for details.*

9. Proof request  
The message sent by the relying party to the holder describing the verifiable attributes and appropriate conditions (predicates, issuer of attributes, schema of the credentials used, etc) that the holder need to satisfy. [Example](non-public-objects.md#Proof%2Request)

10. Credential Definition  
For the holder to create a proof from a credential and the relying party to verify the proof generated from a credential, they need the issuer's public key. These public keys correspond to the secret keys the issuer used to sign the credential. The credential definition also specifies the signature scheme used by the issuer and references the schema for the credential. The credential definition is created by the issuer using a [transaction](public-objects.md#Credential%20Definition)

11. Credential Offer  
An issuer might want to give a credential to a holder. It communicates the type of credential on offer, and possibly its proposed contents. It may also communicate a price that must be paid before the credential is issued. Credential offers sent to a potential holder are typically followed by credential request that flow the other direction. [Example](non-public-objects.md#Credential%20Offer)

12. Credential Request  
A holder might request an issuer for a certain credential. The message used for this communication is called Credential Request. [Example](non-public-objects.md#Credential%2Request)

13. Revocation Authority  
This is entity responsible for managing the revocation of credentials. This might or might not be the issuer of the credential.

14. Revocation registry definition  
This describes the kind of revocation mechanism used for managing the revocation and/or issuance of credentials. The kind of data structure used, keys to manage that data structure and other data needed to compute the witness is mentioned here. This is done by the revocation authority in a [transaction](public-objects.md#Revocation%20Registry%20Definition) 

15. Revocation registry entry  
When claims are issued and/or revoked, the revocation registry needs to be updated, this is done by the revocation authority in a [transaction](public-objects.md#Revocation%20Registry%20Entry)