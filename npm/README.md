# computer-use-linux

NPM wrapper for the `computer-use-linux` MCP server. Current release:
[`@agent-sh/computer-use-linux@0.2.1`](https://www.npmjs.com/package/@agent-sh/computer-use-linux/v/0.2.1).

Security note: this server can control the local Linux desktop. Tools such as
`click`, `type_text`, `press_key`, `perform_action`, and `set_value` are
mutating and can change real application state. The MCP tool list includes
`ToolAnnotations` so hosts can distinguish read-only observation from mutating
desktop actions.

```bash
npm install -g @agent-sh/computer-use-linux@0.2.1
computer-use-linux doctor
hermes mcp add computer-use-linux --command computer-use-linux --args mcp
hermes mcp test computer-use-linux
```

The package downloads the matching Linux x86_64 or aarch64 binary from the
GitHub release for this package version and verifies the `.sha256` asset before
installing it. It also installs the matching `computer-use-linux-cosmic` helper
used for COSMIC desktop window targeting.

If you already built or installed the binary yourself, set
`COMPUTER_USE_LINUX_BIN=/path/to/computer-use-linux` to make the wrapper use
that executable instead.
