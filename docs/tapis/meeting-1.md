# Meeting 1: February 2nd, 2026

## What Got Done

### Created `hello_cli`

- I chose `clap` (Command Line Argument Parser) because it seems like it is the most common, supported, and documented crate for CLIs
- In short, `clap` is a Rust library (crate) that takes the arguments the program receives from the shell, parses and validates them, and then gives some data structure/output
- `clap` has two high level layers
    - Definition layer: how you describe your CLI with arguments, flags, subcommands, etc via Rust structs or a builder API
    - Parsing layer: At runtime, `clap` reads `argv`, matches it against your definition, validates input, returns output

- Resources:
    - <https://docs.rs/clap/latest/clap/>
    - <https://blessed.rs/crates>
    - <https://crates.io/>
    - <https://rust-cli.github.io/book/index.html>
    - <https://rust-cli-recommendations.sunshowers.io/>

### `tms_loadtest` Testing Suite

- This is almost done
- Features of the test suite:
    - Ability to chose to test a single endpoint or all of them
    - Ability to test different phases of testing:
        - Smoke: A small test to confirm everythings working
        - Ladder: A sequence of runs gradually increasing concurrency
            - Users scale
            - Iterations stay constant
        - Spike: Lots of users at once doing a small amount of requests
            - Not sure if this is really possible because of launching time of users with Goose
        - Soak: Long test to catch leaks at a expected amount of load
        - If you don't specify, it will do all tests, or you can choose just one test to run

## What's Next

- Finish testing suite
    - I still need to work on finalizing the testing data points I want to use and displaying the results in a nice readable way — it is in a state of working as intended right now though with all the options discussed ealier

