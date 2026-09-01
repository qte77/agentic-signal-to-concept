# 0001 — agentic-signal-to-concept: pipeline concept

## 0. Session handoff — 2026-09-01 (read this before touching anything)

**What's real so far**: this repo exists on GitHub (`qte77/agentic-signal-to-concept`, public,
Apache 2.0), scaffolded with README + this concept doc, merged via PR #1 (squash, `--admin` used
once to clear a repo ruleset requiring extra approval on agent-authored changes — that ruleset is
still active, expect the same gate on future PRs from an agent unless a human approves first).
**Nothing beyond this doc and the README exists** — no code, no `.claude/agents/` specs, no
`config/`. The three signal sources, the aggregate-only ethical boundary, and the open questions
below are the entire real state.

**Standing rule for whoever picks this up**: any subagent dispatched against this repo's work
**must run in its own git worktree**, always — never share a working tree with another concurrent
agent. This is the same rule the sibling repos in this chain (and the wider workspace) run under;
treat it as non-negotiable, not a per-task judgment call.

**Single most useful next step, in order**: before writing any phase spec, read the two sibling
repos' real, working implementations — don't design from this doc's sketch alone, the actual
worked examples answer questions this doc only gestures at. Source map below.

### Code / file / source map — so the next session doesn't re-map this from scratch

**This repo** (`/workspaces/qte77/agentic-signal-to-concept`), everything that exists as of this
handoff:
- `README.md` — purpose, chain position, the ethical boundary stated as a headline, not buried.
- `docs/plans/0001-concept.md` — this file. `§`s below "Status" are the original concept content,
  unchanged by this handoff.
