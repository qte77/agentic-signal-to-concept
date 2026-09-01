# 0002 — agentic-signal-to-concept: v1 agent-spec build

## Status

**File-creation slice merged to main; HN-only pilot run complete (2026-09-01).** All files listed in
the code/file/source map below exist and are committed via PR #4 (0001 corrections) and PR #5 (this
arc's build), both squash-merged with `gh pr merge --squash --admin`, branches deleted. Supersedes
0001's "no subagent specs, no `config/`" KISS boundary — see the note added to `0001-concept.md`'s
Status section. `uv run pytest` (6/6 pass), a hand-written mixed-sourcing fixture, and a real
`complaint-miner` pilot run (3 patterns, 6 sourced HN quotes, checked in at
`examples/pkm-tools-pilot/`) are all genuinely verified, not just planned. See the remaining-work
table below for what's still open, owner-gated, or deferred.

## Convention note

This plan uses a **separate** `docs/handoffs/0002-signal-to-concept-v1.md` file, per this
workspace's `unattended-execution.md` rule (`docs/plans/NNNN-slug.md` + `docs/handoffs/NNNN-slug.md`
pair). 0001 embedded its handoff as a `§0` section inside the plan doc instead — that was the
deviation, not this. Future arcs in this repo should use the separate-file pair.

## Why this arc exists

0001 recorded the concept, the four signal-source categories, the source-3 ethical boundary, and a
list of open questions — deliberately not a build plan. Two of those open questions were resolved
with real evidence on 2026-09-01 (see `0001-concept.md`'s corrected §1 and the Output-format-handoff
open question): Reddit's unauthenticated scraping is confirmed blocked (OAuth API terms still
unverified), and a concept candidate from this repo can only feed
`agentic-grounded-persona-eval`'s Phases 1–2, not Phase 3, until something ships. This arc turns the
remaining, still-genuinely-open design work (exact phase/subagent breakdown, output format) into
real `.claude/agents/*.md` specs — deliberately scoped to v1's smallest workable slice (HN+ProductHunt
complaint mining, GitHub+Show HN build-pattern scanning), not the full four-category, multi-platform
design 0001 sketches. Deferred sources stay deferred, tracked in the table below, not silently
dropped.

## Code / file / source map

**Sibling repos this design mirrors** (both read in full, not paraphrased from memory):
- `agentic-grounded-persona-eval/.claude/agents/research-analyst.md` — Phase 1 shape: Input /
  What-to-do / If-a-source-is-blocked / Output sections; sourced-quotes-only discipline; the
  `findings/<ts>-<slug>-findings.md` naming convention.
- `agentic-grounded-persona-eval/.claude/agents/persona-synthesizer.md` — Phase 2 shape: glob +
  lexicographically-last-match precondition; "do not invent evidence to unblock yourself"; the
  thin-grounding-must-survive rule.
- `agentic-grounded-persona-eval/scripts/verify_sourcing.py` (74 lines) + `tests/test_verify_sourcing.py`
  (6 tests) + `pyproject.toml` (`packages = ["scripts"]`, `testpaths = ["tests"]`, `pytest>=8.0.0`
  dev dep, hatchling build backend) — all read directly and confirmed byte-for-byte before porting.
- `agentic-grounded-persona-eval/config/target.example.md` — confirmed fields: Name, Live URL, Slug,
  Assumed ICPs, Research constraints. This repo's `candidates/*-candidate.md` output mirrors these.
- `polyfetch-scrape/USING.md` (sibling clone at `../polyfetch-scrape`, read in full 2026-09-01) — the
  designated fetch tool for `complaint-miner` and `build-pattern-scanner`'s "Fetch tooling" sections:
  `uv run --directory ../polyfetch-scrape polyfetch fetch <url> --json`, no install/submodule needed.
  Used instead of a summarizing web-fetch tool specifically to avoid the paraphrase-risk caveat a
  real prior run in this chain flagged. Confirmed via `polyfetch fetch --help` that its CLI has no
  request-body/header flags, so it cannot make ProductHunt's authenticated GraphQL POST call —
  `complaint-miner.md` uses a direct HTTP client for that one call instead.
