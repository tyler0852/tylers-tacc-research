# Meeting 8: March 27th, 2026

## What Got Done

### Spotted Some Inconsistencies in the OpenAPI Specification
```bash
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
```
- And a `BasicResponse` is defined as
```bash
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
```
- But when running the `hello` endpoint we get
```bash
tyler@wireless-10-146-198-159 tapis_cli % curl https://dev.tapis.io/v3/oauth2/hello
{"message":"Hello from Tapis","metadata":{},"result":"","status":"success","version":"26Q1.0"}
```
- Here we see `result` which there has been no mention of before
- What is causing this? Outdated spec? Scheme incomplete? API returning undocument field?

- In the `DeviceCodeResponse` scheme
```bash
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
```
- We see `verification_uri`. Should this be `verification_url`?

### Working on `tapis_cli`

- Current have a detailed plan and have layed out the structure
- Almost at a point where you can call all endpoints from the CLI. I need to implement the code that will allow the user to put in field arguments for `GenerateDeviceCode` and `GenerateTokens`
    - Will also need to handle how these endpoints can give different responses depending on the users input

## Road Blockers
- I don't have a client so I haven't been able to test the main endpoints needed for the device code

## Next Steps
- Test endpoints and program with a valid CLI
- Add code for field arguments
- Start implementing device code flow