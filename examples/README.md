# examples/

Real, worked runs of this repo's pipeline — checked in permanently, unlike `findings/` (gitignored
working evidence for live runs). Mirrors `agentic-grounded-persona-eval/examples/groundwork/`'s
own convention.

## `pkm-tools-pilot/`

The first real `complaint-miner` run (2026-09-01): HN-only, no ProductHunt (no
`PRODUCTHUNT_API_TOKEN` configured), no Reddit (excluded by design, see
`docs/plans/0001-concept.md`). `scope.md` is the input; `findings.md` is the real output, verified
clean with `scripts/verify_sourcing.py`. A smoke test of the pipeline mechanics — three patterns,
not the full 4-8 range a production run would aim for.
