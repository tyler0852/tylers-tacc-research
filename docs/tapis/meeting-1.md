# Meeting 1: February 2nd, 2026

## What Got Done

### Read First 4 Chapters of TRPL

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
