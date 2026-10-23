<p align="center">
  <img src="icon-192.png" width="96" height="96" alt="Diavgis">
</p>

<h1 align="center">Diavgis MCP Server</h1>

<p align="center">
  <strong>Greek public procurement, queryable by AI agents.</strong><br>
  2.18M acts · 88k entities · 255k chains · tenders, awards, contracts and payments linked into one process.
</p>

<p align="center">
  <a href="https://diavgis.gr">diavgis.gr</a> ·
  <a href="https://api.diavgis.gr/ocds">OCDS 1.1 API</a> ·
  <a href="https://diavgis.gr/llms.txt">llms.txt</a>
</p>

---

## What this is

A remote [Model Context Protocol](https://modelcontextprotocol.io) server that lets an AI agent query Greek
public-procurement records directly — no scraping, no PDF parsing, no local setup.

The data comes from three official sources, **cross-linked**:

| Source | What it is |
|---|---|
| **Διαύγεια** (Diavgeia) | the Greek state transparency portal — every public decision must be published there |
| **ΚΗΜΔΗΣ** (KIMDIS) | the central electronic registry of public contracts |
| **TED** | Tenders Electronic Daily, the EU-wide procurement journal |

Records that belong to the same procurement are joined into a **chain**, so a tender, the award that
followed it, the signed contract and the payments made against it can be read as one story rather than
four unrelated documents. That linkage is the reason this server exists.

## Endpoint

```
https://mcp.diavgis.gr/mcp
```

Streamable HTTP. Supported protocol revisions: `2026-07-28`, `2025-11-25`, `2025-06-18`, `2025-03-26`,
`2024-11-05`.

Health check: <https://mcp.diavgis.gr/health>

## Quick start

Add to your MCP client configuration:

```json
{
  "mcpServers": {
    "diavgis": {
      "type": "http",
      "url": "https://mcp.diavgis.gr/mcp"
    }
  }
}
```

To raise rate limits, send an API key as a bearer token. Keys and their limits are at
<https://diavgis.gr/pricing>.

```json
{
  "mcpServers": {
    "diavgis": {
      "type": "http",
      "url": "https://mcp.diavgis.gr/mcp",
      "headers": { "Authorization": "Bearer YOUR_KEY" }
    }
  }
}
```

## Tools

| Tool | What it answers |
|---|---|
| `find_entity` | Resolve a buyer or supplier **name** to its ΑΦΜ — the first call when you only have a name |
| `search_tenders` | Search acts across all three sources — by text, buyer, supplier, CPV, region, amount, date |
| `expiring_contracts` | Contracts running **now** that expire within 0, 7, 30 or 180 days |
| `get_act` | Full detail of one act by Διαύγεια ΑΔΑ or ΚΗΜΔΗΣ ΑΔΑΜ |
| `get_entity` | Profile of a company or public body by 9-digit ΑΦΜ — name, role, spend/receipts |
| `get_chain` | A reconstructed procurement lifecycle: the linked sequence tender → award → contract → payment |
| `rank_entities` | Top buyers or suppliers, by spend or by count |
| `spend_by_cpv` | Committed value by CPV division, optionally for one year |
| `entity_counterparties` | Who an entity actually transacts with — a buyer's top suppliers, or the reverse |
| `get_quota` | Your plan, calls used and remaining, and the rate limits — free, not counted against the quota |

The authoritative list is always the server itself:

```bash
curl -s https://mcp.diavgis.gr/mcp \
  -H 'content-type: application/json' \
  -H 'MCP-Protocol-Version: 2026-07-28' \
  -d '{"jsonrpc":"2.0","id":1,"method":"tools/list"}'
```

## Other ways in

The same data is available without MCP:

- **OCDS 1.1** — <https://api.diavgis.gr/ocds> · release packages, keyless, [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/)
- **REST** — documented at <https://diavgis.gr/docs>
- **Markdown / llms.txt** — every public page is available as clean Markdown for LLM consumption
- **Web** — <https://diavgis.gr>

## Notes on the data

- Counterparties that are **natural persons are redacted at source** under EU and Greek data-protection
  law, and carry no supplier identity.
- Greek sources publish **well after** the issue date. The OCDS endpoint states its own settled-through
  date; treat anything more recent as still filling.
- Amounts use a gross basis where the source publishes only one VAT-inclusive figure. The OCDS endpoint
  documents this per field.
- Methodology: <https://diavgis.gr/methodology>

## Licence

Documentation in this repository: [MIT](LICENSE).
The **data** served by the API is licensed [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/) —
attribute to *Diavgis* with a link to <https://diavgis.gr>.
