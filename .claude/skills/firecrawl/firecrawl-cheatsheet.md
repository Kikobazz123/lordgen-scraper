# Firecrawl Cheat Sheet

Quick reference for every Firecrawl tool: what it does and when to reach for it.
Full skill docs: [`.claude/skills/firecrawl/SKILL.md`](.claude/skills/firecrawl/SKILL.md)

## How this project is set up

- **MCP server**: `firecrawl` registered globally (user scope) via
  `claude mcp add --transport http firecrawl https://mcp.firecrawl.dev/v2/mcp-oauth --scope user`,
  authenticated with `claude mcp login firecrawl`. Prefer MCP tools when they're
  loaded in the session.
- **REST fallback**: `.env` holds `FIRECRAWL_API_KEY` for direct calls to
  `https://api.firecrawl.dev/v2/...` when MCP tools aren't available in a
  given session (e.g. a session started before the MCP server was registered).
- **CLI fallback**: `npx -y firecrawl-cli@latest <command>` — needs Node.js
  (installed on this machine) on PATH.

All three surfaces (MCP, REST, CLI) expose the same underlying operations —
pick whichever is actually loaded/working in the moment.

## Tool reference

| Tool | What it does | When to use it |
|---|---|---|
| **search** | Web search that returns results, optionally with full page content | Discovery — you have a topic/question but no URL yet. Always start here when you don't know the target page. |
| **scrape** | Extracts clean markdown/HTML/JSON from a single known URL (including public PDFs, DOCX, etc.) | You already have a URL and want its content. Default tool once discovery is done. |
| **interact** | Drives a live browser — clicks, form fills, navigation, login | Plain scrape doesn't get the data because the page needs a click, a login, pagination, or JS-rendered state to reach it. |
| **parse** | Converts a **local or non-public** file (PDF, DOCX, DOC, ODT, RTF, XLSX, XLS, HTML; ≤50MB) to markdown/JSON/summary | The source is a file on disk or behind auth, not a public URL. Use `-S` for an AI summary, `-Q` to ask a question of the doc. |
| **crawl** | Bulk-extracts an entire site or section of one | You need many/all pages under a domain, not just one URL. |
| **map** | Discovers URLs on a site without extracting content | You need a sitemap/URL list first, before deciding what to scrape. |
| **monitor** | Sets up a recurring check (cron or natural language, e.g. "every 30 minutes") that diffs a page/crawl/search over time and can notify via webhook, email, or Slack | The request implies recurrence — "alert me when X changes", "track this page" — rather than a one-time read. Cheaper and more correct than polling with repeated scrapes. |
| **research** (search-papers, inspect-paper, read-paper, related-papers, search-github) | Searches a scientific-paper index (metadata, full-text passages, citations) and GitHub issues/PRs/READMEs | Academic/engineering research questions — not general web content. |
| **ask** | AI support agent that diagnoses a failing Firecrawl call from the job's `jobId` | Any Firecrawl call fails or returns unexpected output. Pass the `jobId` instead of guessing at the fix. |
| **docs-search** | Answers "how does Firecrawl handle X?" questions grounded in official docs, with citations | You need to know Firecrawl's own behavior/limits/params, not scrape someone else's site. |

## Default decision flow

1. No URL yet? -> **search**
2. Have a URL, need its content? -> **scrape**
3. Scrape isn't enough (needs clicks/login/JS)? -> **interact**
4. Source is a local file, not a URL? -> **parse**
5. Need many pages from one site? -> **crawl** (or **map** first if you just need the URL list)
6. Need to know about changes over time? -> **monitor** (not repeated one-off scrapes)
7. Something failed? -> **ask** with the `jobId`
8. Question about Firecrawl itself? -> **docs-search**

## REST endpoints (fallback path used in this project)

Base URL: `https://api.firecrawl.dev/v2` · Auth: `Authorization: Bearer <FIRECRAWL_API_KEY>`

| Endpoint | Maps to tool |
|---|---|
| `POST /search` | search |
| `POST /scrape` | scrape |
| `POST /interact` | interact |
| `POST /parse` | parse (multipart upload) |
| `POST /monitor`, `GET /monitor`, `GET /monitor/{id}/checks` | monitor |
| `GET /search/research/papers`, `.../{id}`, `.../{id}/similar`, `GET /search/research/github` | research |
| `POST /support/ask` | ask |
| `POST /support/docs-search` | docs-search |

`crawl` and `map` are CLI/MCP/SDK operations without a bare REST example
documented here — see https://docs.firecrawl.dev for their endpoints.
