# Handoff — 0002 signal-to-concept v1 build

Full design + remaining-work table: `docs/plans/0002-signal-to-concept-v1.md`. Don't duplicate that
table here — this doc only onboards the next session to it.

## Lead with this: everything below is uncommitted

**Bash was denied for the entire session that wrote this arc.** No branch was created, nothing was
committed, no tests were run, no pilot was executed. Every file this arc created sits directly on
`main`'s working tree, uncommitted. Run `git status` before touching anything — this is deliberate
in-progress work, not stray debris, and it should move to a feature branch before any of it lands.

## What shipped this session (agent-only, all file-creation, no execution)

- Three `.claude/agents/*.md` specs: `complaint-miner`, `build-pattern-scanner` (carries the
  source-3 ethical boundary + the v1 "independent builds" heuristic default),
  `concept-synthesizer`.
- `AGENTS.md` orchestrator (2 parallel research passes → 1 synthesis pass — deliberately not the
  heavier gtm-style validation-loop/dual-mode pattern; see 0002 plan's exclusion table for why).
- `config/scope.example.md`, `findings/README.md`, `candidates/README.md`.
- Ported `scripts/verify_sourcing.py` + `tests/test_verify_sourcing.py` + a minimal `pyproject.toml`
  — all confirmed byte-identical against the sibling repo's originals before writing (not assumed).
- `.gitignore`, `README.md`, `CHANGELOG.md` updates; a one-line supersession note in
  `docs/plans/0001-concept.md`.
- Two corrections to `0001-concept.md` itself, made *before* this arc started (Reddit
  scraping-vs-OAuth-terms distinction; the downstream-handoff-to-persona-eval scoping answer) —
  already committed to that doc's text, just noting they predate this arc's file list.
- A one-line supersession note in `0001-concept.md`'s Status section pointing at this arc.
- `CONTRIBUTING.md` (mirrors `agentic-grounded-persona-eval/CONTRIBUTING.md`'s weight, not
  `Agents-eval`'s heavier one) and a README pointer to it and to `AGENTS.md`, completing this repo's
  README/AGENTS.md/CONTRIBUTING.md convention.
- **Fetch tooling wired in**: `complaint-miner.md` and `build-pattern-scanner.md` now designate
  [`polyfetch-scrape`](https://github.com/qte77/polyfetch-scrape) (sibling clone at
  `../polyfetch-scrape`, no install needed) as the fetch tool for every plain-GET call, to avoid a
  summarizing web-fetch tool's paraphrase risk. Confirmed directly (`polyfetch fetch --help`) that
  its CLI cannot carry a POST body or headers, so ProductHunt's GraphQL call uses a direct HTTP
  client instead — not a guess, checked.
- **Bash access came back mid-session**: ran the 6 ported tests (`uv run pytest` — all pass) and a
  hand-written mixed-sourcing fixture against `verify_sourcing.py` (correctly exits 1, flags only
  the unsourced block). Both are genuinely verified now, not just planned.
- **Workspace-level, outside this repo**: `/workspaces/qte77/.claude/rules/documentation-hierarchy.md`
  — a new rule (auto-loaded for every session under `qte77/*`) stating the README (humans) /
  AGENTS.md (agents) / CONTRIBUTING.md (both) convention as this workspace's SSOT for documentation
  roles, per the user's explicit request mid-session. Not part of this repo's own git history —
  check that file's own location if it ever needs updating.

## What's next, in order

1. `git status`, review the new/modified files, create a feature branch, commit by topic
   (`feat: add complaint-miner spec`, `feat: add build-pattern-scanner spec`, etc. — see this
   workspace's `unattended-execution.md` for the per-topic-commit convention), open a PR.
   **Repo ruleset gate**: this repo has a ruleset requiring extra approval on agent-authored
   changes. Standing practice (confirmed 2026-09-01, supersedes the "one-time exception" framing in
   0001's original handoff): squash-merge with `gh pr merge --squash --admin` to satisfy the gate
   per-merge — **never modify or disable the ruleset itself** to work around it.
2. ~~Run `uv run pytest` on the ported test suite~~ — done this session, 6/6 pass.
3. Run the HN-only pilot: execute `complaint-miner` for real against a real `config/scope.md` (copy
   from the example, fill in a real scope), confirm `scripts/verify_sourcing.py` exits 0 against its
   output. Not yet run — recommend running only when explicitly requested, since it makes live
   HN/PH calls. Fetch HN/GitHub/Show-HN URLs via `polyfetch-scrape` per the specs' "Fetch tooling"
   sections; ProductHunt needs a direct HTTP client, not `polyfetch`.
4. Only after the pilot works: consider `build-pattern-scanner`'s first real GitHub+Show-HN run, and
   the ProductHunt `developer_token` step (owner-gated, self-serve).
5. Everything else is either owner-gated (app-store access path, Reddit OAuth terms) or explicitly
   deferred (Devpost, AI-builder-tool showcases, build-in-public threads) — see the 0002 plan's
   table for exact done-whens. Don't build subagent coverage for any of these until their gate
   clears; they're tracked, not forgotten.

## Watch-outs

- **`polyfetch-scrape` is a sibling clone, not a dependency of this repo.** `../polyfetch-scrape`
  must exist as a checkout next to this repo for the fetch-tooling instructions to work — it's not
  vendored, submoduled, or added to `pyproject.toml`. If a future environment doesn't have that
  sibling clone, the specs' "Fetch tooling" sections need a fallback path documented.
- **Standing worktree rule** (from 0001's own handoff, still in force): any subagent dispatched
  against this repo's work runs in its own git worktree — never shared with a concurrent agent.
- The `build-pattern-scanner` ethical boundary is load-bearing, not decorative — if extending it to
  a new sub-source (Devpost, etc.), carry the boundary language forward into that addition, don't
  let it apply only to GitHub/Show HN by accident.
- `candidates/*-candidate.md` is tracked (not gitignored) — it's this repo's actual product output.
  `findings/*-findings.md` is gitignored by default, matching the sibling repo's own convention.
- This is the first arc in this repo to use the separate plan+handoff file pair instead of 0001's
  embedded-handoff style — that's the workspace's actual convention; 0001 was the one-off.