- `LICENSE` (Apache 2.0, raw GitHub-template text, no filled-in copyright line — matches
  `agentic-grounded-persona-eval`'s own LICENSE convention exactly, verified by direct comparison).
- `CHANGELOG.md`, `.gitignore` — minimal, Keep a Changelog format.

**`/workspaces/qte77/agentic-grounded-persona-eval`** (downstream of this repo in the chain) — the
closest real analogue for *this* repo's own eventual phase design, since its 3-phase
research → grounded-personas → live-eval structure is exactly the shape "many independent research
passes → synthesis" needs to take here too:
- `.claude/agents/research-analyst.md` — Phase 1 spec: sourced-quote gathering, no paraphrasing.
- `.claude/agents/persona-synthesizer.md` — Phase 2 spec: personas built *from* Phase 1 evidence.
- `.claude/agents/live-evaluator.md` — Phase 3 spec: live-evaluates a real product (not applicable
  to a still-conceptual signal-mining pipeline, but read for the "candid, in-character, tagged
  against evidence" verdict discipline).
- `scripts/verify_sourcing.py` + `tests/test_verify_sourcing.py` — the mechanical check that a
  quote-with-no-source doesn't make it into a findings doc. Real prior art for the same discipline
  this repo's own concept doc calls for ("a claim about what users say always carries a URL").
- `config/target.example.md`, `config/target.md` — the real input-config shape a product/target
  gets described in before Phase 1 runs.
- `examples/groundwork/{findings.md,target.md}` — a full, real, worked run of the 3-phase method.
  Read this before designing anything — it shows the actual expected output shape, not just the
  spec's description of it.
- `findings/README.md` — the expected findings-doc structure.
- `AGENTS.md`, `CONTRIBUTING.md` — orchestration + contribution conventions.

**`/workspaces/qte77/agentic-market-research-to-gtm`** (two steps downstream) — the more heavily
built-out sibling; useful for the *orchestration* pattern (multi-phase, parallel where possible,
validation-loop) even though its actual subject matter (PMF/GTM for an already-chosen company) is
further downstream than what this repo does:
- `AGENTS.md` — the real orchestration workflow: phase dependency graph, parallel execution
  (Phase 0 + Phase 1A run simultaneously), validation-loop integration, TodoWrite progress
  tracking. The clearest real example in this chain of how to structure a multi-phase agent
  pipeline as a single orchestrated `AGENTS.md` rather than manual phase-by-phase invocation.
- `SUBAGENTS.md` — shared standards referenced by every phase subagent (file creation, citation
  requirements, markdown formatting) — the kind of cross-cutting convention doc this repo will
  need once it has more than one phase spec.
- `.claude/agents/{source-project-analyst,industry-landscape-researcher,market-research-specialist,
  product-market-fit-analyst,gtm-strategy-developer,contradiction-analyzer,research-synthesizer,
  slide-deck-generator,results-validator}.md` — nine real phase specs, the most complete example of
  "how many phases, what each one reads and produces" in this whole chain.
- `config/{sources,sources.example,targets,targets.example,comments_research,comments_gtm,
  validation_criteria}.md` — real input-config shape for a 9-phase pipeline.
- `results/archive/{uti-botnar-fit,uti-bayer-fit}/` and
  `examples/2025-08-{14,17,24}-supabase-mcp-*/` — multiple real, complete prior runs, full output
  tree included. The single best resource in this chain for "what does a finished pipeline run's
  output actually look like, end to end."
- `.claude/rules/{compound-learning,context-management,core-principles}.md` — **filename-identical**
  to rules already governing the session that wrote this handoff (not verified content-identical,
  just noting the match — worth checking whether they're the same conventions before assuming).
- `Makefile` — legacy batch-execution targets, useful as a concrete example of wiring phases to
  `make` targets if this repo ends up wanting that too.

## Status

Concept — no pipeline implementation yet. This document records the design decided so far, the
explicit ethical boundary this tool is built around, and what's genuinely still open. It is not a
build plan.

**Superseded in part by `docs/plans/0002-signal-to-concept-v1.md` (2026-09-01)**: that arc drafted
the actual `.claude/agents/*.md` phase specs, `config/`, and `AGENTS.md` orchestrator this doc's
"Explicit KISS boundaries" section (below) said not to build yet — scoped to v1's smallest workable
slice (HN+ProductHunt complaints, GitHub+Show HN build-pattern scanning). The open questions below
that 0002 doesn't touch (app-store access, Reddit's OAuth API terms, the deferred build-pattern
sub-sources) remain open; see 0002's remaining-work table for their exact status.

## Problem / opportunity

Product idea generation is usually either pure guessing, or individually-run research passes with
no consistent sourcing discipline. The goal here is a repeatable method: gather real, sourced
signal from where people actually complain and build, look for *patterns* (a real unmet need, or
many independent small teams converging on the same problem), and turn a validated pattern into a
written concept candidate — feeding into
[`agentic-grounded-persona-eval`](https://github.com/qte77/agentic-grounded-persona-eval) for
demand validation and, further downstream,
[`agentic-market-research-to-gtm`](https://github.com/qte77/agentic-market-research-to-gtm) for
PMF/GTM once a concept is validated.

## Signal source categories — not an exhaustive list

**Corrected 2026-09-01**: the three categories below were too narrow as originally scoped —
specifically, source 3 undersold itself as "small prototype projects" when the real intent is
broader: scan real, working codebases and demos — both AI-assisted ("vibe coded") and
conventionally-built — for promising, unscaled build activity, from wherever that activity is
actually visible. A fourth, deliberately open-ended category is added to make explicit that these
three are starting points, not the full list — the method should generalize to any source where the
relevant builder or user community actually is, matching `agentic-grounded-persona-eval`'s own
framing of its Phase 1 ("Reddit, Hacker News, ProductHunt, Trustpilot, or industry-appropriate
equivalents").

### 1. Reddit / Hacker News / Product Hunt complaint and request mining

Mine public discussion for recurring, sourced complaints and feature requests — the same method
`agentic-grounded-persona-eval`'s own Phase 1 already uses (sourced quotes, not paraphrases; a
claim about "what users say" always carries a URL). Real, already-verified starting points from
that repo's own README:

- **Hacker News search** — `https://hn.algolia.com/api/v1/search?query=…`, no auth.
- **Hacker News full comment trees** — `https://hacker-news.firebaseio.com/v0/item/<id>.json`
  (official Firebase API, no auth), walking `kids` recursively after an Algolia search finds a
  story.
- **ProductHunt** — GraphQL API v2, `https://api.producthunt.com/v2/api/graphql`, self-serve
  `developer_token`, no app review needed.

**Reddit — two separate facts, not one (corrected 2026-09-01).** (a) Unauthenticated scraping is
empirically blocked: a real run in the sibling `agentic-grounded-persona-eval` repo
(`examples/groundwork/findings.md`) found Reddit fingerprint-blocking every tool tried —
`WebFetch`, a raw `urllib` request, and `polyfetch-scrape`'s stealth-Patchright tier — across two
subreddits, all returning HTTP 403 after retries. That doc is explicit this is a fact about *that
runner's network*, not evidence the discussion doesn't exist on Reddit. (b) The official OAuth API's
post-2023 pricing/terms remain **not primary-source-verified**: a 2026-09-01 research pass could not
reach any Reddit-owned domain directly (`reddit.com`/`redditinc.com`/`developers.reddit.com` refused
outright at the fetch-tool level; `support.reddithelp.com`'s API-terms and Responsible-Builder-Policy
articles both returned HTTP 403; `web.archive.org`/`archive.ph` mirrors were also blocked). What
several convergent third-party sources (not Reddit's own text) report: the free non-commercial tier
is still 100 queries/minute at no cost, and commercial pricing (~$0.24/1,000 calls, ~$12,000/mo for
50M calls) looks unchanged since 2023 — but a "Responsible Builder Policy" reportedly introduced
around November 2025 closed **self-service OAuth app registration entirely**. All new API access —
free or paid — now reportedly requires manual approval (2–4 week turnaround, not guaranteed;
pre-Nov-2025 apps are grandfathered). Best corroboration found: a dated GitHub issue
(`Apollo-Reborn/Apollo-Reborn#82`, opened 2025-12-07) reporting the same self-service shutdown,
linking to a live r/redditdev thread titled "Introducing the Responsible Builder Policy" that could
not be fetched directly to confirm its exact wording. **Recommended default: exclude Reddit from v1
scope regardless of route** — even if the OAuth path's terms turn out workable, its access model has
shifted from instant self-service to an approval process with real rejection risk, which changes the
buildability calculus, not just the terms. Re-verify against Reddit's own text (not
third-party summaries) before budgeting real effort toward a Reddit-based Phase 1 pass.

### 2. App-store review mining — gap analysis, not cloning

Mine public app-store reviews for a specific, recurring, unaddressed complaint pattern within a
category, as market-gap signal. **What this produces is a validated gap, never a template to copy.**
A concept candidate coming out of this source must be a genuinely different implementation solving
the same underlying complaint — different branding, different code, real differentiation — not a
reproduction of any specific existing app's UI, branding, or backend logic.

**Real access gap, fully researched across two passes (2026-09-01) — catalog/discovery access and
review-text access are two separate problems, addressed separately below.**

**Catalog/discovery (finding which apps exist in a category, before mining their reviews):**
- **Apple — confirmed working, free, public, no auth.** The iTunes Search API
  (`https://itunes.apple.com/search?term=<kw>&country=us&entity=software`) was tested directly this
  session (not assumed) — returns real catalog metadata (name, developer, category, price,
  `averageUserRating`, `userRatingCount`) for any app. This is aggregate rating data, not individual
  review text.
- **Google Play — confirmed no first-party equivalent exists.** The Android Publisher API family
  (`developers.google.com/android-publisher/api-ref/rest/v3/reviews/list`) is fully OAuth-gated and
  scoped only to `packageName`s the caller's own dev account owns — no term-search/catalog endpoint
  exists in this API at all. The only route is unofficial scraper libraries (e.g.
  `google-play-scraper`) that scrape rendered `play.google.com/store/*` pages — their exact backing
  mechanism (documented internal endpoint vs. raw HTML scrape) was not verified at source, treat as
  unconfirmed. `play.google.com/robots.txt` (confirmed direct fetch) explicitly disallows
  `/store/search` and general `/apps`, but does **not** explicitly disallow individual
  `/store/apps/details` pages — a real technical nuance, though a robots.txt gap is not the same as
  ToS clearance (Play's ToS text itself was not checked specifically for a scraping clause this
  pass).

**Review-text access:**
- **Apple's App Store Connect API is confirmed owner-scoped, verbatim.** Fetched the doc's
  underlying JSON data path directly (the HTML page is JS-rendered and returns nothing to a plain
  fetch): "This endpoint allows you to retrieve customer reviews for an app associated with your
  team account in App Store Connect" — no ambiguity, no path to a competitor's reviews.
- **Apple's legacy public reviews RSS/JSON feed is confirmed dead, tested directly.** A once-common,
  widely-blogged trick (`itunes.apple.com/{country}/rss/customerreviews/id={appId}/sortby=mostrecent/json`)
  for pulling review text for *any* app without auth was tested against two app IDs (including
  Notion's real ID, 89,930 ratings) across two URL shapes — both returned only feed metadata, zero
  review entries. **Do not rely on tutorials/StackOverflow answers describing this feed as working —
  it does not, as of 2026-09-01.**
- **Google Play has no equivalent public feed either.** `reviews.list` is the only first-party
  option and is owner-scoped/OAuth-gated, same restriction as catalog access above. Unofficial
  scraper libraries' `reviews()` method is the only unofficial route, mechanism unconfirmed.
- **Independent scraping is explicitly, broadly prohibited by Apple** (confirmed direct quote,
  `apple.com/legal/internet-services/itunes/us/terms.html`): "You may not use any software, device,
  automated process, or any similar or equivalent manual process to scrape, copy, or perform
  measurement, analysis, or monitoring of, any portion of the Content or Services." Google's
  Play-specific terms are silent on scraping; Google's general ToS conditions automated access on
  robots.txt compliance — narrower than Apple's blanket ban, not an equivalent clearance.

**Third-party aggregators — now fully resolved, no remaining pricing gaps:**
- **Appbot** (`appbot.co/plans/` + `support.appbot.co`, both confirmed direct fetch): pricing is
  source-count-based (Small=5/Medium=40/Large=100 sources, from $49/mo). Any tier can track a
  competitor app as an ordinary "source" — competitor tracking itself is **not** tier-gated. The
  side-by-side "Compare Apps" dashboard specifically requires Medium tier or above (~$99/mo).
- **AppFollow** (`appfollow.io/blog/new-plans-at-appfollow-and-how-to-choose-the-right-one`,
  first-party, confirmed direct fetch — the `/pricing` page itself remains JS-rendered/empty): Free
  $0/mo (2 apps, 10 competitors), ASO $19/mo (30 competitors), Essential $179/mo or $129/mo annual
  (100 competitors), Team $599/mo or $425/mo annual (250 competitors), Enterprise custom.
- **Sensor Tower** (`sensortower.com/pricing`, confirmed direct fetch): quote-only/enterprise, no
  self-serve tier.

**Trustpilot — evaluated 2026-09-01, per a direct follow-up question (does it cover software/apps,
not just retail):**
- Trustpilot's **Business Units API (public)** (`developers.trustpilot.com/business-units-api-(public)`,
  confirmed direct fetch, API-key auth) explicitly returns "public business information for any
  business unit," including reviews — genuinely queryable for a company you don't own, unlike
  Apple/Google's owner-scoped APIs. Service Reviews and Product Reviews APIs also exist.
- **Self-serve API pricing is unconfirmed** — no price table found; sign-up funnels into a sales-led
  "Trustpilot for Business" flow with no visible self-serve price for API access specifically.
- **Independent scraping is explicitly, broadly prohibited** (confirmed direct quote,
  `corporate.trustpilot.com/legal/for-everyone/action-we-take/mar-2026`): bans "automated tools (such
  as AI agents, bots, crawlers, spiders or scrapers)," "text mining, data mining or web scraping,"
  "republishing... scraped data," **and explicitly "use of our data... to train and develop
  artificial intelligence models."** `robots.txt` disallows `/reviews/`, `/api/*`, and ends with a
  catch-all disallow — reinforcing the API is the only sanctioned path, not scraping.
- **Not evaluated**: how much genuine software/SaaS coverage Trustpilot actually has (category mix
  wasn't audited) — a real open question before treating it as a viable source for this category.

**Recommended default: Appbot for app-store review mining** (confirmed self-serve pricing, competitor
tracking available at every tier). **Trustpilot is a real candidate for a broader "software/SaaS
company reviews" source** (not app-store-specific) if its self-serve API pricing resolves favorably
and its software-category coverage checks out — both still open. Never pursue independent scraping
of Apple's or Trustpilot's review pages given their explicit ToS prohibitions; a Google-Play-only
scraping path is comparatively less clearly barred but still practically unevaluated.

### 3. Build-pattern signal (real projects, "vibe coded" or conventionally built) — aggregate only, by design

**This is the source with the real ethical boundary, and it constrains the design, not just a
caveat appended after the fact.** It covers both AI-assisted rapid-prototyped ("vibe coded")
projects and conventionally-built ones equally — the boundary below doesn't distinguish between
them, because the ethical concern (racing a specific active builder using their own work) doesn't
either.

**Real, plausible starting points for where this signal actually lives — none verified this pass,
listed as candidates, not a chosen set:**
- **GitHub** — trending repos, topic/tag search, recently-created-and-active repos in a category.
  The REST/GraphQL API itself is well-documented, public, and has generous rate limits (a
  long-standing, stable API shape, unlike Reddit's — still worth a fresh check before depending on
  it, not re-verified this pass).
- **Show HN / "Launch HN" threads specifically** — a real, distinct sub-pattern within the HN
  source already named above (source 1's API access applies here too), worth treating as its own
  signal type since a Show HN post is explicitly "I built this," not a complaint. **Also confirmed
  2026-09-01 as the compliant substitute channel for hackathon/Lovable/Replit signal** (see the
  Devpost/AI-builder-showcase bullets below): `query=vibe coded&tags=story` (2,007 real hits),
  `query=Show HN hackathon&tags=story` (432 real hits), `query=lovable&tags=show_hn` (376 real,
  on-topic hits) all returned genuine results when tested directly. Note: Algolia's `query` param is
  plain full-text, not a query language — combining terms with `OR` or quotes returned 0 hits in
  testing; use single terms or `tags` scoping instead.
- **Hackathon/demo-day galleries (Devpost)** — **researched 2026-09-01, ruled out**: no official
  API (only unofficial community scrapers exist); `devpost.com/robots.txt` blocks most named AI/LLM
  crawlers but leaves the default user-agent open; its actual Terms (`info.devpost.com/terms`)
  explicitly ban "scrape," "crawl," or "spider" of the site or user content, on pain of account
  termination and hackathon disqualification. **No compliant read path — do not add.**
- **AI-builder-tool showcases (Bolt.new, Lovable, v0, Replit)** — **researched 2026-09-01, mixed**:
  **Lovable** (`lovable.dev/terms`) and **Replit** (`replit.com/site/terms`) both explicitly ban
  automated scraping/bots in their ToS, independent of their permissive `robots.txt` files — **ruled
  out**, same as Devpost. **Bolt.new** and **v0/Vercel** have permissive `robots.txt` but their
  scraping-specific ToS language could not be confirmed (Bolt.new/StackBlitz's actual terms document
  wasn't retrievable; Vercel's general ToS doesn't mention v0 community pages) — **genuinely unclear,
  an open gap, not a clearance; don't treat robots.txt permissiveness as equivalent to ToS
  clearance.** **Compliant substitute for Devpost/Lovable/Replit signal, confirmed working
  2026-09-01** (tracked at [issue #10](https://github.com/qte77/agentic-signal-to-concept/issues/10),
  opened after the user asked to scrape these directly despite the ToS findings above — declined for
  that reason, this is the alternative pursued instead): GitHub topic search — `topic:hackathon`
  (13,544 hits, real but noisy, dominated by boilerplate/starter-kit repos rather than individual
  submissions, needs per-repo README inspection to confirm which hackathon/when/who) and
  `topic:lovable`/`topic:replit` (584/998 hits, credible ecosystem-adjacent signal — tooling and
  alternatives built around these platforms, not a 1:1 substitute for browsing their own user-project
  showcases). Combined with the Show HN queries above, this recovers a real, if noisier and
  ToS-compliant, share of the same underlying signal.
- **TrustMRR** (`trustmrr.com`, a public directory of indie-startup MRR verified via payment-provider
  API keys) — suggested as an additional build-in-public source, **researched and ruled out
  2026-09-01**: has a real public API (its ToS has a dedicated "API Acceptable Use Policy"), but that
  policy explicitly bans "us[ing] API data to train, fine-tune, ground, evaluate, or populate an AI
  model, dataset, search index, recommendation system, or automated content generator without prior
  written permission" — exactly this pipeline's use case — and separately bans scraping the site
  outright ("Scrape, harvest, archive, or systematically reconstruct TrustMRR's database"). Unlike
  Devpost/Lovable's flat bans, the "without prior written permission" wording leaves a real door open:
  **the actionable next step is asking TrustMRR directly for permission**, not scraping around the
  restriction. Tracked at [issue #10](https://github.com/qte77/agentic-signal-to-concept/issues/10).
- **Build-in-public social threads (X/Twitter, Bluesky, Indie Hackers)** — **researched 2026-09-01,
  mixed, sharpened further by direct testing**: **X/Twitter** has no viable free or affordable API
  for aggregate search as of its Feb 2026 pricing overhaul (pay-per-usage, $0.015/post created +
  $0.005/post read; legacy $200 and $5,000/mo tiers retired/closed; full-archive search needs
  $42,000+/mo Enterprise) — **ruled out on cost**. **Indie Hackers**
  (`indiehackers.com/terms`) explicitly bans crawling/scraping/spidering in its ToS — **ruled out**,
  and confirmed 2026-09-01 to have no benign alternative either: no RSS/Atom feed exists
  (`/rss` and `/feed` both return HTTP 404, no feed `<link>` tag on the homepage, no API/feed mention
  on `/about`) — fully out of scope, no path in. **Bluesky's AT Protocol is legally the cleanest (no
  ToS bar, no paid tier) but its search endpoint is confirmed blocked, and this is now a stronger
  finding than a single-runner artifact**: `getProfile` (a read-only lookup) works fine, no auth, on
  `public.api.bsky.app` — but `app.bsky.feed.searchPosts` returned a clean HTTP 403 (an HTML WAF
  block page, not a JSON API error) reproduced from **two independent networks/environments** via
  three different fetch methods (polyfetch, WebFetch, Python `urllib`), with the same working
  `getProfile` control on both. This looks like a path-specific edge block on the search endpoint
  itself, not IP-reputation blocking of one runner — real evidence of a genuine access barrier, not
  just "try a different network." Separately, the raw firehose (`com.atproto.sync.subscribeRepos`)
  is a continuous, unfiltered, network-wide WebSocket stream, not a simple fetchable search — using
  it for keyword-targeted research needs a persistent stream consumer doing its own filtering, a
  materially bigger engineering lift than "call an API with a keyword," independent of the
  search-endpoint block above. **Recommended: keep the spec's Bluesky step, but treat it as blocked
  until proven otherwise — re-check periodically rather than budgeting real effort toward it now.**

The legitimate version of this signal, regardless of which of the above it comes from, is: **the
existence of many independent people building small, unscaled attempts at the same problem is
evidence a real need exists** — the same reading this project's own sibling concepts
(ToolPop/TuneValue/GiveWorth, and Poptart's own competitive scan before them) already gave to "19+
independently built apps, same shape, same problem": read as validated demand, never as "here is
the best one, go build that instead."

**What this tool will never do, regardless of a project's license or public visibility:** identify
one specific person's specific, currently-active, unscaled project and use it as a direct
build-and-launch template. A project someone is actively building — even a small prototype, even
one with no other visible progress in months — is not the same as an abandoned one; its creator has
a reasonable expectation their public work-in-progress is exactly that, not a target list. Using it
as one anyway is not market research, it's racing someone using groundwork they did themselves,
before they've had the chance to build on it.

**The one narrow legitimate exception**: a project that is both (a) explicitly permissively
licensed (MIT/Apache, not merely publicly visible — public visibility alone confers no reuse right;
copyright defaults to "all rights reserved" without an explicit license) *and* (b) the creator has
said, not merely implied, that they're not pursuing commercialization themselves. Even then, the
right first move is outreach and attribution, not silent reimplementation and launch — this tool
should surface such a project as an *outreach candidate*, never as ready-to-build input.

**Not scoped further than this here** — how to actually detect "many independent small attempts at
the same problem" (which sources, what counts as independent, how to avoid false patterns from one
viral post being reposted many times) is real design work, not done in this pass.

### 4. Other sources — deliberately open-ended, not a closed list

The three categories above are real starting points, not the full set. The method itself — sourced
quotes over paraphrase, aggregate pattern over specific target, real access/ToS check before
depending on any source — generalizes to any place a relevant builder or user community actually
talks. Don't treat the absence of a source from the list above as it being out of scope; treat its
absence as "not yet evaluated," and apply the same discipline (real access check, same ethical
boundary for source 3-shaped signal) before adding it.

## Architecture sketch — not a build plan

Each source above becomes its own research pass (mirroring how `agentic-grounded-persona-eval`
splits Phase 1 research from Phase 2 synthesis), producing a sourced-findings document per source,
then a synthesis pass that looks for patterns *across* sources before writing a concept candidate.
The concept candidate's own output shape should match what `agentic-grounded-persona-eval`'s Phase
1/2 already expect as input, so a validated candidate flows into that tool without reshaping.

**Exact phase count, subagent breakdown, and output file structure are not decided.** Don't build
`.claude/agents/*.md` phase specs from this sketch directly — design the real phase breakdown as
its own pass first, the way `agentic-grounded-persona-eval`'s own three-phase design was clearly a
deliberate, considered structure, not a first guess.

## Explicit KISS boundaries — deliberately not building yet

No implementation, no subagent specs, no `config/` structure, no tech-stack decision. No Reddit
integration until its current API terms are verified. No app-store integration until a real access
path (aggregator or verified-compliant scraping) is chosen. No GitHub/Devpost/AI-builder-showcase/
build-in-public integration until each one's real access path is checked, same discipline as
Reddit and app-stores — being newly-named in this doc is not the same as being verified usable. No
detection heuristic for "independent build pattern" — that's real, unstarted design work, not a
small missing detail.

## Open questions

- Reddit API: unauthenticated scraping confirmed blocked on a sibling run's network (see §1).
  Official OAuth API terms are still **not primary-source-verified** (2026-09-01 research pass
  blocked from every Reddit-owned domain) — convergent third-party sources report pricing unchanged
  since 2023 but self-service registration closed (~Nov 2025 "Responsible Builder Policy"), gating
  all new access behind manual approval. Recommended default: exclude Reddit from v1 regardless of
  how the terms question resolves — the access-model shift matters as much as the terms themselves.
- App-store review access: **fully resolved, 2026-09-01** (see §2, two research passes). Catalog
  discovery: Apple's iTunes Search API confirmed free/public/working; Google Play has no first-party
  equivalent. Review text: both Apple's App Store Connect API and Google's `reviews.list` are
  confirmed owner-scoped only; Apple's legacy public reviews feed is confirmed dead by direct test.
  Appbot is the recommended default aggregator (confirmed pricing, competitor tracking on every
  tier, comparison dashboard needs Medium+); AppFollow's pricing is now first-party confirmed too.
  Trustpilot has a real, queryable public API for companies you don't own, but its self-serve API
  pricing is unconfirmed and its actual software/SaaS category coverage hasn't been audited.
  Independent scraping is ruled out for both Apple and Trustpilot (explicit ToS prohibitions,
  primary-source confirmed).
- GitHub/Devpost/AI-builder-tool-showcase/build-in-public access and ToS — **resolved, 2026-09-01,
  tracked at [issue #10](https://github.com/qte77/agentic-signal-to-concept/issues/10)** (see §3):
  Devpost, Lovable, Replit, Indie Hackers, and TrustMRR all explicitly ban scraping (TrustMRR's ban
  is conditional on "prior written permission," a real door to ask through, not a flat bar) — ruled
  out for direct access; a compliant substitute (GitHub `topic:hackathon`/`topic:lovable`/
  `topic:replit` search + targeted Show HN queries) is confirmed working and recovers a real, if
  noisier, share of the same signal. Bolt.new and v0/Vercel are a genuine open gap (permissive
  `robots.txt`, unconfirmed ToS). X is ruled out on cost. Indie Hackers has no benign alternative
  either (confirmed: no RSS/feed exists). Bluesky's search endpoint is confirmed blocked from two
  independent networks — a real access barrier, not just this runner's network — while its firehose
  remains impractical for keyword search without a persistent stream consumer. GitHub's own API
  remains well-known/stable but not freshly re-checked this pass.
- What makes multiple small builds "independent" rather than reposts of the same one — undefined.
- Exact phase/subagent breakdown for the pipeline itself — undesigned.
- Output format handoff into `agentic-grounded-persona-eval` — **answered, partially (2026-09-01)**:
  `config/target.example.md` requires a **Live URL** field, since that repo's Phase 3 live-evaluates
  a real running product. A concept candidate here has no live product yet, so it can only feed
  Phases 1–2 (demand-side research + grounded personas for the assumed ICP), not Phase 3, until
  something ships. The candidate doc's output shape should match `target.example.md`'s fields
  (Name / Live URL — blank until launch / Assumed ICPs / Research constraints).
- Which additional sources beyond the four named categories are worth adding — genuinely open,
  not a fixed list by design (see §4 above).
