# VeriTeknik MCP Server

Manage your VeriTeknik servers, DNS, domains, backups, invoices and support tickets from your AI assistant.

VeriTeknik is a managed hosting and infrastructure provider based in Turkey.
This is a hosted, remote MCP server: there is nothing to install or run locally.
This repository holds the public description of the server; the server itself runs at VeriTeknik.

| | |
|---|---|
| Endpoint | `https://veriteknik.com/api/mcp` |
| Transport | Streamable HTTP |
| Authentication | OAuth 2.1 with PKCE (Dynamic Client Registration and Client ID Metadata Documents) |
| Registry name | `com.veriteknik/mcp` |
| Documentation | https://veriteknik.com/docs/en/mcp/ |
| Listed in | [Claude connector directory](https://claude.ai/directory/veriteknik), [GitHub MCP Registry](https://github.com/mcp/com.veriteknik/mcp) |

## What you can ask

- "List my VeriTeknik servers - which ones are running?"
- "What were the disk and memory figures for web-01 this week?"
- "Is MySQL running? Show me the last 200 lines of the nginx error log."
- "When does `example.com` expire, and what are its DNS records?"
- "Add a TXT record for my domain verification."
- "Do I have any unpaid invoices?"
- "Open a support ticket for this error and summarise the logs in it."

## Capabilities

The catalog holds 46 tools.
Every tool carries a title and `readOnlyHint` / `destructiveHint` / `openWorldHint` annotations, and a connection only sees the tools its permission envelope allows.

- **Servers (VPS and dedicated):** list and inspect servers, usage and bandwidth, uptime monitoring, snapshots, start / stop / restart, and order or delete a VPS from prepaid balance. Deleting a server needs your approval in the VeriTeknik panel, unless you created it as a disposable sandbox from the same connection.
- **Inside a server (read-only):** live health snapshot (load, memory, disks, services, containers, listening ports), log tails and search, directory listing, file reads and content search, backup inventory. Credential files (`.env`, private keys, password files) are never opened and output is masked.
- **Server tasks:** install a TLS certificate, publish a static site, run diagnostic commands, and (with a separate permission) run admin commands under the server's own safety policy. Each admin batch is shown to you in full first and runs only after you confirm it.
- **Domains and DNS:** list domains, check availability and pricing, register a domain from balance, read a zone, and add / update / delete DNS records.
- **Billing:** invoices, invoice lines, contracts, wallet balance and VPS cost estimates.
- **Support:** list and read tickets, open a ticket, reply to one.
- **Team:** list company members and roles.

## Setup

You need a VeriTeknik account linked to a company, with **Enable MCP access** turned on by a company owner or admin under **Settings > Agent Connections > AI Agent Access (MCP)**.

### VS Code / GitHub Copilot

Use **Install MCP server** on the [GitHub MCP Registry page](https://github.com/mcp/com.veriteknik/mcp), or add it by hand in `.vscode/mcp.json`:

```json
{
  "servers": {
    "veriteknik": {
      "type": "http",
      "url": "https://veriteknik.com/api/mcp"
    }
  }
}
```

The first agent-mode call asks you to sign in.

### Claude Code

```bash
claude mcp add --transport http veriteknik https://veriteknik.com/api/mcp
```

Then run `/mcp` in a session and sign in to `veriteknik`.

### Claude Desktop and claude.ai

Add VeriTeknik from the [Claude connector directory](https://claude.ai/directory/veriteknik), or search for "VeriTeknik" in Claude's connector directory, then connect and approve the VeriTeknik consent page.

You can also add it as a custom connector: **Settings > Connectors > Add custom connector**, and paste `https://veriteknik.com/api/mcp`.

### Cursor, Windsurf, Gemini CLI, Codex and others

Most clients accept this shape:

```json
{
  "mcpServers": {
    "veriteknik": {
      "url": "https://veriteknik.com/api/mcp"
    }
  }
}
```

Field names differ per client (Windsurf uses `serverUrl`, Gemini CLI uses `httpUrl`).
The documentation has a tab for each client: https://veriteknik.com/docs/en/mcp/#setup

## How access works

There are no static API keys.
Connecting, or your first tool call, opens the VeriTeknik consent page in the browser, where you draw the connection's envelope:

- **Company:** one connection acts for one company.
- **Permission groups:** read-only groups are on by default; anything that changes state, spends money or touches a server is off until you switch it on. You can never grant more than your own role holds.
- **Monthly spend cap:** 0 USD by default. Orders only ever draw on the company's existing prepaid balance; the connection never sees card details.
- **Lifetime:** 90 days, then the connection expires and asks for a fresh approval.

With two-factor authentication on, connecting and widening a connection ask for a fresh code.
You can narrow, widen or revoke a connection at any time from **Settings > Agent Connections > Connected Apps (MCP)**; changes apply to the very next call.

Every state-changing call carries a request id, so a retried request never creates a second ticket, snapshot or order.

## Support

Open a ticket from the VeriTeknik panel, or see https://veriteknik.com/docs/en/mcp/.
