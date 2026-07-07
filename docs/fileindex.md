---
doc_type: fileindex
managed_by: sync-repo-docs
current_through_commit: c7e4fda72a113994cf4eccf524826bb98512e507
current_through_date: 2026-07-06T00:30:11-04:00
---

# File Index

## Top-Level Layout
- `src/` - Rust CLI, MCP server, diagnostics, screenshot, accessibility, input, and windowing code.
- `src/windowing/` - compositor-specific window list/focus backends and shared window types.
- `src/bin/` - secondary binaries, currently `computer-use-linux-cosmic`.
- `gnome-shell-extension/` - GNOME Shell extension for exact Wayland window targeting.
- `npm/` - npm wrapper, postinstall binary downloader, and npm package docs.
- `skills/` - packaged Hermes skill for safe MCP server setup and desktop-control procedure.
- `scripts/` - safety/contract checks and auxiliary Node schema tooling.
- `docs/` - managed repo docs and doc-sync metadata.

## Key Directories
- `src/windowing/backends/` - GNOME, KWin, Hyprland, i3, and COSMIC backend implementations.
- `gnome-shell-extension/computer-use-linux@avifenesh.dev/` - DBus service that lists and activates
  GNOME Shell windows.
- `npm/bin/` - executable wrapper exposed by the npm package.
- `scripts/zod-check/` - Node-based schema check helper workspace.
- `skills/computer-use-linux/` - agent-facing usage guide for Hermes.
- `target/` - local Cargo build output; generated and not a source-of-truth directory.

## Key Files
- `README.md` - product overview, install paths, support matrix, MCP tool list, and safety contract.
- `AGENTS.md` - repo guardrails for live desktop-control software.
- `CLAUDE.md` - symlink to `AGENTS.md`.
- `Cargo.toml` and `Cargo.lock` - Rust crate metadata, binary declarations, and locked dependency graph.
- `package.json` - npm wrapper package metadata and packaging scripts.
- `src/main.rs` - CLI command dispatcher and MCP entrypoint.
- `src/server.rs` - MCP tool definitions, annotations, schemas, and handler logic.
- `src/diagnostics.rs` - `doctor` readiness/capability report and setup reporting.
- `src/screenshot.rs` - screenshot backend chain, payload caps, compression, and coordinate metadata.
- `src/remote_desktop.rs` - portal and key/text input paths.
- `src/abs_pointer.rs` - uinput absolute-pointer backend.
- `src/atspi_tree.rs` - accessibility tree, action, and value-setting implementation.
- `src/windows.rs` and `src/windowing/registry.rs` - window target resolution and backend selection.
- `src/bin/computer-use-linux-cosmic.rs` - COSMIC helper binary.
- `gnome-shell-extension/computer-use-linux@avifenesh.dev/extension.js` - GNOME Shell DBus window-control service.
- `npm/install.js` - release binary download and sha256 verification.
- `npm/bin/computer-use-linux.js` - Node wrapper that launches the bundled or overridden binary.
- `scripts/mcp_safety_check.py` - MCP contract and safety smoke test.
- `install.sh` - clone-based installer for system dependencies, binaries, ydotoold, AT-SPI, and GNOME extension setup.

## Change Hotspots
- MCP tool changes usually touch `src/server.rs`, the backing module in `src/`, README tool docs,
  `skills/computer-use-linux/SKILL.md`, and `scripts/mcp_safety_check.py`.
- Desktop readiness changes should review `src/diagnostics.rs`, `README.md`, and the relevant
  backend modules.
- Window targeting changes should review `src/windows.rs`, `src/windowing/registry.rs`,
  `src/windowing/backends/*`, and the GNOME extension or COSMIC helper when applicable.
- Screenshot behavior changes should review `src/screenshot.rs`, `src/server.rs`, README payload
  guidance, and any tests around image sizing or metadata.
- Input behavior changes should review `src/remote_desktop.rs`, `src/abs_pointer.rs`,
  `src/server.rs`, and terminal/window targeting helpers.
- Release/package changes should keep `Cargo.toml`, `Cargo.lock`, `package.json`, `npm/`,
  `install.sh`, `README.md`, `npm/README.md`, and `skills/computer-use-linux/SKILL.md` aligned.

## Deferred or Unclear Areas
- Real desktop behavior depends on the active compositor, portals, AT-SPI, and `ydotoold`; automated
  checks cannot fully prove every session backend.
- `target/` is generated build output and should not be used to infer source ownership.
