# Schema, Schema Elements and Encodings
*Note:The objects described below are the desired format, the current format will soon be changed to the one mentioned below. Also in the actual ledger transaction, the format described below will be wrapped in a `data` section and there will be one more section called `reqMetadata` that will contain metadata like the author of transaction, nonce for the transaction, etc.*  

## Schema
Examples:
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
