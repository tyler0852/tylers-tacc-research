# Meeting 3 - September 17th, 2025

## What Got Done

- Here is the process I used to create/list/delete keys
    ```bash
    curl  \
      -H "X-TMS-TENANT: test" \
      -H "X-TMS-ADMIN-ID: ~~admin" \
      -H "X-TMS-ADMIN-SECRET: $TMS_PASS" \
      "http://localhost:3000/v1/tms/client/list" | jq
    ```



## Road Blockers

## Whats Next
