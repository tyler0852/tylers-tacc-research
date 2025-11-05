# Meeting 7 - November 5th, 2025

## What Got Done

### Created v1 of tms_min

- Built a minimal Rust server using Poem and SQLite to emulate how `tms_server` interacts with SQLite
- Implemented three endpoint:
    - `/baseline`: Simple read test to see if the server is up
    - `/writeheavy`: Inserts 10 rows of text into SQLite database
    - `/readheavy`: Reads 10 rows of text from SQLite database
- The `/writeheavy` endpoint orginally inserted 1000 rows per requests. This was reduced to 10 to try and better reflect `/createkey` 
- Server writes to a local `tms_min.db` file inside the `tms_min` directory

### Created tms_min_loadtest

- Created another loadtest similar to `tms_loadtest`, just simplified
- Ex input:
```bash
cargo run --release -- --host http://127.0.0.1:3000 --users 1000 --iterations 1000 --scenarios writeheavy
```

### Results

| Scenario | Users | Iterations | Throttle | Avg Latency (ms) | Max (ms) | Fails | Runtime | Notes |
|:-----------|:------|:-----------|:----------|:----------------|:-----------|:--------|:----------|:-------|
| writeheavy (1000 rows) | 10 | 1,000 | None | 2,937 | 15,550 | 9 (0%) | 26 min | Slow but stable; minor connection resets |
| writeheavy (1000 rows) | 1000 | 100,000 | None | 390.9 | 60,531 | 99.9% | ~1 hr | Crashed with “Too many open files (os error 24)” and timeouts |
| writeheavy (10 rows) | 50 | 1,000 | None | 102.8 | 5,360 | 1 (0%) | 153 s | Completed successfully; no DB lock contention |
| writeheavy (10 rows) | 1000 | 100,000 | None | 5.29 | 41,041 | 99.4% | ~37 min | High throughput (~40k req/s) before hitting file descriptor cap |



## Road Blocker and Questions


## Next Steps
