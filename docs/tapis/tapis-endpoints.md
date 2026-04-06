# Tapis Endpoints

This doc shows how to use the relevant tapis endpoints and their outputs

## Apps Healthcheck

```bash
tyler@Tylers-MacBook-Pro tapis_cli % curl https://dev.tapis.io/v3/apps/healthcheck

{"status":"success","message":"TAPIS_HEALTHY Health check received by Apps Service.","version":"26Q1.0-a4c37081","commit":"a4c37081","build":"2026-03-04T15:00:26Z","metadata":null,"result":"Health check received. Count: 101783"}% 
```

## Auth Hello

```bash
tyler@Tylers-MacBook-Pro tapis_cli % curl https://dev.tapis.io/v3/oauth2/hello

{"message":"Hello from Tapis","metadata":{},"result":"","status":"success","version":"26Q1.0"}
```

## Auth Device Code

```bash
tyler@Tylers-MacBook-Pro tapis_cli % curl -H "Content-Type: application/json" \
  -d '{"client_id":"tylers_client"}' \
  https://tacc.tapis.io/v3/oauth2/device/code
{"message":"Token created successfully.","metadata":{},"result":{"client_id":"tylers_client","device_code":"0vfjUOsDvgdQWJRBWcCJEVxnOqwxr1FbnPdXnw4Z","expires_in":"Mon, 06 Apr 2026 10:27:50 GMT","user_code":"GyiUVrMh","verification_uri":"https://tacc.tapis.io/v3/oauth2/device?client_id=tylers_client"},"status":"success","version":"26Q1.0"}
```

## Auth Clients

```bash
tyler@Tylers-MacBook-Pro tapis_cli % curl -H "X-Tapis-Token: $JWT" https://tacc.tapis.io/v3/oauth2/clients
{"message":"Clients retrieved successfully.","metadata":{},"result":[{"active":true,"callback_url":"","client_id":"tylers_client","client_key":"3kRdPMv7arpRn","create_time":"Mon, 06 Apr 2026 10:10:24 GMT","description":"","display_name":"tylers_client","last_update_time":"Mon, 06 Apr 2026 10:10:24 GMT","owner":"tjo656","tenant_id":"tacc"}],"status":"success","version":"26Q1.0"}
```