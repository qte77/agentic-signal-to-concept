# examples/

Real, worked runs of this repo's pipeline — checked in permanently, unlike `findings/` and
`config/scope.md` (both gitignored working state for live runs). Mirrors
`agentic-grounded-persona-eval/examples/groundwork/`'s own convention.

## Convention

`examples/<date-time-iso>-<slug>/` — one directory per archived run, named with the same
orchestrator-generated ISO timestamp and slug used for that run's `findings/`/`candidates/` files.
Each directory holds copies of that run's `scope.md` and findings file(s) — the inputs and raw
sourced evidence that would otherwise only exist as gitignored working state. It does **not**
duplicate a synthesized candidate: `candidates/<same-ts>-<slug>-candidate.md` already tracks that
permanently as this repo's actual product output (see `candidates/README.md`); an example entry
links to it instead of copying it. Not every run needs archiving here — only ones worth keeping as a
permanent worked reference.

The one entry from before this convention existed (`2026-09-01-pkm-tools-pilot/`) uses date-only
precision because no exact run timestamp was recorded for it at the time — not fabricated to fit the
pattern retroactively.

## `2026-09-01-pkm-tools-pilot/`

The first real `complaint-miner` run (2026-09-01): HN-only, no ProductHunt (no
`PRODUCTHUNT_API_TOKEN` configured), no Reddit (excluded by design, see
`docs/plans/0001-concept.md`). `scope.md` is the input; `findings.md` is the real output, verified
clean with `scripts/verify_sourcing.py`. A smoke test of the pipeline mechanics — three patterns,
not the full 4-8 range a production run would aim for — and `build-pattern-scanner`/
`concept-synthesizer` did not run yet at this point.

## `2026-09-03T231104Z-pkm-tools/`

The first real run of the **full** 3-phase pipeline (2026-09-03) — `complaint-miner`,
`build-pattern-scanner`, and `concept-synthesizer` all executed for real against the same scope for
the first time. `scope.md` is the input; `complaints-findings.md` (5 patterns, 14 sourced HN quotes)
and `builds-findings.md` (4 patterns, 24 independent build instances, GitHub + Show HN; Bluesky
reconfirmed blocked) are the real Phase 1 outputs, both verified clean with
`scripts/verify_sourcing.py`. The synthesized candidate lives at
[`candidates/2026-09-03T231104Z-pkm-tools-candidate.md`](../candidates/2026-09-03T231104Z-pkm-tools-candidate.md)
("Recallect").

## `2026-09-04T043009Z-pkm-tools/`

A second full-pipeline run against the same `pkm-tools` scope (2026-09-04), one day later — the
first run where `PRODUCTHUNT_API_TOKEN` was actually configured and ProductHunt genuinely
contributed evidence. `complaints-findings.md` (5 patterns, 13 sourced quotes across HN +
ProductHunt) documents a real environment constraint worth knowing about before reusing PH as a
source: this fetch environment redacts ProductHunt commenter usernames (`user.username` always
returns `"[REDACTED]"`, confirmed content-based via a field-aliasing test) — PH quotes here are
cited by comment-permalink URL and date instead of a handle. `builds-findings.md` (4 patterns, 21
new independent instances / 24 total citations) shows that a day-later re-run barely changed the
underlying build population (0 new GitHub PKM-topic repos) — what changed was query-term coverage,
explicitly framed as an undercount correction to the prior run, not new build activity; also caught
a GitHub topic-tag spam/farm cluster and excluded it. Both verified clean with
`scripts/verify_sourcing.py`. The synthesized candidate,
[`candidates/2026-09-04T043009Z-pkm-tools-candidate.md`](../candidates/2026-09-04T043009Z-pkm-tools-candidate.md),
independently re-derived the same core concept ("Recallect") from this run's evidence rather than
anchoring on the prior candidate, but with a materially different — and better-corroborated — trust
pillar (data portability/lock-in, not plugin-execution risk).

## `2026-09-04T060617Z-discovery/`

The first real `signal-discoverer` run (Phase 0, `docs/plans/0003-broad-discovery.md`) — a broad,
unfiltered pull across Show HN (full 30-day window confirmed, 3,670 titles, bucketed around
Algolia's 1,000-hit cap) and ProductHunt (narrower than intended, effectively one calendar day —
PH's daily-launch-cohort timestamp convention meant an unbounded pull never advanced past
2026-09-03; both the cause and the fix for next time are documented in the file and folded back into
`signal-discoverer.md`). 8 candidate categories promoted, 2 explicitly rejected as grab-bags (Games,
Browser Extensions) rather than silently dropped. No `scripts/verify_sourcing.py` run against this
one — it's aggregate cluster data, not sourced quotes, per the spec's own Output section.

