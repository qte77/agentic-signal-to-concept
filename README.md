# agentic-signal-to-concept

Mine public complaint and build signal — complaints and requests (Reddit, Hacker News, Product
Hunt, app-store reviews), and real build activity, "vibe coded" or conventionally built (GitHub,
Show HN, hackathon/demo galleries, AI-builder-tool showcases, build-in-public threads), plus
whatever other source turns out to matter — into grounded, honestly-sourced product concept
candidates. The discovery stage of a three-tool chain. See
[`docs/plans/0001-concept.md`](docs/plans/0001-concept.md) for the real source-by-source breakdown
and what's verified vs. still open per source.

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

**v1 agent specs drafted, pilot run pending.** `.claude/agents/{complaint-miner,
build-pattern-scanner,concept-synthesizer}.md`, an `AGENTS.md` orchestrator, and a ported
`scripts/verify_sourcing.py` exist, scoped to v1's smallest workable slice (HN + ProductHunt
complaint mining, GitHub + Show HN build-pattern scanning). Nothing has been executed yet — no
pilot run, no tests run. See [`docs/plans/0002-signal-to-concept-v1.md`](docs/plans/0002-signal-to-concept-v1.md)
for the build plan and remaining work, and [`docs/plans/0001-concept.md`](docs/plans/0001-concept.md)
for the original concept and what's still genuinely open (app-store access, Reddit's OAuth API
terms, deferred build-pattern sub-sources).

## Running or extending this

- **Environment**: copy [`.env.example`](.env.example) to `.env` and fill in `PRODUCTHUNT_API_TOKEN`
  (required for `complaint-miner`'s ProductHunt pass) and optionally `GITHUB_TOKEN` (raises
  `build-pattern-scanner`'s GitHub rate limit).
- **AI agents**: read [`AGENTS.md`](AGENTS.md) for the orchestration commands.
- **Contributors (human or agent)**: read [`CONTRIBUTING.md`](CONTRIBUTING.md) for how to change
  the method itself, run tests, and this repo's commit conventions.

## License

Licensed under the [Apache License 2.0](LICENSE).
