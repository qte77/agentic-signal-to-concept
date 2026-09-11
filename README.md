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

**Both pipeline modes shipped and running for real** (see `AGENTS.md`'s "Two modes"):
**horizontal** (`signal-discoverer`, Phase 0) finds candidate categories from a broad, unfiltered
pull — now across five sources (Show HN, ProductHunt, GitHub, cv.inc hackathon listings, Hugging
Face Spaces) — without one being named up front; **vertical** (`complaint-miner` +
`build-pattern-scanner` → `concept-synthesizer`) deep-dives one named category. Five concept
candidates exist so far — see [`candidates/`](candidates/) and [`examples/`](examples/) for the
archived runs behind them. `signal-discoverer` has run three times for real (9 candidate categories
in its most recent run, holding steady since run 2); three categories from its first run were
deep-dived in parallel via git worktrees. The most recent run's standout finding: a sponsor
(MongoDB) funding two hackathons explicitly themed around agent/coding-agent memory — direct
stated-demand confirmation of the category that's topped all three discovery runs.
A new standalone phase, `name-brand-vetter`, vets candidate product names against a two-tier
clearance pass (cheap filter on every generated name; an expensive USPTO/EUIPO/RDAP/GitHub "PR-launch
sweep" on 2-3 finalists only) — runnable any time a concept exists, this repo's own or an external
one. See [`docs/plans/0004-name-brand-vetting.md`](docs/plans/0004-name-brand-vetting.md) for the
current arc, [`docs/plans/0003-broad-discovery.md`](docs/plans/0003-broad-discovery.md) for discovery
(source breadth, calibration findings, what's next), [`docs/plans/0002-signal-to-concept-v1.md`](docs/plans/0002-signal-to-concept-v1.md)
for the original pipeline's build plan, and [`docs/plans/0001-concept.md`](docs/plans/0001-concept.md)
for the original concept and what's still genuinely open (Reddit's OAuth API terms, TrustMRR
outreach).

## Running or extending this

- **Environment**: copy [`.env.example`](.env.example) to `.env` and fill in `PRODUCTHUNT_API_TOKEN`
  (required for `complaint-miner`'s ProductHunt pass) and optionally `GITHUB_TOKEN` (raises
  `build-pattern-scanner`'s GitHub rate limit).
- **AI agents**: read [`AGENTS.md`](AGENTS.md) for the orchestration commands.
- **Contributors (human or agent)**: read [`CONTRIBUTING.md`](CONTRIBUTING.md) for how to change
  the method itself, run tests, and this repo's commit conventions.

## License

Licensed under the [Apache License 2.0](LICENSE).
