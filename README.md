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

## Publishing to the MCP Registry

[`server.json`](./server.json) is the official registry manifest for `io.roundhouseai/roundhouse` (schema `2025-12-11`). Versions are immutable, so change the `version` field before each publish. `0.1.0` is the live entry and has no `repository`; `0.1.1` adds it. Merging this repo does not publish anything.

The workflow [Publish to MCP Registry](./.github/workflows/publish-mcp.yml) runs only from **Actions → Run workflow**, or when a `v*` tag is pushed (for example `v0.1.1`). It does not run on branch pushes or pull requests.

1. Add a repository Actions secret named `MCP_REGISTRY_PRIVATE_KEY` (Settings → Secrets and variables → Actions). The value is the hex Ed25519 private key for `mcp-publisher login dns --private-key`, not a PEM file, and it must not be committed. It has to match the apex TXT record on `roundhouseai.io` (`v=MCPv1; k=ed25519; p=5zy/BpIBA8nWAIaVZLjIPBH23VZdVFvJSRP1U0Bb0hY=`). Confirm with `dig TXT roundhouseai.io +short`.
2. After this change is on `main`, open **Actions → Publish to MCP Registry → Run workflow** and select `main`. Alternatively, tag the merged commit and push the tag (`git tag v0.1.1 && git push origin v0.1.1`). Push the tag only when you want that publish to happen.

The workflow logs in with `mcp-publisher login dns --domain roundhouseai.io --private-key <that secret>`, validates `server.json`, then runs `mcp-publisher publish`.

To rotate the key (required if the private key for the current TXT record is gone), use OpenSSL 3 or newer. On macOS, the system `openssl` is LibreSSL and cannot generate Ed25519 keys; use Homebrew's `openssl@3`.

```bash
openssl genpkey -algorithm Ed25519 -out key.pem
PUBLIC_KEY="$(openssl pkey -in key.pem -pubout -outform DER | tail -c 32 | base64)"
echo "roundhouseai.io. IN TXT \"v=MCPv1; k=ed25519; p=${PUBLIC_KEY}\""
PRIVATE_KEY="$(openssl pkey -in key.pem -noout -text | grep -A3 'priv:' | tail -n +2 | tr -d ' :\n')"
printf '%s\n' "$PRIVATE_KEY"
```

Copy that hex into `MCP_REGISTRY_PRIVATE_KEY`, then delete `key.pem`. Replace the apex TXT record with the new value and delete the previous `v=MCPv1` record in the same change. The record has to be on `roundhouseai.io` itself, not under `_mcp-auth` or `_mcp-registry`. A stale MCPv1 record left on the apex is tried first and verification fails. Wait until `dig TXT roundhouseai.io +short` shows the new record and not the old one, update the secret, then run the workflow.

## License

[MIT](./LICENSE)
