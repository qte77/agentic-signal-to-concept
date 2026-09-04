# Agent/coding-agent memory — Build-pattern findings (first run)

## §Research

Scope per `config/scope.md`: independent small-build activity (AI-assisted "vibe coded" or
conventionally built, treated identically) converging on **agent/coding-agent memory and
persistent-context tools for AI coding agents** (Claude Code, Cursor, Codex, Windsurf, etc.) across
sessions — not a single product. No date window is bounded in `scope.md`; this run applies
`build-pattern-scanner`'s own v1 default: a 12-month window, **2025-09-04 through 2026-09-04** (run
date). This is the first-ever execution of this spec for this scope — surfaced by
`signal-discoverer`'s run (`discovery/2026-09-04T060617Z-categories.md`, category #1).

### Independence heuristic (v1 default, applied as-is)

Per `.claude/agents/build-pattern-scanner.md`: two builds count as independent only if ALL of —
distinct author/org handles; both created within the 12-month window; no direct fork/clone
relationship to each other; no shared canonical upstream repo. **Stated plainly as the spec's coarse
v1 default, not a validated methodology.**

**Basis for the "no fork" clause:** none of this run's GitHub Search API queries used a `fork:`
qualifier, and GitHub's Search API excludes forks from results by default without one — so every
GitHub-sourced item below is fork-filtered by GitHub itself, not by per-repo inspection of the
`.fork` field.

**Two heuristic weaknesses observed this run, flagged rather than silently resolved:**

1. **Repeat naming, not repeat authorship.** The words "engram," "mneme," and "mnemosyne" (memory
   figures from Greek myth) each recur across 2–3 *independently owned* repos below (e.g.
   `Gentleman-Programming/engram`, `cdzzy/engram`, and `aiengram/engram` via Show HN — three distinct
   handles, three distinct repos, no fork relationship visible). Treated as convergent naming
   inspiration, not evidence of copying — but flagged because a shared name is an easy thing to
   mistake for a shared origin.
2. **`recallops` name collision, possible non-independence.** `tang-vu/recallops` (created
   2026-09-01) and `milos-plavsic/recallops` (created 2026-07-31) are two different GitHub accounts
   with the *same* product name, both self-described as "governed agent memory... policy authorizes
   recall," both apparently hackathon entries. Unlike the engram/mneme cases, this could plausibly be
   two submissions by the same team/person under different hackathon-registration handles rather than
   two independent builders converging on an idea. **Not counted as two independent instances below**
   — cited once, under Pattern 3, with this caveat stated rather than silently doubled.

### Source coverage

- **GitHub Search API (`search/repositories`), authenticated `gh` CLI** — reachable, 30 req/min
  authenticated confirmed live via `env -u GH_TOKEN -u GITHUB_TOKEN gh api rate_limit --jq
  '.resources.search'` (`{"limit":30,"remaining":30,...}`). Queries run (all `created:>2025-09-04`
  unless noted): `"agent memory" in:name,description`, `topic:agent-memory`, `"coding agent" memory
  persistent`, `topic:mcp-memory`, `topic:hackathon agent memory`, `topic:lovable OR topic:replit
  memory agent`, `"bolt.new" memory agent`, `"v0.dev" memory agent`, `topic:bolt memory`. All fetched
  via `gh api -X GET search/repositories --jq '...'` (field-selected, no paraphrase). **Found** —
  large, genuinely on-topic population; not blocked, not empty for the core queries.
