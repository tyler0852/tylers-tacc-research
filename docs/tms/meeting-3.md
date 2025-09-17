# Meeting 3 - September 17th, 2025

## What Got Done

- I kept running into an issue where it would take me a long time to figure something out and then when I did figure it out, I wouldn't document how I did it well. This readthedocs will server the purpose of sharing my notes, allowing code snippets in the notes, and documentation for myself.

- Went on a rather long expedition documenting the process of client and public key creation, how to list them, and how to delete them.
- Up until this week I was trying to treat them as the same thing and kept getting them confused.
- A client will have many pubkeys associtated with it. If you delete the client, all pubkeys associated with the client are also deleted.
- After a lot of playing around with it, I wanted to layout a 6-step process
    1. Create a client: Registers an app in the tenant with `client_id` and `client_secret`
    2. List clients
    3. Create a pubkey: Using `client_id` and `client_secret`
    4. List pubkeys
    5. Delete the pubkey
    6. Delete the client
 
Step 1: Create a client

```bash
tyler@Tylers-MacBook-Pro tms_server % curl -s -X POST "http://localhost:3000/v1/tms/client" \
  -H "X-TMS-TENANT: test" \
  -H "X-TMS-ADMIN-ID: ~~admin" \
  -H "X-TMS-ADMIN-SECRET: $TMS_PASS" \
  -H "Content-Type: application/json" \
  -H "accept: application/json; charset=utf-8" \
  -d '{
    "client_id": "testclient1",
    "tenant": "test",
    "app_name": "testapp1",
    "app_version": "1.0"
  }' | jq

{
  "client_id": "testclient1",
  "client_secret": "dc870931171135b82a8235ad328b6e326c5bf4826f34ffd3",
  "result_code": "0",
  "result_msg": "success"
}
```

Step 2: List Clients

```bash
tyler@Tylers-MacBook-Pro tms_server % curl -s -X GET "http://localhost:3000/v1/tms/client/list" \
  -H "X-TMS-TENANT: test" \
  -H "X-TMS-ADMIN-ID: ~~admin" \
  -H "X-TMS-ADMIN-SECRET: $TMS_PASS" \
  -H "accept: application/json; charset=utf-8" | jq

{
  "clients": [
    {
      "app_name": "testapp1",
      "app_version": "1.0",
      "client_id": "testclient1",
      "created": "2025-09-17T19:36:54.008386Z",
      "enabled": 1,
      "id": 1,
      "tenant": "test",
      "updated": "2025-09-17T19:36:54.008386Z"
    }
  ],
  "num_clients": 1,
  "result_code": "0",
  "result_msg": "success"
}
```

Step 3: Create a pubkey
- I first exported the client id and secret

```bash
tyler@Tylers-MacBook-Pro tms_server % curl -s -X POST "http://localhost:3000/v1/tms/pubkeys/creds" \
  -H "X-TMS-TENANT: test" \
  -H "X-TMS-CLIENT-ID: $X_TMS_CLIENT_ID" \
  -H "X-TMS-CLIENT-SECRET: $X_TMS_CLIENT_SECRET" \
  -H "Content-Type: application/json" \
  -d '{
    "client_user_id": "testuser1",
    "host": "testhost1",
    "host_account": "testhostaccount1",
    "num_uses": 0,
    "ttl_minutes": 0,
    "key_type": ""
  }' | jq

{
  "expires_at": "2025-09-17T19:49:29Z",
  "key_bits": "256",
  "key_type": "ed25519",
  "max_uses": "0",
  "private_key": "-----BEGIN OPENSSH PRIVATE KEY-----\nb3BlbnNzaC1rZXktdjEAAAAABG5vbmUAAAAEbm9uZQAAAAAAAAABAAAAMwAAAAtzc2gtZW\nQyNTUxOQAAACDvrYFigPHQv9qjDn6e2auJWhlv7RACu/pARalSNJHRjAAAAIiWGvPolhrz\n6AAAAAtzc2gtZWQyNTUxOQAAACDvrYFigPHQv9qjDn6e2auJWhlv7RACu/pARalSNJHRjA\nAAAECQZpdJY2CWjumG2l+m47SU5kegIapuiWE5clxBJtlKl++tgWKA8dC/2qMOfp7Zq4la\nGW/tEAK7+kBFqVI0kdGMAAAAAAECAwQF\n-----END OPENSSH PRIVATE KEY-----\n",
  "public_key": "ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIO+tgWKA8dC/2qMOfp7Zq4laGW/tEAK7+kBFqVI0kdGM",
  "public_key_fingerprint": "SHA256:V2Kz/rjouXPc9Q9av67SLoeTIpo/W/9KcaPMnTVTLaw",
  "remaining_uses": "0",
  "result_code": "0",
  "result_msg": "success"
}
```