- `agentic-market-research-to-gtm/AGENTS.md` + `SUBAGENTS.md` — read for the parallel-execution
  pattern only ("single message, multiple Task calls" for independent phases); its heavier
  validation-loop/dual-execution-mode/per-source-citation-format apparatus is deliberately **not**
  adopted here — see the exclusion table in the next section.

**New files this arc creates** (all in this repo, `/workspaces/qte77/agentic-signal-to-concept`):
```
.claude/agents/complaint-miner.md
.claude/agents/build-pattern-scanner.md
.claude/agents/concept-synthesizer.md
AGENTS.md
config/scope.example.md
findings/README.md
candidates/README.md
scripts/verify_sourcing.py       (ported verbatim)
tests/test_verify_sourcing.py    (ported verbatim)
pyproject.toml                   (new, minimal — mirrors sibling's shape)
```
**Modified:** `.gitignore` (add `config/scope.md`, `findings/*-findings.md` — `candidates/*-candidate.md`
stays tracked), `README.md` (Status section, plus a new "Running or extending this" section pointing
at `AGENTS.md`/`CONTRIBUTING.md`), `CHANGELOG.md`, `docs/plans/0001-concept.md` (one-line supersession
note).

**Also new, added mid-arc (tracked here so nothing orphans):**
```
CONTRIBUTING.md                                    (new — mirrors agentic-grounded-persona-eval's
                                                     CONTRIBUTING.md weight, not Agents-eval's heavier one)
.env.example                                       (new — PRODUCTHUNT_API_TOKEN required, GITHUB_TOKEN
                                                     optional; closes a docs-audit gap: the PH token had
                                                     no defined env var name before this)
examples/README.md                                 (new)
examples/pkm-tools-pilot/{scope.md,findings.md}    (new — the real HN-only pilot run, checked in
                                                     permanently, mirrors the sibling repo's
                                                     examples/groundwork/ convention)
```
**Workspace-level, outside this repo's git history — not part of this repo's own file map, noted
here only so the next session knows it exists:**
`/workspaces/qte77/.claude/rules/documentation-hierarchy.md` — states the README (humans) / AGENTS.md
(agents) / CONTRIBUTING.md (both) convention as this workspace's SSOT, referencing `Agents-eval/` as
the heavier exemplar and the three `agentic-*` repos as the lightweight instantiation.

## Architecture — v1 scope

```
Phase 1a: complaint-miner        ─┐  parallel (single message, two Task calls)
Phase 1b: build-pattern-scanner  ─┘
Phase 2:  concept-synthesizer    → candidates/<ts>-<slug>-candidate.md
```

- **complaint-miner** — HN (Algolia search + Firebase full-tree walk, no auth) + ProductHunt
  (GraphQL v2, self-serve `developer_token`). **Reddit excluded from v1 by design**: unauthenticated
  scraping confirmed fingerprint-blocked on a sibling run (three tools, two subreddits, all HTTP
  403); official OAuth API terms remain unverified — do not attempt Reddit scraping.
