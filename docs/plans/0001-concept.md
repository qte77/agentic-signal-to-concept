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

## The three signal sources, and how each is scoped

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

**Reddit is explicitly unresolved here, not assumed available.** Reddit's official API pricing
changed materially in 2023 (a real, publicized policy shift) — current terms, current free-tier
limits, and whether they're workable for this use case have **not been checked this pass**. Verify
before building anything that depends on Reddit specifically; don't assume the pre-2023 free-access
shape still holds.

### 2. App-store review mining — gap analysis, not cloning

Mine public app-store reviews for a specific, recurring, unaddressed complaint pattern within a
category, as market-gap signal. **What this produces is a validated gap, never a template to copy.**
A concept candidate coming out of this source must be a genuinely different implementation solving
the same underlying complaint — different branding, different code, real differentiation — not a
reproduction of any specific existing app's UI, branding, or backend logic.

**Real access gap, unresolved**: official first-party APIs don't cover this (Apple's App Store
Connect API only covers apps you already own; there is no equivalent first-party review-scraping
API for competitors on either store). Realistically this needs either a paid third-party review
aggregator (e.g. AppFollow, Sensor Tower — neither evaluated here) or careful, ToS-verified scraping
of public review pages. **Not evaluated this pass — a real prerequisite before this source is
usable, not a detail to skip.**

### 3. Build-pattern signal (small / prototype / MVP-stage projects) — aggregate only, by design

**This is the source with the real ethical boundary, and it constrains the design, not just a
caveat appended after the fact.**

The legitimate version of this signal is: **the existence of many independent people building
small, unscaled attempts at the same problem is evidence a real need exists** — the same reading
this project's own sibling concepts (ToolPop/TuneValue/GiveWorth, and Poptart's own competitive
scan before them) already gave to "19+ independently built apps, same shape, same problem": read as
validated demand, never as "here is the best one, go build that instead."

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
path (aggregator or verified-compliant scraping) is chosen. No detection heuristic for "independent
build pattern" — that's real, unstarted design work, not a small missing detail.

## Open questions

- Reddit API: current terms, current free-tier viability — unverified.
- App-store review access: aggregator vs. compliant scraping — unevaluated, no default chosen.
- What makes multiple small builds "independent" rather than reposts of the same one — undefined.
- Exact phase/subagent breakdown for the pipeline itself — undesigned.
- Output format handoff into `agentic-grounded-persona-eval` — not yet matched against that repo's
  actual Phase 1 input expectations, only assumed compatible.
