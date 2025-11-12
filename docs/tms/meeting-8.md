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

| Scenario | Users | Iterations | %CPU | MEM Usage | State / Behavior | Notes |
|:-----------|:------|:-----------|:------|:------------|:------------------|:--------|
| writeheavy | 10 | 1,000 | — | — | Stable | Baseline sanity check. |
| writeheavy | 10 | 10,000 | — | — | Stable | Still negligible impact. |
| writeheavy | 10 | 100,000 | 100–120 | ~5 MB | Alternates between running/sleeping/stuck | Indicates periodic I/O stalls during sustained writes. |
| writeheavy | 1000 | 100,000 | 100–120 | ~50 MB peak | Running steadily | CPU plateaued as concurrency increased — consistent with DB write contention. |
| readheavy | 10 | 1,000 | — | — | Stable | Baseline read. |
| readheavy | 10 | 10,000 | — | — | Stable | Same negligible load. |
| readheavy | 10 | 100,000 | ~800 | — | Running | Extremely high throughput — memory-bound reads. |
| readheavy | 1000 | 100,000 | ~830 | — | Running continuously | Saturation near 100 users — connection pool limit bottleneck. |
| readheavy (pool = 12) | 1000 | 100,000 | ~1000 | — | Running continuously | Higher connection cap removed bottleneck — jobs completed much faster. |



## Road Blockers and Questions

## Next Steps
