![superseded](../superseded.png)
# Schema, Schema Elements and Encodings
*Note: The objects described below are the desired format, the current format will soon be changed to the one mentioned below. The format below is only for the `data` part of the ledger transaction.*  

## Schema
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

## Schema Elements
These are the low-level reusable elements that can be composed into Schema. 
```
first_name: {spec: "subject's first given name"}

middle_name: {spec: "subject's other given names minus surname"}

surname: {spec: "subject's surname"}  

address_line: {spec: "one line of a physical address"}  

city: {spec: "third line of a physical address"}  

us_state: {spec: "one of the states or territories of the USA"}  

zip_code: {spec: "US zip code"}  

birthdate: {spec: "date subject was born"}  

expires: {spec: "date credential expires"}  

driving_class: {spec: "US driving classification"} 

hair_color: {spec: "color of the subject's hair"}  

eye_color: {spec: "color of the subject's eyes"}  

weight: {spec: "subject's weight"}  

height: {spec: "subject's height"}  

corrective_lenses: {spec: "subject is required to wear corrective lenses"}  

daytime_only: {spec: "a subject's privilege is restricted to daytime"}  
```

## Encodings
Data must be encoded in order to prove things about it. Cryptographic methods work with large integer in finite fields, so we must encode elements of a credential in integers. 
```
since_1870: {spec: "date as a count of days since 1/1/1870"}  

us_driving_class: {spec: "US driving class; 'standard' -> 0, 'commercial' -> 1"}  

utf8_sha256: {spec: "SHA256 hash of string encoded as UTF-8"}  

alpha2: {spec: "two character abbreviation"}  

dpbc: {spec: "9 digit integer; 5 digit zip code encoded with trailing zeros; remove hyphen in zip+4; examples: '90210' -> 90210000, '90210-2222' -> 902102222"}  

hair_color_codes: {spec: "color of hair; bald -> "BA", black -> "BL, blonde -> "BE", brown -> "BR", gray -> "GR", red/auburn -> "RA", sandy -> "SA", unknown -> "UN", white -> "WH"}  

hair_color: {spec: "color of hair; 'bald' -> 9, 'black' -> 1, 'blonde' -> 2, 'brown' -> 3, 'gray' -> 4, 'red/auburn' -> 5, 'sandy' -> 6, 'unknown' -> 7, 'white' -> 8"}  

eye_color: {spec: "color of eyes; 'black' -> 0, 'blue' -> 1, 'brown' -> 2, 'dichromat' -> 3, 'gray' -> 4, 'green' -> 5, 'hazel' -> 6, 'maroon' -> 7, 'pink' -> 8, 'unknown' -> 9"}  
weight_lbs: {spec: "weight in whole number of pounds (avoirdupois)"}  

weight_grams: {spec: "weight in whole number of grams"}  

length_half_inches: {spec: "length in whole number of half inches"}  

length_mm: {spec: "length in whole number of millimeters"}  

bool: {spec: "'true' -> 2, 'false' -> 1, 'unknown' -> 0"}  
```
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

## Subjects
Fields can have a subject, which communicates who or what the attribute is about.  
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
## Code references to ledger objects
1. [SCHEMA](https://github.com/hyperledger/indy-sdk/blob/778a38d92234080bb77c6dd469a8ff298d9b7154/libindy/src/services/ledger/types.rs#L144)