# Tapis CLI

## Cargo.toml Dependencies

```bash
clap = { version = "4", features = ["derive"] }
```

### `clap`
- `clap` is a Rust crate that turns command line input into structured data
- `derive` lets us define the CLI using Rust structs/enums & attributes
    - Struct for global flags/options
    - enum for subcommands
