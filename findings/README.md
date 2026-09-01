# findings/

Per-run, per-source research output. One file per source pass per run:
`<date-time-iso>-<scope-slug>-{complaints,builds}-findings.md`. The two files from the same run
share their `<date-time-iso>` prefix (generated once by the orchestrator), so re-running only one
source pass later creates a new, independently-timestamped file rather than overwriting anything.

`findings/*-findings.md` is gitignored by default — these are working evidence for a run, not the
repo's tracked product output (see `candidates/README.md` for that). See
[`examples/`](../examples/README.md) for a real, permanently-checked-in worked run.

## Expected structure

- **§Research** — sourced quotes grouped by pattern, with a "Source coverage" note up top listing
  what was tried, what worked, and what was blocked, empty, or excluded-by-scope, and why. Written
  by `complaint-miner` or `build-pattern-scanner`.

`scripts/verify_sourcing.py` checks this mechanically — every quote block needs a source URL.
