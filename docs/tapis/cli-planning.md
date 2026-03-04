# CLI Planning Document


## File Systems Layout


## Prechecks


## What Endpoints from Tapis do we Need


## How the User call each Endpoint

- Goal: we want it to be very clear and have a standardized way the user calls an endpoint

- Tapis offers different `services` which each have their own openAPI spec
    - Examples: Apps, Authenticator, Jobs, etc.

- Every `service` on Tapis has many endpoints that have an OpenAPI `operationId`

- The user will use `service` and `operationID` as defined in the OpenAPI spec to call endpoints
- Example:
```bash
cargo run -- <service> <operationId>
cargo run -- authenticator hello
./target/release/tapis authenticator hello
```

### Defining `service` and `operationID`

- To make the OpenAPI specs translate better to the CLI, we need to define some assumptions
- We will change both `service` and `operationId` by:
    - Making everything lowercase
    - Using kebab-case (no-spaces-everything-seperated-by-dashes)

- While this would be the standard, I plan to make an alias so that the user could run the endpoint with `service` and `operationId` completly unchanged

### Encountering Multiple Methods for the Same `operationId`




## Error Handling


## Device Code


## Health Check Flow