# agentic-signal-to-concept — orchestration

## Two modes, plus a standalone naming phase

- **Horizontal mode** — "what should we look into?" `signal-discoverer` alone: one broad, unfiltered
  pull across sources, no category named up front, output is a ranked candidate-category list. No
  `config/scope.md` needed. See `docs/plans/0003-broad-discovery.md`.
- **Vertical mode** — "here's the category, go deep." The original 3-agent pipeline
  (`complaint-miner` + `build-pattern-scanner` → `concept-synthesizer`) against one named category in
  `config/scope.md`. See `docs/plans/0002-signal-to-concept-v1.md`.
- **Naming phase** — "what do we call it?" `name-brand-vetter` alone, run any time after a concept
  exists (this repo's own or external). Not gated on horizontal/vertical mode having just run — it
  reads `config/name.md`, not `config/scope.md`. See `docs/plans/0004-name-brand-vetting.md`.

A full run is typically horizontal once, then vertical once per category chosen from its output — but
either mode runs standalone: skip horizontal when the category is already known; run horizontal
without ever following up in vertical mode if the goal is just a category-landscape snapshot.
Running vertical mode on more than one category at once is where **parallel git worktrees** apply
(see below) — horizontal mode is always a single run, never parallelized across worktrees itself.

```
Horizontal:  signal-discoverer     → discovery/<ts>-categories.md (no config/scope.md needed)
                                       ↓ human picks N categories
Vertical ×N: complaint-miner       ─┐  parallel — single message, two Task tool calls
             build-pattern-scanner ─┘  (in worktree N, if N>1 — see "Running more than one category")
             concept-synthesizer     → candidates/<date-time-iso>-<scope-slug>-candidate.md
```

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

**Before dispatching any subagent into a new worktree, copy `.env` into it too** —
`git worktree add` only carries tracked files; `.env` is gitignored, so a fresh worktree has none of
it by default and `complaint-miner`'s ProductHunt pass will read as blocked for a reason that has
nothing to do with the token itself. Confirmed the hard way (2026-09-04): copying `.env` in *after*
dispatching left a timing race that blocked ProductHunt in all three worktrees of that run anyway.
Copy it as part of worktree setup, before Phase 1 launches, every time.

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

## Naming a concept

Once a concept exists (`candidates/*-candidate.md` from this repo, or an external one), run
`name-brand-vetter` standalone: copy `config/name.example.md` to `config/name.md`, point it at the
concept (Option A: this repo's candidate path; Option B: a standalone description — never this
repo's own concept and an external one in the same run), and set the **Output ownership** field.
**External concepts (e.g. sfclarity/sfsanity) must never have their naming-run output committed to
this repo's tracked `names/`/`examples/`** — see the standing sfclarity/sfsanity boundary; the run
itself is fine, only the output placement differs. See `.claude/agents/name-brand-vetter.md` for the
full two-tier (cheap filter / PR-launch sweep) design.

## Worktree rule

Standing rule from this repo's own handoff history: any subagent dispatched against this repo's
work runs in its own git worktree, always — never shared with a concurrent agent.

## What this deliberately doesn't do

No validation-loop subagent, no phase-dependency table, no per-source-type citation-format rules, no
standalone `SUBAGENTS.md` — all excluded as premature machinery for a 3-spec pipeline. See
`docs/plans/0002-signal-to-concept-v1.md` for the full rationale. **Superseded in one respect**: 0002
also excluded "dual execution modes" as premature at 3 specs — the spec count grew to 4
(`signal-discoverer`, `docs/plans/0003-broad-discovery.md`) and the user explicitly asked for
horizontal/vertical to be named modes, which is exactly the reconsideration trigger 0002 itself named
("only if this pipeline's spec count or stakes actually grow"). The two modes above are that
reconsideration — still no *dual execution* modes in 0002's original sense (concise/detailed ×
conservative/ambitious output framing, borrowed from `agentic-market-research-to-gtm`'s heavier
pattern); this is a horizontal/vertical *scope* distinction, a different axis entirely.
