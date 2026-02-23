# Cargo.toml

- The manifest for this Rust project
- Sets Rust edition
- Lists dependencies (crates) and their enabled features

## Package

```bash
[package]
name = "tapis_cli"
version = "0.1.0"
edition = "2024"
```

- `name`: The crate/binary name
    - This will become the defualt executable


## Dependencies

```bash
clap = { version = "4", features = ["derive"] }
reqwest = { version = "0.12", features = ["blocking", "rustls-tls"] }
serde = { version = "1", features = ["derive"] }
serde_json = "1"
```

### `clap`
- Purpose: It parses CLI args into a struct/enum you can match on
- `derive`: Lets us define the CLI using Rust structs/enums & attributes
    - Struct for global flags/options
    - enum for subcommands

### `reqwest`
- Purpose: Used to make HTTP requests to Tapis endpoints
- `blocking`: Avoids dealing with async runtime for now
- `rustls-tls`: Enables HTTPS support

### `serde`
- Purpose: Deserialize JSON responses into a struct. Used to convert the JSONs given from Tapis endpoint calls
- `derive`: avoids manually parsing and mapping

### `serde_json`
- Purpose: Helps format JSON for `serde`