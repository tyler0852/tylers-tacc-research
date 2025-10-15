# Meeting 5 - October 15th, 2025

## What Got Done

### Load Testing Results (Plaintext HTTP)

#### Single-User Baseline

| Users | Iterations | Throttle | Scenario | Avg Latency (ms) | Max (ms) | Fails | Runtime | Notes |
|:------|:-----------|:----------|:-----------|:----------------|:-----------|:--------|:----------|:-------|
| 1 | 100 | None | createkey | 0.52 | 9 | 0 | ~1 s | Baseline sanity check |
| 1 | 1 000 | None | createkey | 0.09 | 7 | 0 | ~1 s | stable |
| 1 | 10 000 | None | createkey | 0.04 | 5 | 0 | ~6 s | stable |
| 1 | 50 000 | None | createkey | 0.04 | 10 | 0 | ~27 s | stable |
| 1 | 500 000 | None | createkey | 0.05 | 61 | 0 | ~4.6 min | stable |
| 1 | 1 000 000 | None | createkey | 0.05 | 82 | 0 | ~8.9 min | Stable long run, heaviest endpoint |
| 1 | 1 000 000 | None | getversion | 0.00 | 1 | 0 | ~36 s | 100% success, lightweight endpoint |
| 1 | 1 000 000 | None | getclient | 0.00 | 6 | 0 | ~155 s | 100% success, lightweight endpoint |


---

#### Moderate Load (5 Users)

| Users | Iterations | Throttle | Scenario | Avg Latency (ms) | Max (ms) | Fails | Runtime | Notes |
|:------|:-----------|:----------|:-----------|:----------------|:-----------|:--------|:----------|:-------|
| 5 | 10 000 ea | None | createkey | 2.22 | 658 | 0 | 31 s | Early concurrency impact |
| 5 | 50 000 ea | None | createkey | 2.75 | 882 | 0 | 2.8 min | Latency variance rising |
| 5 | 100 000 ea | None | createkey | 3.09 | 1 097 | 0 | 6.0 min | Still stable |
| 5 | 500 000 ea | None | createkey | 3.39 | 1 588 | 0 | 32.4 min | Strong sustained performance |

---

#### High Load (10 Users, No Throttling)

| Users | Iterations | Throttle | Scenario | Avg Latency (ms) | Max (ms) | Fails | Runtime | Notes |
|:------|:-----------|:----------|:-----------|:----------------|:-----------|:--------|:----------|:-------|
| 10 | 1 000 ea | None | createkey | 0.07 | 9 | 0 | 10 s | Clean short run |
| 10 | 2 500 ea | None | createkey | 2.51 | 358 | 0 | 18 s | Fully stable |
| 10 | 3 000 ea | None | createkey | 48.09 | 60 003 | 9 | 52 s | First timeouts after ~11 k req |
| 10 | 3 500 ea | None | createkey | 47.02 | 60 002 | 9 | 32 s | Instability confirmed |
| 10 | 5 000 ea | None | createkey | 179.89 | 60 003 | 30 | 2.2 min | Server froze after ~10 k req |

---

#### Higher Load (40 Users, No Throttling)

| Users | Iterations | Throttle | Scenario | Avg Latency (ms) | Max (ms) | Fails | Runtime | Notes |
|:------|:-----------|:----------|:-----------|:----------------|:-----------|:--------|:----------|:-------|
| 10 | 1 000 ea | None | createkey | 0.07 | 9 | 0 | 10 s | Clean short run |
| 10 | 2 500 ea | None | createkey | 2.51 | 358 | 0 | 18 s | Fully stable |
| 10 | 3 000 ea | None | createkey | 48.09 | 60 003 | 9 | 52 s | First timeouts after ~11 k req |
| 10 | 3 500 ea | None | createkey | 47.02 | 60 002 | 9 | 32 s | Instability confirmed |
| 10 | 5 000 ea | None | createkey | 179.89 | 60 003 | 30 | 2.2 min | Server froze after ~10 k req |

---

#### Throttled Load Tests

| Users | Iterations | Throttle | Scenario | Avg Latency (ms) | Max (ms) | Fails | Runtime | Notes |
|:------|:-----------|:----------|:-----------|:----------------|:-----------|:--------|:----------|:-------|
| 40 | 1 000 | None | createkey | 0.07 | 12 | 0 | ~40 s | Fully stable |
| 40 | 5 000 | None | createkey | 2.57 | 458 | 40 (0.45%) | ~57 s | Froze after 8900 of the 200,000 runs |



---

#### Overall Findings

- Stable operation up to ≈ 250 req/s with 10-user load for 7 minutes
- Failures appear all timeouts from tms_server crashing 
- Throughput and latency scale predictably  
- Throttling prevents overload and preserves performance stability.  


## Road Blockers and Questions

- When running test for 'getkey', I need the import for 'TMS_PUBLEY_FINGERPRINT'

- Couldn't figure out how to bypass cert checking while still including encryption
    - Tried to just use ChatGPT to see if the problem can be solved quickly, turn out I need to actually learn it
    - Created a new 'tms_loadtest_modified'
    - Tried to create a custom 'reqwest::Client' that ignores invalid certs
    - Seems like I was getting version incompatability, need to spend more time to fully understand

## What's Next

- Do more research about how to bypass cert checking 

- See if I can run a test that has things fail during, not just when it crashes