- **Show HN via HN's Algolia API** — reachable, unauthenticated, via `polyfetch fetch <url>
  --show-body`. Query: `query=agent memory&tags=show_hn&numericFilters=created_at_i>1725436800`
  (12-month window), 30 hits returned, full-text query (not a query language, per spec's confirmed
  Algolia behavior). **Found** — real, on-topic, high-density signal concentrated in the last ~2.5
  weeks of the window (2026-08-18 through 2026-09-03), plausibly reflecting Algolia's relevance/date
  ranking surfacing recent posts first rather than the category itself being that concentrated; not
  independently verified against an all-time baseline this run.
- **Bluesky public post-search API** — **confirmed still blocked.** `polyfetch fetch
  'https://public.api.bsky.app/xrpc/app.bsky.feed.searchPosts?q=agent%20memory%20coding' --json`
  returned a fresh, literal `FingerprintBlock` at HTTP 403 after 3 patchright attempts — consistent
  with the spec's documented block. Not re-tried against the sibling `getProfile` endpoint this run
  (already independently confirmed working in prior runs' documentation); treated as **blocked** per
  the four-state discipline, not absence of signal.
- **Step-4 Bolt.new/v0-Vercel GitHub substitute, run for real for the first time this repo-wide** —
  `"bolt.new" memory agent` (2 hits, both false-positive/off-topic: a shell-script health-check repo
  and a generic Bolt.new-style IDE clone, neither an agent-memory tool), `"v0.dev" memory agent` (0
  hits), `topic:bolt memory` (2 hits, both false positives — "Bolt" matching the Neo4j **Bolt wire
  protocol**, unrelated to the Bolt.new AI builder). **Found-empty for this category**: the substitute
  channel exists and returns real results for other categories, but surfaces no on-topic
  agent-memory signal here — a query-term collision (Bolt.new vs. Neo4j Bolt), not a source failure.
  `topic:lovable OR topic:replit memory agent` also **found-empty** (0 hits) for this category.
- **Devpost, AI-builder-tool showcases (Lovable, Replit, Bolt.new, v0/Vercel), Indie Hackers/X,
  TrustMRR** — **excluded-by-scope**, per the spec's ruling (ToS-banned scraping / no viable API /
  explicit anti-AI-training clause). Not attempted directly; GitHub-topic and Show HN substitutes
  used instead (see above).

### Ethical boundary applied

Every pattern below names an aggregate count and lists multiple independent instances; no single
project is presented as "the one to build like," and none is offered as a build-and-launch template.
Stars, HN points, and org-vs-individual ownership appear only as neutral corroborating metadata,
never as a ranking signal elevating one project above the others in its pattern. No project below is
claimed to be MIT/Apache-licensed *and* explicitly non-commercializing by its creator, so the spec's
narrow outreach-candidate exception is not invoked for any of them.

---

### Pattern 1 — 15 cited independent instances (of a substantially larger population seen): MCP or
local-storage persistent memory built specifically *for coding agents* (Claude Code, Cursor, Codex,
Windsurf, OpenCode, etc.), cross-session, most commonly served over MCP

This is the dominant convergence: builders shipping a standalone tool, one job, "give your coding
agent memory across sessions/restarts," almost always via an MCP server or a local file/SQLite store
consumed by multiple named coding-agent products at once (not building a new coding agent itself).

> "Portable project memory across Claude Code, Codex and OpenCode, plus token accounting measured
> from harness transcripts. Local file I/O, no API calls, no telemetry."
> — cytostack, [cytostack/openwolf](https://github.com/cytostack/openwolf), created 2026-03-15

> "Open-source coding agent memory. Records issues, attempts, fixes and decisions, then warns your
> agent before it repeats an approach that already failed. Native MCP server for Claude Code, Cursor,
> Antigravity and Codex. 100% local, no cloud, no telemetry. MIT."
> — riponcm, [riponcm/projectmem](https://github.com/riponcm/projectmem), created 2026-05-09

> "Persistent memory system for AI coding agents. Agent-agnostic Go binary with SQLite + FTS5, MCP
> server, HTTP API, CLI, and TUI."
> — Gentleman-Programming (org), [Gentleman-Programming/engram](https://github.com/Gentleman-Programming/engram),
> created 2026-02-16

> "Persistent project memory for AI coding agents. Structured scaffold + drift detection CLI."
> — mex-memory (org), [mex-memory/mex](https://github.com/mex-memory/mex), created 2026-03-21
> (independently corroborated by a Show HN launch of the same project — see Pattern 2's
> cross-reference note)

> "Open-source cross-agent memory layer for coding agents via MCP. Compatible with Claude Code,
> Codex, Cursor, Windsurf, Gemini CLI, Antigravity, OpenClaw, Hermes Agent, Oh-my-Pi, Pi, Copilot,
> Kiro, OpenCode, and Trae."
> — AVIDS2, [AVIDS2/memorix](https://github.com/AVIDS2/memorix), created 2026-02-14

> "Persistent Knowledge Graph Memory for AI Coding Agents. Adds long-term context (goals, strategies,
> preferences) to Cursor, Windsurf, & VS Code via MCP."
> — Hexecu, [Hexecu/mcp-neuralmemory](https://github.com/Hexecu/mcp-neuralmemory), created 2026-01-17

> "agentmemory: persistent memory for coding agents (Claude Code, OpenAI Codex, Cursor, Agent) with
> local markdown storage, daily logs, scratchpad, and qmd semantic search."
> — jayzeng, [jayzeng/agentmemory](https://github.com/jayzeng/agentmemory), created 2026-02-21

> "Give your coding agent a persistent memory. source-of-truth keeps a living docs/ catalog of what
> exists, why, and what rules apply — so AI stops rebuilding, deleting, and breaking things it never
> knew were there. Works with Claude, Codex, Cursor, Gemini, Copilot & more."
> — ngocquang, [ngocquang/source-of-truth](https://github.com/ngocquang/source-of-truth), created
> 2026-06-23

> "mneme — Persistent memory system for AI coding agents. Single binary, zero dependencies. Hybrid
> retrieval (BM25 + vector + graph), automatic consolidation, multi-scope memory, and agent-agnostic
> via MCP."
> — wirvii (org), [wirvii/mneme](https://github.com/wirvii/mneme), created 2026-04-13

> "Persistent long-term memory for Claude Code agents — SQLite + FTS5 + sqlite-vec + RRF hybrid
> recall over MCP, with Chinese tokenization. Local-first, graceful degradation."
> — AgentGameLab (org), [AgentGameLab/mneme](https://github.com/AgentGameLab/mneme), created
> 2026-06-22 (same product name as wirvii/mneme above; different owner, different repo, no fork
> relationship visible in search results — a third independently-owned "mneme" exists too, see next)

> "Enable AI coding agents to retain long-term knowledge, task progress, and execution context using
> a three-layer persistent memory system."
> — bilay314, [bilay314/mneme](https://github.com/bilay314/mneme), created 2026-03-09

> "Persistent memory for AI coding agents. Your agent learns your codebase the way a senior engineer
> would — what files go together, what you usually edit next. Works with Claude Code, Cursor, Cline,
> Continue. 100% local."
> — dfrostar, [dfrostar/neuralmind](https://github.com/dfrostar/neuralmind), created 2026-04-16

> "Persistent, local memory for developers and their coding agents. Records commands, output, errors,
> and the fixes that worked into SQLite and serves them over MCP."
> — wuisabel-gif, [wuisabel-gif/MemWhale](https://github.com/wuisabel-gif/MemWhale), created
> 2026-06-17

> "Yadgar (یادگار) — Persian for 'memento, keepsake.' A persistent memory engine for AI coding agents
> that survives sessions and /clears: it decays what you stop touching, promotes what recurs, filters
> recall to the git branch you're on, and pairs every memory with a curated wiki searched through the
> same ranking pipeline. Works with any MCP client."
> — m-agahi, [m-agahi/yadgar](https://github.com/m-agahi/yadgar), created 2026-07-25

> "Memory for AI Agents"
> — moorcheh-ai (org), [moorcheh-ai/memanto](https://github.com/moorcheh-ai/memanto), created
> 2026-03-23 (independently corroborated by a Show HN launch of the same project — see Pattern 2's
> cross-reference note)

**More seen this run, not individually cited above (representative name list, same pattern, distinct
handles, all within window):** `kiwagu/zero-memory`, `mrkinglollipop/Khipu`, `janblade/MaiKS`,
`vshulcz/deja-vu`, `mthines/lorekit`, `x-cmd-install/obsidian-mind`,
`Boybo5580/verified-memory-vault`, `lleontor705/cortex`, `EremesNG/thoth-mem`, `dcellison/kai`,
`xianzhon/pi-cloud`, `prjct-app/cli`, `JKHeadley/instar`, `earthloong/memex`, `repairman29/chump`,
`solo-agent/solo`, `ReflexioAI/claude-smart`, `CodeAbra/iai-personal-memory-engine`. GitHub's
`"coding agent" memory persistent created:>2025-09-04` query alone reported **649 total matches**
(not exhaustively read; the above is a filtered, individually-verified sample).

---

### Pattern 2 — 12 cited independent instances: Show HN "I built this" launches for coding-agent
memory tools, heavily concentrated in the last ~2.5 weeks of the window (2026-08-18 – 2026-09-03)

Distinct signal type from Pattern 1 (a public "I built this" launch post, not just a repo existing) —
per the spec, not a sub-case of a complaint. All from `hn.algolia.com` `query=agent
memory&tags=show_hn`, distinct author handles, distinct projects, all self-describe explicitly as
memory/context tools for coding or AI agents.

> "Show HN: Engram – Shared procedural runbook memory for AI coding agents via MCP"
> — yash200096, [Show HN post](https://news.ycombinator.com/item?id=49552882) linking
> [aiengram/engram](https://github.com/aiengram/engram), created 2026-09-03, 1 point (a third,
> independently-owned "Engram"/"engram"-named project — see Pattern 1's naming caveat)

> "Show HN: Rta-Smriti – local-first project memory for coding agents"
> — SulabhDubey, [Show HN post](https://news.ycombinator.com/item?id=49511544) linking
> [sulabhdubey/rta-smriti-brain](https://github.com/sulabhdubey/rta-smriti-brain), created
> 2026-08-31, 2 points

> "Show HN: Decispher – persistent engineering context and memory for coding agents"
> — iamalizaidi, [Show HN post](https://news.ycombinator.com/item?id=49509142), created 2026-08-31,
> 5 points (no linked URL in the API response; matches "Decispher," a product also named in this
> run's `config/scope.md` carried-forward discovery notes — independent re-surfacing via a different
> query/source, corroborating the discovery pass rather than duplicating it)

> "Show HN: Rafter – an MCP server that shares one team's memory, skills and agents"
> — kristohb, [Show HN post](https://news.ycombinator.com/item?id=49476048) linking
> https://heyrafter.xyz/, created 2026-08-28, 2 points

> "Show HN: Knowl – agent memory with write-time supersession, 0.90 on MAB"
> — dat999zx, [Show HN post](https://news.ycombinator.com/item?id=49465138) linking
> https://knowl.cloud, created 2026-08-27, 3 points (same author also posted about the same project
> on 2026-08-22 — [id=49399942](https://news.ycombinator.com/item?id=49399942) — counted once here
> per the independence heuristic's same-author dedup; "Knowl" also named in this run's
> `config/scope.md` discovery notes, independently corroborated)

> "Show HN: Contextual – local codebase memory for AI coding agents"
> — aarjunm04, [Show HN post](https://news.ycombinator.com/item?id=49460693) linking
> https://contextuallabs.dev, created 2026-08-27, 1 point

> "Show HN: A lightweight, stateless database for agent memory"
> — anuptalwalkar, [Show HN post](https://news.ycombinator.com/item?id=49450816) linking
> https://polign.com/blog-edge-agent-memory, created 2026-08-26, 36 points

> "Show HN: Mnemosyne Local hierarchical memory engine for AI agents (MCP Native)"
> — mfathy7, [Show HN post](https://news.ycombinator.com/item?id=49410254) linking
> [M4F-S/mnemosyne](https://github.com/M4F-S/mnemosyne), created 2026-08-23, 10 points ("Mnemosyne"
> also named in this run's `config/scope.md` discovery notes, independently corroborated)

> "Show HN: Active Source of Truth for Your Coding Agents"
> — anphamthanh, [Show HN post](https://news.ycombinator.com/item?id=49405261) linking
> https://meetless.ai, created 2026-08-23, 4 points

> "Show HN: Knownbase, an MCP server for persistent AI agent memory"
> — knownbase_dev, [Show HN post](https://news.ycombinator.com/item?id=49359680) linking
> https://knownbase.dev/, created 2026-08-19, 2 points

> "Show HN: Seahorse – an agent's memory that lives in your own notes"
> — ssanvi_builds, [Show HN post](https://news.ycombinator.com/item?id=49357513) linking
> [ssanvi-builds/seahorse](https://github.com/ssanvi-builds/seahorse), created 2026-08-19, 1 point

> "Show HN: CommitLore – Git memory for decisions coding agents keep forgetting"
> — weplay0628, [Show HN post](https://news.ycombinator.com/item?id=49356637) linking
> [MongLong0214/commitlore](https://github.com/MongLong0214/commitlore), created 2026-08-19, 1 point

**Cross-referenced, not double-counted:** `architdhamija`'s [Show HN post](https://news.ycombinator.com/item?id=49547372)
("Mex 0.8.0 – team memory for coding agents," created 2026-09-03) is the same `mex-memory/mex`
project already cited in Pattern 1; `supportm`'s [Show HN post](https://news.ycombinator.com/item?id=49356590)
("Memanto Is a Memory Agent," created 2026-08-19) is the same `moorcheh-ai/memanto` project already
cited in Pattern 1. Both are corroboration of Pattern 1 instances, not additional independent
instances, and are excluded from this pattern's count of 12.

**What the concentration suggests:** all 12 cited posts (and both cross-referenced ones) fall inside
2026-08-18–2026-09-03, the final 2.5 weeks of the 12-month window, out of an Algolia result set sorted
by relevance/recency rather than a uniform date scan. That concentration is plausibly a query-ranking
artifact (recent posts surface first) rather than evidence the category itself only heated up in late
August — not independently verified against an all-time/date-sorted baseline this run.

---

### Pattern 3 — 10 cited independent instances (of 89 raw hits): agent-memory builds explicitly tied
to a named hackathon or competition, via the `topic:hackathon` GitHub-topic substitute channel

Per the spec's compliant substitute for the ruled-out Devpost/hackathon-showcase channel. Broader than
"coding agent" specifically — most of these are general AI-agent memory rather than a coding-agent
tool by name — kept as a distinct, weaker-fit pattern rather than merged into Pattern 1. **Noise
filter applied per the spec's v1 heuristic:** repos over ~500 stars excluded as
established tools rather than individual hackathon submissions — this excluded
[`outsourc-e/hermes-workspace`](https://github.com/outsourc-e/hermes-workspace) (6,561 stars) from
this pattern's count. README/description inspected for each cited repo below to confirm which
hackathon, when, and by whom, per the spec's requirement.

> "Persistent policy memory that governs agent hiring, payments, and trust across sessions."
> — tang-vu, [tang-vu/recallops](https://github.com/tang-vu/recallops), created 2026-09-01 (name
> collision with `milos-plavsic/recallops` below — see the independence-heuristic caveat above; cited
> once)

> "TRACE: memory-powered BNPL agent on Base Sepolia. Sibyl remembers, Alex (Virtuals ACP) requests,
> TRACE decides, Base settles."
> — 0andadream, [0andadream/Trace](https://github.com/0andadream/Trace), created 2026-08-21

> "Private, evidence-first document agent with persistent local memory, exact citations, reversible
> actions, and bounded Nemotron reasoning."
> — ellmos-ai, [ellmos-ai/NemoFold](https://github.com/ellmos-ai/NemoFold), created 2026-08-30

> "Verified AI project handoff agent that turns scattered project evidence into persistent,
> evidence-backed knowledge and handoff readiness."
> — atharv9017, [atharv9017/project-memory](https://github.com/atharv9017/project-memory), created
> 2026-08-29

> "Long-term memory for AI agents on HydraDB. Answers from memory with citations, abstains when
> unsure. Hack Hydra 2026, Track 3."
> — mwihoti, [mwihoti/engram](https://github.com/mwihoti/engram), created 2026-08-19 (a fourth,
> independently-owned "engram"-named project — Hack Hydra 2026 entry, distinct from the three cited
> in Patterns 1–2)

> "Agent memory layer built on HydraDB's temporal graph model — represents fact supersession
> explicitly and abstains when the answer isn't there. Evaluated against a vector-only baseline on
> LongMemEval-S for Hack Hydra Track 3."
> — PranayBhatnagar, [PranayBhatnagar/agent-memory-graph](https://github.com/PranayBhatnagar/agent-memory-graph),
> created 2026-08-20 (same hackathon as `mwihoti/engram` above, different competing team/handle — not
> a fork, independently entered)

> "Strands Agents SDK + Cogram procedural memory (file:line citations) — Agents for Humans 2026"
> — xqscora, [xqscora/cogram-strands-afh](https://github.com/xqscora/cogram-strands-afh), created
> 2026-08-27

> "3D memory island where Google ADK agents keep your memories as grounded books... All Things
> Agentic Hackathon entry."
> — hec-ovi, [hec-ovi/memory-keepers](https://github.com/hec-ovi/memory-keepers), created 2026-08-08

> "Drift-adaptive, zero-write-token memory for a Qwen agent (Qwen Cloud Hackathon, Track 1
> MemoryAgent). Two token-free signals; +0.35 clean recall under drift vs SOTA; Alibaba ECS + OSS."
> — somtri, [somtri/flashbulb](https://github.com/somtri/flashbulb), created 2026-07-20

> "Governed SRE agent memory on CockroachDB and AWS: similarity discovers evidence; policy authorizes
> recall."
> — milos-plavsic, [milos-plavsic/recallops](https://github.com/milos-plavsic/recallops), created
> 2026-07-31 (see the `recallops` name-collision caveat above — this and `tang-vu/recallops` are cited
> as one combined instance, not two, pending confirmation either way)

---

### Summary

- **Patterns found:** 3 — (1) MCP/local-storage persistent memory built specifically for named coding
  agents, (2) Show HN "I built this" launches for the same category, concentrated in the last 2.5
  weeks of the window, (3) hackathon-tagged general agent-memory builds (weaker fit, broader than
  "coding agent" specifically).
- **Cited independent instances:** 37 total sourced citations with author handle + repo/post URL +
  creation date (Pattern 1: 15 · Pattern 2: 12 · Pattern 3: 10), all within the 12-month window
  (2025-09-04–2026-09-04), all distinct authors, none forked from another per GitHub's default
  fork-exclusion in search results. Two caveats stated rather than silently resolved: the
  `recallops` name-collision (cited once, not two) and the recurring "engram/mneme/mnemosyne" naming
  convention across otherwise-unrelated owners (four independently-owned "engram"-named projects
  alone: `Gentleman-Programming/engram`, `cdzzy/engram` (seen, not cited above), `aiengram/engram`,
  `mwihoti/engram`).
- **Much larger population seen but not individually cited:** GitHub's `"coding agent" memory
  persistent` query alone reported 649 total matches; `"agent memory" in:name,description` reported
  5,210 (broad, includes many off-topic/tangential hits given the two-word full-text match); `topic:
  agent-memory` reported 3,275; `topic:hackathon agent memory` reported 89 raw hits (10 cited above
  after the >500-star filter and one name-collision merge).
- **Source states:** GitHub Search API — found (authenticated `gh` CLI, 30 req/min, no rate-limit
  issues this run) · Show HN (Algolia) — found · Bluesky — **blocked** (fresh 403 `FingerprintBlock`
  confirmed this run) · Devpost/Lovable-Replit-Bolt.new-v0-Vercel showcases/Indie Hackers/TrustMRR —
  excluded-by-scope (GitHub-topic and Show HN substitutes used instead, per spec) · the Bolt.new/v0
  GitHub substitute specifically (`topic:bolt`, `"bolt.new"`, `"v0.dev"`) — **found-empty for this
  category** (2 false-positive hits from the unrelated Neo4j Bolt wire protocol, 0 genuine matches);
  `topic:lovable OR topic:replit` combined with memory/agent terms — found-empty (0 hits).
- **Net read for the concept-synthesis stage:** this category shows a large, currently-accelerating
  population of independent small builders converging on "persistent, cross-session, cross-agent
  memory for coding agents specifically" — most commonly shipped as a small MCP server or local
  file/SQLite store, agent-agnostic across Claude Code/Cursor/Codex/Windsurf/OpenCode. The Show HN
  concentration in the last 2.5 weeks of the window and the sheer GitHub match counts (649 for the
  tightly-scoped "coding agent" + memory + persistent query) both point toward this being an active,
  not cooling, convergence as of this run's date — consistent with the discovery pass's original
  signal for this category.
