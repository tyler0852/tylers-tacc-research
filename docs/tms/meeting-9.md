# Meeting 9 - November 19th, 2025

## What Got Done

### Modified Cargo.toml in tms_min

- Removed the `macros` feature
- Had no change
- Only has features in `tms_server`

### Refinded tms_min endpoints

- Utimately, I was not able to reproduce the error
- I however might have found evidence that leads us to believe it is not sqlite
- Under high load, I was able to get failures, not just the server fully crashing
    - Returned errors like:
 
```bash
thread 'tokio-runtime-worker' panicked at src/main.rs:46:14:
called `Result::unwrap()` on an `Err` value: Database(SqliteError { code: 5, message: "database is locked" })
```
- This is a typical sqlite error under high load
- `tms_server` never gave errors like this, would just suddenly crash

## Road Blockers and Questions


## Next Steps
