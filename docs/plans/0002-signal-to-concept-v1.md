# 0002 — agentic-signal-to-concept: v1 agent-spec build

## Current status and what's next, in order

**Closed arc — full 3-phase pipeline has run for real multiple times, producing this repo's first
concept candidates.** All files in the code/file/source map below exist and are committed. Remaining
open items are owner-gated or explicitly deferred (see the remaining-work table) — the next real
piece of work for this pipeline lives in `docs/plans/0003-broad-discovery.md`, not here.

1. ~~Three `.claude/agents/*.md` specs (`complaint-miner`, `build-pattern-scanner`,
   `concept-synthesizer`), `AGENTS.md` orchestrator, `config/scope.example.md`,
   `findings/README.md`, `candidates/README.md`, ported `scripts/verify_sourcing.py` +
   `tests/test_verify_sourcing.py` + minimal `pyproject.toml`, `.gitignore`/`README.md`/
   `CHANGELOG.md` updates, `CONTRIBUTING.md`~~ — done 2026-09-01, PR #4 (0001 corrections) + PR #5
   (this arc's build), both squash-merged with `gh pr merge --squash --admin`, branches deleted.
   **Repo ruleset gate note, still live**: this repo has a ruleset requiring extra approval on
   agent-authored changes; `--admin` on squash-merge is standing practice — **never modify or
   disable the ruleset itself** to work around it. If `git push`/`gh` fail with an invalid-token or
   403 error, unset both `GH_TOKEN` and `GITHUB_TOKEN` first (`env -u GH_TOKEN -u GITHUB_TOKEN
   <command>`) — an invalid `GH_TOKEN`/under-scoped `GITHUB_TOKEN` both shadow the working stored
   `gh` login.
2. ~~Run `uv run pytest` on the ported test suite~~ — done 2026-09-01, 6/6 pass. A hand-written
   mixed-sourcing fixture against `verify_sourcing.py` also confirmed correct (exits 1, flags only
   the unsourced block).
3. ~~Run the HN-only pilot~~ — done 2026-09-01: `complaint-miner` run for real (scope: PKM tool
   complaints), fetched via `polyfetch fetch --show-body`, 3 patterns / 6 sourced quotes,
   `verify_sourcing.py` exits 0. Checked in at `examples/2026-09-01-pkm-tools-pilot/` (renamed
   2026-09-03 to match the generalized `examples/<date-time-iso>-<slug>/` convention).
4. ~~`build-pattern-scanner`'s first real GitHub+Show-HN run, then `concept-synthesizer`'s first
   real run~~ — done 2026-09-03: 4 patterns, 24 independent instances, Bluesky reconfirmed blocked;
   synthesized this repo's first concept candidate, "Recallect"
   (`candidates/2026-09-03T231104Z-pkm-tools-candidate.md`). Archived at
   `examples/2026-09-03T231104Z-pkm-tools/`.
   ~~ProductHunt `developer_token` provisioned~~ — done 2026-09-04: a second full-pipeline run
   against `pkm-tools` verified it live and got real PH evidence for the first time (5 patterns, 13
   quotes across HN+PH). Independently re-derived candidate "Recallect"
   (`candidates/2026-09-04T043009Z-pkm-tools-candidate.md`) from fresh evidence — same core concept,
   materially different/better-corroborated trust pillar. Archived at
   `examples/2026-09-04T043009Z-pkm-tools/`.
   ~~`build-pattern-scanner` GitHub calls authenticated via `gh` CLI~~ — done 2026-09-04: replaced
   unauthenticated `polyfetch` GitHub search calls with `gh api`, corrected `.env.example`'s
   rate-limit claim (search API is 10/min unauth · 30/min auth, not the core API's 5,000/hour —
   confirmed live via `gh api rate_limit`). **Workspace-specific, kept out of the spec**: `gh`/`rtk
   proxy gh` calls need `env -u GH_TOKEN -u GITHUB_TOKEN` first in this workspace (same
   invalid-`GH_TOKEN`-shadows-valid-login issue as item 1).
   ~~Bolt.new / v0-Vercel ToS resolution~~ — done 2026-09-04: both checked at primary source
   (Bolt.new via operator StackBlitz's real terms, v0 via Vercel's Acceptable Use Policy), both
   explicitly ban scraping — ruled out, joining Devpost/Lovable/Replit/TrustMRR.
   `docs/plans/0001-concept.md` §3 and `build-pattern-scanner.md` step 4 updated with citations; the
   compliant-substitute pattern (GitHub topic search) extended to `topic:bolt`/`"v0.dev"`/
   `"bolt.new"`.
5. **Categories beyond `pkm-tools`, and genuine categoryless discovery — migrated to arc 0003.** Per
   the user's direction 2026-09-03 ("we dont only want to look for PKM") and 2026-09-04 ("broad scan
   for ideas without pre-defined categories"), this became its own arc
   (`docs/plans/0003-broad-discovery.md`) rather than a bolt-on here — a new discovery phase plus
   parallel worktree execution across categories is a new capability, not a small tweak. Check that
   plan for all subsequent work; nothing further is tracked in this arc.
6. **Environment note for future sessions in this repo**: Bash permission settings in this
   workspace intentionally deny shell-exploration commands (`ls`, `find`, version-probes like `rtk
   --version`) while allowing substantive commands (`pwd`, `date`, `git`, `uv run`, `polyfetch
   fetch`) — a subagent not told this explicitly will get stuck retrying `ls` and burn its turns.
   Brief every dispatched subagent to use the Read/Write tools for file checks/writes instead, and
   go straight to substantive Bash commands.

## Watch-outs

- **`polyfetch-scrape` is a sibling clone, not a dependency of this repo.** `../polyfetch-scrape`
  must exist as a checkout next to this repo for the fetch-tooling instructions to work — it's not
  vendored, submoduled, or added to `pyproject.toml`. If a future environment doesn't have that
  sibling clone, the specs' "Fetch tooling" sections need a fallback path documented.
- **Standing worktree rule** (from 0001, still in force): any subagent dispatched against this
  repo's work runs in its own git worktree — never shared with a concurrent agent. Not actually
  load-bearing until arc 0003's parallel category runs (see that plan).
- The `build-pattern-scanner` ethical boundary is load-bearing, not decorative — if extending it to
  a new sub-source, carry the boundary language forward into that addition, don't let it apply only
  to GitHub/Show HN by accident.
- `candidates/*-candidate.md` is tracked (not gitignored) — it's this repo's actual product output.
  `findings/*-findings.md` is gitignored by default.
- **This plan file used to pair with a separate `docs/handoffs/0002-signal-to-concept-v1.md`** —
  merged into this single file 2026-09-07 per `unattended-execution.md`'s updated one-file-per-arc
  rule. 0001's original embedded-handoff style (a single file) was the pattern all along; the
  separate-file pair used for 0002/0003 was the deviation, now reverted.

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
| HN-only pilot run (real `complaint-miner` execution) | **shipped** (2026-09-01) | Real run against a "PKM tool complaints" scope, via `polyfetch fetch --show-body` per the spec's fetch-tooling section. 3 patterns, 6 sourced quotes, 2 independent authors per pattern. `verify_sourcing.py` exits 0. Checked in permanently at `examples/2026-09-01-pkm-tools-pilot/` (renamed 2026-09-03 to match the generalized `examples/<date-time-iso>-<slug>/` convention below; mirrors the sibling repo's `examples/groundwork/` convention). |
| `build-pattern-scanner` first real execution | **shipped** (2026-09-03) | Real GitHub + Show HN run against the `pkm-tools` scope (Bluesky reconfirmed blocked). 4 patterns, 24 independent instances after active de-duplication. `verify_sourcing.py` exits 0. Flagged a real independence-heuristic gap (no "shared brief/curriculum" clause) via a hackathon-mandated-Obsidian-vault cluster it correctly excluded. Findings archived at `examples/2026-09-03T231104Z-pkm-tools/builds-findings.md`. |
| `concept-synthesizer` first real execution | **shipped** (2026-09-03) | Dual-source synthesis from the same run's two findings files. Produced this repo's first concept candidate, "Recallect" (`candidates/2026-09-03T231104Z-pkm-tools-candidate.md`), with explicit thin-grounding/mechanism-vs-user-population hedges rather than overstated convergence. `verify_sourcing.py` exits 0. |
| `examples/<date-time-iso>-<slug>/` archival convention | **shipped** (2026-09-03) | Generalized from the one-off `pkm-tools-pilot/` naming; documented in `examples/README.md`. Holds a run's `scope.md` + findings (otherwise gitignored working state) without duplicating the candidate, which `candidates/` already tracks permanently. |
| ProductHunt-inclusive `complaint-miner` run | **shipped** (2026-09-04) | Second full-pipeline run, `pkm-tools` scope: `PRODUCTHUNT_API_TOKEN` provisioned and verified live (auth check + real API call before use). 5 patterns, 13 sourced quotes across HN+PH. Discovered and documented a real environment constraint (PH commenter usernames redacted in this fetch environment) and a real methodology default (topic search is a dead end, `post(slug:)` lookups for known products work) — both folded into `complaint-miner.md`. Archived at `examples/2026-09-04T043009Z-pkm-tools/`. |
| `build-pattern-scanner` GitHub calls authenticated via `gh` CLI | **shipped** (2026-09-04) | Replaced unauthenticated `polyfetch` GitHub search calls with `gh api` (already-logged-in CLI) — confirmed live (`gh api rate_limit`) that the *search* endpoint's real limit is 10/min unauthenticated, 30/min authenticated, not the core API's 5,000/hour figure `.env.example` previously (incorrectly) implied applied here; `.env.example` corrected. This workspace also needs `env -u GH_TOKEN -u GITHUB_TOKEN` before `gh`/`rtk proxy gh` calls (invalid `GH_TOKEN` shadows the valid stored login) — noted above, not baked into the spec (workspace-specific, not portable). |
| Bolt.new / v0-Vercel sub-sources | **shipped/resolved — ruled out** (2026-09-04) | Both checked at primary source (StackBlitz's real terms for Bolt.new; Vercel's Acceptable Use Policy for v0) — both explicitly ban automated scraping/data-extraction. Moved from "genuine open gap" (permissive `robots.txt` was never equivalent to ToS clearance) to ruled-out, joining Devpost/Lovable/Replit/TrustMRR. `docs/plans/0001-concept.md` §3 and `build-pattern-scanner.md` step 4 updated with citations; the existing GitHub-topic-search compliant substitute extended to `topic:bolt`/`"v0.dev"`/`"bolt.new"` (not yet run for real as of this arc's close — see `docs/plans/0003-broad-discovery.md` for whether it has been since). |
| `config/scope.example.md` "Query terms used" field | **shipped** (2026-09-04) | Added so a re-run of the same scope can distinguish "more instances because the population grew" from "more instances because this run tried different search terms" — the exact ambiguity the 2026-09-04 build-pattern-scanner run surfaced (undercount correction, not new activity). |
| Git branch + commit + PR for this arc's files | **shipped** (2026-09-01, PR #4 + PR #5) | Both squash-merged with `gh pr merge --squash --admin`; branches deleted, remote-tracking refs pruned. |
| ProductHunt `developer_token` | **shipped** (2026-09-04) | Provisioned (self-serve, PH account) and referenced from `.env` (see `.env.example`) — verified live in the ProductHunt-inclusive `complaint-miner` run above. |
| App-store review access path | **shipped/resolved** (2026-09-01, PR #8+#9) | Appbot recommended as default aggregator (confirmed pricing, competitor tracking on every tier); Apple/Trustpilot scraping ruled out (ToS); Trustpilot's own API pricing/coverage still open, not blocking. |
| Reddit OAuth API terms | owner, deferred | Still **not primary-source-verified** — every Reddit-owned domain was blocked to a 2026-09-01 research pass. Convergent third-party sources report a ~Nov 2025 "Responsible Builder Policy" gating all new access behind manual approval. Recommended default: exclude Reddit from v1 regardless of how this resolves. |
| Verify `polyfetch fetch`'s CLI supports a POST request body (for ProductHunt's GraphQL call) | **shipped/resolved** (2026-09-01) | Confirmed via `polyfetch fetch --help`: no `--data`/`--body`/`--headers` flag exists. `complaint-miner.md` updated to use a direct HTTP client for ProductHunt instead. |
| Devpost / Lovable / Replit / Indie Hackers sub-sources | **shipped/resolved — ruled out** (2026-09-01, PR #11, [issue #10](https://github.com/qte77/agentic-signal-to-concept/issues/10)) | Each confirmed at primary source to ban automated scraping in its ToS (Replit names ML/AI data extraction specifically); user asked to scrape anyway, declined. Compliant substitute (GitHub `topic:hackathon`/`topic:lovable`/`topic:replit` + targeted Show HN queries) confirmed working and shipped in `build-pattern-scanner.md` instead. Indie Hackers additionally confirmed to have no RSS/feed alternative. |
| TrustMRR sub-source | **shipped/resolved — ruled out, door open** (2026-09-01, PR #11) | Has a real API, but its Acceptable Use Policy bans AI-model use "without prior written permission" — not scraped; real next step is asking TrustMRR directly, not built into any spec yet. |
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
