# agentic-signal-to-concept

Mine public complaint and build-signal (Reddit, Hacker News, Product Hunt, app-store reviews) into
grounded, honestly-sourced product concept candidates — the discovery stage of a three-tool chain.

## Where this sits in the chain

```
agentic-signal-to-concept        -- this repo. Raw public signal -> candidate concepts.
        |
        v
agentic-grounded-persona-eval    -- validate a candidate against sourced, grounded personas
        |                           (github.com/qte77/agentic-grounded-persona-eval)
        v
agentic-market-research-to-gtm   -- take a validated concept through PMF / GTM / pitch
                                     (github.com/qte77/agentic-market-research-to-gtm)
```

Each tool is independently usable; the chain is a convention, not a hard dependency.

## The problem this solves

Idea generation is usually either pure guessing, or a scattershot of individually-run research
passes with no consistent method or sourcing discipline. This tool formalizes a repeatable
version: gather real, sourced signal from where people actually complain and build, look for
*patterns across many independent sources* (a real gap, or many small independent teams
converging on the same unmet need), and turn a validated pattern into a written concept —
never an unsourced guess dressed up as research.

## What this does NOT do — a boundary, not a limitation

**Signal is read at the aggregate/pattern level — the existence of many independent complaints or
many independent small builds in a category — never at the level of one specific person's specific
active project.** Finding one identifiable person's current, unscaled prototype and using it as a
direct build-and-launch template is explicitly out of scope for this tool, regardless of the
project's license status or public visibility. The tool answers "does a real gap exist," never
"whose specific unfinished work should we build instead of them." See
[`docs/plans/0001-concept.md`](docs/plans/0001-concept.md) for the full reasoning.

## Status

**Concept-stage — no pipeline implementation yet.** The actual phase design (how many stages, what
each one produces, which subagents) hasn't been worked out. See
[`docs/plans/0001-concept.md`](docs/plans/0001-concept.md) for what's decided vs. still open.

## License

Licensed under the [Apache License 2.0](LICENSE).
