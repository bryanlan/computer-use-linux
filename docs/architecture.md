---
doc_type: architecture
managed_by: sync-repo-docs
current_through_commit: c7e4fda72a113994cf4eccf524826bb98512e507
current_through_date: 2026-07-06T00:30:11-04:00
---

# Architecture

## System Overview
`computer-use-linux` is a Rust MCP server and CLI for observing and controlling a live Linux
desktop. It exposes AT-SPI accessibility trees, bounded screenshots, compositor-aware window
listing/focusing, and input synthesis through both an MCP stdio server and a local debugging CLI.
The repository also ships a Node/npm wrapper, a GNOME Shell extension for exact GNOME Wayland
window targeting, a COSMIC helper binary, and an agent-facing Hermes skill.

First-class runtime surfaces:
- `src/main.rs` is the Rust CLI entrypoint for `mcp`, `doctor`, `setup`, `setup-window-targeting`,
  app/window/state/screenshot commands, and hidden development probes.
- `src/server.rs` is the MCP tool surface. It defines the tool handlers, input/output schemas, and
  MCP safety annotations that distinguish read-only observation from mutating desktop actions.
- `src/diagnostics.rs` owns the structured `doctor` report and readiness/capability model used by
  both humans and MCP hosts.
- `src/windowing/` owns compositor-specific window backends for GNOME, KWin, Hyprland, i3, and
  COSMIC.
- `npm/` packages prebuilt binaries and exposes the npm wrapper.
- `gnome-shell-extension/computer-use-linux@avifenesh.dev/` exposes a GNOME Shell DBus service for
  exact window list/focus behavior when native introspection is locked down.

## Main Components
- `src/atspi_tree.rs` reads accessible applications, snapshots trees, performs AT-SPI actions, and
  sets element values.
- `src/screenshot.rs` captures screenshots through GNOME Shell, portal, or `gnome-screenshot`
  fallback, then downscales/compresses payloads within MCP-safe byte and dimension caps.
- `src/remote_desktop.rs` implements portal pointer/keyboard sessions and key/text input support.
- `src/abs_pointer.rs` provides the uinput absolute-pointer backend used for coordinate actions.
- `src/windows.rs` and `src/windowing/` resolve, list, and focus desktop windows with per-backend
  failure explanations.
- `src/cosmic_helper.rs` and `src/bin/computer-use-linux-cosmic.rs` support COSMIC Wayland window
  control.
- `install.sh` installs system packages, Rust binaries, `ydotoold`, GNOME AT-SPI settings, and the
  bundled GNOME extension for clone-based installs.
- `npm/install.js` downloads and verifies release binaries, including the COSMIC helper, while
  `npm/bin/computer-use-linux.js` launches the packaged binary and sets helper env vars.
- `scripts/mcp_safety_check.py` is the contract smoke test for MCP tools, annotations, schemas, and
  prompt-injection-sensitive tool descriptions.

## Data Flow
For MCP use, a host starts `computer-use-linux mcp`; `serve_mcp` creates the `ComputerUseLinux`
server and exposes annotated tools through the `rmcp` stdio transport. Read-only tools call the
diagnostics, windowing, AT-SPI, and screenshot modules and return JSON or bounded image payloads.
Mutating tools first resolve a target through window, element, or coordinate selectors, then invoke
portal input, `ydotool`, uinput absolute-pointer actions, or AT-SPI semantic actions.

The CLI uses the same modules directly. `doctor` hydrates desktop session environment, probes
portals, AT-SPI, window backends, and input backends, and returns a single readiness JSON document.
`setup` and `setup-window-targeting` perform local configuration changes for accessibility and the
GNOME extension, while `apps`, `state`, `windows`, and `screenshot` provide debug readbacks.

Packaging flows keep the main binary and COSMIC helper paired. Cargo builds both binaries from
`Cargo.toml`; the npm postinstall downloads matching release assets and verifies `.sha256` files;
the wrapper sets `COMPUTER_USE_LINUX_COSMIC_HELPER` when the helper is bundled.

## External Integrations
- AT-SPI accessibility bus for app trees and semantic actions.
- `org.freedesktop.portal.RemoteDesktop`, screenshot/screencast portals, GNOME Shell DBus
  screenshot APIs, and `gnome-screenshot` fallback.
- `ydotoold` and uinput for keyboard/pointer synthesis.
- GNOME Shell extension DBus service, GNOME Shell Introspect, KWin scripting, `hyprctl`, `i3-msg`,
  and the COSMIC helper for window listing/focus.
- crates.io, npm, and GitHub release assets for distribution.
- MCP hosts such as Codex Desktop, Claude Code/Desktop, Hermes Agent, or other stdio MCP clients.

## Key Decisions
- Preserve MCP tool annotations whenever tool behavior changes; callers rely on the
  read-only/destructive/idempotent/open-world hints.
- Keep Wayland-first behavior and report backend failures explicitly instead of falling back to
  vague state.
- Screenshot responses must stay bounded and include coordinate metadata so callers can map
  downscaled previews back to desktop coordinates.
- `doctor` is the canonical structured readiness surface and should remain machine-readable.
- The COSMIC helper is part of the runtime package and must ship with install-script, Cargo, and npm
  flows.
- Managed docs are synchronized against the live tree and finalized to the current git `HEAD`;
  commit dossier files are navigation context, not source of truth.

## Operational Notes
Use `docs/agent_docs/running_tests.md` for safe verification commands. This repo controls the live
desktop: commands such as `computer-use-linux setup`, `setup-window-targeting`, MCP mutating tools,
and input/screenshot operations can reveal or change local GUI state and should not be treated as
ordinary tests.