## `2026-09-04T070136Z-agent-memory/`, `2026-09-04T070138Z-terminal-ux/`, `2026-09-04T070139Z-freelancer-finance/`

The first real **vertical-mode** runs on categories sourced from horizontal discovery, all three run
in parallel via dedicated git worktrees (`docs/plans/0003-broad-discovery.md`) — the first real
exercise of this repo's standing worktree rule. Each holds `scope.md` + `complaints-findings.md` +
`builds-findings.md` for its category; ProductHunt was blocked in all three (a worktree-setup timing
gap, since fixed — see `AGENTS.md`) so all three sit on a consistent HN-only complaint baseline, no
cross-category inconsistency. Synthesized candidates:
[`candidates/2026-09-04T070136Z-agent-memory-candidate.md`](../candidates/2026-09-04T070136Z-agent-memory-candidate.md)
("Contextlint"),
[`candidates/2026-09-04T070138Z-terminal-ux-candidate.md`](../candidates/2026-09-04T070138Z-terminal-ux-candidate.md)
("Vigil" and "Overflow"),
[`candidates/2026-09-04T070139Z-freelancer-finance-candidate.md`](../candidates/2026-09-04T070139Z-freelancer-finance-candidate.md)
("Freehold Finance" and "Upfront Terms"). A calibration check comparing discovery's predicted rank
against actual yield across these three is recorded in `docs/plans/0003-broad-discovery.md`'s
remaining-work table.

## `2026-09-04T081918Z-discovery/`

The second real `signal-discoverer` run, and the first to include GitHub (added to the spec after
the first discovery run) alongside Show HN and ProductHunt. 9 candidate categories promoted (up from
8), 5 rejected. Two things worth knowing before reusing this as a reference:

- **This run's 30-day window overlaps ~99% with the first run's** (a ~2h14m shift) — most category
  counts here re-measure the same underlying corpus, not two days of fresh growth; the file states
  per-category which counts are re-measurement vs. genuinely new (one category, job-search/
  interview-prep tooling, didn't appear in the first run at all and is real new signal).
- **A new ethical-boundary case, correctly excluded**: 33 GitHub repos under `dsh-plugin`/
  `deepseek-harness` topics all turned out to be plugins/wrappers for one specific upstream project
  (`deepseek-ai/deepseek-harness`), not independent convergence — excluded outright rather than
  reframed as an "ecosystem" category, per the spec's boundary.

Also confirmed live: ProductHunt's `first` parameter silently caps at 20 regardless of what's
requested (real daily volume is 431–1,331 posts, so this run's PH sample is ~2.7% of the window,
correctly reported as a sample); GitHub's `stars:>500` threshold (216 repos, after `stars:>250`
returned too many at 510) kept the pull in the low-hundreds range the spec asks for.

## `2026-09-07T043304Z-patent-signal-research/`

