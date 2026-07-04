---
doc_type: running_tests
managed_by: sync-repo-docs
current_through_commit: 4970cf7d2b15a2005f0bd8d453babad1cc66e6a4
current_through_date: 2026-07-03T00:36:58-04:00
---

# Running Tests

## Primary Commands
- `cargo fmt --all -- --check`
- `cargo check --locked --all-targets`
- `cargo clippy --locked --all-targets -- -D warnings`
- `cargo test --locked --no-fail-fast`
- `scripts/mcp_safety_check.py`
- `agnix .` - upstream full gate when `agnix` is installed; the current local environment may not
  provide this command.

## Targeted Test Patterns
- Fast compile check: `cargo check --locked`
- Rust unit/integration tests: `cargo test --locked`
- MCP safety contract only: `scripts/mcp_safety_check.py`
- npm wrapper syntax/packaging: `node --check npm/install.js`, `node --check npm/bin/computer-use-linux.js`, `npm pack --dry-run`
- Release dry-runs: `cargo publish --dry-run --locked` and `npm pack --dry-run`
- Desktop readiness readback: `cargo run -- doctor` or `computer-use-linux doctor`; this is an
  environment probe, not a hermetic unit test.

## Environment and Fixtures
- Rust checks use the locked Cargo dependency graph in `Cargo.lock`.
- The MCP safety check expects a built `target/debug/computer-use-linux` binary or an explicitly
  provided binary path, because it starts the MCP server and validates `tools/list` output.
- `computer-use-linux doctor` depends on the current Linux desktop session, session DBus,
  portals, AT-SPI, compositor APIs, `ydotoold`, and uinput permissions.
- npm postinstall downloads GitHub release assets unless
  `COMPUTER_USE_LINUX_SKIP_DOWNLOAD=1`, `COMPUTER_USE_LINUX_LOCAL_BINARY`, or
  `COMPUTER_USE_LINUX_DOWNLOAD_BASE` is set.
- `agnix .` requires the external `agnix` CLI; it is not installed in every development
  environment.

## Edge Cases
- This is live desktop-control software. Do not run MCP mutating tools, `setup`, or
  `setup-window-targeting` as generic tests unless changing that behavior and prepared for local
  desktop configuration changes.
- Screenshot and window tests can depend on foreground/background process context, portal prompts,
  and compositor-specific permissions.
- On GNOME Wayland, the extension backend may require logout/login after installation before exact
  window targeting works.
- COSMIC packaging changes must keep the helper binary next to the main binary or set
  `COMPUTER_USE_LINUX_COSMIC_HELPER`.

## Known Gaps
- Parser and contract tests cannot replace manual validation on GNOME, KWin, Hyprland, i3, COSMIC,
  and generic X11/Wayland sessions.
- The support matrix is partly manual; include the desktop/session tested when changing compositor,
  portal, screenshot, accessibility, or input behavior.
