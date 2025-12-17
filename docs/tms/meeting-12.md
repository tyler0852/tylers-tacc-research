# Meeting 12 - December 17th, 2025

## What Got Done

### Load Test Results

| Scenario  | Users | Iterations | Throttle | Req/s | Avg Latency (ms) | P99 (ms) | Fails | Runtime |
|----------|-------|------------|----------|-------|------------------|----------|-------|---------|
| createkey | 5 | 1000 | None | 1000 | 0.04 | 2 | 0 | 5s |
| createkey | 5 | 10000 | None | 2632 | 0.95 | 10 | 0 | 19s |
| createkey | 5 | 100000 | None | 2370 | 1.46 | 22 | 0 | 205s |
| createkey | 5 | 1000000 | None | 1903 | 2.02 | 23 | 0 | 2628s |
| createkey | 10 | 1000 | None | 1000 | 0.07 | 2 | 0 | 10s |
| createkey | 10 | 10000 | None | 2128 | 3.31 | 14 | 0 | 47s |
| createkey | 10 | 100000 | None | 1724 | 5.2 | 17 | 0 | 580s |
| createkey | 50 | 1000 | None | 1000 | 0.06 | 2 | 0 | 50s |


### Observations From 50 Users and 10,000 Iterations

- Errors from server logs
    - 2025-12-17T20:36:11.586768000Z ERROR [tokio-runtime-worker] src/v1/tms/pubkeys_create.rs:182 - ERROR: NOT AUTHORIZED Credential mismatch for client testclient1 in tenant test.
    - 2025-12-17T20:36:11.586861000Z ERROR [tokio-runtime-worker] src/v1/tms/pubkeys_create.rs:230 - Missing or expired dependency: pool timed out while waiting for an open connection
    - 2025-12-17T20:37:11.628850000Z ERROR [tokio-runtime-worker] src/utils/authz.rs:269 - Unable to retrieve secret for client ID 'testclient1': pool timed out while waiting for an open connection
    - 2025-12-17T20:37:41.646208000Z ERROR [tokio-runtime-worker] src/v1/tms/pubkeys_create.rs:135 - ERROR: pool timed out while waiting for an open connection
    - 2025-12-17T20:51:42.108131000Z ERROR [tokio-runtime-worker] src/utils/tms_utils.rs:361 - Unable to determine if tenant 'test' is enabled: pool timed out while waiting for an open connection

- Erros from load test
    - 20:37:41 [WARN] "/v1/tms/pubkeys/creds": error sending request for url (http://localhost:3000/v1/tms/pubkeys/creds): operation timed out


## Next Steps
