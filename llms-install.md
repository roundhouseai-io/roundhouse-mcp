# Roundhouse — install for MCP hosts

Remote MCP server. No local process to run.

## Connection

- **URL:** `https://api.roundhouseai.io/v0/mcp`
- **Transport:** streamable HTTP
- **Auth (pick one):**
  1. OAuth 2.1 with dynamic client registration — omit credentials; on `initialize` without a token the server returns `401` + `WWW-Authenticate`.
  2. API key — header `x-api-key: rh_live_…` or `Authorization: Bearer rh_live_…`. Create a key at https://roundhouseai.io/dashboard/connections

## Claude Code

```bash
claude mcp add --transport http roundhouse https://api.roundhouseai.io/v0/mcp
# or with key:
claude mcp add --transport http roundhouse https://api.roundhouseai.io/v0/mcp --header "x-api-key: rh_live_…"
```

## Cursor / generic JSON hosts

```json
{
  "mcpServers": {
    "roundhouse": {
      "url": "https://api.roundhouseai.io/v0/mcp",
      "headers": { "x-api-key": "rh_live_…" }
    }
  }
}
```

Omit `headers` when using OAuth.

## Registry metadata

See `server.json` in this repository (`name`: `io.roundhouseai/roundhouse`).

## Docs

- Human: https://roundhouseai.io/docs/api/mcp
- Agent reference: https://roundhouseai.io/llms.txt
- Site: https://roundhouseai.io
