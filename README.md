<div align="center">

<h1>
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="docs/assets/dragon-ouroboros-dark.svg">
    <source media="(prefers-color-scheme: light)" srcset="docs/assets/dragon-ouroboros.svg">
    <img alt="ZeroCode" src="docs/assets/dragon-ouroboros.svg" width="96">
  </picture>
  <br>
  ZeroCode (<code>zerocode</code>)
</h1>

**ZeroCode** is an AI coding agent harness (forked from upstream grok-build; Apache-2.0).
It provides an agent runtime for embedding in editors and desktop hosts via the
Agent Client Protocol (ACP), plus an optional full-screen TUI. The product path
for ZeroWork Desktop is **ACP stdio only** — do not embed the TUI.

[Installing the released binary](#installing-the-released-binary) ·
[Building from source](#building-from-source) ·
[Documentation](#documentation) ·
[Repository layout](#repository-layout) ·
[Development](#development) ·
[Contributing](#contributing) ·
[License](#license)

**Upstream reference:** [xai-org/grok-build](https://github.com/xai-org/grok-build) (Apache-2.0). This fork brands as **ZeroCode**.

This repository contains the Rust source for the `zerocode` CLI and its agent
runtime. It is synced from upstream `xai-org/grok-build` (originally SpaceXAI monorepo exports).

A small `SOURCE_REV` file at the root records the full monorepo commit SHA
for the version of the code present in this tree.

</div>

---

## Installing / running

ZeroCode ships the **`zerocode`** binary from this fork (build from source below).
Do **not** use the upstream `x.ai/cli` installer for ZeroWork Desktop Host wiring.

```sh
cargo build -p xai-grok-pager-bin --release
./target/release/zerocode --version
# Host ACP path:
#   zerocode agent … stdio
```

Upstream reference changelog (historical): https://x.ai/build/changelog

## Building from source

Requirements:

- **Rust** — the toolchain is pinned by [`rust-toolchain.toml`](rust-toolchain.toml);
  `rustup` installs it automatically on first build.
- **[DotSlash](https://dotslash-cli.com)** — required so hermetic tools under
  [`bin/`](bin/) (notably [`bin/protoc`](bin/protoc)) can download and run.
  Install it and ensure `dotslash` is on your `PATH` **before** building:

  ```sh
  cargo install dotslash
  # or: prebuilt packages — https://dotslash-cli.com/docs/installation/
  /usr/bin/env dotslash --help   # sanity check
  ```

- **protoc** — proto codegen resolves [`bin/protoc`](bin/protoc) via DotSlash,
  or falls back to a `protoc` on `PATH` / `$PROTOC`.
- macOS and Linux are supported build hosts; Windows builds are best-effort
  and not currently tested from this tree.

```sh
cargo run -p xai-grok-pager-bin              # build + launch the TUI
cargo build -p xai-grok-pager-bin --release  # release binary: target/release/zerocode
cargo check -p xai-grok-pager-bin            # fast validation
```

The binary artifact is named **`zerocode`** (crate package remains `xai-grok-pager-bin` for now).
Data directory default: **`~/.zerowork`** (override with `$GROK_HOME`).
Host spawn: `zerocode agent … stdio`. Env for desktop Host: **`ZEROCODE_BIN`**.
Default outbound auth/telemetry URLs are emptied in this fork. ZeroWork Host must use **BYOK** / local config (API key + `GROK_XAI_API_BASE_URL` / `[endpoints]`) and **must not** drive xAI browser login. OAuth to `auth.x.ai` is off unless `GROK_OAUTH2_*` is explicitly set.

## Documentation

Upstream online docs (historical reference):
[docs.x.ai/build/overview](https://docs.x.ai/build/overview).

The user guide ships with the pager crate:
[`crates/codegen/xai-grok-pager/docs/user-guide/`](crates/codegen/xai-grok-pager/docs/user-guide/)
— getting started, keyboard shortcuts, slash commands, configuration, theming,
MCP servers, skills, plugins, hooks, headless mode, sandboxing, and more.

## Repository layout

| Path | Contents |
|------|----------|
| `crates/codegen/xai-grok-pager-bin` | Composition-root package; builds the `zerocode` binary |
| `crates/codegen/xai-grok-pager` | The TUI: scrollback, prompt, modals, rendering |
| `crates/codegen/xai-grok-shell` | Agent runtime + leader/stdio/headless entry points |
| `crates/codegen/xai-grok-tools` | Tool implementations (terminal, file edit, search, ...) |
| `crates/codegen/xai-grok-workspace` | Host filesystem, VCS, execution, checkpoints |
| `crates/codegen/...` | The rest of the CLI crate closure (config, MCP, markdown, sandbox, ...) |
| `crates/common/`, `crates/build/`, `prod/mc/` | Small shared leaf crates pulled in by the closure |
| `third_party/` | Vendored upstream source (Mermaid diagram stack) — see below |

> [!IMPORTANT]
> The root `Cargo.toml` (workspace members, dependency versions, lints,
> profiles) is **generated** — treat it as read-only. Prefer editing per-crate
> `Cargo.toml` files.

## Development

```sh
cargo check -p <crate>        # always target specific crates; full-workspace builds are slow
cargo test -p xai-grok-config # per-crate tests
cargo clippy -p <crate>       # lint config: clippy.toml at the repo root
cargo fmt --all               # rustfmt.toml at the repo root
```

## Contributing

> [!NOTE]
> External contributions are not accepted. See [`CONTRIBUTING.md`](CONTRIBUTING.md).

## License

First-party code in this repository is licensed under the **Apache License,
Version 2.0** — see [`LICENSE`](LICENSE).

Third-party and vendored code remains under its original licenses. See:

- [`THIRD-PARTY-NOTICES`](THIRD-PARTY-NOTICES) — crates.io / git dependencies,
  bundled UI themes, and **in-tree source ports** (including openai/codex and
  sst/opencode tool implementations)
- [`crates/codegen/xai-grok-tools/THIRD_PARTY_NOTICES.md`](crates/codegen/xai-grok-tools/THIRD_PARTY_NOTICES.md)
  — crate-local notice for the codex and opencode ports (license texts +
  Apache §4(b) change notice)
- [`third_party/NOTICE`](third_party/NOTICE) — vendored Mermaid-stack index
