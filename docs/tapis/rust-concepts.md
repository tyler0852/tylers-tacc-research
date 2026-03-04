# Rust Concepts Used in This CLI

This document briefly defines the Rust concepts already used in the `tapis_cli` codebase.

---

## Cargo + `Cargo.toml` basics

Cargo is Rust’s build system and dependency manager. `Cargo.toml` declares your package metadata (name/version/edition) and the external crates (like `clap`, `reqwest`, `serde`) your project depends on, including optional **features** that enable extra functionality.

---

## Dependencies + feature flags

A crate’s **features** are compile-time toggles that turn on extra APIs. For example, `clap`’s `derive` feature enables `#[derive(Parser)]`, and `reqwest`’s `blocking` + `rustls-tls` features enable synchronous HTTP calls using Rustls for TLS.

---

## Crate/module system (`mod`, `crate::`)

A Rust **crate** is the compiled unit for your project (your whole CLI). `mod client; mod apps; mod auth;` declares modules so code can be split across files, and `crate::...` refers to items starting from the crate root.

---

## Imports and namespacing (`use ...`)

`use` brings names (types, functions, traits) into scope so you don’t have to write full paths. For example `use clap::{Parser, Subcommand};` lets you write `Cli::parse()` and `#[derive(Subcommand)]` without prefixing with `clap::`.

---

## Structs (`struct`)

A `struct` groups related fields into a single type. You use structs for your parsed CLI arguments (`Cli`), reusable configuration (`Client`), and JSON response shapes (`HealthcheckResponse`, `AuthHelloResponse`).

---

## Enums (`enum`)

An `enum` represents one of several variants, often used to model “either/or” choices. Your `Commands` enum represents which subcommand the user chose (`Healthcheck` vs `AuthHello`).

---

## Derive macros (`#[derive(...)]`)

A **derive macro** auto-generates Rust code for a type at compile time. For example, `#[derive(Parser)]` makes Clap able to parse arguments into `Cli`, and `#[derive(Deserialize)]` makes Serde able to build your response structs from JSON.

---

## Attributes (`#[...]`)

Attributes are metadata annotations that configure code generation or behavior. In your CLI, `#[arg(...)]` and `#[command(...)]` tell Clap how to interpret fields and subcommands from the command line.

---

## Ownership + borrowing (references like `&Client`, `&self`)

Rust tracks who owns each value to prevent memory bugs without a garbage collector. Passing `&Client` or using `&self` means you’re borrowing the `Client` without taking ownership, so multiple functions can use the same `Client` safely.

---

## `impl` blocks (associated functions vs methods)

An `impl` block defines functions that belong to a type. `Client::new(...)` is an **associated function** (called on the type), while `apps_healthcheck_url(&self)` is a **method** (called on an instance like `client.apps_healthcheck_url()`).

---

## Pattern matching (`match`)

`match` branches on the shape/value of a type in an exhaustive way. You match on `cli.command` (a `Commands` enum) to call the correct function for each subcommand.

---

## Error handling with `Result<T, E>`

`Result` represents either success (`Ok(T)`) or failure (`Err(E)`). Your functions return `Result<..., Box<dyn std::error::Error>>` so callers can handle failures instead of crashing.

---

## The `?` operator

`?` is shorthand for “if this is an error, return it immediately; otherwise unwrap the success value.” It keeps request/parse code clean when chaining fallible operations like HTTP calls and JSON parsing.

---

## Trait objects / dynamic dispatch (`Box<dyn Error>`)

`Box<dyn std::error::Error>` is a heap-allocated “any error” type that can hold many different error kinds behind a common interface (`Error`). This is convenient for small CLIs because it avoids defining a custom error enum early on.

---

## Visibility (`pub`) and module boundaries

Items are private to their module by default. `pub` exposes structs/functions/fields so other modules can use them (e.g., `pub struct Client` allows `apps` and `auth` modules to accept a `&Client`).

---

## String handling (`String`, `&str`, `format!`, trimming)

`String` is an owned, growable string type, while `&str` is a borrowed string slice. You use `format!` to build URLs and `trim_end_matches('/')` to normalize the base URL so you don’t end up with double slashes.

---

## HTTP requests with `reqwest` (blocking)

`reqwest` is an HTTP client library for Rust. Using `reqwest::blocking::get(...)` performs a synchronous request, then you read the body with `resp.text()?` to get the response as a `String`.

---

## JSON deserialization (`serde`, `serde_json`)

Serde is Rust’s framework for converting between Rust types and structured data formats. `serde_json::from_str` parses JSON text into your typed response structs, using `#[derive(Deserialize)]` and matching JSON keys to struct field names.

---