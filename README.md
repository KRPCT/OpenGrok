<div align="center">

<h1>OpenGrok (<code>grok</code>)</h1>

**English** · [简体中文](README.zh-CN.md)

A terminal-based AI coding agent — a full-screen TUI that understands your
codebase, edits files, runs shell commands, searches the web, and manages
long-running tasks.

</div>

---

> **About this fork.** OpenGrok is an independent, rebranded fork of
> [xAI's **Grok Build**](https://github.com/xai-org/grok-build), distributed
> under the Apache License 2.0. It is **not affiliated with, sponsored by, or
> endorsed by** SpaceXAI or xAI. "Grok" is a trademark of its respective owner;
> the Apache License does not grant rights to that mark. See [`NOTICE`](NOTICE)
> for attribution and the list of modifications made in this fork.

## Highlights

- Full-screen terminal UI with scrollback, prompt, and modals.
- Reads and edits your codebase; runs shell commands, search, and web fetch.
- Runs interactively, headlessly (`grok -p "…"` for scripting/CI), or embedded
  in editors via the Agent Client Protocol (ACP).
- Pluggable model backends, including custom / third-party OpenAI-compatible
  endpoints configured in `~/.grok/config.toml`.

## Building from source

Requirements:

- **Rust** — the toolchain is pinned by [`rust-toolchain.toml`](rust-toolchain.toml);
  `rustup` installs it automatically on first build.
- **protoc** — proto codegen resolves [`bin/protoc`](bin/protoc) (a
  [dotslash](https://dotslash-cli.com) launcher) or falls back to a `protoc` on
  `PATH` / `$PROTOC`.
- macOS and Linux are supported build hosts; Windows builds are best-effort.

```sh
cargo run   -p xai-grok-pager-bin            # build + launch the TUI
cargo build -p xai-grok-pager-bin --release  # release binary: target/release/xai-grok-pager
cargo check -p xai-grok-pager-bin            # fast validation
```

The binary artifact is named `xai-grok-pager`; the launched command is `grok`.
On first launch it opens your browser to authenticate. Custom / third-party
OpenAI-compatible model endpoints can be configured in `~/.grok/config.toml`
(see the user guide under `crates/codegen/xai-grok-pager/docs/user-guide/`).

## Repository layout

| Path | Contents |
|------|----------|
| `crates/codegen/xai-grok-pager-bin` | Composition-root package; builds the `xai-grok-pager` binary |
| `crates/codegen/xai-grok-pager` | The TUI: scrollback, prompt, modals, rendering |
| `crates/codegen/xai-grok-shell` | Agent runtime + leader/stdio/headless entry points |
| `crates/codegen/xai-grok-tools` | Tool implementations (terminal, file edit, search, ...) |
| `crates/codegen/xai-grok-workspace` | Host filesystem, VCS, execution, checkpoints |
| `crates/codegen/...` | The rest of the CLI crate closure (config, MCP, markdown, sandbox, ...) |
| `crates/common/`, `crates/build/`, `prod/mc/` | Small shared leaf crates pulled in by the closure |
| `third_party/` | Vendored upstream source (Mermaid diagram stack) |

> [!IMPORTANT]
> The root `Cargo.toml` (workspace members, dependency versions, lints,
> profiles) is **generated** — treat it as read-only. Prefer editing per-crate
> `Cargo.toml` files.

## Development

```sh
cargo check -p <crate>        # always target specific crates; full-workspace builds are slow
cargo test  -p xai-grok-config # per-crate tests
cargo clippy -p <crate>       # lint config: clippy.toml at the repo root
cargo fmt --all               # rustfmt.toml at the repo root
```

## License

First-party code in this repository is licensed under the **Apache License,
Version 2.0** — see [`LICENSE`](LICENSE) and [`NOTICE`](NOTICE).

Third-party and vendored code remains under its original licenses. See:

- [`THIRD-PARTY-NOTICES`](THIRD-PARTY-NOTICES) — crates.io / git dependencies,
  bundled UI themes, and in-tree source ports (including openai/codex and
  sst/opencode tool implementations).
- [`crates/codegen/xai-grok-tools/THIRD_PARTY_NOTICES.md`](crates/codegen/xai-grok-tools/THIRD_PARTY_NOTICES.md)
  — crate-local notice for the codex and opencode ports.
- [`third_party/NOTICE`](third_party/NOTICE) — vendored Mermaid-stack index.
