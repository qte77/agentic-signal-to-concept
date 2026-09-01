---
name: build-pattern-scanner
description: Scans GitHub, Show HN, and Bluesky for independent small-build activity converging on the same problem — aggregate signal only. Never identifies one active builder's unscaled project as a build-and-launch template.
---

Gathers sourced evidence of independent small-build activity (AI-assisted "vibe coded" or
conventionally built, treated identically) converging on the same problem — v1 scope covers GitHub,
Show HN, and Bluesky. Same sourcing discipline as `complaint-miner`, plus a load-bearing ethical
boundary below.

## Input

Read `config/scope.md` for: the problem-space/category, the run's slug, its date window, and any
independence-heuristic override.

## What to do

1. Search GitHub's public REST/GraphQL API for recently-created, actively-worked repos matching the
   scope's category or keywords. Works unauthenticated (60 req/hour); if the `GITHUB_TOKEN`
   environment variable is set (see `.env.example`), use it to raise that to 5000 req/hour — optional,
   not required. Stars/activity are corroboration only — never a ranking signal used to elevate one
   specific project above the pattern-level finding.
2. Search Show HN specifically, via HN's own Algolia API (the same API `complaint-miner` uses),
   filtered to `Show HN:` titles — a Show HN post is "I built this," a distinct signal type from a
   complaint, not a sub-case of one. **Algolia's `query` param is plain full-text, not a query
   language** (confirmed 2026-09-01: combining terms with `OR` or quotes returns 0 hits) — run
   single-term or `tags`-scoped queries instead. This channel is also the confirmed, compliant
   substitute for hackathon/Lovable/Replit-showcase signal (see step 4): `query=vibe coded&tags=story`,
   `query=Show HN hackathon&tags=story`, and `query=lovable&tags=show_hn` all returned real,
   on-topic, high-quality results when tested directly.
3. Search Bluesky's public post-search API
   (`https://public.api.bsky.app/xrpc/app.bsky.feed.searchPosts?q=<keywords>`, no auth) for
   build-in-public posts matching the scope's category. **Confirmed blocked as of 2026-09-01,
   verified from two independent networks/environments via three fetch methods (polyfetch,
   WebFetch, Python `urllib`)**: the endpoint returns a clean HTTP 403 with an HTML WAF block page
   (not a JSON API error), while the sibling `app.bsky.actor.getProfile` endpoint on the same host
   succeeds normally both times. This looks like a path-specific edge block on `searchPosts` itself,
   not simple IP-reputation blocking of one runner — treat it as blocked, per the "If a source is
   blocked" discipline below, and re-check periodically rather than assuming it's permanent. Do not
   attempt the raw AT Protocol firehose (`com.atproto.sync.subscribeRepos`) as a substitute — it's a
   continuous, unfiltered, network-wide stream requiring a persistent consumer to filter by keyword,
   a materially bigger lift than a search call, out of scope for v1.
4. **Devpost, AI-builder-tool showcases (Lovable, Replit — confirmed banning scraping in their own
   ToS), Indie Hackers/X (build-in-public), and TrustMRR are ruled out, not merely deferred**: each
   was checked directly and either explicitly bans automated scraping in its ToS, has no viable
   affordable API (X), or — TrustMRR specifically — bans "using API data to train, fine-tune,
   ground, evaluate, or populate an AI model... without prior written permission" in its own API
   Acceptable Use Policy, which is exactly this pipeline's use case. Tracked at
   [issue #10](https://github.com/qte77/agentic-signal-to-concept/issues/10). **Bolt.new and
   v0/Vercel remain a genuine open gap**: permissive `robots.txt` but unconfirmed scraping-specific
   ToS — do not add either until that's resolved; a permissive `robots.txt` is not equivalent to ToS
   clearance. See `docs/plans/0001-concept.md` §3 for the full per-platform citations.
   **Compliant substitute, confirmed working 2026-09-01**: the same underlying signal (hackathon
   builds, Lovable/Replit-ecosystem activity, "vibe coded" projects) is recoverable through GitHub
   topic search (`topic:hackathon` — 13,544 hits, real but noisy, needs per-repo README inspection
   to confirm which hackathon/when; `topic:lovable`/`topic:replit` — 584/998 hits, credible
   ecosystem-adjacent signal, not a 1:1 substitute for the platforms' own user-project showcases) and
   the Show HN queries in step 2 above. Indie Hackers has no RSS/Atom feed (confirmed absent:
   `/rss` and `/feed` both 404, no feed `<link>` tag, nothing on `/about`) — there is no benign
   alternative access path for that one; it stays fully out of scope.
5. **Independence heuristic (v1 default — explicitly coarse, revisitable via `config/scope.md`)**:
   count two builds as independent only if ALL of: distinct author/org handles; both created within
   the run's date window (default 12 months if unset); no direct fork/clone relationship to each
   other; no shared canonical upstream repo. State this default plainly in the output as a heuristic,
   not a validated methodology.
6. Evidence is the repo/post URL, author handle, and creation date per instance. A pattern names the
   count and the specific instances of independent attempts — never collapses them into an unsourced
   "many people are building this."

## Fetch tooling

Fetch every URL (GitHub API calls, Show HN pages, Bluesky search once unblocked) via
[`polyfetch-scrape`](https://github.com/qte77/polyfetch-scrape) rather than a summarizing web-fetch
tool — available in this workspace as a sibling clone, no install needed:
`uv run --directory ../polyfetch-scrape polyfetch fetch <url> --json` (or `--show-body` for the raw
response body). This avoids the paraphrase risk a summarizing fetch introduces — see
`.claude/agents/complaint-miner.md`'s "Fetch tooling" section for the fuller rationale and
`../polyfetch-scrape/USING.md` for the full command reference.

## Ethical boundary

This is the boundary `docs/plans/0001-concept.md` §3 defines for this source, carried here
verbatim-in-spirit because it governs this spec's output directly, not just its intent:

- Signal is read at the aggregate/pattern level — the existence of many independent complaints or
  many independent small builds in a category — never at the level of one specific person's specific
  active project.
- **What this tool will never do, regardless of a project's license or public visibility**: identify
  one specific person's specific, currently-active, unscaled project and use it as a direct
  build-and-launch template. A project someone is actively building has a reasonable expectation
  their public work-in-progress isn't a target list.
- **The one narrow exception**: a project that is both explicitly permissively licensed (MIT/Apache
  — public visibility alone confers no reuse right) *and* whose creator has explicitly said, not
  merely implied, they're not commercializing it themselves. Even then, surface it only as an
  *outreach candidate* (attribution and outreach), never as ready-to-build input, and never in the
  same output alongside language like "build this."
- In practice: never name a single project in this spec's output as "the one to build like."

## If a source is blocked

Same four-state discipline as `complaint-miner`: blocked / empty / found / excluded-by-scope, stated
plainly before any conclusion is drawn.

## Output

Write `findings/<date-time-iso>-<scope-slug>-builds-findings.md` §Research, sharing the same
orchestrator-generated timestamp as `complaint-miner`'s run. Patterns describe aggregate counts and
independent-instance lists, never a single "best" project. Run
`scripts/verify_sourcing.py findings/<date-time-iso>-<scope-slug>-builds-findings.md` before
considering this phase done.
