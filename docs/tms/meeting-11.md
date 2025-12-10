# Meeting 11 - December 10th, 2025

## What Got Done

### Finished `tms_getclient_only_v1`

- `tms_min` showed us that SQLite is likely not the problem

- `tms_getclient_only_v1` gives us a baseline version to keep building upon that ensures use of the same `tms_server` stack

- Currently does not fail. Could easily handle 100 users doing 100,000 iterations while `tms_server` fails at 10 users doing 100,000 iterations

## Next Steps

- Incrementally re-add all the `tms_server` features
