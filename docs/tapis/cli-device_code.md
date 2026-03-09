# Device Code Planning Doc

## High Level Overview
- CLI asks `Authenticator` for a device code
- Authenticator gives back a `device_code`, a `user_code`, an expiration, and a `verification_url`
- The CLI tells the user, go to this URL and enter this code
- While the user does this, the CLI polls the token endpoint with `device_code`
- Once the user finishes, the token endpoint return the Tapis tokens

## Required Endpoints For Device Code

- `v3/oauth2/device/code`
    - `operationId`: generate_device_code
    - Method: Post
    - First step of the device code by asking the auth server to start a device login session
    - Requires the user to post with the `NewDeviceCode` scheme
        - The only thing that this requires is `client_id`
    - Returns a `DeviceCodeResponse` scheme that gives the user
        - device_code, user_code, client_id, expires_in, verification_uri

- `/v3/oauth2/tokens`
    - `operationId`: create_token

## Helpful Endpoints

- `/v3/oauth2/hello`
    - `operationId`: hello
    - Checks if we can talk to auth service

- `/v3/oauth2/.well-known/oauth-authorization-server`
    - `operationId`: get_server_metadata
    - Returns OAuth server metadata