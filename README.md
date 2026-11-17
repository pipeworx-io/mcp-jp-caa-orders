# @pipeworx/jp-caa-orders

Has the Japan Consumer Affairs Agency (消費者庁) announced an administrative
disposition against this company: a cease-and-desist order (措置命令) or surcharge
payment order (課徴金納付命令) under the Act against Unjustifiable Premiums and
Misleading Representations, or a disposition under the Specified Commercial
Transactions Act (door-to-door, mail-order, telephone-solicitation, multi-level,
continuing-service and goods-purchase businesses)?

Part of [Pipeworx](https://pipeworx.io) — an MCP gateway connecting AI agents to 2014+ live data sources. This is an independent, unofficial integration — not affiliated with, endorsed by, or published by the upstream provider.

## Tools

- `jp_caa_orders_search(name, law?, order_type?, limit?)` — exact or partial name
  match after Unicode folding and dropping corporate-form words. Each match gives
  the announcement date, law, order type, business type, the companies named, the
  announcement's title and a link to the agency's announcement page. Also returns
  `result` (`hit` / `no_hit`), `total_matches`, `notices_read`, `covers`,
  `unnamed_notices`, `omitted_non_company` and `data_as_of`.

## Auth

Keyless.

## Data sources

- Japan Consumer Affairs Agency (消費者庁), announcements of administrative
  dispositions under the two laws, from fiscal 2021 (April 2021) onward. Read from the
  publisher on request and held in the serving process for an hour (about 0.8 MB
  across the years). The agency publishes no table, so each disposition is read from the
  title of its announcement, which names the company. `data_as_of` is the day the
  lists were read; `covers.to` is the latest announcement.

## Behaviour worth knowing

- About 190 dispositions name a company in the title. Announcements whose title names no
  business (for example "two businesses") cannot be searched by name and are
  counted in `unnamed_notices`. Respondents that are not companies are not served
  and are counted in `omitted_non_company`.
- The agency's lists carry no corporate numbers, so there is no number search.
- Earlier years, and the agency's commitment-plan approvals and warnings, are not
  dispositions of this kind and are not read.
- The agency site answers its list pages with a redirect; it is followed.
- A list page without the expected markup, a year that parses to nothing or an
  HTTP failure is refused, so a source that is down is never read as "no order".

## Quick Start

Add to your MCP client (Claude Desktop, Cursor, Windsurf, etc.):

```json
{
  "mcpServers": {
    "jp-caa-orders": {
      "url": "https://gateway.pipeworx.io/jp-caa-orders/mcp"
    }
  }
}
```

### What this endpoint actually serves

`tools/list` at `https://gateway.pipeworx.io/jp-caa-orders/mcp` returns the tools in the table
above **plus the shared Pipeworx meta-tools** — `ask_pipeworx`,
`discover_tools`, `search_within`, `remember`/`recall` and the rest of the
gateway-wide set. So the tool count you see is larger than this table: a
single-pack endpoint currently lists roughly 30 shared tools alongside the
pack's own. The connection's `initialize` response states its exact scope, and
is the authoritative answer for a given day.

This is deliberate, not multiplexing by accident. The meta-tools are what let a
scoped connection answer a question this pack does not cover — via
`ask_pipeworx`, which routes across the whole catalog — without you adding a
second MCP server. There is currently no way to mount a pack endpoint without
them; if the extra schemas cost you more context than the routing is worth,
connect to the full gateway once rather than to several pack endpoints.

Or connect to the full Pipeworx gateway to get every pack's tools listed
directly, instead of just this one's:

```json
{
  "mcpServers": {
    "pipeworx": {
      "url": "https://gateway.pipeworx.io/mcp"
    }
  }
}
```

Both URLs reach the same gateway and the same 2014+ data sources. The
only difference is which pack's tools are listed **directly**; `ask_pipeworx`
reaches all of them from either one.

## No MCP client? Call it over HTTP

```bash
curl -X POST https://gateway.pipeworx.io/v1/tools/jp_caa_orders_search \
  -H 'Content-Type: application/json' \
  -d '{"name":"株式会社ジャパネットたかた"}'
```

No account needed for the first calls. Inspect any tool: `GET https://gateway.pipeworx.io/v1/tools/jp_caa_orders_search`. Find one: `POST https://gateway.pipeworx.io/v1/tools/search_packs` with `{"query":"..."}`.

## Run it from npm

`@pipeworx/mcp-jp-caa-orders` is a local stdio bridge to the Pipeworx gateway: your MCP client
starts it with `npx`, and it forwards every call to the same
`https://gateway.pipeworx.io/jp-caa-orders/mcp` endpoint shown above. It needs network access;
there is no offline mode.

```json
{
  "mcpServers": {
    "jp-caa-orders": {
      "command": "npx",
      "args": ["-y", "@pipeworx/mcp-jp-caa-orders"]
    }
  }
}
```

Without a key it calls anonymously, capped at 50 calls a day. A free account
raises that to 200 a day: get a key at https://pipeworx.io/signup and add
`"env": { "PIPEWORX_API_KEY": "<your key>" }` to the server entry.
The key is sent as a Bearer token, the same way a hosted connection sends it,
so calls are metered and billed exactly as they are there.

It lists **only** this pack's tools by default. Set `PIPEWORX_META_TOOLS=1` to
also list `ask_pipeworx` and the other gateway-wide tools.

Behind an HTTPS proxy: Node's built-in `fetch` does not read `HTTPS_PROXY` by
default. Newer Node releases honour it when `NODE_USE_ENV_PROXY=1` is set; check
your Node version's documentation.

## Using with ask_pipeworx

Instead of calling tools directly, you can ask questions in plain English —
this works on the pack endpoint above as well as on the full gateway:

```
ask_pipeworx({ question: "your question about Jp Caa Orders data" })
```

The gateway picks the right tool and fills the arguments automatically.

## More

- [Docs and guides](https://pipeworx.io/docs)
- [pipeworx.io](https://pipeworx.io)

## License

MIT
