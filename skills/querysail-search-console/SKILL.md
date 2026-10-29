---
name: querysail-search-console
description: Answer questions about a site's Google Search traffic, rankings, indexing and sitemaps with the QuerySail Google Search Console tools. Use when the user asks about clicks, impressions, CTR, average position, top queries or pages, traffic drops, whether a page is indexed, canonicals, or sitemap errors.
---

# Search Console analysis with QuerySail

The QuerySail MCP server (`querysail-gsc`) gives read-only access to the user's own Google Search Console data.

## Setup when the tools are missing

If no `querysail-gsc` tools (such as `list_properties`) are available, the server is not connected. Ask the user before you change their configuration, then add it for their client:

- Claude Code: run `claude mcp add --transport http --scope user querysail-gsc https://mcp.querysail.com/gsc/mcp`. The user then runs `/mcp`, chooses `querysail-gsc` and signs in.
- Codex: run `codex mcp add querysail-gsc --url https://mcp.querysail.com/gsc/mcp`, then `codex mcp login querysail-gsc`, which opens the sign-in page.
- Cursor: add `"querysail-gsc": { "url": "https://mcp.querysail.com/gsc/mcp" }` under `mcpServers` in `~/.cursor/mcp.json`. The user then connects it in Cursor's MCP settings and signs in.
- VS Code: add `"querysail-gsc": { "type": "http", "url": "https://mcp.querysail.com/gsc/mcp" }` under `servers` in `mcp.json`. The user signs in when VS Code asks.
- Other clients: follow https://querysail.com/google-search-console#connect.

The user signs in with a QuerySail account that has Google Search Console connected. If they have none, they can start the free trial at https://querysail.com. There is no API key. The tools may only appear in a new session.

## Workflow

1. Call `list_properties` first and use the exact `property_id` it returns (for example `sc-domain:example.com` or `https://www.example.com/`). Do not guess property IDs.
2. Pick the tool:
   - Totals, trends or breakdowns by query, page, country, device, date, hour or search appearance: `get_performance`.
   - "What changed", drops, gains, before/after: `compare_performance`.
   - "Is this page indexed", canonical, last crawl, noindex or robots blocking: `inspect_urls` (up to 20 URLs per call, all in the same property).
   - Sitemap status, errors or warnings: `list_sitemaps`.
3. Default to the last 28 days unless the user names a range.

## Reporting

- Always state the property, the exact date range and any filters used. Search Console dates are in Pacific Time and the latest 2–3 days can be incomplete.
- CTR is returned as a fraction; show it as a percentage.
- Query and page breakdowns contain only Google's top rows, so they may not add up to the totals. Say so when it matters.
- Report changes between periods as observed differences, not causes.
- URL inspection describes Google's last indexed version, not a live test.

## Limits

QuerySail is read-only. It cannot submit sitemaps, request indexing or change settings; tell the user to do that in Search Console. It has no keyword search volume. Google Analytics data comes from the separate `querysail-ga4` server.
