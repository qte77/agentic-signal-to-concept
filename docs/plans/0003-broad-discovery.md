# 0003 — agentic-signal-to-concept: categoryless discovery + parallel category runs

## Current status and what's next, in order

The user asked twice in one session (2026-09-04) for genuinely categoryless discovery, not a
hand-picked list of categories to choose from. This arc adds a new Phase 0 (`signal-discoverer`)
that does one broad, bounded pull (now five sources: Show HN, ProductHunt, GitHub, cv.inc hackathon
listings, Hugging Face Spaces — no topic filter) and clusters it into candidate categories — the
existing 3-agent pipeline (`docs/plans/0002-signal-to-concept-v1.md`) still runs per-category
afterward, unchanged. Parallel execution across chosen categories uses git worktrees, this repo's
own standing rule that was never actually load-bearing until this arc (every prior run used the
shared working tree with no real collision, since a single category's two Phase-1 agents write to
two different filenames — N *categories* running concurrently would collide on the single shared
`config/scope.md`).

1. ~~Write `.claude/agents/signal-discoverer.md`~~ — done 2026-09-04.
2. ~~Add `discovery/README.md` + `.gitignore` entry~~ — done 2026-09-04.
3. ~~Update `AGENTS.md`: Phase 0 + worktree-requirement~~ — done 2026-09-04.
4. ~~Run `signal-discoverer` for real once~~ — done 2026-09-04: Show HN fully covered (3,670 titles,
   6-bucket pull), ProductHunt narrower than intended (one calendar day, root-caused and folded back
   into the spec — see the remaining-work table). 8 categories promoted, 2 rejected as grab-bags.
   Archived at `examples/2026-09-04T060617Z-discovery/`.
