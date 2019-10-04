![superseded](../superseded.png)
# Proof Request
The message sent by the relying party to the holder describing the verifiable attributes and appropriate conditions 
(predicates, issuer of attributes, schema of the credentials used, etc) that the holder need to satisfy. 
```json
{
  "name": "<name of the proof request>",
  "version": "<version of the proof request>",
  "attributes": {
      "<attribute name, like age>": "<attribute type, like number>",
      "<attribute name, like ssn>": "<attribute type, like string>",
      "<attribute name, like status>": "<attribute type, like enum>"
  },
  "verifiableAttributes": "<a list of attribute names for which the proof has to be presented>",
  "nonce": "<a nonce acting as request id>"
}
```
