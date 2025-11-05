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

| Scenario | Users | Iterations | Throttle | Avg Latency (ms) | Fails | Runtime |
|:-----------|:------|:-----------|:----------|:----------------|:--------|:----------|
| writeheavy (1000 rows) | 10 | 1,000 | None | 2,937 | 9 (0%) | 26 min | 
| writeheavy (1000 rows) | 1000 | 100,000 | None | 390.9 | 99.9% | ~1 hr | 
| writeheavy (10 rows) | 50 | 1,000 | None | 102.8 | 1 (0%) | 153 s | 
| writeheavy (10 rows) | 1000 | 100,000 | None | 5.29 | 99.4% | ~37 min | 



## Road Blocker and Questions


## Next Steps
