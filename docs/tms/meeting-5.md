# Meeting 5 - October 15th, 2025

## What Got Done

### Load Testing Results (Plaintext HTTP)

#### 1. Single-User Baseline

| Users | Iterations | Throttle | Scenario | Avg Latency (ms) | Max (ms) | Fails | Runtime | Notes |
|:------|:-----------|:----------|:-----------|:----------------|:-----------|:--------|:----------|:-------|
| 1 | 100 | None | createkey | 0.52 | 9 | 0 | ~1 s | Baseline sanity check |
| 1 | 1 000 | None | createkey | 0.09 | 7 | 0 | ~1 s | Clean run |
| 1 | 10 000 | None | createkey | 0.04 | 5 | 0 | ~6 s | Sustained, no degradation |
| 1 | 50 000 | None | createkey | 0.04 | 10 | 0 | ~27 s | ~1 850 req/s, stable |
| 1 | 500 000 | None | createkey | 0.05 | 61 | 0 | ~4.6 min | Consistent throughput |
| 1 | 1 000 000 | None | createkey | 0.05 | 82 | 0 | ~8.9 min | Stable long run |
| 1 | 1 000 000 | None | getversion | 0.00 | 1 | 0 | ~36 s | ~27 800 req/s throughput, 100% success (200); constant 1 ms latency, confirms lightweight endpoint |


---

#### 2. Moderate Load (5 Users)

| Users | Iterations | Throttle | Scenario | Avg Latency (ms) | Max (ms) | Fails | Runtime | Notes |
|:------|:-----------|:----------|:-----------|:----------------|:-----------|:--------|:----------|:-------|
| 5 | 10 000 ea | None | createkey | 2.22 | 658 | 0 | 31 s | Early concurrency impact |
| 5 | 50 000 ea | None | createkey | 2.75 | 882 | 0 | 2.8 min | Latency variance rising |
| 5 | 100 000 ea | None | createkey | 3.09 | 1 097 | 0 | 6.0 min | Still stable |
| 5 | 500 000 ea | None | createkey | 3.39 | 1 588 | 0 | 32.4 min | Strong sustained performance |

---

#### 3. High Load (10 Users, No Throttling)

| Users | Iterations | Throttle | Scenario | Avg Latency (ms) | Max (ms) | Fails | Runtime | Notes |
|:------|:-----------|:----------|:-----------|:----------------|:-----------|:--------|:----------|:-------|
| 10 | 1 000 ea | None | createkey | 0.07 | 9 | 0 | 10 s | Clean short run |
| 10 | 2 500 ea | None | createkey | 2.51 | 358 | 0 | 18 s | Fully stable |
| 10 | 3 000 ea | None | createkey | 48.09 | 60 003 | 9 | 52 s | First timeouts after ~11 k req |
| 10 | 3 500 ea | None | createkey | 47.02 | 60 002 | 9 | 32 s | Instability confirmed |
| 10 | 5 000 ea | None | createkey | 179.89 | 60 003 | 30 | 2.2 min | Server froze after ~10 k req |

---

#### 4. Throttled Load Tests

| Users | Iterations | Throttle | Scenario | Avg Latency (ms) | Max (ms) | Fails | Runtime | Notes |
|:------|:-----------|:----------|:-----------|:----------------|:-----------|:--------|:----------|:-------|
| 10 | 3 000 ea | 10 req/s per user (≈100 req/s total) | createkey | 4.27 | 381 | 0 | 69 min | Fully stable for entire duration |
| 10 | 3 000 ea | 250 req/s per user (≈2 500 req/s total) | createkey | 2.18 | 17 | 0 | 2.4 min | Stable upper-limit throughput |
| 10 | 20 000 ea | 250 req/s per user (≈2 500 req/s total) | createkey | 54.7 | 60 171 | 70 | 12.3 min | Stable ~7 min, then progressive timeouts (64 timeouts, 3 500s, 2 401s, 1 400) |

---

#### 5. Overall Findings

- Stable operation up to **≈ 2 500 req/s total** under 10-user load.  
- Failures appear as **timeouts → mixed 400/401/500 codes** during stall.  
- Throughput and latency scale predictably until resource exhaustion.  
- **Throttling** prevents overload and preserves performance stability.  
- Next step: **repeat tests under TLS** (with certificate checking disabled) to measure encryption overhead and confirm identical failure patterns.


## Road Blockers and Questions

## What's Next