A research pass testing whether patent databases (old/current/expired) could serve as a new idea
source. Both hypotheses tested — expired patents as public-domain ideas, active filings as
demand/investment signal — held up against real sampled content (Apple's US8046721B2, expired;
US11556230B2, active to 2035; a 2024 Salesforce EPO filing), and CPC classification codes proved a
stronger clustering primitive than GitHub/PH topics. **Not wired into any spec**: the only
credential-free paths (individual Google Patents pages, EPO's Publication Server) allow known-number
lookups only — robots.txt explicitly disallows Google Patents' search surface, so neither can
*discover* candidates by category. Every path that can search is owner-gated, and USPTO's gate is now
unusually steep (ID.me identity verification, confirmed 2026-06–08) compared to this pipeline's
existing credentials — flagged as a real decision point for the repo owner, not defaulted. The file
demonstrates the patent-family risk with a real sourced example: the sampled expired patent's own
family list shows a related patent still marked Active.

## `2026-09-05T204416Z-startup-registries/`

A one-off research pass (not a `signal-discoverer` run) testing whether public startup/indie-project
registries — `startups.gallery`, `betalist.com`, `uneed.best`, `wip.co` — could serve as an idea/ROI
source. All four cleared ToS (a different outcome than the Devpost/Lovable/Replit/Indie Hackers
precedent in issue #10), though `uneed.best`'s ToS itself would have barred a direct scrape — what
actually clears it is a sanctioned public read-only MCP server/API disclosed in its privacy policy,
not used this pass. `startups.gallery` turned out to be VC-funded companies, not indie projects,
despite its name. 6 niches ranked by a stated ROI/feasibility proxy (no real financial data exists
per listing); top pick: agentic/coding-agent infrastructure tooling (3 independent indie builders
converging on trust/data/backlink infra). One AI-detection-evasion listing was flagged and excluded
on ethics grounds, not scored. All load-bearing ToS claims were independently raw-verified (not left
as WebFetch summaries) before this file was finalized.

## `2026-09-05T213930Z-hackathon-signal-research/`

A one-off research pass testing whether hackathon content (sponsor challenge statements, problem
taxonomies, judging criteria) offers signal distinct from HN/PH/GitHub. Of three platforms checked,
only `cv.inc`/`cerebralvalley.ai` cleared the ToS gate — Devpost and AGI House are both confirmed,
unambiguous blocks (exact clauses quoted in the file). The hypothesis held up on real content: a
completed hackathon's public gallery showed 102 independent teams in one ~31-hour window, several
citing the sponsor's own problem-statement codes directly — denser convergence than GitHub/HN's
30-day/12-month windows. Verified three ways (the original raw fetch, an independent raw re-fetch,
and a `--tier patchright --wait-until networkidle` render cross-check) before being folded into
`signal-discoverer.md` and `build-pattern-scanner.md` as a narrowly-scoped fourth source.

## `2026-09-05T233231Z-additional-sources-research/`

A follow-on research pass vetting 6 more candidate sources: SourceHut, Major League Hacking, Kaggle,
Hugging Face, the Y Combinator company directory, and Stack Overflow/Stack Exchange. Only Hugging
Face cleared cleanly and was added to `signal-discoverer.md`/`build-pattern-scanner.md` as a fifth
source — its public `/api/spaces` JSON endpoint surfaced a single hackathon org with 603
independently-submitted Spaces across 22 collections, larger than cv.inc's own 102-team example, with
zero scrape/AI-training language anywhere in its ToS (independently raw-verified, not left as a
subagent's summary). Major League Hacking technically clears ToS but was flagged, not added — its own
site is a directory/certification layer; the real content lives on each member hackathon's separate,
unchecked external domain. The other four (SourceHut, Kaggle, Y Combinator, Stack Overflow/Stack
Exchange) are all confirmed blocked, several only after a second pass chased down a
separately-incorporated Acceptable Use Policy the first pass had missed — an advisor review caught
that gap before the file was finalized, the same GitLab/Vercel-shaped pattern this repo now checks
for by default.

## `2026-09-11T200013Z-naming-sources-research/`

A research pass for arc 0004 (`docs/plans/0004-name-brand-vetting.md`) testing access to every source
the new `name-brand-vetter` spec's Clear/PR-launch-sweep sections need. The load-bearing question:
does USPTO's **trademark** search share the ID.me identity-verification gate
`2026-09-07T043304Z-patent-signal-research/` found on USPTO's *patent* Open Data Portal? **No** — the
Trademark Search/TSDR public UI is confirmed open for individual lookups, no login required; only the
separate TSDR bulk API needs a key, and whether that key's current issuance needs ID.me is genuinely
unresolved (not guessed either way). EUIPO's own website is blocked (robots.txt + a blanket TDM
copyright opt-out) but its official API is free/self-service/low-friction; WIPO's Global Brand
Database is blocked by both an active technical captcha and a PATENTSCOPE-shaped ToS clause — excluded
from the new spec entirely. RDAP is free/unauthenticated for domain existence and age, with a
confirmed coverage gap on `.io`/`.co`/`.me` (DNS-delegation fallback documented, with its weaker
"registered" vs. "available" asymmetry stated explicitly). GitHub is already clear. Social-handle
checking is confirmed blocked on X (raw-verified) and Instagram (WebSearch-corroborated, flagged as
such rather than treated as equally verified) — both become a manual/owner checklist item, not an
automated step, in the new spec.

## `2026-09-07T050600Z-discovery/`

The third real `signal-discoverer` run, the first to exercise all five sources (Show HN, ProductHunt,
GitHub, cv.inc, Hugging Face Spaces) in one pass. 9 categories promoted — same count as run 2, every
one of run 2's categories still present. 90.4% window overlap with run 2 (vs. ~99% between runs 1/2)
gave a genuine ~2.87-day fresh tail to isolate real growth from re-measurement. Standout finding:
cv.inc surfaced two independent MongoDB-sponsored hackathons explicitly themed "agent memory/
persistent context" — one completed in-window with a 94-team public gallery (independently verified
by fetching the gallery directly: "Projects: 94 (3 placed)") — direct sponsor-stated demand
confirming the #1 category across three straight runs and four sources. Hugging Face Spaces came back
genuinely thin for this specific window (7 of 1,000 free-text matches, 0 of 174 exact-tag matches,
checked nine ways total) — a real, multiply-verified finding about window-dependence, not a tooling
failure. Two methodology corrections folded back into `signal-discoverer.md`: cv.inc's reliable entry
point (`llms.txt` → `sitemap-md.xml`, not a guessed events path) and a new Output requirement to
archive raw per-source ID lists, closing a gap that blocked a precise repo-level diff against run 2
this time (run 2's raw GitHub list was never saved).
