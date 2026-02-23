# main.rs

## CLI Definition

```bash
#[derive(Parser, Debug)]
struct Cli {
```

- `#[derive(Parser, Debug)]`: tells clap to generate parsing logic from this struct
- `struct Cli`: defines how we use the command line
- Sets a base url of "https://dev.tapis.io" if the user doesn't provide one

## Subcommands enum

```bash
#[derive(Subcommand, Debug)]
enum Commands {
    Healthcheck,
    AuthHello,
}
```

- Defines the valid subcommands the user can use

## main

- Parses cli args
- Creates a client struct
- matches the chosen subcommand to determine output