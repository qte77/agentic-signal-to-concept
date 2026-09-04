# agentic-signal-to-concept — orchestration

## Pipeline

```
Phase 0 (optional): signal-discoverer → discovery/<ts>-categories.md (no config/scope.md needed)
                                          ↓ human picks a category from the list
Phase 1a: complaint-miner        ─┐  parallel — single message, two Task tool calls
Phase 1b: build-pattern-scanner  ─┘
Phase 2:  concept-synthesizer    → candidates/<date-time-iso>-<scope-slug>-candidate.md
```

Phase 0 is optional and independent of Phases 1–2: run it when the category itself is undecided
("what should we look into?"); skip straight to Phase 1 when the category is already known. See
`docs/plans/0003-broad-discovery.md` for the full design.

## Before running

1. **Category already known?** Copy `config/scope.example.md` to `config/scope.md` and fill it in for
   this run. **Category undecided?** Run `signal-discoverer` first (no `config/scope.md` needed for
   that phase) — its output is a ranked candidate-category list, not a scope file; write
   `config/scope.md` from whichever candidate is chosen afterward.
2. Generate the run's ISO timestamp once — `date -u +%Y-%m-%dT%H%M%SZ` — and pass it to both Phase 1
   subagents so their output filenames share it and sort together. (Phase 0 generates its own
   timestamp independently — it has no Phase 1 counterpart to share one with.)

## Running more than one category at once

`config/scope.md` is a single, shared, gitignored file — two categories' Phase 1 agents running
concurrently against the same working tree would clobber each other's scope. **Give each concurrent
category its own git worktree** (`git worktree add <path> -b <branch>`), each with its own
`config/scope.md` written inside it, and dispatch that category's full pipeline into that worktree.
This is this repo's standing worktree rule (present since 0001's own handoff) — running a single
category at a time never made it load-bearing before; running several at once does.

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
