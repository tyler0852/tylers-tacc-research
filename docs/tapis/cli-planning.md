# CLI Planning Document


## File Systems Layout

```
tapis-cli/
├── Cargo.toml
├── README.md
├── Config/
│   ├── tapis-cli.toml
│   └── Anything needed to set up env
└── src/
    ├── main.rs
    ├── device_code.rs
    ├── client.rs
    ├── services/
    │   ├── apps.rs
    │   ├── auth.rs
    │   └── all service files
    └── utils/
        ├── error.rs
        └── Any helper files
```      

## Prechecks

- Validate the CLI input
- Required env variables
- Filesystem state
- Things that all services will use
- The code that will do this will be spread out through `main.rs`, `device_code.rs`, `client.rs`


## What Endpoints from Tapis do we Need

| service | method | endpoint | operationid | small summary |
|---|---|---|---|---|
| Authenticator | GET | /v3/oauth2/hello | hello | Checks if auth service is up|
| Authenticator | POST | /v3/oauth2/device/code | generate_device_code | Device Code Flow step 1: generate device_code + user_code for the CLI. |
| Authenticator | POST | /v3/oauth2/tokens | create_token | Device Code Flow step 2: exchange device_code (+ client_id) for a Tapis JWT. |
| Apps | GET | /v3/apps/healthcheck | healthCheck | Checks if apps service is up |


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

- The `service` name is often long and unintuitive. I will make short hands for each one
- Ex: The `service` "Tapis Application API" will become "apps"
- Ex: The "Authenticator" `service` will become "auth"

- For `operationID`, the current plan is to keep it the exact same as the OpenAPI spec but to just make it all lowercase
- After inspecting the OpenAPI specs for different services, it looks like all operation ID's are unique
    - Even the same endpoint with different method calls gives those different method calls a different `operationId`
    - This means the user will never have to specify a post, get, etc.

- The user would be able to find clear instruction for how to use this via the cml or via a RTD 
- cml line workflow:
```bash
tapis-cli --help
```
- This outputs the different services available, something like:
```bash
auth
apps
files
etc
```
- Then the user can do
```bash
tapis-cli auth --help
```
- This will define the different operation ID's the user can user


### Including Parameters

- Some endpoints require parameters (path params, query params, headers, request body)
- We need to derive them from the spec and standardize how it will be used in the CLI
- FILL

## Error Handling

- Make a HTTP call wrapper in `client.rs` that every endpoint will use
- Will use the reqwest crate the handle errors
- The wrapper makes a reqwest request to check the respone status
- If status is 2xx, return the response
- If the status is not 2xx, treat it as a failure, capture status message, return an error on the CLI

