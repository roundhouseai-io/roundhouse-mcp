# Roundhouse MCP

Remote [MCP](https://modelcontextprotocol.io) server for **read-only analytics** on [x402](https://www.x402.org/) agent payments and [ERC-8004](https://eips.ethereum.org/EIPS/eip-8004) agent identity.

- **Endpoint:** `https://api.roundhouseai.io/v0/mcp` (streamable HTTP)
- **Auth:** OAuth 2.1 (dynamic client registration) or API key (`x-api-key` / `Authorization: Bearer rh_live_…`)
- **Site:** [roundhouseai.io](https://roundhouseai.io) · **Docs:** [MCP docs](https://roundhouseai.io/docs/api/mcp) · **llms.txt:** [roundhouseai.io/llms.txt](https://roundhouseai.io/llms.txt)

Roundhouse AI is a PLC-listed company based in Singapore. The server never moves funds, signs transactions, or holds keys. Catalog and pricing tools are open; settlement, wallet, and agent tools need a free account or an API key.

This public repo holds connect instructions, [`server.json`](./server.json) (official MCP Registry shape), and install notes for hosts. The product source lives elsewhere.

## Quick install

### Claude Code / Claude Desktop

```bash
claude mcp add --transport http roundhouse https://api.roundhouseai.io/v0/mcp
```

With an API key (from [Connections](https://roundhouseai.io/dashboard/connections)):

```bash
claude mcp add --transport http roundhouse https://api.roundhouseai.io/v0/mcp \
  --header "x-api-key: rh_live_…"
```

Omit the header to sign in with OAuth when the host supports it.

### Cursor

Add to MCP settings (Cursor Settings → MCP), or merge into your MCP config:

```json
{
  "mcpServers": {
    "roundhouse": {
      "url": "https://api.roundhouseai.io/v0/mcp"
    }
  }
}
```

With an API key:

```json
{
  "mcpServers": {
    "roundhouse": {
      "url": "https://api.roundhouseai.io/v0/mcp",
      "headers": {
        "x-api-key": "rh_live_…"
      }
    }
  }
}
```

Replace `rh_live_…` with a key from the [dashboard](https://roundhouseai.io/dashboard/connections). If your host prompts for OAuth on a 401, you can skip the key.

### Other MCP hosts (streamable HTTP)

Point the host at:

| Field | Value |
|--------|--------|
| Transport | Streamable HTTP |
| URL | `https://api.roundhouseai.io/v0/mcp` |
| Auth | OAuth 2.1 (DCR) **or** header `x-api-key: rh_live_…` (also `Authorization: Bearer rh_live_…`) |

See [`llms-install.md`](./llms-install.md) for a machine-oriented install brief, and [`server.json`](./server.json) for the registry manifest.

## What you get

17 read-only tools covering:

- Latest settlements and payer→payee flows
- EVM / Solana wallets as agents or merchants
- ERC-8004 agents, registry reputation, and feedback
- Facilitators and payment graphs
- Catalog of third-party x402 paid APIs (price comparison)
- Read-only SQL over the settlement index

Also: CLI [`@roundhouseai/x402`](https://www.npmjs.com/package/@roundhouseai/x402) (`rh-x402`), open catalog HTTP APIs, and [llms.txt](https://roundhouseai.io/llms.txt).

## Links

- [Website](https://roundhouseai.io)
- [MCP documentation](https://roundhouseai.io/docs/api/mcp)
- [API documentation](https://roundhouseai.io/docs/api)
- [x402 API catalog](https://roundhouseai.io/services)
- [ERC-8004 agents](https://roundhouseai.io/agents)
- [Privacy](https://roundhouseai.io/privacy) · [Terms](https://roundhouseai.io/terms)

## License

[MIT](./LICENSE)
