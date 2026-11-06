# samsara-cli

`samsara` is the command-line client for the Samsara platform. It logs in once
in the browser, as the same user with the same permissions that user has in
the dashboard, and then runs platform calls from a shell: one organization at
a time, printing a table for a human or JSON for a pipeline. It is a second
OAuth client of the authorization server behind Samsara MCP, so the tools
available in an MCP session are reachable from a terminal and from a script
without a long-lived API key.

## Install

Downloads are published on the [Releases](https://github.com/samsarahq/samsara-cli/releases) page. Per-package channels arrive with the first release:

- macOS (Homebrew): `brew install samsarahq/tap/samsara` — coming soon
- macOS/Linux (install script) — coming soon
- Windows (winget): `winget install Samsara.CLI` — coming soon

## Sign in

```bash
samsara auth login          # sign in through the browser
samsara auth status         # who you are signed in as, and from where
samsara org list            # the organizations your sign-ins cover
samsara org use 12345       # the organization bare commands act on
```

A sign-in covers one organization on one Samsara cloud and records a profile
named `<region>-<organization>`. Signing in to a second organization, or a
second cloud, adds a sign-in beside the first; `--org`, `--profile` and
`--region` pick between them.

## Commands

```bash
samsara auth login                       # sign in through the browser
samsara auth login --no-browser --callback-port 8976   # on a host with no browser
samsara auth status                      # who you are signed in as, and from where
samsara auth refresh                     # mint a new access token now
samsara auth logout                      # remove the selected sign-in from this machine
samsara org list                         # the organizations your sign-ins cover
samsara org use 12345                    # the organization bare commands act on
samsara profile list                     # the stored sign-ins --profile can name
samsara mcp                              # bridge a stdio MCP client to Samsara MCP
samsara mcp --org 12345                  # the same, pinned to one organization
samsara mcp tools                        # the tools this sign-in reaches, by domain
samsara mcp call drivers/list --org 12345 --jq '.data[].name'   # call one tool from a shell
samsara version                          # the build in a bug report
```

A tool call that writes, on a sign-in covering more than one organization, has
to name it with `--org`. A read-only tool does not, and a one-organization
sign-in never does.

## Samsara MCP from a stdio client

`samsara mcp` speaks MCP over standard input and output and relays it to the
Samsara MCP server for the configured region, signed in as `samsara auth
status` reports. It is for clients that spawn a local process; a client that
speaks HTTP with OAuth connects to the regional endpoint directly and needs
no bridge.

Claude Code:

```bash
claude mcp add samsara -- samsara mcp
```

Cursor, in `.cursor/mcp.json` for one project or `~/.cursor/mcp.json` for all
of them:

```json
{
  "mcpServers": {
    "samsara": { "command": "samsara", "args": ["mcp"] }
  }
}
```

Requests and answers pass through unchanged, so the server owns the tool
surface: a tool it adds appears without a new CLI, and a domain granted later
arrives as a `tools/list_changed` without a restart.

## Documentation

Full documentation is coming to developers.samsara.com.

## License

MIT — see [LICENSE](LICENSE). The source of the CLI is private; this
repository distributes the built binaries.
