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
**Source breadth done**: GitHub added as a third discovery source (no new access/ToS cost, reuses
`build-pattern-scanner`'s existing `gh` CLI auth), researched and ranked against BetaList (no ban,
no API — outreach candidate) and GH Archive (real next tier, needs BigQuery or raw-JSON parsing).
**Second discovery run done**, first to exercise GitHub for real: 9 categories (up from 8), a new
ethical-boundary case caught and correctly excluded (a single project's plugin ecosystem, not
independent convergence), and two real corrections folded back into the spec (PH's `first`-capped-
at-20 behavior; explicit window-overlap reporting against the prior run, since the two runs turned
out ~99% overlapping). Archived at `examples/2026-09-04T081918Z-discovery/`.
**Alpha/beta reframing (2026-09-05)**: user pushed back that discovery's top-ranked categories are
"already crowded" and asked for decision-ready alpha/beta picks instead of more categories. Root
cause identified: `signal-discoverer`'s three sources (Show HN titles, PH launches, GitHub repos) are
all supply-side/build signal — discovery's rank is a build-volume ranking, which is why the
calibration check above never correlated with vertical yield (yield is driven by complaint intensity,
a variable discovery never measured). A same-session experiment to close that gap cheaply (HN
Algolia comment-count as a demand proxy) was tested and falsified — see the new remaining-work row
below. **Working conclusion**: alpha detection stays a two-stage process, not a one-shot automation —
horizontal flags low-count/single-source-leaning categories as cheap tentative alpha candidates (no
new implementation needed, just reading the existing ranked list differently); vertical mode's real
sourced quotes remain the only reliable demand confirmation. Beta (a differentiated angle inside an
already-crowded category) is vertical-only by nature — it requires reading what existing builds
actually do, which no aggregate count can reveal.
**Next real step**: pick a category using the low-count/single-source lens above (see remaining-work
table) rather than the raw top-of-list rank, or wait longer before a third discovery run (to get real
window separation per the overlap lesson above).
**Source breadth, round 2 (2026-09-05)**: three research passes tested whether additional public
registries/platforms offer signal this pipeline doesn't already have. Startup/indie-project
registries (`startups.gallery`/`betalist.com`/`uneed.best`/`wip.co`) were researched and ranked for
ROI/feasibility but not wired into any spec (see remaining-work table). Hackathon content was tested
against Devpost/cv.inc/AGI House — only `cv.inc` cleared ToS and was added as a fourth source to
`signal-discoverer.md` and `build-pattern-scanner.md`; GitLab and Codeberg were also checked as
substitutes for the already-blocked Bolt.new/v0/Lovable ecosystem-mention search and are both
excluded (GitLab's separate API Terms of Use bans bulk/systematic scraping; Codeberg's robots.txt
explicitly disallows `anthropic-ai`/`ClaudeBot`/`Claude-Web`) — GitHub's existing `topic:hackathon`/
`topic:bolt`/`topic:lovable` queries remain the only compliant path for that signal. A further pass
surveying more candidate sources (SourceHut, MLH, Kaggle, Hugging Face, YC directory, Stack
Overflow/Stack Exchange) landed: **Hugging Face Spaces added as a fifth source** (public
`/api/spaces` endpoint, zero scrape/AI-training language, independently verified — richer sampled
content than cv.inc's own example). SourceHut, Kaggle, YC directory, and Stack Overflow/Stack
Exchange are all confirmed blocked (Kaggle and Stack Exchange each needed a second pass to catch a
separately-incorporated Acceptable Use Policy the first pass missed). MLH clears ToS but wasn't added
— directory layer only, real content lives on each event's separate, unchecked external domain.

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
| Source breadth: GitHub added to `signal-discoverer` | **shipped** (2026-09-04) | `signal-discoverer.md` step 3 added — broad `created:>X stars:>N sort:stars-desc` GitHub search, no new access/ToS work (reuses `build-pattern-scanner`'s existing `gh` CLI auth pattern exactly). Researched and ranked against 3 alternatives (GitHub's own unofficial "trending" page — no official API, not worth pursuing since this achieves the same signal legitimately; BetaList — no explicit ban but no public API/feed either, real next step is asking directly, not scraping; GH Archive — real candidate, but needs BigQuery credentials or raw-JSON parsing, bigger lift, tracked below). Confirmed working in the second real discovery run: `stars:>500` (216 repos) after `stars:>250` returned too many (510). |
| Source breadth: BetaList outreach | deferred, owner | No explicit ToS ban found, but no public API/RSS either — a real email/outreach step, not an agent task, same shape as the still-open TrustMRR ask from `docs/plans/0001-concept.md`. |
| Source breadth: GH Archive | deferred, genuine next tier | Free, public, no usage restrictions found — the full GitHub public event firehose, richer than search-indexed repos alone. Needs BigQuery credentials (owner-gated) or raw hourly-JSON parsing (engineering lift) before it's addable; not attempted in v1. |
| Second real `signal-discoverer` run (first with GitHub) | **shipped** (2026-09-04) | 9 categories promoted (up from 8), 5 rejected. New ethical-boundary case caught and correctly excluded (33 GitHub repos all plugins for one upstream project, `deepseek-ai/deepseek-harness` — not independent convergence) — worked example folded into the spec's ethical-boundary section. Two real corrections folded back into the spec: PH's `first` param silently caps at 20/page regardless of requested value (real daily volume 400-1,300+, so PH sampling is ~2-3% of the window, not exhaustive); explicit window-overlap reporting against the prior run added as a new step, since this run's window turned out ~99% overlapping with the first run's (~12h apart) — most category counts were the same corpus re-measured, one category (job-search/interview-prep tooling) was genuinely new. Archived at `examples/2026-09-04T081918Z-discovery/`. |
| Demand-side count proxy for horizontal ranking (alpha/beta reframing) | **investigated, rejected** (2026-09-05) | Tested whether a cheap per-category HN Algolia comment-count (`nbHits`, no quote reading) could serve as a demand signal to pair against discovery's existing supply counts, producing a demand:supply ratio at the horizontal stage instead of only post-hoc at vertical stage. Falsified after 3 of 9 planned queries: HN's Algolia `query` param does relevance-ranked OR-matching over individual words, not phrase or boolean-AND matching — `query=memory` returned 4,104 hits dominated by unrelated senses (RAM, human memory); `query=agent memory` (368 hits) and the quoted `query="agent memory"` (same non-adjacent match) both still top-hit an "Ask HN: Who is hiring?" comment listing "agents" and "memory" as unrelated resume keywords. `nbHits` cannot distinguish category-relevant complaint signal from incidental word co-occurrence without per-hit reading, which erodes the cost savings the approach was chasing. **No spec change made** — folded into `signal-discoverer.md`'s Scope section as a rejected approach so it isn't re-attempted. Resolution: alpha detection stays two-stage (horizontal flags low-count/single-source-leaning categories cheaply; vertical confirms demand via real sourced quotes), not a one-shot horizontal automation. |
| Startup/indie-project registries as an idea/ROI source | **researched, not wired in** (2026-09-05) | `startups.gallery`/`betalist.com`/`uneed.best`/`wip.co` all cleared ToS (raw-verified independently, not left as WebFetch summaries); 6 niches ranked by a stated ROI/feasibility proxy. Not added to any spec — per-listing content is thinner than HN/GitHub (taglines, occasional vote counts, rarely real traction data), so a dedicated pass would mostly re-run this same manual read; `uneed.best`'s sanctioned MCP/API path is the one piece worth real integration work if this is revisited. Archived at `examples/2026-09-05T204416Z-startup-registries/`. |
| cv.inc / cerebralvalley.ai hackathon listings added as a fourth source | **shipped** (2026-09-05) | `signal-discoverer.md` step 4 and `build-pattern-scanner.md` step 5 both added — event/theme clustering at discovery, public-gallery-as-convergence-evidence at vertical stage. Of three hackathon platforms checked (Devpost, cv.inc, AGI House), only cv.inc clears ToS; Devpost and AGI House are both confirmed, unambiguous blocks (exact clauses in both spec files). Verified three independent ways (raw fetch, independent re-fetch, `--tier patchright --wait-until networkidle` render cross-check) before landing. Archived at `examples/2026-09-05T213930Z-hackathon-signal-research/`. |
| GitLab/Codeberg as substitutes for Bolt.new/v0/Lovable mention-search | **checked, excluded** (2026-09-05) | Tested as compliant channels for the same GitHub-style topic/mention search `build-pattern-scanner.md` step 4 already does for Bolt/v0/Lovable ecosystem signal. Both excluded: GitLab's separate API Terms of Use (`handbook.gitlab.com/handbook/legal/api-terms/`, Section 1.3.9) bans "bulk collection or scraping of information, including for repeated or systematic bulk exporting GitLab API Data"; Codeberg's robots.txt explicitly names `anthropic-ai`/`ClaudeBot`/`Claude-Web` in its disallow list. GitHub's existing `topic:hackathon`/`topic:bolt`/`topic:lovable` queries remain the only compliant path for this signal. Folded into both spec files so neither is re-attempted. |
| Hugging Face Spaces added as a fifth source | **shipped** (2026-09-05) | `signal-discoverer.md` step 5 and `build-pattern-scanner.md` step 6 both added — public `/api/spaces` JSON endpoint, zero scrape/crawl/bot/AI-training language in ToS (independently raw-verified, not left as a subagent summary). One sampled hackathon org (603 Spaces, 22 collections) is richer than cv.inc's own 102-team example. Archived at `examples/2026-09-05T233231Z-additional-sources-research/`. |
| SourceHut / Kaggle / YC directory / Stack Overflow-Stack Exchange as additional sources | **checked, excluded** (2026-09-05) | All confirmed blocked at primary source: SourceHut (robots.txt names `ClaudeBot` explicitly; ToS separately bans ML-training use); Kaggle (ToS + incorporated Acceptable Use Policy ban crawling unconditionally; official API has no separate terms, defers to the same ban); YC company directory (site-wide ToS bans "data mining, robots, scraping"; doesn't reach this pipeline's existing HN sourcing via `hn.algolia.com`); Stack Overflow/Stack Exchange (robots.txt blanket-disallows with `ai-train=no`; incorporated Acceptable Use Policy bans automated collection "for developing, building, training... any generative AI... tool" from "any Network website or Service" — reaching its API too). Kaggle and Stack Exchange each needed a second pass to catch the incorporated AUP the first pass missed (caught by an internal advisor review before finalizing). Folded into both spec files so none are re-attempted. |
| Major League Hacking as a source | **checked, not worth building against** (2026-09-05) | Clears ToS/robots.txt cleanly, but its own site (`mlh.com`) is a directory/certification layer only — sponsor challenge text, judging criteria, and project galleries all live on each member hackathon's separately-operated external domain (e.g. `hackrice.com`), each needing its own unchecked ToS. Flagged in both spec files as researched-and-declined, not silently dropped. |

## Handoff

See `docs/handoffs/0003-broad-discovery.md` for the onboarding-shaped version of this table and
what's next in order.