5. ~~Owner gate~~ — done 2026-09-04: user confirmed the recommended default (#1 agent memory, #3
   terminal/session UX, #5 freelancer finance), and separately asked for horizontal/vertical to be
   named pipeline modes (done — see `AGENTS.md`'s "Two modes" section).
6. ~~Parallel worktree runs~~ — done 2026-09-04: three worktrees
   (`../asc-agent-memory`, `../asc-terminal-ux`, `../asc-freelancer-finance`), full 3-agent pipeline
   in each, merged back via one PR per category. Produced 3 candidates: `Contextlint` (agent-memory),
   `Vigil`/`Overflow` (terminal-ux), `Freehold Finance`/`Upfront Terms` (freelancer-finance). Real
   gap hit and fixed: `.env` isn't carried by `git worktree add` (gitignored) — now an explicit
   pre-dispatch step in `AGENTS.md`.
7. ~~Calibration check~~ — done 2026-09-04, per explicit user request: compared discovery's rank to
   actual vertical yield. Did not hold on raw volume (see the remaining-work table for the full
   comparison) — a genuinely useful negative result for interpreting future discovery ranks, not a
   bug to fix.
8. ~~Source breadth~~ — done 2026-09-04: GitHub added as a third discovery source (no new access/ToS
   cost, reuses `build-pattern-scanner`'s `gh` CLI auth exactly). BetaList and GH Archive researched
   and tracked as next-tier/owner-gated rather than built (see the remaining-work table).
9. ~~Second real `signal-discoverer` run (first with GitHub)~~ — done 2026-09-04: 9 categories (up
   from 8), 5 rejected. Caught and correctly excluded a new kind of finding — a single project's
   plugin ecosystem (33 repos, all for `deepseek-ai/deepseek-harness`) that looked cluster-shaped but
   wasn't independent convergence; folded into the spec's ethical-boundary section as a worked
   example. Two more corrections folded back: PH's `first`-capped-at-20 behavior, and explicit
   window-overlap reporting (this run's window turned out ~99% overlapping the first run's, ~12h
   apart — most counts were the same corpus re-measured, one category was genuinely new). Archived
   at `examples/2026-09-04T081918Z-discovery/`.
10. ~~Alpha/beta reframing~~ — done 2026-09-05, per explicit user pushback that discovery's top
    categories are "already crowded." Root cause: discovery's three sources (at the time) were all
    supply-side/build signal, so its rank measures build volume, not demand — which is also why the
    calibration check (item 7) never correlated with vertical yield. A cheap-automation fix (HN
    Algolia comment-count as a demand proxy) was tested and falsified — see the remaining-work table
    for the exact queries and result. Resolution: alpha detection stays two-stage — horizontal flags
    low-count/single-source-leaning categories as cheap tentative alpha candidates (no new
    implementation needed), vertical mode's sourced quotes remain the only real demand confirmation.
    Beta (a differentiated angle inside an already-crowded category, e.g. terminal-ux's unaddressed
    "bookmark before crash" ask among 6 saturated `claude --resume` wrappers) is vertical-only by
    nature — no aggregate count can reveal it.
11. **Next real step, not yet decided**: two discovery runs now exist, giving real material toward
    issue #20 (trend-aware recurring runs) once there's genuine time separation between runs — this
    run's own note says spacing future runs further apart (or diffing `objectID`s against the prior
    run) is needed before a velocity signal means anything. Otherwise: pick a category using the
    low-count/single-source lens from item 10 — but filter out categories run 2's own text already
    flags as heterogeneous form-factors rather than single problems (#3 MCP tooling, #5 local-first,
    #9 macOS menu-bar all carry that explicit caveat, so their low counts don't mean low supply on a
    real single problem). Run 2's #4 (job-search/interview-prep tooling — 21 HN + 12 PH, cross-source,
    single-problem-shaped, and genuinely new this run, not a re-measurement of run 1) or #8
    (habit-tracking/journaling — 21 HN + 8 PH, same shape) are the cleaner candidates by this lens. Or
    wait for real elapsed time before a third discovery run. Awaiting user direction.
12. ~~Source breadth round 2~~ — done 2026-09-05: cv.inc/cerebralvalley.ai added as a fourth source
    to both `signal-discoverer.md` and `build-pattern-scanner.md` (only one of three hackathon
    platforms checked — Devpost, cv.inc, AGI House — that clears ToS; verified three independent
    ways). GitLab and Codeberg checked as Bolt/v0/Lovable mention-search substitutes and excluded
    (GitLab's separate API Terms of Use bans bulk/systematic scraping; Codeberg's robots.txt names
    `anthropic-ai`/`ClaudeBot`/`Claude-Web` explicitly) — GitHub's existing `topic:hackathon`/
    `topic:bolt`/`topic:lovable` queries remain the only compliant path. Startup/indie-project
    registries (`startups.gallery`/`betalist.com`/`uneed.best`/`wip.co`) researched and ranked but not
    wired into any spec — thinner per-listing content than HN/GitHub. `.gitignore` broadened from
    `discovery/*-categories.md` to `discovery/*.md`. Both research passes archived under `examples/`.
13. ~~Further source-breadth pass~~ — done 2026-09-05: Hugging Face Spaces added as a fifth source to
    both spec files (public `/api/spaces` endpoint, zero scrape/AI-training language, independently
    verified; one sampled hackathon org's 603 Spaces beat cv.inc's own 102-team example). SourceHut,
    Kaggle (website + API), the YC company directory, and Stack Overflow/Stack Exchange (website +
    API) are all confirmed blocked — Kaggle and Stack Exchange each needed a second pass to catch an
    incorporated Acceptable Use Policy the first pass missed. Major League Hacking clears ToS but
    wasn't added (directory layer only, real content on unchecked external per-event domains).
    Archived at `examples/2026-09-05T233231Z-additional-sources-research/`.
14. ~~Patent databases as an idea source~~ — researched 2026-09-07, not wired in. Both hypotheses
    (expired patents as public-domain ideas; active filings as demand signal) confirmed real against
    sampled content, but not compliantly automatable as a discovery step — the only credential-free
    paths allow known-patent-number lookups only, not category/keyword search. Real decision point
    surfaced, not defaulted: USPTO's ODP now requires ID.me identity verification for an API key
    (materially higher friction than any credential this pipeline has used); EPO OPS is free/
    self-service if this is ever pursued. See the remaining-work table for full detail. Archived at
    `examples/2026-09-07T043304Z-patent-signal-research/`.
15. ~~Third real `signal-discoverer` sweep~~ — done 2026-09-07, first to exercise all five sources.
    90.4% window overlap with run 2 (vs. ~99% between runs 1/2) — enough separation to isolate a
    genuine ~2.87-day fresh tail. 9 categories promoted (same count as run 2, all of run 2's
    categories still present). **Standout finding**: cv.inc surfaced two independent MongoDB-sponsored
    hackathons explicitly themed "agent memory/persistent context" (one completed in-window with a
    94-team public gallery, independently verified — direct sponsor-stated-demand confirmation of the
    #1 category HN/GitHub/PH have now surfaced three runs straight). Hugging Face Spaces came back
    genuinely thin for this window (verified nine ways, not a tooling failure) — a real finding about
    window-dependence, folded into the spec. Two methodology corrections folded back into
    `signal-discoverer.md`: cv.inc's reliable entry point (`llms.txt` → `sitemap-md.xml`, not a
    guessed events path) and a new Output requirement to save raw per-source ID lists (closes the
    gap that blocked a precise repo-level diff against run 2 this time). Archived at
    `examples/2026-09-07T050600Z-discovery/`.
16. ~~Vertical run on discovery category #4 (job search/application tooling)~~ — done 2026-09-11: the
    user picked category #4 from run 2/3's ranked list (cross-source, confirmed genuinely new in run 2,
    unchanged ranking through run 3 — not yet vertically explored, unlike category #1). Full 3-agent
    pipeline run for real: `complaints-findings.md` (HN only, 7 patterns, 24 quotes — ProductHunt
    blocked by a new subagent-env-isolation gap, distinct from the worktree `.env`-copying issue, now
    documented in `AGENTS.md`) and `builds-findings.md` (GitHub + Show HN + HF Spaces corroboration, 5
    patterns, 35 cited instances — the densest convergence this pipeline has found to date, including
    two GitHub repos independently verified at 71,287 and 41,844 stars). Synthesized candidate
    **Signoff** (`candidates/2026-09-11T220526Z-job-search-tooling-candidate.md`) draws exactly one
    candidate from five distinct build shapes the evidence surfaced — three heavily-saturated shapes
    (resume tailoring, tracking dashboards, interview prep, 23 combined instances with zero matching
    complaints-side pull) were read as a saturation warning and explicitly excluded rather than
    inflated into extra candidates. Archived at `examples/2026-09-11T220526Z-job-search-tooling/`.
17. **Next real step, not yet decided**: category #1 (agent memory/persistent context) is now
    confirmed across four sources over three runs, arguably past needing further discovery-stage
    validation — a strong candidate for its own vertical-mode run if not already superseded by the
    existing `Contextlint`/`Memolint` candidate from the first vertical batch. Category #8
    (habit-tracking/journaling) is the next clean single-problem-shaped candidate by the low-count/
    single-source lens, not yet explored. Otherwise: keep watching for genuinely new categories or wait
    longer before a fourth discovery run. Awaiting user direction.

## Watch-outs

- **Discovery output is not sourced-quote evidence** — it's aggregate counts/clusters, a triage step.
  Don't run `scripts/verify_sourcing.py` against it and don't expect it to pass the same bar as a
  `findings/*.md` file; state this distinction in the spec so nobody mistakes a missing citation for
  a bug.
- **Ethical boundary applies at discovery too** — even at the "just clustering titles" stage, never
  let a cluster collapse into naming one specific project as the reason a category looks promising.
- **The worktree rule is finally load-bearing, not decorative** — get the per-worktree
  `config/scope.md` write right (each worktree needs its own, since `config/scope.md` is gitignored
  and won't exist in a fresh worktree checkout by default).
- **This repo's Bash environment intentionally denies `ls`/`find`/version-probes** while allowing
  substantive commands (`gh api`, `polyfetch fetch`, `uv run`) — dispatched agents need to be told
  this explicitly and to use Read/Write tools for file checks instead. See
  `docs/plans/0002-signal-to-concept-v1.md` item 6 for the fuller note — don't duplicate it there,
  just don't forget it when briefing new subagents for this arc either.
- **Raw `curl` for external network calls is also denied by this sandbox's classifier** (confirmed
  2026-09-05) — use `polyfetch-scrape` for a raw fetch instead. A saved tool-output file also can't
  be searched with `grep` in this sandbox — use a Python one-liner (`open(path).read()` +
  `str.find`/keyword search) instead.
- **This plan file used to pair with a separate `docs/handoffs/0003-broad-discovery.md`** — merged
  into this single file 2026-09-07 per `unattended-execution.md`'s updated one-file-per-arc rule.
- **A backgrounded shell process (`nohup ... &`) does not survive to the next Bash call in this
  sandbox** — confirmed 2026-09-07: the third discovery run lost a full 30-query ProductHunt pull
  cycle discovering this the hard way (the shell that launched it was torn down before it could
  finish). Run long external-API pulls synchronously with a generous `timeout` instead of
  backgrounding them.
- **Archive each run's raw per-source ID lists, not just the narrative categories file** — the
  third run couldn't do a precise repo-level diff against the second because the second's raw
  GitHub list was never saved anywhere in this repo. `signal-discoverer.md`'s Output section now
  asks for a `<date-time-iso>-raw-ids.json` alongside the categories file going forward.

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

- **`signal-discoverer`** (new agent) — a single broad, lightly-filtered pull across five sources
  (Show HN, ProductHunt, GitHub, cv.inc, Hugging Face Spaces — all unfiltered by topic, recent
  window), clustered bottom-up into emergent candidate categories. Deliberately bounded in v1, not
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
- **cv.inc / cerebralvalley.ai (added 2026-09-05)**: hackathon event/gallery listings, no ToS or
  robots.txt restriction — the only one of three hackathon platforms checked (Devpost, cv.inc, AGI
  House) that clears. See `signal-discoverer.md` step 4 for full detail.
- **Hugging Face Spaces (added 2026-09-05)**: public `/api/spaces` JSON endpoint, no ToS or
  robots.txt restriction. See `signal-discoverer.md` step 5 for full detail.
- **Explicitly deferred, not attempted in v1**: a broad HN *comment* firehose (complaint-signal
  search without a category term) — HN's Algolia API doesn't support the kind of query that would
  make that bounded and cheap in one pass; see this plan's Scope section above. Do not attempt
  Reddit, app-store, or GitHub-issue discovery either — each needs its own access/ToS check first,
  the same discipline `docs/plans/0001-concept.md` §1–§3 already applied to the existing pipeline's
  sources. GH Archive (`gharchive.org` — free, public, no usage restrictions found, the full GitHub
  public event firehose) is a real candidate next source but needs either BigQuery credentials
  (owner-gated) or raw hourly-JSON parsing (a real engineering lift) — tracked, not attempted in v1.
  GitLab, Codeberg, SourceHut, Kaggle, the YC company directory, and Stack Overflow/Stack Exchange
  were all checked and excluded (see the remaining-work table); Major League Hacking clears ToS but
  wasn't added (thin content, see the same table).
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
- `.gitignore` — add `discovery/*.md` (gitignored working evidence, same treatment as
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
| N parallel category runs via git worktrees | **shipped** (2026-09-04) | Three worktrees (`../asc-agent-memory`, `../asc-terminal-ux`, `../asc-freelancer-finance`), full 3-agent pipeline run in each — first real exercise of the standing worktree rule. Real gap found and fixed: `git worktree add` doesn't carry gitignored files, so a fresh worktree has no `.env` — all three categories' `complaint-miner` runs hit ProductHunt blocked as a result (timing: `.env` was copied in mid-run, after each had already checked). Consistent across all three, so no cross-category inconsistency, but worth fixing properly before the next multi-worktree run (see watch-outs above). Findings/candidates archived at `examples/2026-09-04T070136Z-agent-memory/`, `examples/2026-09-04T070138Z-terminal-ux/`, `examples/2026-09-04T070139Z-freelancer-finance/`; candidates at matching `candidates/*.md` paths. Merged back via one PR per category. |
| Calibration check: does discovery's rank predict vertical yield? | **shipped** (2026-09-04) | Compared discovery's qualitative rank (#1 > #3 > #5) against actual Phase 1 output volume. Result did **not** hold on raw volume: #3 terminal-ux (11 patterns, 72 sourced items, 50 build instances) outproduced #1 agent-memory (10 patterns, 71 items, 37 instances); #5 freelancer-finance had the lowest volume (10 patterns, 53 items, 28 instances) but produced the single most acute finding of any category — a PE-rollup pricing-shock complaint with 100+ comments and direct switching intent. **Read (n=3, suggestive not conclusive):** discovery's rank is a reasonable coarse breadth filter but doesn't predict richness *or* acuteness reliably — it measures convergence breadth, not the intensity of any single complaint thread, a distinct dimension it isn't designed to catch. Not a build item; noted here as calibration context for interpreting future discovery ranks. |
| Broad HN comment-firehose discovery | deferred, genuine open gap | Not attempted in v1 — needs its own bounded-query design before it's cheap enough to run; don't add until that design exists. |
| Semantic (embeddings-based) clustering | deferred, tracked at [issue #19](https://github.com/qte77/agentic-signal-to-concept/issues/19) | Not built — keyword/bigram frequency hasn't yet demonstrably missed a real cluster; build once there's a concrete miss to point at. |
| Trend-aware / recurring discovery runs | deferred, tracked at [issue #20](https://github.com/qte77/agentic-signal-to-concept/issues/20) | Not built — only two real discovery runs exist, ~12h apart (~99% window overlap) — needs real elapsed time before a velocity signal is calibratable. |
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
| Third real `signal-discoverer` run (first with all 5 sources) | **shipped** (2026-09-07) | 9 categories promoted (same as run 2, all present again). cv.inc surfaced two MongoDB-sponsored "agent memory" hackathons (one with a 94-team public gallery, independently verified) — strongest cross-source confirmation of category #1 across three runs. Hugging Face came back thin for this window (verified nine ways). Two corrections folded back into `signal-discoverer.md`: cv.inc's `llms.txt`→`sitemap-md.xml` entry path, and a new Output requirement to archive raw per-source IDs (closes a diffing gap this run hit against run 2). Archived at `examples/2026-09-07T050600Z-discovery/`. |
| Patent databases (old/new/expired) as an idea source | **researched, not wired in** (2026-09-07) | Both hypotheses (expired patents as public-domain ideas; active filings as demand/investment signal) confirmed real and distinct from this pipeline's existing sources against real sampled content (Apple's US8046721B2, expired; US11556230B2, active to 2035; a 2024 Salesforce EPO filing) — CPC codes are a stronger clustering primitive than GitHub/PH topics. **Not built as a discovery step**: the only credential-free paths (`patents.google.com` individual pages, EPO's Publication Server) allow known-patent-number lookups only — robots.txt explicitly disallows Google Patents' search surface, so neither can *discover* candidates by category, only enrich a number already in hand. Every path that *can* search (USPTO ODP, EPO OPS, Google Patents BigQuery) is owner-gated. **Real decision point for the repo owner, not a routine `.env` addition**: USPTO's Open Data Portal now requires a personal USPTO.gov account with MFA plus ID.me identity verification for an API key (confirmed, effective 2026-06 to 2026-08) — a materially higher-friction gate than any credential this pipeline has asked for. EPO OPS (free, self-service OAuth2, ~4M req/month, non-commercial/eval use) is the lower-friction alternative if this is ever pursued. Recommendation: direct API access over any MCP server (several exist, none official, none remove the underlying gate). Archived at `examples/2026-09-07T043304Z-patent-signal-research/`. |
