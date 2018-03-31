# Problem sets
1. Enduser problems
1. How to implement smart policy that adds value
1. SPAM

# Objectives
1. Support highly-trusted Edge Agents
1. Support semi-trusted Cloud Agents
1. Support low-trust Cloud Services (like an Agency Router)
1. Agents need to be able to act on my behalf according to some authorization or policy

# Messaging Layers (Daniel & Devin)
1. Highly-trusted Edge Agents
1. Semi-trusted Cloud Agents
    1. Need to know something about my messages in some cases
1. Low-trust Cloud Services (like an Agency Router)

Low trust doesn't mean you don't trust them. It means you don't have to trust them.

# Messaging Layers (Daniel & Devin)
1. Comms or Routing Layer
1. Agent Layer
    1. threads of interactions
1. 
    
# Orthogonal principles
1. Edge, Cloud, Untrusted & 
1. Packaging layers


# Example Scenario
Alice sends message to Bob

# Example Structure
```json
{
  "route_msg": sealed_box({
    
  })
  "cloud_msg": {...},
  "edge_msg": {
    "header": {
      "type": "CLAIM",
      "type_version": "0.1"
    },
    "payload": {<some claim>}
}
```

```json
}
```


# Packaging
1. The message itself
1. Add header (and possibly sig) for message
1. Interaction context
1. Routing: where you want to send something


# Future optimization
1. Encrypting information so that agents can see a subset, and the enduser can see all, without redundancy.

