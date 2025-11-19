# Meeting 9 - November 19th, 2025

## What Got Done

### Modified Cargo.toml in tms_min

- Removed the `macros` feature
- Had no change
- Only has features in `tms_server`

### Refinded tms_min endpoints

- Utimately was not able to reproduce the error
- I however might have found evidence that leads us to believe it is not sqlite
- Under high load, I was able to get failures, not just the server fully crashing
    - Returned errors like:
 
```bash
thread 'tokio-runtime-worker' panicked at src/main.rs:46:14:
called `Result::unwrap()` on an `Err` value: Database(SqliteError { code: 5, message: "database is locked" })
```


## Road Blockers and Questions


## Next Steps
