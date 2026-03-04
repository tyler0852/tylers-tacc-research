# CLI Planning Document


## File Systems Layout

tapis-cli/
  Cargo.toml
  README.md
  docs/
    planning.md
    specs.md              # notes on how you ingest OpenAPI
  src/
    main.rs               # clap entrypoint, dispatch
    cli.rs                # clap structs/enums (top-level parsing)
    error.rs              # shared error type + Result alias
    config/
      mod.rs              # load config, select profile
      types.rs            # Config/Profile structs
    fs/
      mod.rs              # paths + read/write helpers for config/auth/cache
    client/
      mod.rs              # Client struct, base request builder
      request.rs          # build/send requests, error_for_status, tracing
      response.rs         # response parsing helpers (json/text)
    services/
      mod.rs              # service registry + dispatch helpers
      authenticator/
        mod.rs            # authenticator command group
        ops.rs            # operations (hello, device flow, etc.)
        types.rs          # request/response structs (serde)
      apps/
        mod.rs
        ops.rs
        types.rs
    auth/
      mod.rs              # login, token storage, refresh logic
      device_code.rs      # device authorization flow
    output/
      mod.rs              # formatting: json/raw/table later

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

- We need a way to handle one `operationId` having multiple methods (GET, POST, etc.)
- We could have the user specify every time, but could be a hassle if the endpoint doesn't need it
- Solution: Don't require it unless it is needed, if needed and user doesn't provide, give prompt telling them to
- Example:
```bash
cargo run -- <service> <METHOD> <path> 
```

### Including Parameters

- Some endpoints require parameters (path params, query params, headers, request body)
- We need to derive them from the spec and standardize how it will be used in the CLI
- FILL

## Error Handling


## Device Code


## Health Check Flow