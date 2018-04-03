# Proof Format

A proof is a JSON document that derives from one or more credentials. It
demonstrates in a cryptographic/mathematical way that attributes within
credentials held by the prover satisfy constraints requested by a verifier.

Proof formats are likely to evolve over time, as the ecosystem's
requirements for proving become more sophisticated, and as claims
become richer. The format documented here is a good starting point,
and should be sufficient for most high-priority use cases for the
next year or two.

Proofs generally conform to the following pattern:

```json
{
  "msg_type": "proof",
  "version": "0.1",
  "to_did": "did:sov:BnRXf8yDMUwGyZVDkSENeq",
  "from_did": "did:btc:GxtnGN6ypZYgEqcftSQFnC",
  "proof_request_id": "cCanHnpFAD",
  "proofs": { ... },
  "aggregated_proof": { ... },
  "requested_proof": { ... }
}
```

The `msg_type` and `version` fields are required and help to classify
the json. `to_did` and `from_did` are optional and serve to bind the doc
to a particular pairwise interaction (where `to_did` is the verifier, and
`from_did` is the prover). Normally proofs are transmitted over a
pairwise channel, so this information would be redundant--but having
these fields continues to bind the proof to the channel even when
data is at rest.

The formats of `proofs` and `aggregated_proof` are documented and governed
by indy-sdk.

