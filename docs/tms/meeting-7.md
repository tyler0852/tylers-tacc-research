# Meeting 7 - November 5th, 2025

## What Got Done

### Created tms_min

- Built a minimal Rust server using Poem and SQLite to emulate how `tms_server` interacts with SQLite
- Implemented three endpoint:
    - `/baseline`: Simple read test to see if the server is up
    - `/writeheavy`: Inserts 1000 rows of text into SQLite database
    - `/readheavy`: Reads 1000 rows of text from SQLite database
- Server writes to a local `tms_min.db` file inside the `tms_min` directory

### Created tms_min_loadtest

- Created another loadtest similar to `tms_loadtest`, just simplified
- Ex input:
```bash
cargo run --release -- --host http://127.0.0.1:3000 --users 1000 --iterations 1000 --scenarios writeheavy
```

### Results

## Road Blocker and Questions


## Next Steps
