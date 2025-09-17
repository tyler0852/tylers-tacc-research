# Meeting 3 - September 17th, 2025

## What Got Done

- Here is the process I used to create/list/delete keys
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


## Road Blockers

## Whats Next
