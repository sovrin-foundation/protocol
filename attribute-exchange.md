# Attribute Exchange
Attributes describe the characterstics of an identity, name, birthdate, location, current employer, are all attributes (some attributes may change over time). A credential is a collection of attributes of an identity. When the credential is attested (using a digital signature) by another entity (usually an authority of some sort), the credential is called a **verifiable credential** and each attribute of the credential is called a **verifiable attribute**.  
Verifiable attributes allow individuals to use the relationship they have with trusted institutions to establish new trusted relationships with others, by sharing those attributes. Sovrin's attribute exchange protocol is described below

## Objects:
__Taken from [Schema Elements doc](https://docs.google.com/document/d/1VT8myB5XcCJIrIU2xEE3Vgxpfa47aFswh1qDCFCBtIg/edit#). Reference this in that doc__
1. Schema  
Describes the various attributes present in a credential. Contains a field called *name* and an ordered array of locally named references to Schema Elements (see below) and other Schemas. Every member of a schema must have a unique name. It can optionally contains a field called *spec* that describes the schema.
Note: When a field’s "name" attribute is missing, it inherits its name from the schema or schema element that named.
  ```
  {
    "name": "name",
    "items": [
      {name: "first", element: <first_name>, encoding: <utf8_sha256>},
      {name: "middle", element: <middle_name>, encoding: <utf8_sha256>},
      {name: "last", element: <last_name>, encoding: <utf8_sha256>}
    ]
  }

  {
    "name": "address",
    "items": [
      {name: "line_1", element: <address_line>, encoding: <utf8_sha256>},
      {name: "line_2", element: <address_line>, encoding: <utf8_sha256>},
      {name: "line_3", element: <address_line>, encoding: <utf8_sha256>},
      {element: <city>, encoding: <utf8_sha256>},
      {name: "state", element: <us_state>, encoding: <alpha2>},
      {element: <zip_code>, encoding: <dpbc>}
    ]
  }

  {
    "name": "work_address",
    "spec": "address where subject is employed",
    "items": [{schema: <address>, flatten: true}]
  }

  {
    "name": "home_address",
    "spec": "address where subject lives",
    "items": [{schema: <address>, flatten: true}]
  }

  {
    "name": "loan_application",
    "spec": "application for a basic loan",
    "items": [
      {schema: <name>},
      {schema: <work_address>},
      {schema: <home_address>},
    …
    ]
  }

  ```

2. Schema Elements  
These are the low-level reusable elements that can be composed into Schema.  
Examples:  
  i. first_name: {spec: "subject's first given name"}
  ii. middle_name: {spec: "subject's other given names minus surname"}
  surname: {spec: "subject's surname"}  
  iii. address_line: {spec: "one line of a physical address"}  
  iv: city: {spec: "third line of a physical address"}  
  v:  us_state: {spec: "one of the states or territories of the USA"}  
  vi: zip_code: {spec: "US zip code"}  
  vii:  birthdate: {spec: "date subject was born"}  
  viii: expires: {spec: "date credential expires"}  
  ix: driving_class: {spec: "US driving classification"} 
  x: hair_color: {spec: "color of the subject's hair"}  
  xi: eye_color: {spec: "color of the subject's eyes"}  
  xii: weight: {spec: "subject's weight"}  
  xiii: height: {spec: "subject's height"}  
  xiv: corrective_lenses: {spec: "subject is required to wear corrective lenses"}  
  xv: daytime_only: {spec: "a subject's privilege is restricted to daytime"}  

3. Encodings  
Data must be encoded in order to prove things about it. Cryptographic methods work with large integer in finite fields, so we must encode elements of a credential in integers.
Examples:  
  i. since_1870: {spec: "date as a count of days since 1/1/1870"}  
  ii. us_driving_class: {spec: "US driving class; 'standard' -> 0, 'commercial' -> 1"}  
  iii. utf8_sha256: {spec: "SHA256 hash of string encoded as UTF-8"}  
  iv. alpha2: {spec: "two character abbreviation"}  
  v. dpbc: {spec: "9 digit integer; 5 digit zip code encoded with trailing zeros; remove hyphen in zip+4; examples: '90210' -> 90210000, '90210-2222' -> 902102222"}  
  vi. hair_color_codes: {spec: "color of hair; bald -> "BA", black -> "BL, blonde -> "BE", brown -> "BR", gray -> "GR", red/auburn -> "RA", sandy -> "SA", unknown -> "UN", white -> "WH"}  
  vii. hair_color: {spec: "color of hair; 'bald' -> 9, 'black' -> 1, 'blonde' -> 2, 'brown' -> 3, 'gray' -> 4, 'red/auburn' -> 5, 'sandy' -> 6, 'unknown' -> 7, 'white' -> 8"}  
  viii. eye_color: {spec: "color of eyes; 'black' -> 0, 'blue' -> 1, 'brown' -> 2, 'dichromat' -> 3, 'gray' -> 4, 'green' -> 5, 'hazel' -> 6, 'maroon' -> 7, 'pink' -> 8, 'unknown' -> 9"}  
  ix. weight_lbs: {spec: "weight in whole number of pounds (avoirdupois)"}  
  x. weight_grams: {spec: "weight in whole number of grams"}  
  xi. length_half_inches: {spec: "length in whole number of half inches"}  
  xii. length_mm: {spec: "length in whole number of millimeters"}  
  xiii. bool: {spec: "'true' -> 2, 'false' -> 1, 'unknown' -> 0"}  

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
    name: "person"m
    "items": [
      {schema: <name>},
      {schema: <address>},
      {schema_element: <birthdate>, encoding: <since_1870\},
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
This is the object that posseses the credential. It might or might not be the subject of the credential, like a father possesing the birth certificate credential is the holder of the credential, the child being the subject.

6. Issuer   
The object issuing the credential to a holder. This usually is an organisation like some government authority or even a private organisation. This object also possese some secret cryptographic data that is used to sign the credentials at the time of issuance.

7. Relying party  
The object that requests (from the holder) and receives verifiable attributes or some predicates over those. It then validates the signature over the verifiable attributes.

8. Proof  
The holders shares the verifiable attributes and any predicates over those with the relying party in the form of a proof. It contists of any revealed attribute values and additional cryptographic data that is sufficient for the relying party to verify the issuer's signature.  
Structure: TBD (include ZKLang, proof of correctness)

9. Proof request  
The message sent by the relying party to the holder describing the verifiable attributes and appropriate conditions (predicates, issuer of attributes, schema of the credentials used, etc) that the holder need to satisfy. 

10. Credential Definition  
For the holder to create a proof from a credential and the relying party to verify the proof generated from a credential, they need the issuer's public key. These public keys correspond to the secret keys the issuer used to sign the credential. The credential definition also specifies the signature scheme used by the issuer and references the schema for the credential.
```
  "type":"CL",        // the signature schema
  "schemaId":21,      // txn no of the schema
  "publicKey": {
    "N": <a large number>,
    "S": <a large number>,
    "Z": <a large number>,
    "R": {
      "a1": <a large number>,
      "a2": <a large number>,
      .....
    }
  },
```

11. Credential Offer  
An issuer might want to give a credential to a holder. The message used for this communication is called Credential Offer. More... TBD

12. Credential Request  
A holder might request an issuer for a certain credential. The message used for this communication is called Credential Request. More... TBD

13. Revocation registry TBD
