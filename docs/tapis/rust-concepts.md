# Rust Concepts Used in This CLI

Quick definitions of the Rust stuff that shows up in `tapis_cli` so far.

## Cargo + `Cargo.toml` basics

Cargo is the tool that builds/runs Rust projects and manages dependencies. `Cargo.toml` is where I define the package info (name/version/edition) and list crates I depend on (like `clap`, `reqwest`, `serde`), including any feature flags I turn on.

## Dependencies + feature flags

A crate can expose “features” which are compile-time switches for extra functionality. Example: `clap` with `derive` lets me use `#[derive(Parser)]`. `reqwest` with `blocking` + `rustls-tls` lets me do synchronous HTTP requests with Rustls handling TLS.

## Crate/module system (`mod`, `crate::`)

A “crate” is basically the whole compiled project. `mod client; mod apps; mod auth;` splits my code into separate files/modules. `crate::...` is how I refer to things starting from the crate root (basically the top of the project).

## Imports and namespacing (`use ...`)

`use` pulls names into scope so I don’t have to write long paths. Example: `use clap::{Parser, Subcommand};` means I can write `Cli::parse()` and `#[derive(Subcommand)]` without prefixing everything with `clap::`.

## Structs (`struct`)

A `struct` is a “bundle of fields” that forms a type. I use structs for parsed CLI args (`Cli`), shared config (`Client`), and JSON response shapes (like `HealthcheckResponse`, `AuthHelloResponse`).

## Enums (`enum`)

An `enum` is “one of several options.” In my CLI, `Commands` represents which subcommand was chosen (like `Healthcheck` vs `AuthHello`).

## Derive macros (`#[derive(...)]`)

`#[derive(...)]` auto-generates code at compile time. `#[derive(Parser)]` generates Clap parsing for my CLI structs. `#[derive(Deserialize)]` generates Serde code so my structs can be built from JSON.

## Attributes (`#[...]`)

Attributes are those `#[...]` annotations that configure behavior. For Clap, stuff like `#[arg(...)]` and `#[command(...)]` tells it how to interpret flags, defaults, and subcommands.

## Ownership + borrowing (`&Client`, `&self`)

Rust enforces ownership rules so memory stays safe without a garbage collector. When I pass `&Client` or use `&self`, I’m borrowing instead of moving ownership, so multiple functions can share the same `Client` safely.

## `impl` blocks (associated functions vs methods)

An `impl` block is where I define functions for a type. `Client::new(...)` is an associated function (called on the type). `apps_healthcheck_url(&self)` is a method (called on an instance like `client.apps_healthcheck_url()`).

## Pattern matching (`match`)

`match` is Rust’s clean way to branch on enums/values. I `match` on `cli.command` to decide which subcommand handler to run.

## Error handling with `Result<T, E>`

`Result` is either `Ok(value)` or `Err(error)`. I return `Result<..., Box<dyn std::error::Error>>` so errors can be handled instead of crashing, and so I can reuse one return type for different error sources.

## The `?` operator

`?` means: “if this is an error, return it right away; otherwise give me the success value.” It keeps HTTP + JSON parsing code from turning into a bunch of nested `match` blocks.

## Trait objects / dynamic dispatch (`Box<dyn Error>`)

`Box<dyn std::error::Error>` is a convenient “any error” container. It lets me return different error types through one interface without defining a custom error enum yet.

## Visibility (`pub`) and module boundaries

Things are private to their module by default. `pub` makes them accessible from other modules. Example: `pub struct Client` lets other modules accept and use a `Client`.

## String handling (`String`, `&str`, `format!`, trimming)

`String` is an owned growable string; `&str` is a borrowed view into a string. I use `format!` to build URLs, and `trim_end_matches('/')` so the base URL is consistent and I don’t create `//` accidentally.

## HTTP requests with `reqwest` (blocking)

`reqwest` is the HTTP client crate. Using `reqwest::blocking::get(...)` makes a synchronous request, and `resp.text()?` reads the response body into a `String`.

## JSON deserialization (`serde`, `serde_json`)

Serde handles converting between Rust types and data formats. `serde_json::from_str` takes JSON text and builds my response structs, using `#[derive(Deserialize)]` and matching JSON keys to struct fields.