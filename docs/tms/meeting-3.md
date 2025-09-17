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
```bash
tyler@Tylers-MacBook-Pro tms_server % curl -s \
  -H "X-TMS-TENANT: test" \
  -H "X-TMS-ADMIN-ID: $TMS_USER" \
  -H "X-TMS-ADMIN-SECRET: $TMS_PASS" \
  "http://localhost:3000/v1/tms/client/list" | jq

{
  "clients": [
    {
      "app_name": "testapp1",
      "app_version": "1.0",
      "client_id": "testclient1",
      "created": "2025-09-17T16:57:08Z",
      "enabled": 1,
      "id": 1,
      "tenant": "test",
      "updated": "2025-09-17T16:57:08Z"
    }
  ],
  "num_clients": 1,
  "result_code": "0",
  "result_msg": "success"
}
```
- Created a client called `testclient1`
```bash
tyler@Tylers-MacBook-Pro tms_server % curl -s -X POST "http://localhost:3000/v1/tms/pubkeys/creds" \
  -H "X-TMS-TENANT: test" \
  -H "X-TMS-CLIENT-ID: testclient1" \
  -H "X-TMS-CLIENT-SECRET: secret1" \
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
  "expires_at": "2025-09-17T17:59:10Z",
  "key_bits": "256",
  "key_type": "ed25519",
  "max_uses": "0",
  "private_key": "-----BEGIN OPENSSH PRIVATE KEY-----\nb3BlbnNzaC1rZXktdjEAAAAABG5vbmUAAAAEbm9uZQAAAAAAAAABAAAAMwAAAAtzc2gtZW\nQyNTUxOQAAACD/z7UZGk2Sdd3lVneinNtUEjGewGi+GlH8eXzj+ftEDAAAAIh0cKYcdHCm\nHAAAAAtzc2gtZWQyNTUxOQAAACD/z7UZGk2Sdd3lVneinNtUEjGewGi+GlH8eXzj+ftEDA\nAAAECyvvzZmK9J2wmQCA47RHbz1HlBy9tTcwG8PUxdSGZ/w//PtRkaTZJ13eVWd6Kc21QS\nMZ7AaL4aUfx5fOP5+0QMAAAAAAECAwQF\n-----END OPENSSH PRIVATE KEY-----\n",
  "public_key": "ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIP/PtRkaTZJ13eVWd6Kc21QSMZ7AaL4aUfx5fOP5+0QM",
  "public_key_fingerprint": "SHA256:gpz/kvTVb6mY164lDDyuOCZeKSI3ojrik2TIAN55Qv0",
  "remaining_uses": "0",
  "result_code": "0",
  "result_msg": "success"
}
```
- List the keys to verify creation
```bash
tyler@Tylers-MacBook-Pro tms_server % curl -s -X GET "http://localhost:3000/v1/tms/pubkeys/list" \
  -H "X-TMS-TENANT: test" \
  -H "X-TMS-CLIENT-ID: testclient1" \
  -H "X-TMS-CLIENT-SECRET: secret1" \
  -H "accept: application/json; charset=utf-8" | jq

{
  "num_pubkeys": 2,
  "pubkeys": [
    {
      "client_id": "testclient1",
      "client_user_id": "testuser1",
      "created": "2025-09-17T17:55:28.246562Z",
      "expires_at": "2025-09-17T17:55:28Z",
      "host": "testhost1",
      "host_account": "testhostaccount1",
      "id": 1,
      "initial_ttl_minutes": 0,
      "key_bits": 256,
      "key_type": "ed25519",
      "max_uses": 0,
      "public_key": "ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAINRaNZpO4UL0RO7nc7bx5SSrbFC0rRmHFQgRm+3avTeg",
      "public_key_fingerprint": "SHA256:/4M9b09o0WVRryMWMi4eds3ex9EZmRvfWE7iPYGUsLw",
      "remaining_uses": 0,
      "tenant": "test",
      "updated": "2025-09-17T17:55:28.246562Z"
    },
    {
      "client_id": "testclient1",
      "client_user_id": "testuser1",
      "created": "2025-09-17T17:59:10.973027Z",
      "expires_at": "2025-09-17T17:59:10Z",
      "host": "testhost1",
      "host_account": "testhostaccount1",
      "id": 2,
      "initial_ttl_minutes": 0,
      "key_bits": 256,
      "key_type": "ed25519",
      "max_uses": 0,
      "public_key": "ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIP/PtRkaTZJ13eVWd6Kc21QSMZ7AaL4aUfx5fOP5+0QM",
      "public_key_fingerprint": "SHA256:gpz/kvTVb6mY164lDDyuOCZeKSI3ojrik2TIAN55Qv0",
      "remaining_uses": 0,
      "tenant": "test",
      "updated": "2025-09-17T17:59:10.973027Z"
    }
  ],
  "result_code": "0",
  "result_msg": "success"
}
```
- What I did last time I'm realizing was try to delete the pubkey using the client route
```bash
tyler@Tylers-MacBook-Pro tms_server % curl -s -X DELETE "http://localhost:3000/v1/tms/client/del/2" \
  -H "X-TMS-TENANT: test" \
  -H "X-TMS-ADMIN-ID: ~~admin" \
  -H "X-TMS-ADMIN-SECRET: $TMS_PASS" \
  -H "accept: application/json; charset=utf-8" | jq

{
  "num_deleted": 0,
  "result_code": "0",
  "result_msg": "Client 2 NOT FOUND - Nothing deleted"
}
```
- If I did want to delete the client I would do this
    - Notice that the id is `testclient1` and not `1`
```bash
tyler@Tylers-MacBook-Pro tms_server % curl -s -X DELETE "http://localhost:3000/v1/tms/client/del/testclient1" \
  -H "X-TMS-TENANT: test" \
  -H "X-TMS-ADMIN-ID: ~~admin" \
  -H "X-TMS-ADMIN-SECRET: $TMS_PASS" \
  -H "accept: application/json; charset=utf-8" | jq

{
  "num_deleted": 1,
  "result_code": "0",
  "result_msg": "Client testclient1 deleted"
}
tyler@Tylers-MacBook-Pro tms_server % curl -s -X GET "http://localhost:3000/v1/tms/client/list" \
  -H "X-TMS-TENANT: test" \
  -H "X-TMS-ADMIN-ID: ~~admin" \
  -H "X-TMS-ADMIN-SECRET: $TMS_PASS" \
  -H "accept: application/json; charset=utf-8" | jq

{
  "clients": [],
  "num_clients": 0,
  "result_code": "0",
  "result_msg": "success"
}
```


## Road Blockers and Questions

- For awhile I thought pubkeys were clients

## Whats Next
