# Scope — pilot run

## Run scope

- **Problem-space / category**: note-taking / personal-knowledge-management (PKM) tool complaints —
  recurring frustrations with existing tools (Notion, Obsidian, Roam, Evernote, etc.), not a single
  product.
- **Slug**: pkm-tools
- **Date window**: not bounded — this is a smoke-test pilot of the pipeline mechanics, not a
  time-boxed production research run.

## Source inclusions/exclusions (optional)

HN + ProductHunt for this run (`PRODUCTHUNT_API_TOKEN` now configured in `.env` — first run to
include it; verify via environment check, don't assume). Reddit and app-store excluded per
`docs/plans/0001-concept.md`.

## Independence heuristic override (optional)

Not applicable — this pilot exercises `complaint-miner` only, not `build-pattern-scanner`.
