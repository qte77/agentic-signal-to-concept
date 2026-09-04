# discovery/

Per-run output of `signal-discoverer`, Phase 0 of the pipeline: `<date-time-iso>-categories.md`, a
ranked list of candidate problem-space categories surfaced from a broad, unfiltered pull — not a
category-scoped research pass the way `findings/` is.

`discovery/*-categories.md` is gitignored by default, same treatment as `findings/*-findings.md` —
working evidence for a run, not this repo's tracked product output. A discovery run worth keeping
permanently gets archived under `examples/<date-time-iso>-discovery/`, matching the convention used
for every other run type (see `examples/README.md`).

## Expected structure

- **Window and source coverage** — what was pulled, from where, over what window, and each source's
  state (found / blocked / excluded).
- **Ranked candidate categories** — one subsection per category: name, per-source signal count,
  cross-source vs. single-source flag, rationale.

Unlike `findings/`, this output is not checked by `scripts/verify_sourcing.py` — it's aggregate
counts and cluster rationale, not sourced quotes. See `.claude/agents/signal-discoverer.md` for the
full spec.
