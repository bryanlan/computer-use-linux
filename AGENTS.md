# AGENTS.md

This repo is a Rust MCP server and CLI for controlling Linux desktops through AT-SPI, screenshots, compositor-aware window targeting, and input synthesis.

## Quick Rules
- Treat this as live desktop-control software: observation tools can expose local window contents, and mutating tools can change real application state.
- Preserve MCP safety annotations when adding or changing tools; callers rely on the read-only/destructive/open-world hints.
- Keep Wayland-first behavior intact and X11 paths best-effort; report backend failures explicitly instead of silently falling back to vague behavior.
- Update `README.md`, `npm/README.md`, and `skills/computer-use-linux/SKILL.md` when user-facing commands, install paths, or MCP tool contracts change.
- Include the desktop/session tested when changing compositor, portal, accessibility, screenshot, or input behavior.

## Build / Test / Verify
- Install: `cargo check --locked`
- Dev: `cargo run -- doctor`
- Test: `cargo test --locked`
- Verify: `cargo fmt --all -- --check && cargo check --locked --all-targets && cargo clippy --locked --all-targets -- -D warnings && cargo test --locked --no-fail-fast && scripts/mcp_safety_check.py && agnix .`

## Repo Map
- `src/main.rs` — CLI and MCP server entrypoint.
- `src/server.rs` — MCP tool surface and request handling.
- `src/diagnostics.rs` — `doctor` readiness reporting.
- `src/atspi_tree.rs`, `src/screenshot.rs`, `src/remote_desktop.rs`, and `src/abs_pointer.rs` — accessibility, screenshot, and input backends.
- `src/windowing/` — GNOME, KWin, Hyprland, i3, COSMIC, and registry/targeting code.
- `src/bin/computer-use-linux-cosmic.rs` — bundled COSMIC Wayland helper binary.
- `npm/` — Node wrapper, binary download/install script, and npm package documentation.
- `skills/computer-use-linux/` — agent-facing skill packaged with the repo.

## Repo-Specific Guardrails
- `computer-use-linux doctor` should remain a single structured JSON readiness report with clear blockers and recommended next steps.
- Screenshot payloads must stay bounded before returning to MCP hosts; preserve metadata needed to map downscaled images back to desktop coordinates.
- Window targeting should explain which backend won or why each backend failed across GNOME, KWin, Hyprland, i3, and COSMIC paths.
- `computer-use-linux-cosmic` must continue to ship alongside the main binary for `./install.sh`, `cargo install`, and npm wrapper installs.
- Release/package changes should also run `cargo publish --dry-run --locked` and `npm pack --dry-run`.

## Additional References
- `README.md` — product overview, support matrix, install paths, MCP tools, and safety contract.
- `CONTRIBUTING.md` — CI-equivalent verification commands and PR expectations.
- `SECURITY.md` — vulnerability reporting policy.
- `npm/README.md` — npm wrapper behavior and packaging notes.
