# Device Code Planning Doc

## High Level Overview
- CLI asks `Authenticator` for a device code
- Authenticator gives back a `device_code`, a `user_code`, an expiration, and a `verification_url`
- The CLI tells the user, go to this URL and enter this code
- While the user does this, the CLI polls the token endpoint with `device_code`
- Once the user finishes, the token endpoint return the Tapis tokens

## Required Endpoints For Device Code

- `/v3/oauth2/hello`
    - `operationId`: `hello`
    - Method: `GET`
    - Checks if we can talk to the auth service
    - Not part of the login flow itself, just a connectivity check

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

    NewToken:
      type: object
      properties:
        username:
          type: string
          description: The username being authenticated (for password grant).
        password:
          type: string
          description: The password assoicated with the username being authenticated (for password grant).
        client_id:
          type: string
          description: The client_id being authenticated (for device_code grant).
        client_key:
          type: string
          description: The client_key being authenticated (optional for authorization_code grant).
        grant_type:
          type: string
          description: The OAuth2 grant type being used; either password, authorization_code or refresh_token.
        redirect_uri:
          type: string
          description: The client's redirect URI (for authorization_code grant).
        code:
          type: string
          description: The authorization code associated with the request (for authorization_code grant).
        device_code:
          type: string
          description: The device code associated with the request (for device_code grant)
        refresh_token:
          type: string
          description: The refresh token associated with the request (for refresh_token grant).

    TokenResponse:
      type: object
      required: [access_token]
      properties:
        access_token:
          type: object
          description: A Tapis access token object.
          properties:
            access_token:
              type: string
              description: The actual access token as a JWT
            id_token:
              type: string
              description: The actual access token as a JWT
            expires_at:
              type: string
              description: The time, as a string in UTC, when the token expires.
            expires_in:
              type: integer
              description: The amount of time, in seconds, when the token will expire.
            jti:
              type: string
              description: Unique identifier for the token
        refresh_token:
          type: object
          description: A Tapis refresh token object.
          properties:
            refresh_token:
              type: string
              description: The actual refresh token as a JWT
            expires_at:
              type: string
              description: The time, as a string in UTC, when the token expires.
            expires_in:
              type: integer
              description: The amount of time, in seconds, when the token will expire.
            jti:
              type: string
              description: Unique identifier for the token
```