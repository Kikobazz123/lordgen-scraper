# Lordgen AI Scraper

A web scraping project built on [Firecrawl](https://firecrawl.dev). Scrapes
are turned into structured data (JSON/CSV/markdown) for downstream use, e.g.
importing into Google Sheets.

## Tooling

- **Firecrawl** is the scraping engine. Quick per-tool reference (search,
  scrape, interact, parse, crawl, map, monitor, research, ask, docs-search):
  see [firecrawl-cheatsheet.md](firecrawl-cheatsheet.md).
- Full setup/usage skill: [.claude/skills/firecrawl/SKILL.md](.claude/skills/firecrawl/SKILL.md).
- MCP server `firecrawl` is registered globally (user scope) and authenticated
  via `claude mcp login firecrawl` — prefer it when loaded in-session. Falls
  back to direct REST calls (`FIRECRAWL_API_KEY` in `.env`) or the
  `firecrawl-cli` when MCP tools aren't available.

## Project layout

- `.env` — `FIRECRAWL_API_KEY`, gitignored
- `output/` — scrape results (CSV/markdown) ready for spreadsheet import
- `firecrawl-cheatsheet.md` — per-tool quick reference
- `.claude/skills/firecrawl/` — project-scoped Firecrawl skill

## Conventions

- Save scrape output to `output/` in a format suited to the destination —
  CSV for spreadsheet import, markdown for readable content dumps.
- When extracting structured fields from a site, define an explicit JSON
  schema for the scrape rather than relying on free-form extraction, so
  output columns are predictable.
- Flag placeholder/template content found on scraped sites (e.g. leftover
  boilerplate text or stock data) rather than treating it as real.
