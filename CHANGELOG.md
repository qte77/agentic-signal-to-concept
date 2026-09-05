# Changelog

All notable changes to agentic-signal-to-concept. Format follows
[Keep a Changelog](https://keepachangelog.com/en/1.1.0/).

## [Unreleased]

### Added

- `cv.inc`/`cerebralvalley.ai` hackathon listings added as a fourth source to both
  `signal-discoverer.md` (Phase 0: event/theme clustering, sponsor "what we're looking for" language
  as demand signal) and `build-pattern-scanner.md` (Phase 1: public hackathon galleries as
  compressed-convergence build evidence, scoped to `config/scope.md`'s category) — the only one of
  three hackathon-listing platforms checked (Devpost, cv.inc, AGI House) that clears ToS; Devpost and
  AGI House are both confirmed, unambiguous blocks (exact clauses cited in the new spec sections).
  Verified three independent ways (raw fetch, independent re-fetch, patchright/networkidle render
  cross-check) before landing. Also checked and excluded GitLab (its separate API Terms of Use, not
  just its main ToS, bans "bulk collection or scraping... systematic bulk exporting" of API data) and
  Codeberg (robots.txt explicitly names `anthropic-ai`/`ClaudeBot`/`Claude-Web` in its disallow list)
  as substitutes for the already-ruled-out Bolt.new/v0/Lovable ecosystem-mention search — GitHub's
  existing `topic:hackathon`/`topic:bolt`/`topic:lovable` queries (already documented in
  `build-pattern-scanner.md` step 4) remain the only compliant path for that signal. Two research
  passes archived: `examples/2026-09-05T204416Z-startup-registries/` (startup/indie-project
  registries as an ROI/idea source — 4 sources checked, all cleared, 6 niches ranked) and
  `examples/2026-09-05T213930Z-hackathon-signal-research/` (the hackathon-content research itself).
  `.gitignore` broadened from `discovery/*-categories.md` to `discovery/*.md` so all
  `signal-discoverer`-adjacent research output (not just `-categories.md` runs) gets consistent
  gitignore-then-archive-if-worthwhile treatment.
- Alpha/beta reframing of the discovery→vertical pipeline (2026-09-05): root-caused why discovery's
  rank never predicted vertical yield (the earlier calibration check's open question) — all three
  `signal-discoverer` sources are supply-side/build signal, so its rank measures build volume, never
  demand. Tested and rejected a cheap fix (HN Algolia comment-count as a demand proxy): HN's Algolia
  `query` parameter does relevance-ranked OR-matching over individual words, not phrase/boolean-AND
  matching, so `nbHits` can't separate category-relevant complaint signal from incidental word
  co-occurrence (`query=agent memory` top-hit an unrelated "Who is hiring?" comment). Folded into
  `signal-discoverer.md`'s Scope section so it isn't re-attempted. Resolution: alpha detection stays
  two-stage — horizontal cheaply flags low-count/single-source-leaning categories (filtering out ones
  already flagged as heterogeneous form-factors, not single problems); vertical mode's sourced quotes
  remain the only real demand confirmation. Beta (a differentiated angle inside an already-crowded
  category) is vertical-only by nature. Documented in `docs/plans/0003-broad-discovery.md`'s
  remaining-work table and `docs/handoffs/0003-broad-discovery.md`.
- Second real `signal-discoverer` run, the first to exercise GitHub as a source: 9 candidate
  categories (up from 8), 5 rejected. Caught and correctly excluded a new kind of finding — a
  33-repo GitHub topic cluster that looked independently-convergent but was entirely plugins for one
  upstream project (`deepseek-ai/deepseek-harness`), not independent teams — folded into
  `signal-discoverer.md`'s ethical-boundary section as a worked example. Two further corrections
  folded back into the spec: ProductHunt's `first` parameter silently caps at 20/page regardless of
  the value requested (real daily volume is 400-1,300+ posts, so PH sampling is ~2-3% of a window,
  not exhaustive); and an explicit window-overlap check against the prior discovery run, since this
  run's window turned out ~99% overlapping the first run's (~12h apart) — most category counts were
  the same corpus re-measured, one category (job-search/interview-prep tooling) was genuinely new.
  Archived at `examples/2026-09-04T081918Z-discovery/`.
- `signal-discoverer` gained a third source, GitHub (broad `created:>X stars:>N sort:stars-desc`
  search, no `topic:` filter) — no new access/ToS work needed, reuses `build-pattern-scanner`'s
  existing authenticated `gh` CLI pattern exactly. Researched and ranked against three alternatives:
  GitHub's own "trending" page (no official API, not pursued), BetaList (no ban but no public
  API/feed either — a real outreach step, not scraping), and GH Archive (a real next-tier candidate,
  needs BigQuery credentials or raw-JSON parsing — tracked, not built yet).
- First real vertical-mode runs sourced directly from horizontal discovery: three categories
  (agent/coding-agent memory, terminal/session UX for coding agents, freelancer finance/expense
  tools) run in parallel via dedicated git worktrees — the first real exercise of this repo's
  standing worktree rule. Three new concept candidates: `Contextlint`, `Vigil`/`Overflow`, `Freehold
  Finance`/`Upfront Terms`. `AGENTS.md` now names horizontal (discovery) and vertical (per-category
  deep-dive) as explicit pipeline modes. A calibration check compared discovery's predicted category
  rank against actual vertical-mode yield — the ranking did not hold on raw volume, a useful negative
  result recorded in `docs/plans/0003-broad-discovery.md` rather than treated as a bug. Two follow-on
  ideas from that check (semantic clustering, trend-aware recurring discovery runs) were deferred
  with tracking issues (#19, #20) rather than built immediately.
- New Phase 0, `signal-discoverer` (`docs/plans/0003-broad-discovery.md`): a broad, unfiltered pull
  across Show HN and ProductHunt that surfaces candidate problem-space categories, rather than
  requiring a category named up front. First real run found 8 candidate categories (2 additional
  clusters explicitly rejected as grab-bags), archived at
  `examples/2026-09-04T060617Z-discovery/`. Two real corrections from that run folded back into the
  spec: HN's actual 30-day volume (3,000-4,000+ titles, not "a few hundred") and ProductHunt's
  daily-launch-cohort timestamp behavior plus its working `postedAfter`/`postedBefore` date filter
  (the spec had wrongly said no date-range filter exists). `AGENTS.md` updated with the new Phase 0
  step and the worktree requirement for running multiple categories in parallel.

- Second full-pipeline run against the `pkm-tools` scope (2026-09-04), the first with
  `PRODUCTHUNT_API_TOKEN` configured — ProductHunt genuinely contributed evidence for the first
  time. Independently re-derived candidate `candidates/2026-09-04T043009Z-pkm-tools-candidate.md`
  ("Recallect") from fresh evidence rather than anchoring on the prior run's candidate, landing on
  the same concept but with a materially different, better-corroborated trust pillar. Archived at
  `examples/2026-09-04T043009Z-pkm-tools/`.
- `build-pattern-scanner`'s GitHub calls now authenticated via the `gh` CLI instead of unauthenticated
  `polyfetch` requests — corrects a rate-limit claim in `.env.example` (GitHub's *search* API, which
  this pipeline actually calls, is limited to 10/min unauthenticated / 30/min authenticated, not the
  core API's 5,000/hour, confirmed live via `gh api rate_limit`).
- `config/scope.example.md` gained an optional "Query terms used" field so re-runs of the same scope
  can distinguish real population growth from a run simply trying different search terms.
- First real execution of the full 3-phase pipeline (`complaint-miner` + `build-pattern-scanner` +
  `concept-synthesizer`) against the `pkm-tools` scope, producing this repo's first concept
  candidate, **Recallect** (`candidates/2026-09-03T231104Z-pkm-tools-candidate.md`). Archived at
  `examples/2026-09-03T231104Z-pkm-tools/`. `scripts/verify_sourcing.py` passes on all three output
  files.
- Generalized `examples/`'s naming from a single one-off (`pkm-tools-pilot/`) to a repeatable
  `examples/<date-time-iso>-<slug>/` convention (documented in `examples/README.md`), so a run's
  `scope.md` and findings survive being checked in even though `findings/` and `config/scope.md`
  stay gitignored; the prior one-off entry was renamed to `2026-09-01-pkm-tools-pilot/` to match
  (date-only precision, since no exact run timestamp was recorded for it originally).

- Initial scaffold: README, Apache 2.0 license, and `docs/plans/0001-concept.md` laying out the
  pipeline design, the explicit aggregate-signal-only boundary, and open questions. No pipeline
  implementation yet — concept-stage.
- v1 agent specs (`docs/plans/0002-signal-to-concept-v1.md`): `.claude/agents/{complaint-miner,
  build-pattern-scanner,concept-synthesizer}.md`, an `AGENTS.md` orchestrator, `config/scope.example.md`,
  `findings/README.md`, `candidates/README.md`, and a ported `scripts/verify_sourcing.py` +
  `tests/test_verify_sourcing.py` + `pyproject.toml`. Not yet executed — pilot run and tests are
  pending Bash access (see the 0002 handoff).
- `.env.example` (`PRODUCTHUNT_API_TOKEN` required, `GITHUB_TOKEN` optional) with matching
  references in `complaint-miner.md`/`build-pattern-scanner.md` and a README "Environment" pointer;
  `.env` added to `.gitignore`.
- `CONTRIBUTING.md`, and a README "Running or extending this" pointer to it and to `AGENTS.md` —
  completing this repo's README (humans) / AGENTS.md (agents) / CONTRIBUTING.md (both) convention,
  now stated as a workspace-level rule at `/workspaces/qte77/.claude/rules/documentation-hierarchy.md`
  (outside this repo).

### Fixed

- Corrected two open questions in `docs/plans/0001-concept.md` with verified evidence: Reddit's
  unauthenticated-scraping-vs-OAuth-API distinction, and the downstream-handoff scoping into
  `agentic-grounded-persona-eval` (Phases 1-2 only, not Phase 3, until something ships).
- Bolt.new and v0/Vercel, previously recorded as a "genuine open gap" (permissive `robots.txt`,
  unconfirmed ToS), are now confirmed ruled out — both checked at primary source 2026-09-04 (Bolt.new
  via its operator StackBlitz's real terms; v0 via Vercel's Acceptable Use Policy), both explicitly
  ban scraping/automated data extraction.
