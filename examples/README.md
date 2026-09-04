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
