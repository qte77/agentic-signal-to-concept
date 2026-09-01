# agentic-signal-to-concept — orchestration

## Pipeline

```
Phase 1a: complaint-miner        ─┐  parallel — single message, two Task tool calls
Phase 1b: build-pattern-scanner  ─┘
Phase 2:  concept-synthesizer    → candidates/<date-time-iso>-<scope-slug>-candidate.md
```

## Before running

1. Copy `config/scope.example.md` to `config/scope.md` and fill it in for this run.
2. Generate the run's ISO timestamp once — `date -u +%Y-%m-%dT%H%M%SZ` — and pass it to both Phase 1
   subagents so their output filenames share it and sort together.

## Running it

Launch `complaint-miner` and `build-pattern-scanner` in a **single message with two Task tool
calls** — they have no dependency on each other, so run them concurrently, don't serialize them.
Each writes its own dated findings file (never a shared file — no concurrent-append risk).

Launch `concept-synthesizer` once both have reported back (or reported blocked) — its own
precondition check in `.claude/agents/concept-synthesizer.md` handles the case where only one
Phase 1 pass produced real output.

## After running

Run `scripts/verify_sourcing.py` against both findings files and the candidate file. To hand off a
candidate downstream, copy its Name / Assumed ICPs into
`agentic-grounded-persona-eval`'s `config/target.md` — its Live URL stays "Not yet built," so the
candidate feeds that repo's Phases 1–2 only, not its live-evaluation Phase 3.

## Worktree rule

Standing rule from this repo's own handoff history: any subagent dispatched against this repo's
work runs in its own git worktree, always — never shared with a concurrent agent.

## What this deliberately doesn't do

No validation-loop subagent, no dual execution modes, no phase-dependency table, no per-source-type
citation-format rules, no standalone `SUBAGENTS.md` — all excluded as premature machinery for a
3-spec pipeline. See `docs/plans/0002-signal-to-concept-v1.md` for the full rationale. Reconsider
only if this pipeline's spec count or stakes actually grow.
