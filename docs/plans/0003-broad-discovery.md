# 0003 — agentic-signal-to-concept: categoryless discovery + parallel category runs

## Status

**Full arc complete same session (2026-09-04): discovery ran, categories were picked, all three
verticals ran end-to-end, and a calibration check compared prediction to outcome.**
`signal-discoverer.md` exists and has run for real (8 categories promoted, 2 rejected as grab-bags,
archived at `examples/2026-09-04T060617Z-discovery/`). The user confirmed the recommended default
(#1 agent memory, #3 terminal/session UX, #5 freelancer finance) and asked for horizontal/vertical to
be named modes (done, see `AGENTS.md`). All three ran as parallel git worktrees — the first real
exercise of this repo's standing worktree rule — producing 3 new concept candidates (`Contextlint`,
`Vigil`/`Overflow`, `Freehold Finance`/`Upfront Terms`) and a genuinely useful negative result: raw
discovery-stage signal count did not reliably predict vertical-mode yield (see the calibration row
below). Two follow-on ideas from mid-arc discussion were deferred with tracking issues rather than
built ([#19](https://github.com/qte77/agentic-signal-to-concept/issues/19) semantic clustering,
[#20](https://github.com/qte77/agentic-signal-to-concept/issues/20) trend-aware recurring runs).
**Next real step**: source breadth (what's addable beyond Show HN + ProductHunt) — research, not yet
started.

## Why this arc exists

Every real run so far (`docs/plans/0002-signal-to-concept-v1.md`) required a human — or the
orchestrating session — to name a category (`pkm-tools`) before `complaint-miner`/
`build-pattern-scanner` could run at all; the method finds convergence *within* a named category, it
doesn't discover which categories are worth looking at in the first place. The user asked twice in
one session (2026-09-04) for something broader: "why not do a broad scan for ideas without
pre-defined categories" — not a batch of hand-picked categories, genuine bottom-up discovery. They
separately asked for parallel execution across categories once discovered.

This arc adds exactly that as a new upstream phase, without changing what already works:
**discovery finds candidate categories; the existing 3-agent pipeline still runs per-category,
unchanged.** Parallel execution (git worktrees) is where the per-category phase gets faster once
there's more than one category to run — it doesn't apply to discovery itself, which is one broad
pass, not N narrow ones.

## Architecture

```
Phase 0: signal-discoverer      → discovery/<ts>-categories.md (ranked candidate categories)
                                   ↓ human picks N categories (decide-by-default: top 3 by signal count)
Phase 1a+1b × N: complaint-miner + build-pattern-scanner   → N parallel git worktrees, one per category
Phase 2 × N:     concept-synthesizer                        → one candidate per category
```

- **`signal-discoverer`** (new agent) — a single broad, lightly-filtered pull across HN Show HN
  (unfiltered by topic, recent window) and ProductHunt (leaderboard/recent posts, unfiltered by
  topic), clustered bottom-up into emergent candidate categories. Deliberately bounded in v1, not
  exhaustive (see Scope below) — KISS/YAGNI: enough to surface real candidates, not a research
  crawler. Ethical boundary applies here too, re-stated at this earliest possible point: output is
  category-level ("N posts cluster around local-first data tools"), never a specific project.
- **Existing pipeline, unchanged**: `complaint-miner` → `build-pattern-scanner` → `concept-synthesizer`
  still run exactly as `docs/plans/0002-signal-to-concept-v1.md` specifies, once per chosen category.
- **Parallel execution via git worktrees**: this repo's own standing rule (`AGENTS.md`, and 0001's
  original handoff) has said "any subagent dispatched against this repo's work runs in its own git
  worktree, always" since the very first arc — genuinely not followed by any real run to date,
  because a single category's two Phase-1 agents write to two different filenames and never
  collided. **N parallel categories collide for real**: `config/scope.md` is one shared, gitignored
  file every category's agents read — two categories running concurrently against the same working
  tree would clobber each other's scope. Each parallel category run therefore needs its own worktree
  (own checkout, own `config/scope.md`), matching the standing rule for the first time it's actually
  load-bearing rather than precautionary.

## Scope for v1's discovery pass — deliberately bounded

- **Show HN**: `tags=show_hn`, a recent window (default 30 days — wide enough for real signal, narrow
  enough to stay a few hundred titles, not thousands), all titles read, clustered by theme. No
  category keyword filter — this is the actual "broad" part.
- **ProductHunt**: recent posts (leaderboard or `postsV2` without a `topic` filter) over the same
  window, names + taglines read and clustered. Requires `PRODUCTHUNT_API_TOKEN` (now configured);
  falls back to Show-HN-only discovery if unset, same blocked/owner-gated discipline as
  `complaint-miner`.
- **GitHub (added 2026-09-04, source-breadth follow-on)**: `created:>{cutoff} stars:>{threshold}
  sort:stars-desc`, no `topic:` qualifier, via the already-authenticated `gh` CLI — no new
  access/ToS work needed, reuses `build-pattern-scanner`'s existing GitHub auth pattern exactly. A
  star threshold (not a topic filter) bounds the pull, the GitHub equivalent of Show HN's Algolia
  cap.
- **Explicitly deferred, not attempted in v1**: a broad HN *comment* firehose (complaint-signal
  search without a category term) — HN's Algolia API doesn't support the kind of broad boolean query
  that would make this bounded and cheap in one pass; a real design needs its own pass, not a
  same-arc bolt-on. Reddit/app-store/GitHub-issue discovery are out of scope for the same reason
  categories were excluded from the v1 pipeline: each needs its own access/ToS check first. GH
  Archive (`gharchive.org` — free, public, no usage restrictions found, the full GitHub public event
  firehose) is a real candidate next source but needs either BigQuery credentials (owner-gated) or
  raw hourly-JSON parsing (a real engineering lift) — tracked, not attempted in v1.
- **Output cap**: 5–10 candidate categories per run, ranked by aggregate signal count (not a
  precision science — a coarse triage step, the same spirit as `build-pattern-scanner`'s "explicitly
  coarse v1 default" independence heuristic).

## Code / file / source map

**New files this arc creates:**
```
.claude/agents/signal-discoverer.md   (new agent spec)
discovery/README.md                    (mirrors findings/README.md's shape)
```
**Modified:**
- `AGENTS.md` — add Phase 0 (discovery) ahead of Phase 1a/1b; document the worktree requirement for
  N-parallel-category runs now that it's load-bearing, not just standing-rule boilerplate.
- `.gitignore` — add `discovery/*-categories.md` (gitignored working evidence, same treatment as
  `findings/*-findings.md` — a discovery run worth keeping permanently gets archived under
  `examples/`, same convention as every other run type).
- `CONTRIBUTING.md` / `README.md` — status pointers once the new phase is real, not just planned.

## Remaining work (single table)

| Item | Gate | Done-when |
|---|---|---|
| `signal-discoverer.md` spec | **shipped** (2026-09-04) | File exists: Input/What-to-do/If-blocked/Output sections, ethical boundary re-stated, bounded-scope discipline from this plan's "Scope" section carried in verbatim-in-spirit. |
| `discovery/README.md` + `.gitignore` update | **shipped** (2026-09-04) | File exists; `discovery/*-categories.md` gitignored, matching `findings/`'s treatment. |
| `AGENTS.md` Phase 0 + worktree-requirement update | **shipped** (2026-09-04) | Diagram updated; worktree requirement stated as load-bearing for N>1 parallel category runs, not just inherited boilerplate. |
| First real `signal-discoverer` run | **shipped** (2026-09-04) | Real Show HN + PH pull, clustered into 8 candidate categories (2 rejected as grab-bags), `discovery/2026-09-04T060617Z-categories.md` written, archived at `examples/2026-09-04T060617Z-discovery/`. Two real corrections folded back into the spec: HN's actual volume (3,000-4,000+ titles/30d, not "a few hundred") and PH's daily-cohort timestamp behavior + working `postedAfter`/`postedBefore` filter (the spec had wrongly said PH has no date-range filter — corrected). |
| Pick N categories from the first real discovery run | **shipped** (2026-09-04) | User confirmed the recommended default: #1 agent memory, #3 terminal/session UX, #5 freelancer finance. |
| N parallel category runs via git worktrees | **shipped** (2026-09-04) | Three worktrees (`../asc-agent-memory`, `../asc-terminal-ux`, `../asc-freelancer-finance`), full 3-agent pipeline run in each — first real exercise of the standing worktree rule. Real gap found and fixed: `git worktree add` doesn't carry gitignored files, so a fresh worktree has no `.env` — all three categories' `complaint-miner` runs hit ProductHunt blocked as a result (timing: `.env` was copied in mid-run, after each had already checked). Consistent across all three, so no cross-category inconsistency, but worth fixing properly before the next multi-worktree run (see watch-outs in the handoff). Findings/candidates archived at `examples/2026-09-04T070136Z-agent-memory/`, `examples/2026-09-04T070138Z-terminal-ux/`, `examples/2026-09-04T070139Z-freelancer-finance/`; candidates at matching `candidates/*.md` paths. Merged back via one PR per category. |
| Calibration check: does discovery's rank predict vertical yield? | **shipped** (2026-09-04) | Compared discovery's qualitative rank (#1 > #3 > #5) against actual Phase 1 output volume. Result did **not** hold on raw volume: #3 terminal-ux (11 patterns, 72 sourced items, 50 build instances) outproduced #1 agent-memory (10 patterns, 71 items, 37 instances); #5 freelancer-finance had the lowest volume (10 patterns, 53 items, 28 instances) but produced the single most acute finding of any category — a PE-rollup pricing-shock complaint with 100+ comments and direct switching intent. **Read (n=3, suggestive not conclusive):** discovery's rank is a reasonable coarse breadth filter but doesn't predict richness *or* acuteness reliably — it measures convergence breadth, not the intensity of any single complaint thread, a distinct dimension it isn't designed to catch. Not a build item; noted here as calibration context for interpreting future discovery ranks. |
| Broad HN comment-firehose discovery | deferred, genuine open gap | Not attempted in v1 — needs its own bounded-query design before it's cheap enough to run; don't add until that design exists. |
| Semantic (embeddings-based) clustering | deferred, tracked at [issue #19](https://github.com/qte77/agentic-signal-to-concept/issues/19) | Not built — keyword/bigram frequency hasn't yet demonstrably missed a real cluster; build once there's a concrete miss to point at. |
| Trend-aware / recurring discovery runs | deferred, tracked at [issue #20](https://github.com/qte77/agentic-signal-to-concept/issues/20) | Not built — only one real discovery run exists; needs 2-3 more over real elapsed time before a velocity signal is calibratable. |
| Worktree `.env` gap: gitignored files aren't carried by `git worktree add` | **shipped** (2026-09-04) | `AGENTS.md`'s "Running more than one category at once" section now states copying `.env` into each worktree as an explicit pre-dispatch step, before any subagent is launched into it — closes the timing race this run hit (all three categories' `complaint-miner` runs found ProductHunt blocked because `.env` was copied in mid-run, after each had already checked). |
| Source breadth: GitHub added to `signal-discoverer` | **shipped in spec** (2026-09-04), not yet run for real | `signal-discoverer.md` step 3 added — broad `created:>X stars:>N sort:stars-desc` GitHub search, no new access/ToS work (reuses `build-pattern-scanner`'s existing `gh` CLI auth pattern exactly). Researched and ranked against 3 alternatives (GitHub's own unofficial "trending" page — no official API, not worth pursuing since this achieves the same signal legitimately; BetaList — no explicit ban but no public API/feed either, real next step is asking directly, not scraping; GH Archive — real candidate, but needs BigQuery credentials or raw-JSON parsing, bigger lift, tracked below). Next real discovery run should confirm it works as specified. |
| Source breadth: BetaList outreach | deferred, owner | No explicit ToS ban found, but no public API/RSS either — a real email/outreach step, not an agent task, same shape as the still-open TrustMRR ask from `docs/plans/0001-concept.md`. |
| Source breadth: GH Archive | deferred, genuine next tier | Free, public, no usage restrictions found — the full GitHub public event firehose, richer than search-indexed repos alone. Needs BigQuery credentials (owner-gated) or raw hourly-JSON parsing (engineering lift) before it's addable; not attempted in v1. |

## Handoff

See `docs/handoffs/0003-broad-discovery.md` for the onboarding-shaped version of this table and
what's next in order.
