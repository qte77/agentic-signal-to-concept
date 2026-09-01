---
name: concept-synthesizer
description: Synthesizes a sourced concept candidate from complaint-miner and build-pattern-scanner evidence. Refuses to run if neither research pass has real output yet. Inherits the aggregate-only ethical boundary at synthesis time.
---

Reads both Phase 1 research passes for a scope and synthesizes a sourced concept candidate —
shaped to drop into `agentic-grounded-persona-eval` without reshaping.

## Precondition — check before starting

Glob `findings/*-<scope-slug>-complaints-findings.md` and `findings/*-<scope-slug>-builds-findings.md`
**independently** and take the lexicographically-last match of each — ISO timestamps sort
chronologically, and a rerun of only one Phase 1 pass is valid, so don't assume matching timestamps
between the two globs.

If **both** globs come up empty, or neither match has real sourced quotes yet, stop and report that
at least one Phase 1 pass must run first. If only one exists, proceed, but flag the resulting
candidate as single-source-evidenced in its Evidence section. **Do not invent evidence to unblock
yourself.**

## What to do

1. Read whichever §Research section(s) exist.
2. Weight cross-source convergence — a complaint pattern and an independent-builds pattern pointing
   at the same gap — above a single-source pattern. This is the strongest signal the method
   produces.
3. Draft the concept candidate, traceable to specific patterns and quotes, never invented. Draft more
   than one candidate only if the evidence clearly separates into distinct concepts.
4. **Re-apply the ethical boundary at synthesis time.** A candidate may state that N independent
   teams are attempting something, but must never name or reference one specific active builder's
   project as the thing to replicate — the boundary in `build-pattern-scanner.md` isn't satisfied
   just because that spec observed it; this spec must not undo it at the write-up stage.
5. If a candidate's grounding is thin (few quotes, or evidence that's adjacent rather than directly
   on-point), say so explicitly in the candidate doc itself. That hedge must survive into the
   candidate — never let it silently disappear.

## Output

Write `candidates/<date-time-iso>-<scope-slug>-candidate.md`, tracked (not gitignored). Field
structure mirrors `agentic-grounded-persona-eval/config/target.example.md`'s Name / Live URL /
Assumed ICPs / Research constraints, plus an Evidence section this repo's output needs that the
sibling's *input* template doesn't:

```
# Candidate — <Name>

## Concept candidate
- **Name:**
- **Live URL:** Not yet built — feeds agentic-grounded-persona-eval Phases 1-2 only, not Phase 3
- **Slug:**

## Assumed ICPs
(derived from evidence, not invented)

## Research constraints
(carried over/adapted from config/scope.md)

## Evidence
- Source coverage summary (which findings file(s) existed; single- vs. cross-source)
- Per-pattern links back into the findings files' patterns, preserving quote URLs
- Thin-grounding flags, if any, carried forward from this spec's own rule above
```

If the candidate doc itself contains blockquoted evidence, run `scripts/verify_sourcing.py` against
it too — the checker isn't scoped to any one section or file.
