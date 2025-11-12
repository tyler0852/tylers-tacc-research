# Meeting 8 - November 12th, 2025

## What Got Done

### Learned About Connection Pooling

- In order for our rust server and the database to be able to talk, they need to form a connection
- Without connection pooling, every request trys to open a new connection
    - This eventually leds to the "too many files open" error I was getting
- With connection pooling, there is a set amount of connections made that the requests share and wait for

### Implemented Connection Pooling in tms_min

- Create a new file `db_init.db` that establishes the connection pooling that is used by `main.rs`
- This fixed the "too many files open" error

### Ran tms_min_loadtest

| Scenario | Users | Iterations | Fails | CPU (%) | MEM Usage | State / Behavior | Notes |
|-----------|--------|-------------|--------|----------|-------------|------------------|
| **WH** | 10 | 1,000 | 0 | — | — | Stable | Baseline sanity check. |
| **WH** | 10 | 10,000 | 0 | — | — | Stable | Still negligible impact. |
| **WH** | 10 | 100,000 | 0 | 100–120 | ~5 MB | Alternates between running/sleeping/stuck | Indicates periodic I/O stalls during sustained writes. |
| **WH** | 1000 | 100,000 | 0 | 100–120 | ~50 MB peak | Running steadily | CPU usage plateaued as concurrency increased — consistent with DB write contention. |
| **RH** | 10 | 1,000 | 0 | — | — | Stable | Baseline read. |
| **RH** | 10 | 10,000 | 0 | — | — | Stable | Same negligible load. |
| **RH** | 10 | 100,000 | 0 | ~800 | — | Running | Read throughput extremely high, consistent with memory-bound operation. |
| **RH** | 1000 | 100,000 | 0 | ~830 | — | Running continuously | Saturation near 100 users — connection pool limit bottleneck. |
| **RH (pool = 12)** | 1000 | 100,000 | 0 | ~1000 | — | Running continuously | Higher connection cap removed bottleneck — jobs completed much faster. |


## Road Blockers and Questions

## Next Steps
