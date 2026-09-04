# PKM tools — Build-pattern findings

## §Research

Scope per `config/scope.md`: independent small-build activity (AI-assisted "vibe coded" or
conventionally built, treated identically) converging on the note-taking / personal-knowledge-management
(PKM) problem space — Notion/Obsidian/Evernote/Roam-adjacent tools, not a single product. No date
window is bounded in scope.md; this run applies `build-pattern-scanner`'s own v1 default: a 12-month
window (2025-09-03 through 2026-09-03, the run date) for the independence heuristic. This is
`build-pattern-scanner`'s first real execution — no prior run exists to compare against.

### Independence heuristic (v1 default, applied as-is)

Per `.claude/agents/build-pattern-scanner.md`: two builds count as independent only if ALL of —
distinct author/org handles; both created within the 12-month window; no direct fork/clone
relationship to each other; no shared canonical upstream repo. **This is stated plainly as the
spec's coarse v1 default, not a validated methodology.** `config/scope.md`'s "Independence heuristic
override: Not applicable" note predates this spec's existence and was ignored per the orchestrator's
instruction; the spec's own default is applied throughout.

Applying it required active de-duplication — instances excluded from independent-instance counts
because they failed the test:
- **GYST** — posted to Show HN twice, same day (2025-10-09), by two different HN accounts (`ricroz`
  and `arnaudbd`) about the same product (https://gyst.fr/) — almost certainly co-founders of the
  same team, not independent authors. Counted once, not twice.
- **PileaX** — two Show HN launches by the same author (`pileax`), same product. Counted once.
- **`aliasunder`** authored two separate repos in this window (`vault-onboarding`, `vault-cortex`) —
  only one is used per pattern below to avoid inflating the same person's output into two "independent"
  data points.
- Cross-channel duplicates (e.g. `inkeep/open-knowledge` found via both GitHub topic search and its
  own Show HN launch post; `jpmoo/hermesnotes` likewise) are cited with both source URLs but counted
  as one instance.
- **One assumption stated plainly:** 13 of the 24 instances below are sourced from a Show HN post
  rather than directly from the GitHub search API (Pattern 1: 3, Pattern 2: 4, Pattern 3: 3, Pattern
  4: 3); for all 13, the HN post's `created_at` is used as the within-window-proxy date. Of those 13,
  9 link to a GitHub repo — one (`inkeep/open-knowledge`) was cross-checked, since it also surfaced
  directly via the GitHub search API (repo `created_at` 2026-06-03, consistent with its window
  placement); the other 8 repo-linked instances' own `created_at` was **not** separately fetched, so
  it is possible (though unlikely for a Show HN launch) one predates its post by more than the window
  allows. The remaining 4 of the 13 (Mininote, Envy, VaultNote, Tentacle in Pattern 2) link only to a
  product site, not a repo — the HN post date is the only creation-date evidence available for those.
- **Found, but excluded by the independence heuristic** (the query result state is "found" — the
  independence heuristic is a separate, second-stage filter applied after finding, not a new
  fifth source-state): the `topic:hackathon+obsidian` GitHub query surfaced five distinct-author repos
  — `daniyalazhar123/AI-Employee-Vault`, `rj41-w2/Personal-AI-Employee-Hackathon-0`,
  `safdarayubpk/PersonalAIEmployee`, `syeda-hoorain-ali/personal-ai-employee-hackathon`,
  `MrAfoo/ai-employee-hackathon-2026` — all built for a "Personal AI Employee Hackathon." Per the
  spec's explicit instruction to inspect each hackathon repo's README before citing it, the README of
  [`syeda-hoorain-ali/personal-ai-employee-hackathon`](https://raw.githubusercontent.com/syeda-hoorain-ali/personal-ai-employee-hackathon/main/README.md)
  was fetched and read in full: the hackathon's own tiered checklist *mandates* Obsidian as a
  deliverable — "Bronze Tier ... `[X] Obsidian vault with Dashboard.md and Company_Handbook.md`" is a
  required checkbox, not a suggestion. That means these five did not independently choose Obsidian;
  they complied with one hackathon organizer's prescribed architecture. Whatever the organizer's
  reasons for prescribing it, this is one design decision replicated five times under instruction —
  the opposite of the independent-convergence signal this scan looks for — so it is **not** counted as
  a pattern below, despite passing the heuristic's literal distinct-author/no-fork/no-shared-repo
  test. **This is a heuristic weakness worth flagging to the spec owner**: the v1 independence
  heuristic has no clause for "shared brief/curriculum" — a bounded competitive event can make five
  people pass every literal independence test while making the same prescribed choice.

### Source coverage

- **GitHub public REST search API** — reachable, unauthenticated (a `GITHUB_TOKEN` env var was
  present in this session but `polyfetch fetch` has no flag to attach a custom `Authorization`
  header — confirmed in `complaint-miner.md`'s own note about the same CLI — so all six queries below
  ran unauthenticated at the public 60 req/hour rate; well within budget). All fetched via
  `polyfetch fetch <url> --show-body`, piped through `jq` to extract only `full_name`, `owner.login`,
  `created_at`, `stargazers_count`, `description` (the raw response bodies were 3,000+ lines of
  boilerplate URL fields per 30-item page — `jq` filtering, not summarization, kept every field used
  as evidence untouched and verbatim). Queries run: `topic:pkm created:>2025-09-03` (946 total hits),
  `topic:note-taking created:>2025-09-03` (1,735 total hits), `topic:personal-knowledge-management
  created:>2025-09-03` (863 total hits), `"obsidian alternative" created:>2025-09-03` (27 total hits —
  read in full), `topic:hackathon+note-taking` (1 hit — thin), `topic:hackathon+obsidian` (7 hits, all
  read — this surfaced the found-but-excluded hackathon cluster, see the independence-heuristic
  section above). All genuinely found, not blocked or empty. Per the spec's
  noise-reduction heuristic for `topic:hackathon`, results were checked for the >500-star
  exclusion (none of the hackathon-topic hits exceeded it) and preference for within-window creation
  (all did).
- **Show HN via HN's Algolia API** — reachable, unauthenticated, via `polyfetch fetch <url>
  --show-body`. Ran single/dual-term plain-text queries per the spec's confirmed constraint (no
  `OR`/boolean syntax): `note-taking`, `PKM`, `second brain`, `Obsidian`, `knowledge management`,
  `personal wiki` (all `tags=show_hn`, `numericFilters=created_at_i>1756857600` for the 12-month
  window), plus the step-4 compliant substitute for the hackathon/Lovable/Replit-showcase gap:
  `vibe coded notes` (`tags=story`), `vibe coded` (`tags=show_hn`), `lovable notes`
  (`tags=show_hn`, no date filter — thin recent results). All genuinely found, not blocked or empty;
  ~740 raw hits total across queries, all titles read.
- **Bluesky public post-search API** (`public.api.bsky.app/xrpc/app.bsky.feed.searchPosts`) —
  **confirmed still blocked as of this run (2026-09-03)**, consistent with the spec's documented
  2026-09-01 finding. `polyfetch fetch "https://public.api.bsky.app/xrpc/app.bsky.feed.searchPosts?q=obsidian%20alternative&limit=25" --show-body`
  returned: `FingerprintBlock: patchright fetch failed after 3 attempts (status=403)` — an HTTP 403
  at the fetch-tooling layer itself (polyfetch's stealth-Patchright tier), matching the spec's
  description of a path-specific WAF block rather than a generic network failure. Recorded as
  **blocked**, not absence of signal, per the four-state discipline.
- **Devpost, Lovable/Replit showcases, Indie Hackers, TrustMRR, Bolt.new, v0/Vercel** —
  **excluded-by-scope** per the task instructions and the spec's own step 4 (already ruled out or
  deferred in `docs/plans/0001-concept.md` §3 and issue #10). Not attempted this run. The Show HN
  `vibe coded`/`lovable` queries above are the spec's designated compliant substitute for this gap;
  yield specific to the PKM category was thin (most hits were general vibe-coded showcase posts, not
  PKM tools) but not zero — see immediately below.

**Vibe-coded PKM hits (step-4 substitute check):** two hits genuinely on-topic for this category,
both too thin (one instance each, no second independent match found) to form their own pattern:

> "We vibe coded our team's issue tracker, knowledge base, telemetry board"
> — bhackett, [Show HN post](https://news.ycombinator.com/item?id=46207330), created 2025-12-09,
> 8 points

> "I vibe-coded my own productivity system"
> — easylife_ai, [Show HN post](https://news.ycombinator.com/item?id=46861315), created 2026-02-02,
> 1 point

### Ethical boundary applied

Every pattern below names an aggregate count and lists multiple independent instances; no single
project is presented as "the one to build like," regardless of its stars or HN points (which appear
below only as neutral corroborating metadata — never as a ranking signal elevating one project above
the others in its pattern, and not used to order the instances below either).

---

### Pattern 1 — Independent builders repeatedly build "Obsidian alternatives": local-first, plain-markdown editors explicitly positioned against the market leader

8 independent instances, distinct authors, all created within the 12-month window. The GitHub
`"obsidian alternative"` free-text search alone returned 27 total hits — most of them individually
distinct small projects, not forks of one another or of Obsidian itself (which is closed-source and
unforkable on GitHub). A community-maintained directory,
[`slimhk45/awesome-obsidian-alternatives`](https://github.com/slimhk45/awesome-obsidian-alternatives)
(created 2026-03-07, 72 stars), independently corroborates that this is a recognized, populated
category rather than a handful of stray repos — cited here as aggregate corroboration only, not as
one of the 8 counted instances (it is a list, not a build). **Ownership caveat:** of the 8, 2 are
GitHub-Organization-owned rather than individual accounts (`inkeep/open-knowledge`, node type
`Organization`, 3,983 stars; `swarmclawai/swarmvault`, 678 stars) — checked directly via
`owner.type` in the raw GitHub search JSON. The scope is "independent *small-build* activity"; an
org account could mean a small team (consistent with the scope) or something closer to a funded
product launch (less so) — this repo-search field alone can't distinguish the two, so both are kept
in the count but flagged rather than silently treated as identical to the 6 individual-account
instances.

> "The open-source, local-first knowledge engine & Obsidian alternative. Powered by Tauri v2, SQLite
> WASM, and live-preview Markdown."
> — yvliet, [yvliet/flint](https://github.com/yvliet/flint), created 2026-08-31

> "A blazingly fast, local-first writing workspace for notes, journals, and connected knowledge. ...
> An open-source Notion and Obsidian alternative."
> — remcostoeten, [remcostoeten/skriuw](https://github.com/remcostoeten/skriuw), created 2025-10-30

> "Lightweight, vault-free markdown viewer — Electron-based Obsidian alternative"
> — kfrance, [kfrance/pumice](https://github.com/kfrance/pumice), created 2026-03-07

> "The local-first LLM Wiki: open-source knowledge graph builder, RAG knowledge base, and agent
> memory store. ... An Obsidian alternative for personal knowledge management, AI second brain, and
> durable Claude Code / Codex / OpenClaw memory."
> — swarmclawai, [swarmclawai/swarmvault](https://github.com/swarmclawai/swarmvault), created
> 2026-04-06

> "Open-Source Obsidian alternative for note taking and oraganisation, CLI based note taking"
> — karankantaria, [karankantaria/Engram](https://github.com/karankantaria/Engram), created
> 2026-06-19

> "Files.md – Open-source alternative to Obsidian"
> — zakirullin, [Show HN post](https://news.ycombinator.com/item?id=48179677) linking
> [zakirullin/files.md](https://github.com/zakirullin/files.md), created 2026-05-18, 730 points

> "OpenKnowledge – open source AI-first alternative to Obsidian/Notion"
> — engomez, [Show HN post](https://news.ycombinator.com/item?id=48675435) linking
> [inkeep/open-knowledge](https://github.com/inkeep/open-knowledge) (also independently surfaced by
> both the `topic:pkm` and `topic:personal-knowledge-management` GitHub searches, created
> 2026-06-03), created 2026-06-25, 381 points

> "Opal Editor, free Obsidian alternative for markdown and site publishing"
> — rbbydotdev, [Show HN post](https://news.ycombinator.com/item?id=46669478) linking
> [rbbydotdev/opal](https://github.com/rbbydotdev/opal), created 2026-01-18, 36 points

**What this means for a candidate:** the framing "Obsidian, but ___" is not one team's idea — it is
the default self-description a wide, unconnected population of small builders reach for when they
build a note-taking tool at all, across at least eight distinct efforts in one year. That is evidence
of category-level dissatisfaction pointed specifically at Obsidian (not note-taking tools generally)
— not evidence that any one of these particular reimplementations is worth building toward directly.

---

### Pattern 2 — Independent builders converge on "local-first, no cloud, no lock-in" as the note-taking app's core value proposition

6 independent instances, distinct authors, all within the 12-month window. Unlike Pattern 1, none of
these explicitly names Obsidian as the point of comparison — the convergence is on the storage/trust
model itself, not on any one competitor.

> "Local-first second brain with semantic search. Stores notes as Markdown files and indexes them
> with a local ML model — no cloud, no API keys."
> — evan-moon, [evan-moon/memex](https://github.com/evan-moon/memex), created 2026-05-02

> "Mininote: Instant, plain-text note taking without tracking or lock-in"
> — helba-ai, [Show HN post](https://news.ycombinator.com/item?id=49300129) linking
> https://mininote.ink/, created 2026-08-14, 23 points

> "Envy - A flat-file, frictionless note-taking application for macOS"
> — widowlark, [Show HN post](https://news.ycombinator.com/item?id=48987509) linking
> https://envynote.app, created 2026-07-21, 4 points

> "A private, self-hosted notes app that never leaves your hardware. Tags, folders, note linking,
> full-text search, and per-note end-to-end encryption reachable only over your own Tailscale
> network. No cloud, no accounts, no telemetry."
> — Colbysdovi, [Colbysdovi/Lockpad-Public](https://github.com/Colbysdovi/Lockpad-Public), created
> 2026-07-26

> "VaultNote – Local-first encrypted note-taking in the browser"
> — powerwild, [Show HN post](https://news.ycombinator.com/item?id=47279803) linking
> https://vaultnote.saposs.com/, created 2026-03-06, 3 points

> "Tentacle – Local-first note taking app that organizes itself"
> — nicoleao, [Show HN post](https://news.ycombinator.com/item?id=47155400) linking
> https://www.tentaclenote.app/, created 2026-02-25, 2 points (this post independently surfaced in
> both the `note-taking` and `PKM` Algolia queries)

**What this means for a candidate:** "your notes as plain files you own, offline by default" reads as
a load-bearing requirement independently arrived at by builders who never named each other's projects
as inspiration in their descriptions — a structural expectation, not a feature request one team could
satisfy and move on from.

---

### Pattern 3 — Independent builders graft AI-agent memory onto an existing PKM tool's vault, specifically Obsidian's

6 independent instances, distinct authors, all within the 12-month window. This is a distinct
convergence from Patterns 1–2: rather than replacing Obsidian, these builders treat an existing
Obsidian vault as the durable-memory substrate for coding agents (Claude Code, Cursor, Codex, etc.).

> "Persistent memory for Claude Code and 6 other CLI agents, stored as plain markdown in your
> Obsidian vault. Stop re-explaining your projects, decisions and people every session."
> — eugeniughelbur, [eugeniughelbur/obsidian-second-brain](https://github.com/eugeniughelbur/obsidian-second-brain),
> created 2026-03-24

> "Standalone MCP server that gives AI agents access to your Obsidian vault — hybrid search, memory,
> tasks, files, OAuth 2.1."
> — aliasunder, [aliasunder/vault-cortex](https://github.com/aliasunder/vault-cortex), created
> 2026-05-01

> "AI-powered Obsidian plugin for semantic search, vault auditing, note discovery and AI writing"
> — zinverno, [zinverno/obsidian-ai-hub](https://github.com/zinverno/obsidian-ai-hub), created
> 2026-05-13

> "Local-first CLI to make Obsidian vaults searchable for AI agents"
> — nmdra, [Show HN post](https://news.ycombinator.com/item?id=48974142) linking
> [nmdra/notebrain-cli](https://github.com/nmdra/notebrain-cli), created 2026-07-20, 4 points

> "Obsidian-Semantic, a CLI that lets agents search your vault by meaning"
> — ravila4, [Show HN post](https://news.ycombinator.com/item?id=48068741) linking
> [ravila4/obsidian-semantic-search](https://github.com/ravila4/obsidian-semantic-search), created
> 2026-05-08, 4 points

> "Agentic Copilot – Bring Claude Code, OpenCode, Gemini CLI into Obsidian"
> — mrxdev, [Show HN post](https://news.ycombinator.com/item?id=47438423) linking
> [spencermarx/obsidian-ai](https://github.com/spencermarx/obsidian-ai), created 2026-03-19, 9 points

**What this means for a candidate:** builders keep choosing to bolt AI-agent memory *onto* an
existing PKM vault rather than build a new memory store from scratch — Obsidian's plain-markdown
format is being treated, independently and repeatedly, as good-enough persistent-memory
infrastructure. A candidate in this space competes with "just point an MCP server at my existing
vault," not with a blank slate.

---

### Pattern 4 — Independent builders rebuild specific named legacy PKM tools/methodologies from scratch rather than extend an existing one

4 independent instances, distinct authors, all within the 12-month window. Builders explicitly
re-implementing a discontinued or niche tool's exact interaction model, rather than adding features to
an existing one.

> "NvEnvy – A Notational Velocity (NvAlt) Reboot in Swift. OSS"
> — hank2000, [Show HN post](https://news.ycombinator.com/item?id=48325776) linking
> [kenm47/nvEnvy](https://github.com/kenm47/nvEnvy), created 2026-05-29, 3 points

> "Feature-packed TUI note management app inspired by Obsidian"
> — reekta92, [reekta92/clin-rs](https://github.com/reekta92/clin-rs), created 2026-03-17

> "Kimün – A TUI note taking app Obsidian compatible and Vim friendly"
> — pnikosis, [Show HN post](https://news.ycombinator.com/item?id=48542513) linking
> [nico2sh/kimun](https://github.com/nico2sh/kimun), created 2026-06-15, 2 points

> "A Second Brain based on Zettelkasten methodology"
> — seruda, [Show HN post](https://news.ycombinator.com/item?id=46732802) linking
> [serudda/zettelkasten-garden](https://github.com/serudda/zettelkasten-garden), created 2026-01-23,
> 1 point

**What this means for a candidate:** a non-trivial share of build activity in this space is not
"build the next Obsidian" but "faithfully resurrect a specific predecessor tool's exact model"
(Notational Velocity's single-pane search-as-you-type, or Obsidian's own TUI/vim-native form). That
suggests some of the felt gap is about interaction model and platform (terminal-native, single-binary)
rather than feature parity — a candidate chasing feature completeness could miss what these builders
are actually voting for with their time.

---

### Summary

- **Patterns found:** 4
- **Independent instances cited (deduplicated per the heuristic above):** 24
  (Pattern 1: 8 · Pattern 2: 6 · Pattern 3: 6 · Pattern 4: 4)
- **Sourced evidence items:** 24 primary instance citations (each with author handle, repo/post URL,
  and creation date), plus corroborating-only references cited but not counted as independent
  instances: the `awesome-obsidian-alternatives` directory and `inkeep/open-knowledge`'s second
  cross-channel URL (both in Pattern 1), the two thin vibe-coded PKM hits (Source coverage), and the
  five found-but-excluded hackathon repos (Independence heuristic section).
- **Source states:** GitHub — found · Show HN (Algolia) — found · Bluesky — **blocked** (confirmed,
  HTTP 403 FingerprintBlock, consistent with the spec's 2026-09-01 finding) · Devpost/Lovable-Replit
  showcases/Indie Hackers/TrustMRR/Bolt.new/v0-Vercel — excluded-by-scope.