Step 4: List the pubkey

```bash
tyler@Tylers-MacBook-Pro tms_server % curl -s -X GET "http://localhost:3000/v1/tms/pubkeys/list" \
  -H "X-TMS-TENANT: test" \
  -H "X-TMS-CLIENT-ID: $X_TMS_CLIENT_ID" \
  -H "X-TMS-CLIENT-SECRET: $X_TMS_CLIENT_SECRET" \
  -H "accept: application/json; charset=utf-8" | jq

{
  "num_pubkeys": 1,
  "pubkeys": [
    {
      "client_id": "testclient1",
      "client_user_id": "testuser1",
      "created": "2025-09-17T19:49:29.327761Z",
      "expires_at": "2025-09-17T19:49:29Z",
      "host": "testhost1",
      "host_account": "testhostaccount1",
      "id": 1,
      "initial_ttl_minutes": 0,
      "key_bits": 256,
      "key_type": "ed25519",
      "max_uses": 0,
      "public_key": "ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIO+tgWKA8dC/2qMOfp7Zq4laGW/tEAK7+kBFqVI0kdGM",
      "public_key_fingerprint": "SHA256:V2Kz/rjouXPc9Q9av67SLoeTIpo/W/9KcaPMnTVTLaw",
      "remaining_uses": 0,
      "tenant": "test",
      "updated": "2025-09-17T19:49:29.327761Z"
    }
  ],
  "result_code": "0",
  "result_msg": "success"
}
```

Step 5: Delete the pubkey
```bash
tyler@Tylers-MacBook-Pro tms_server % curl -s -X DELETE "http://localhost:3000/v1/tms/pubkeys/del" \
  -H "X-TMS-TENANT: test" \
  -H "X-TMS-CLIENT-ID: $X_TMS_CLIENT_ID" \
  -H "X-TMS-CLIENT-SECRET: $X_TMS_CLIENT_SECRET" \
  -H "Content-Type: application/json" \
  -d '{
    "client_id": "testclient1",
    "tenant": "test",
    "host": "testhost1",
    "public_key_fingerprint": "SHA256:V2Kz/rjouXPc9Q9av67SLoeTIpo/W/9KcaPMnTVTLaw"
  }' | jq

{
  "num_deleted": 1,
  "result_code": "0",
  "result_msg": "Pubkey SHA256:V2Kz/rjouXPc9Q9av67SLoeTIpo/W/9KcaPMnTVTLaw for host testhost1 deleted"
}
```
Step 5.5: Check to make sure it was deleted

```bash
tyler@Tylers-MacBook-Pro tms_server % curl -s -X GET "http://localhost:3000/v1/tms/pubkeys/list" \
  -H "X-TMS-TENANT: test" \
  -H "X-TMS-CLIENT-ID: $X_TMS_CLIENT_ID" \
  -H "X-TMS-CLIENT-SECRET: $X_TMS_CLIENT_SECRET" \
  -H "accept: application/json; charset=utf-8" | jq
{
  "num_pubkeys": 0,
  "pubkeys": [],
  "result_code": "0",
  "result_msg": "success"
}
```

Step 6: Delete the client

```bash
tyler@Tylers-MacBook-Pro tms_server % curl -s -X DELETE "http://localhost:3000/v1/tms/client/del/testclient1" \
  -H "X-TMS-TENANT: test" \
  -H "X-TMS-ADMIN-ID: $TMS_ADMIN_ID" \
  -H "X-TMS-ADMIN-SECRET: $TMS_ADMIN_SECRET" \
  -H "accept: application/json; charset=utf-8" | jq

{
  "num_deleted": 1,
  "result_code": "0",
  "result_msg": "Client testclient1 deleted"
}
```

Step 6.5: Check that the client is deleted
```bash
tyler@Tylers-MacBook-Pro tms_server % curl -s -X GET "http://localhost:3000/v1/tms/client/list" \
  -H "X-TMS-TENANT: test" \
  -H "X-TMS-ADMIN-ID: $TMS_ADMIN_ID" \
  -H "X-TMS-ADMIN-SECRET: $TMS_ADMIN_SECRET" \
  -H "accept: application/json; charset=utf-8" | jq

{
  "clients": [],
  "num_clients": 0,
  "result_code": "0",
  "result_msg": "success"
}
```

## Road Blockers and Questions

- I didn't really run into anything I couldn't figure, just took me awhile...

## Whats Next

- Learn about certificates
- Run more load test, get TLS back in the picture
