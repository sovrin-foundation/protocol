# Proof Request Expression Language
 
## Options

### SQL-like Expressions
I imagine a SQL-like language that allows for complex conditions like:
* Complex natural language expression
    * (age > 18 from passport from US Gov't || (age > 16 from passport from US Gov't && guardian_present is true and date is today from travel_card from TSA)) || underage_ok is true from guardian_waiver from TSA
* symbols
    * passport::US_Govt::age > 18 || (passport::US_Govt::age > 16 && guardian_present == true)
* natural language
    * passport from US_Govt has age > 18
* natural language with complex issuer
    * passport from (US_Govt or CAN_Govt) has age > 18
### Script-like (check on the return value of script, last expression as the return value)
* valid_passport_issuer = (US_Govt or CAN_Govt)
* passport from valid_passport_issuer has age > 18


## Considerations of a proof-request expression:
* predicates between fields and constants
    * equality
    * greater than
    * greater than or equal
    * less than
    * less than or equal
    * between
* predicates between fields and fields (from different claims)
    * for example: proof that I have a passport and the name on the passport matches that of my driver license without revealing the name
* logical composition
    * AND
    * OR
    * NOT
    * explicit order of operation (parentheses)
* claims about claims
    * for example: I have a passport and I have a claim from a reputable authentication service that my picture matches me within the past 3 hours
* conditions about revocation
    * proof of non-revocation
    * incentivizing RPs not to demand ultra-recent proof (this would be inefficient for all parties and add load to the ledger)
* conditions about the issuers
    * for example: any driver license division of the following states (as verified by some federal authority)
* conditions about how the credential was issued
    * All credentials are issued based on some level of authentication. How was the ID Owner authenticated? Did they know the password for their student account at a university? Was there an in-person interview at the driver license division?
* conditions about insurance
    * if credential has errors or omissions
    * if credential is issued to the wrong person
    * if credential has been taken and is being held by the wrong person
    * acceptable insurers
    * who are the insured
    * required level of proceeds
* conditions about semantics
    * perhaps there is a named Type that is "height" that has a named Encoding that is "microinch" that is "integer length in millionths of inches"
