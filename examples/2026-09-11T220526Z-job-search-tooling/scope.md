# Scope

## Run scope

- **Problem-space / category**: AI-assisted job search and application tooling — resume tailoring,
  auto-apply agents, application tracking across ATS systems, interview prep. Sourced from
  `signal-discoverer` run 2 (`examples/2026-09-04T081918Z-discovery/categories.md` category #4),
  confirmed still present (unchanged ranking) in run 3
  (`examples/2026-09-07T050600Z-discovery/categories.md`) — genuinely new signal in run 2 (not a
  re-measurement of run 1), cross-source (21 HN titles + 12 PH posts in-sample), not yet vertically
  explored.
- **Slug**: job-search-tooling
- **Date window**: 12 months (default — no override needed for this run)

## Source inclusions/exclusions (optional)

v1 defaults apply: HN + ProductHunt for complaints; GitHub + Show HN + cv.inc + Hugging Face Spaces
for builds; Reddit and app-store review mining excluded by default.

## Query terms used (optional, fill in after a run)

(filled in by `complaint-miner`/`build-pattern-scanner` per their own Source-coverage sections)

## Independence heuristic override (optional)

None — default applies (distinct author/org handles, created within 12 months, no fork/clone
relation, no shared canonical upstream).

## Discovery signal worth investigating (carried over from `signal-discoverer` run 2/3)

Several titles in the discovery pull frame trust/accuracy of *unsupervised* auto-apply as a
differentiator — "with a Human in the Loop," "deletes listings when employers close them" — implying
the fully-automated version already has a reputation problem. `complaint-miner` should look
specifically for complaints about auto-apply tools submitting bad/mismatched applications, or job
seekers distrusting fully autonomous agents, not just generic "job search is tedious" complaints.
