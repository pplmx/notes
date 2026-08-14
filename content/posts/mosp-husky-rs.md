---
categories:
    - mosp
date: 2024-10-01T18:47:20Z
description: A git hook manager for Rust projects
keywords: git hooks, git hook manager, husky, husky-rs, Rust, Git management
lastmod: 2026-08-14T00:00:00Z
tags:
    - git-hooks
    - husky
title: My Open Source Project "husky-rs"
weight: 98
---



# husky-rs

**husky-rs** is a lightweight Git hook manager tailored for Rust projects.

- [View husky-rs on GitHub](https://github.com/pplmx/husky-rs)
- [Get husky-rs on crates.io](https://crates.io/crates/husky-rs) (latest: v0.4.0)

## Install

Add it to a Rust project (hooks are installed automatically on `cargo build`/`cargo test` once a `.husky` directory exists):

```bash
cargo add husky-rs        # or: cargo add --dev husky-rs
```

Or install the optional CLI (provides `husky init`, `husky add`, `husky list`):

```bash
cargo install husky-rs
```

## Key Features

- **Easy Integration**: Seamlessly integrates into your Rust projects with minimal setup.
- **Automated Efficiency**: Executes automated tasks via Git hooks, improving team collaboration and productivity.
- **Flexible Customization**: Supports a wide variety of Git hooks, allowing for extensive configuration to meet your project’s needs.

Explore the project on [GitHub](https://github.com/pplmx/husky-rs) for more details and contribute to its development!
