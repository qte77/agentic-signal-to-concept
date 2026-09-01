# Scope — copy this file to scope.md and fill in

## Run scope

- **Problem-space / category** (keywords, industry, or complaint theme to mine for):
- **Slug** (used in `findings/<date-time-iso>-<slug>-{complaints,builds}-findings.md` and
  `candidates/<date-time-iso>-<slug>-candidate.md`, kebab-case):
- **Date window** (optional — e.g. "last 12 months"; if left blank, `build-pattern-scanner` still
  needs a bounded window for its independence check and defaults to 12 months):

## Source inclusions/exclusions (optional)

Anything beyond the v1 defaults (HN + ProductHunt for complaints; GitHub + Show HN for builds;
Reddit and app-store review mining excluded by default — see `docs/plans/0001-concept.md`):

-

## Independence heuristic override (optional)

Default (`build-pattern-scanner.md`): distinct author/org handles, created within the date window
above (or 12 months if unset), no fork/clone relation to each other or a shared canonical upstream.
Override here if this run needs a different bar:

-
