# Building from Source (cargo-make)

## 📦 Using Makefile.toml
Spyglass can be built using either the traditional `Makefile` or the `Makefile.toml` with `cargo-make`. 
This guide provides instructions for using `cargo-make`.


### 🔧 Prerequisite

1. Install [Rust](https://www.rust-lang.org/tools/install)
2. Install [cargo-make](https://github.com/sagiegurari/cargo-make):
    ```bash
    cargo install cargo-make
    ```
3. Install Node(NPM): https://nodejs.org


### 🏗️ Setup

```bash
cargo make setup
```

### 🚀 Run
To run both frontend and backend:
```bash
cargo make run
```

For running client only:
```bash
cargo make run-client 
```

For running backend only:
```bash
cargo make run-backend 
```

### 🎉 Bundle
[coming soon...]