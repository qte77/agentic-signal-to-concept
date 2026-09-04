# Changelog

All notable changes to agentic-signal-to-concept. Format follows
[Keep a Changelog](https://keepachangelog.com/en/1.1.0/).

## [Unreleased]

### Added

- First real execution of the full 3-phase pipeline (`complaint-miner` + `build-pattern-scanner` +
  `concept-synthesizer`) against the `pkm-tools` scope, producing this repo's first concept
  candidate, **Recallect** (`candidates/2026-09-03T231104Z-pkm-tools-candidate.md`). Archived at
  `examples/2026-09-03T231104Z-pkm-tools/`. `scripts/verify_sourcing.py` passes on all three output
  files.
- Generalized `examples/`'s naming from a single one-off (`pkm-tools-pilot/`) to a repeatable
  `examples/<date-time-iso>-<slug>/` convention (documented in `examples/README.md`), so a run's
  `scope.md` and findings survive being checked in even though `findings/` and `config/scope.md`
  stay gitignored; the prior one-off entry was renamed to `2026-09-01-pkm-tools-pilot/` to match
  (date-only precision, since no exact run timestamp was recorded for it originally).

- Initial scaffold: README, Apache 2.0 license, and `docs/plans/0001-concept.md` laying out the
  pipeline design, the explicit aggregate-signal-only boundary, and open questions. No pipeline
  implementation yet — concept-stage.
- v1 agent specs (`docs/plans/0002-signal-to-concept-v1.md`): `.claude/agents/{complaint-miner,
  build-pattern-scanner,concept-synthesizer}.md`, an `AGENTS.md` orchestrator, `config/scope.example.md`,
  `findings/README.md`, `candidates/README.md`, and a ported `scripts/verify_sourcing.py` +
  `tests/test_verify_sourcing.py` + `pyproject.toml`. Not yet executed — pilot run and tests are
  pending Bash access (see the 0002 handoff).
- `.env.example` (`PRODUCTHUNT_API_TOKEN` required, `GITHUB_TOKEN` optional) with matching
  references in `complaint-miner.md`/`build-pattern-scanner.md` and a README "Environment" pointer;
  `.env` added to `.gitignore`.
- `CONTRIBUTING.md`, and a README "Running or extending this" pointer to it and to `AGENTS.md` —
  completing this repo's README (humans) / AGENTS.md (agents) / CONTRIBUTING.md (both) convention,
  now stated as a workspace-level rule at `/workspaces/qte77/.claude/rules/documentation-hierarchy.md`
  (outside this repo).

### Fixed

- Corrected two open questions in `docs/plans/0001-concept.md` with verified evidence: Reddit's
  unauthenticated-scraping-vs-OAuth-API distinction, and the downstream-handoff scoping into
  `agentic-grounded-persona-eval` (Phases 1-2 only, not Phase 3, until something ships).
