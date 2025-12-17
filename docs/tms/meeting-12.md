# Meeting 12 - December 17th, 2025

## What Got Done

### Load Test Results

### Errors Codes

#### From Server Logs
- 2025-12-17T20:36:11.586768000Z ERROR [tokio-runtime-worker] src/v1/tms/pubkeys_create.rs:182 - ERROR: NOT AUTHORIZED Credential mismatch for client testclient1 in tenant test.
- 2025-12-17T20:36:11.586861000Z ERROR [tokio-runtime-worker] src/v1/tms/pubkeys_create.rs:230 - Missing or expired dependency: pool timed out while waiting for an open connection
- 2025-12-17T20:37:11.628850000Z ERROR [tokio-runtime-worker] src/utils/authz.rs:269 - Unable to retrieve secret for client ID 'testclient1': pool timed out while waiting for an open connection
- 2025-12-17T20:37:41.646208000Z ERROR [tokio-runtime-worker] src/v1/tms/pubkeys_create.rs:135 - ERROR: pool timed out while waiting for an open connection

#### From Load Test
- 20:37:41 [WARN] "/v1/tms/pubkeys/creds": error sending request for url (http://localhost:3000/v1/tms/pubkeys/creds): operation timed out

## Next Steps
