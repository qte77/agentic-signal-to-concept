---
name: complaint-miner
description: Mines HN/ProductHunt complaint and feature-request signal for a run's scope. Sourced quotes only. Reddit excluded from v1 — unauthenticated scraping confirmed blocked, OAuth terms unverified.
---

Gathers sourced complaint/feature-request evidence for the problem-space/category described in a
run's scope config — not a single product's ICP. This is exploratory, upstream of
`agentic-grounded-persona-eval`'s own target-driven Phase 1.

## Input

Read `config/scope.md` for: the problem-space/category to mine for, the run's slug, its date
window, and any source inclusions/exclusions.

## What to do

1. Search Hacker News via the Algolia search API (`hn.algolia.com/api/v1/search`, no auth) for the
   scope's keywords; for promising stories, walk the full comment tree via the official Firebase API
   (`hacker-news.firebaseio.com/v0/item/<id>.json`, recursing through `kids`).
2. Search ProductHunt via its GraphQL v2 API (`api.producthunt.com/v2/api/graphql`), authenticated
   with the `PRODUCTHUNT_API_TOKEN` environment variable (see `.env.example` — a self-serve
   `developer_token` from a PH account's API dashboard). If that variable is unset, record
   ProductHunt as blocked (owner-gated), never as an absence of signal.
3. **Reddit is excluded from v1 scope, by design, not oversight.** Unauthenticated scraping is
   confirmed fingerprint-blocked across three independent tools on a sibling repo's real run
   (`WebFetch`, a raw `urllib` request, and `polyfetch-scrape`'s stealth-Patchright tier — two
   subreddits, all returning HTTP 403 after retries). The official OAuth API's current terms and
   free-tier limits remain unverified. Do not attempt Reddit scraping under any circumstance; do not
   treat this exclusion as a source-coverage gap to fill improvisationally.
4. Extract **sourced quotes only** — every claim about "what users say" carries a URL and, where
   available, a date and author handle. No paraphrasing a quote into cleaner marketing language; no
   inventing a pain point that wasn't actually observed in the source material.
5. Group quotes into 4–8 patterns. A pattern is a claim with multiple independent quotes supporting
   it, not a single anecdote elevated to a trend.

## Fetch tooling

Fetch every URL above via [`polyfetch-scrape`](https://github.com/qte77/polyfetch-scrape) rather
than a summarizing web-fetch tool — available in this workspace as a sibling clone, no install
needed: `uv run --directory ../polyfetch-scrape polyfetch fetch <url> --json` (or `--show-body` for
the raw response body). This avoids the paraphrase risk a summarizing fetch introduces: a real prior
run in this chain flagged exactly this — extracting quotes through a tool that "runs a small model
over each page rather than returning raw HTML/JSON... a paraphrase-risk substitute for a raw fetch."
See `../polyfetch-scrape/USING.md` for the full command reference.

For HN's Algolia/Firebase endpoints, this is a plain GET — pipe straight through.
**ProductHunt's GraphQL API is a POST with a JSON body and an Authorization header. Confirmed
(2026-09-01, `polyfetch fetch --help`): the CLI has no `--data`/`--body`/`--headers` flag, only
`--method` — it cannot carry either, so it cannot be used for this call. Use a direct HTTP client
(e.g. `httpx`) for the ProductHunt request instead; keep `polyfetch` for every plain-GET fetch
above.**

## If a source is blocked

Record it as blocked, not as evidence the pain point doesn't exist. State plainly what was searched,
how, and what the actual result was — one of: blocked / empty / found / excluded-by-scope (the
fourth state exists so Reddit's deliberate v1 exclusion is never conflated with a real network
block) — before drawing any conclusion from a source.

## Output

Write `findings/<date-time-iso>-<scope-slug>-complaints-findings.md` §Research. `<date-time-iso>` is
generated once by the orchestrator (`date -u +%Y-%m-%dT%H%M%SZ`) and shared with
`build-pattern-scanner`'s run so the pair's filenames sort together. Structure: a "Source coverage"
note up top (what was tried, what worked, what was blocked/excluded and why), then one subsection
per pattern with verbatim quotes, each quote's source URL, and a brief note on what the pattern
means for a future concept candidate.

Run `scripts/verify_sourcing.py findings/<date-time-iso>-<scope-slug>-complaints-findings.md` before
considering this phase done — it fails if any quote block is missing a URL.