- **build-pattern-scanner** — GitHub (public REST/GraphQL search, well-documented, generous rate
  limits) + Show HN (via HN's own Algolia API, filtered to `Show HN:` titles). Devpost,
  AI-builder-tool showcases, and build-in-public threads are **deferred**, not attempted — each
  needs its own ToS check first (0001 §3/§4). Carries the source-3 ethical boundary verbatim-in-spirit
  from `0001-concept.md` §3: aggregate/pattern signal only, never one specific active builder's
  project as a build-and-launch template; the one narrow license+explicit-non-commercialization
  exception surfaces as an outreach candidate only, never as ready-to-build input. Also carries a v1
  "independent builds" heuristic default (distinct author/org handles + created within a bounded
  recent window, default 12 months + no fork/clone relation to each other or a shared upstream) —
  explicitly coarse, overridable per-run via `config/scope.md`, and closes 0001's previously-open
  "what makes builds independent" question with a stated default rather than leaving it undecided.
- **concept-synthesizer** — dual-glob precondition (both findings files, independently
  lexicographically-last-matched; proceeds on partial evidence if only one source ran, flagged as
  single-source); weights cross-source convergence above single-source patterns; re-applies the
  ethical boundary at synthesis time (may say "N independent teams," never names one to copy); output
  shaped to match `agentic-grounded-persona-eval`'s `target.example.md` fields plus an Evidence
  section.

### Deliberately excluded (from `agentic-market-research-to-gtm`'s heavier pattern) — YAGNI/AHA

| Excluded | Why not for v1 |
|---|---|
| `results-validator` subagent + correction loop | 2 parallel research passes + 1 synthesis pass is small enough that `verify_sourcing.py` (mechanical) + each spec's own precondition/blocked-discipline (procedural) are sufficient gates. |
| Dual execution modes (concise/detailed × conservative/ambitious) | This pipeline produces sourced findings + one concept candidate, not investor-facing strategic recommendations — there's no "ambitious vs. conservative" reading of a sourced quote. |
| TodoWrite + phase-dependency table | 2 parallel + 1 sequential is fully described by the diagram above; a dependency table is overhead at this phase count. |
| Per-source-type citation-format rules | Every v1 source is a URL-addressable web resource; the existing sourced-quote-with-URL rule already covers it. |
| Standalone `SUBAGENTS.md` | At 3 specs total, shared conventions are short enough to state directly in each spec — mirrors persona-eval's own choice at the same spec count. Extract only once a 4th+ spec makes duplication actually costly. |

## Remaining work (single table — see `unattended-execution.md`)

| Item | Gate | Done-when |
|---|---|---|
| `complaint-miner.md` spec | **shipped** (2026-09-01, PR #5) | File exists with Input/What-to-do/If-blocked/Output sections; Reddit-exclusion stated verbatim-in-spirit. |
| `build-pattern-scanner.md` spec | **shipped** (2026-09-01, PR #5) | File exists with `## Ethical boundary` section (3 bullets) + independence-heuristic default documented. |
| `concept-synthesizer.md` spec | **shipped** (2026-09-01, PR #5) | File exists with dual-glob precondition + Output matching `candidates/*.md` field structure. |
| Port `verify_sourcing.py` + `test_verify_sourcing.py` + minimal `pyproject.toml` | **shipped** (2026-09-01, PR #5) | Files exist, byte-identical to sibling (confirmed by direct read before writing). |
| `config/scope.example.md` | **shipped** (2026-09-01, PR #5) | File exists; every field it names is referenced by name in at least one agent spec. |
| `AGENTS.md` orchestrator | **shipped** (2026-09-01, PR #5) | File exists; its filenames string-match each spec's Output/Precondition lines — no orphan references either direction. |
| `findings/README.md`, `candidates/README.md`, `.gitignore` update | **shipped** (2026-09-01, PR #5) | Files/edit exist; `findings/*-findings.md` gitignored, `candidates/*-candidate.md` NOT gitignored. |
| `CONTRIBUTING.md` + README pointer section | **shipped** (2026-09-01, PR #5) | Both exist; weight mirrors the chain sibling, not `Agents-eval`. |
| Workspace SSOT rule (`documentation-hierarchy.md`) | **shipped** (2026-09-01, outside this repo) | File exists at `/workspaces/qte77/.claude/rules/`; not part of this repo's own commit. |
| `0001-concept.md` supersession note | **shipped** (2026-09-01, PR #5) | One-line note added pointing from 0001's boundaries/Status to this arc. |
| README.md / CHANGELOG.md status update | **shipped** (2026-09-01, PR #5) | Reflects "v1 agent specs drafted, pilot pending," not "concept-stage, zero specs." |
| Run `uv run pytest` on ported tests | **shipped** (2026-09-01) | `pytest` exits 0 on the 6 ported tests — confirmed twice (`6 passed`), once pre-merge. |
| Fixture check on `verify_sourcing.py` | **shipped** (2026-09-01) | Hand-written mixed fixture (1 sourced + 1 unsourced block) correctly exits 1, flags only the unsourced block at its line. |
| HN-only pilot run (real `complaint-miner` execution) | **shipped** (2026-09-01) | Real run against a "PKM tool complaints" scope, via `polyfetch fetch --show-body` per the spec's fetch-tooling section. 3 patterns, 6 sourced quotes, 2 independent authors per pattern. `verify_sourcing.py` exits 0. Checked in permanently at `examples/pkm-tools-pilot/` (mirrors the sibling repo's `examples/groundwork/` convention). |
| Git branch + commit + PR for this arc's files | **shipped** (2026-09-01, PR #4 + PR #5) | Both squash-merged with `gh pr merge --squash --admin`; branches deleted, remote-tracking refs pruned. |
| ProductHunt `developer_token` | owner | Token provisioned (self-serve, needs a PH account) and referenced from `.env` (see `.env.example`) — still the only credential gate blocking a PH-inclusive `complaint-miner` run. |
| App-store review access path | **shipped/resolved** (2026-09-01, PR #8+#9) | Appbot recommended as default aggregator (confirmed pricing, competitor tracking on every tier); Apple/Trustpilot scraping ruled out (ToS); Trustpilot's own API pricing/coverage still open, not blocking. |
| Reddit OAuth API terms | owner, deferred | Still **not primary-source-verified** — every Reddit-owned domain was blocked to a 2026-09-01 research pass. Convergent third-party sources report a ~Nov 2025 "Responsible Builder Policy" gating all new access behind manual approval. Recommended default: exclude Reddit from v1 regardless of how this resolves. |
| Verify `polyfetch fetch`'s CLI supports a POST request body (for ProductHunt's GraphQL call) | **shipped/resolved** (2026-09-01) | Confirmed via `polyfetch fetch --help`: no `--data`/`--body`/`--headers` flag exists. `complaint-miner.md` updated to use a direct HTTP client for ProductHunt instead. |
| Devpost / Lovable / Replit / Indie Hackers sub-sources | **shipped/resolved — ruled out** (2026-09-01, PR #11, [issue #10](https://github.com/qte77/agentic-signal-to-concept/issues/10)) | Each confirmed at primary source to ban automated scraping in its ToS (Replit names ML/AI data extraction specifically); user asked to scrape anyway, declined. Compliant substitute (GitHub `topic:hackathon`/`topic:lovable`/`topic:replit` + targeted Show HN queries) confirmed working and shipped in `build-pattern-scanner.md` instead. Indie Hackers additionally confirmed to have no RSS/feed alternative. |
| TrustMRR sub-source | **shipped/resolved — ruled out, door open** (2026-09-01, PR #11) | Has a real API, but its Acceptable Use Policy bans AI-model use "without prior written permission" — not scraped; real next step is asking TrustMRR directly, not built into any spec yet. |
| Bolt.new / v0-Vercel sub-sources | deferred, genuine open gap | Permissive `robots.txt` but scraping-specific ToS unconfirmed on either — don't add until resolved; a permissive `robots.txt` is not ToS clearance. |
| Bluesky search sub-source | **shipped in spec, blocked in practice** (2026-09-01, PR #6 draft / PR #11 hardened) | `build-pattern-scanner.md` documents the `searchPosts` call, but it's confirmed blocked (HTTP 403, HTML WAF page) from two independent networks/environments via three fetch methods — a real access barrier, not a single-runner artifact. `getProfile` on the same host works fine. Re-check periodically; don't budget real effort toward it now. |
| Ask TrustMRR for written permission to use its API | owner | A real email/outreach step, not an agent task — no default without a response. |

## Verification for the agent-only slice (no Bash available)

1. **File existence** — every path in the code/file/source map's "new files" list exists.
2. **Cross-file consistency** — `AGENTS.md`'s filenames string-match each spec's Output/Precondition
   lines; no orphan references.
3. **Config-field consistency** — every field in `config/scope.example.md` is referenced by name in
   at least one agent spec.
4. **Port fidelity** — `scripts/verify_sourcing.py` and `tests/test_verify_sourcing.py` match the
   sibling's content exactly (confirmed by direct read before writing, not by trusting a paraphrase).
5. **Deferred, needs Bash**: running the ported pytest suite, a hand-written fixture check against
   `verify_sourcing.py`, the HN-only pilot run, and any git operation.
