# candidates/

This repo's actual product output: sourced concept candidates, one file per synthesis run,
`<date-time-iso>-<scope-slug>-candidate.md`. Unlike `findings/`, this directory is **tracked, not
gitignored** — a candidate is the deliverable, not working evidence.

Written by `concept-synthesizer`, only after at least one of `complaint-miner` or
`build-pattern-scanner` has produced real findings for the same scope. Field structure mirrors
`agentic-grounded-persona-eval/config/target.example.md` (Name / Live URL / Assumed ICPs / Research
constraints) plus an Evidence section — see `.claude/agents/concept-synthesizer.md` for the exact
shape. A candidate's Live URL stays "Not yet built" until something ships; until then it feeds that
sibling repo's Phases 1–2 only, not its live-evaluation Phase 3.
