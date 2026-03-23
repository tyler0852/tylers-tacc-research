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
        - device_code, user_code, client_id, expires_in, verification_url

- `/v3/oauth2/tokens`
    - `operationId`: `create_token`
    - Method: `POST`
    - Second step of the device code flow
    - Used by the CLI to exchange or poll with the device flow information until the auth server returns tokens
    - Requires the user to send a request body using the `NewToken` schema
    - For the device code flow, the main fields the CLI cares about are:
        - `client_id`
        - `device_code`
        - `grant_type`
    - Returns a `TokenResponse` schema
    - This is the endpoint that returns the Tapis JWT and possibly a refresh token

## Helpful Endpoints

- `/v3/oauth2/hello`
    - `operationId`: `hello`
    - Method: `GET`
    - Checks if we can talk to the auth service
    - Not part of the login flow itself, just a connectivity check

- `/v3/oauth2/.well-known/oauth-authorization-server`
    - `operationId`: `get_server_metadata`
    - Method: `GET`
    - Returns OAuth server metadata
    - Helpful for learning things like the token endpoint and supported grant types
    - Not required to complete the basic device code flow

## Summarized OpenAPI Spec

```
openapi: "3.0.2"

info:
  title: "Authenticator"
  description: "REST API and web server providing authentication for a Tapis v3 instance."
  version: "1"
  termsOfService: "https://tapis-project.org"
  contact:
    name: "Authenticator"
    url: "https://tapis-project.org"
    email: "cicsupport@tacc.utexas.edu"
  license:
    name: "BSD 3"
    url: "https://github.com/tapis-project/authenticator"

servers:
- url: http://localhost:5000
  description: Local Development
- url: http://{tenant_id_url}.develop.tapis.io
  description: Tapis Develop instance
  variables:
    tenant_id_url:
      default: dev
      description: The tenant_id associated with the request.
- url: /
  description: catch-all server definition for other Tapis instances.


paths:
  /v3/oauth2/hello:
    get:
      tags:
        - Health Check
      description: Logged connectivity test. No authorization required.
      operationId: hello
      responses:
        '200':
          description: Message received.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/BasicResponse'
        '500':
          description: Server error.

  /v3/oauth2/.well-known/oauth-authorization-server:
    get:
      tags:
        - Metadata
      operationId: get_server_metadata
      description: Get the OAuth2 server metadata for the tenant.
      responses:
        '200':
          description: OK
          content:
            application/json:
              schema:
                allOf:
                  - $ref: '#/components/schemas/BasicResponse'
                properties:
                  result:
                    $ref: '#/components/schemas/OAuth2Metadata'

  /v3/oauth2/device/code:
    post:
      tags:
        - Tokens
      summary: Generate a device code.
      description: Generate a device code; this is the first step in the device_code grant type. See the OAuth2 documentation for details.
      operationId: generate_device_code
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/NewDeviceCode'
      responses:
        '200':
          description: OK
          content:
            application/json:
              schema:
                allOf:
                  - $ref: '#/components/schemas/BasicResponse'
                properties:
                  result:
                    $ref: '#/components/schemas/DeviceCodeResposne'

  /v3/oauth2/tokens:
    post:
      tags:
        - Tokens
      summary: Generate a Tapis JWT
      description: Generate a Tapis JWT using some OAuth2 grant type. Typically, a request to this endpoint is the last step in the token generation process. The fields required in the request payload depend on the grant type being used (see details below).
      operationId: create_token
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/NewToken'
          application/x-www-form-urlencoded:
            schema:
              $ref: '#/components/schemas/NewToken'
      responses:
        '201':
          description: Created
          content:
            application/json:
              schema:
                allOf:
                  - $ref: '#/components/schemas/BasicResponse'
                properties:
                  result:
                    $ref: '#/components/schemas/TokenResponse'


  schemas:
    BasicResponse:
      type: object
      properties:
        version:
          type: string
          description: Version of the API
        message:
          type: string
          description: Brief description of the response
        status:
          type: string
          description: Whether the request was a success or failure.
          enum: [success, failure]
        metadata:
          type: object
          description: Metadata about the result object, including pagination information

    NewDeviceCode:
      type: object
      required: [client_id]
      properties:
        client_id:
          type: string
          description: The client_id requesting the device code.

    DeviceCodeResposne:
      type: object
      required: [device_code, user_code, client_id, expires_in, verification_uri]
      properties:
        device_code:
          type: string
          description: The device code generated for the client
        user_code:
          type: string
          description: The user code generated for the client
        client_id:
          type: string
          description: The client_id of the client
        expires_in:
          type: string
          description: The expiration for the user code
        verification_uri:
          type: string
          description: The url the user should go to to enter their user code

    DeviceCodeResposne:
      type: object
      required: [device_code, user_code, client_id, expires_in, verification_uri]
      properties:
        device_code:
          type: string
          description: The device code generated for the client
        user_code:
          type: string
          description: The user code generated for the client
        client_id:
          type: string
          description: The client_id of the client
        expires_in:
          type: string
          description: The expiration for the user code
        verification_uri:
          type: string
          description: The url the user should go to to enter their user code

    OAuth2Metadata:
      type: object
      properties:
        issuer:
          type: string
          description: The authorization server's issuer identifier.
        authorization_endpoint:
          type: string
          description: URL of the authorization server's authorization endpoint.
        token_endpoint:
          type: string
          description: URL of the authorization server's token endpoint.
        jwks_uri:
          type: string
          description: URL to the public key used to check signatures for the tokens issued by this server.
        registration_endpoint:
          type: string
          description: URL of the authorization server's OAuth 2.0 Dynamic Client Registration endpoint
        grant_types_supported:
          type: array
          items:
            type: string
          description: JSON-serializable list of grant types supported by this server.
```