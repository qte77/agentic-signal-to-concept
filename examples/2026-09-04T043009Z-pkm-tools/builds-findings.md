# PKM tools — Build-pattern findings (run 2, one day after pilot)

## §Research

Scope per `config/scope.md`: independent small-build activity (AI-assisted "vibe coded" or
conventionally built, treated identically) converging on the note-taking / personal-knowledge-management
(PKM) problem space. No date window is bounded in `scope.md`; this run applies
`build-pattern-scanner`'s own v1 default — a 12-month window, **2025-09-04 through 2026-09-04** (the
run date), one day later than the prior run's 2025-09-03–2026-09-03 window.

**This is the second real execution of this spec for this scope.** The prior run
(`examples/2026-09-03T231104Z-pkm-tools/builds-findings.md`, executed 2026-09-03T23:11:04Z) found 4
patterns and 24 independent instances. Per the orchestrator's instruction, this run does not re-paste
those 24 instances. Where this run's population is genuinely unchanged, that is stated plainly. Where
broader or different query terms surfaced instances the prior run's narrower term set missed, or
repos/posts created after the prior run's own execution timestamp, those are cited as new evidence
below, cross-referencing the prior file rather than duplicating it.

**Headline finding of this run:** the underlying population did **not** meaningfully change in one
day — GitHub's `topic:pkm created:>2026-09-02` query returned 0 hits, and the Show HN Algolia
`tags=show_hn` feed filtered to everything posted after the prior run's exact cutoff
(`created_at_i>1788477064`) returned zero PKM-relevant titles. What changed is **query coverage**:
this run's `topic:zettelkasten`, `topic:second-brain`, `"local-first notes"`, `"markdown notes"`,
`"obsidian mcp"`, and `"AI memory agent"` searches — none of which the prior run's query set used —
surfaced a substantially larger population of the same four convergences the prior run already
identified, plus one genuinely new query-driven finding (a GitHub topic-tag spam cluster, see below).
This is framed throughout as **undercount correction**, not new build activity.

### Independence heuristic (v1 default, applied as-is, carried from prior run)

Per `.claude/agents/build-pattern-scanner.md`: two builds count as independent only if ALL of —
distinct author/org handles; both created within the 12-month window; no direct fork/clone
relationship to each other; no shared canonical upstream repo. **Stated plainly as the spec's coarse
v1 default, not a validated methodology**, exactly as the prior run stated it.

**Basis for the "no fork" clause below:** none of this run's GitHub Search API queries used a
`fork:` qualifier, and GitHub's Search API excludes forks from results by default without one — so
every GitHub-sourced item below is fork-filtered by GitHub itself, not by per-repo inspection of the
`.fork` field. The one exception: the three genuinely-new-since-last-run repos (see below) were
checked directly via `/repos/{owner}/{repo}` and confirmed `"fork": false`.

**Two heuristic weaknesses observed this run**, in the same spirit as the prior run's
hackathon-brief finding:

1. **Topic-tag spam/farm cluster (confirmed, excluded).** Two GitHub accounts —
   [`Pitangakeratocele862`](https://github.com/Pitangakeratocele862/knowledge-gravity-lab) and
   [`Rhizomatous-rutherford327`](https://github.com/Rhizomatous-rutherford327/obsidian-zk-anti-cheat) —
   surfaced in the `topic:second-brain`/`topic:zettelkasten` searches. Both match an identical
   two-repo signature: a `<handle-lowercased>.github.io` landing-page repo plus one "product" repo
   whose `topics` array stuffs `second-brain`/`pkm`/`zettelkasten`/`obsidian-md` alongside entirely
   unrelated domains (`blockchain`, `zksnarks`, `netcore-mvc`, `docker` for a Starknet zero-knowledge
   anti-cheat tool; `knowledge-hygiene`/`offline-first` for an Obsidian-vault analyzer whose own
   `.github.io` repo description is near-identical boilerplate). This reads as automated,
   SEO-keyword-stuffed repo generation, not independent build activity, and both are **excluded**
   from every count below. Three other unusually-formatted handles surfaced by the same queries
   (`Scuttlechenopodiaceae6893/LLM-Wiki-KB`, `specialdeliveryabkhas3753/llm-wiki`,
   `adaxial-lineofscrimmage6998/mempalace`) were checked directly and did **not** match the spam
   signature — each has one on-topic repo, no matching `.github.io` pair. They are excluded from
   counts below not as confirmed spam but as **unverified this run** (their descriptions read as
   plausible if oddly-named individual builds; not enough budget remained to corroborate further). A
   further four candidates with similarly unusual handles were never checked at all (GitHub API rate
   limit — see Source coverage): `Vigorous-implosion50/knwldgbox`, `Olgafoliolate832/profile-vault-obsidian`,
   `Statutecontempt951/obsidian-llm-wiki`, `Familylaricariidaecountrydoctor678/chaos-link` — named
   here so a future run can finish the check. Note that `chaos-link`'s description ("Let friends
   disrupt your CS2 gameplay from their phones") paired with a `second-brain` topic tag is visible as
   an off-topic/tag mismatch directly in the search result, without needing the `.github.io`-pair
   check — a plausible fifth spam-signature match, just not corroborated the same way as the first
   two. This is a data-quality caveat worth flagging to the spec owner: v1's independence heuristic
   has no clause for topic-tag-stuffing spam, the same gap class as the prior run's "shared brief"
   finding.
2. **Template-repo self-description (observed, flagged not excluded).** Several second-brain/PKM
   repos surfaced this run explicitly describe themselves as reusable templates for others to copy
   rather than the author's own working system: `eborjaa/synapse` ("Use this template to start your
   own"), `gokhanarkan/minimal-second-brain` ("AI-native Obsidian vault template"),
   `lemoncloud-io/2nd-brain` (Korean: "템플릿", template), `quanb6311/second-brain-setup`,
   `lowqualityloey/loey_space`. None of these five share a common upstream *with each other* (each
   is independently authored, unlike the prior run's single-organizer hackathon cluster), so they
   pass the heuristic's literal fourth clause — but "I built a template for this pattern" is a
   different kind of evidence than "I use this myself," and none of the five are counted as primary
   instances below for that reason (kept here as an observation, not double-counted elsewhere).

**One assumption stated plainly, mirroring the prior run's own caveat:** 14 of the 21 new instances
below are sourced from a Show HN post rather than directly from the GitHub search API (Pattern 2: all
8; Pattern 3: `acertainKnight/project-thoth`, `sqliteai/sqlite-memory`, `perseusai`; Pattern 4: all
3); for all 15, the HN post's `created_at` is used as the within-window-proxy date, and none of those
repos' own `created_at` was separately fetched this run. Two sit close enough to the window's start
(2025-09-04) that this matters most: `logicalicy/ai-zettelkasten-lite` (HN post 2025-10-03) and
`zettelkasten.site` (HN post 2025-11-08) — either repo could in principle predate the window if its
creation lagged its Show HN launch by more than a few weeks, though that is unlikely for a Show HN
post (launches typically follow creation closely).

### Relationship to the prior run — what's carried forward vs. new

- **Pattern 1** (Obsidian alternatives): **no new query run this run beyond a narrow
  `created:>2026-09-02` recheck of the exact free-text term (0 hits)**. The prior run's 8 instances
  stand unchanged; this run does not re-cite them. See the prior file for the full list.
- **Pattern 2** (local-first, no cloud/lock-in): the prior run's 6 instances stand; **this run adds 8
  new independent instances**, found only because this run tried `"local-first notes"` and
  `"markdown notes"` as Algolia query terms — terms the prior run never used.
- **Pattern 3** (AI-agent memory grafted onto a PKM vault): the prior run's 6 Obsidian-vault-specific
  instances stand; **this run adds 10 new independent instances**, broadening the pattern beyond
  "Obsidian vault specifically" to "markdown/notes vault as AI-agent-memory substrate" generally —
  visible only via `topic:second-brain`, `topic:zettelkasten`, `"obsidian mcp"`, and
  `"AI memory agent"` queries the prior run didn't run.
- **Pattern 4** (rebuild a specific named legacy PKM tool/methodology): the prior run's 4 instances
  stand; **this run adds 3 new independent instances**, all Zettelkasten-methodology rebuilds found
  via the `zettelkasten` Algolia term.
- **Genuinely new-since-last-run** (created strictly after the prior run's own execution timestamp,
  2026-09-03T23:11:04Z / epoch 1788477064): three repos —
  [`boryanmarkulis/brain-seed`](https://github.com/boryanmarkulis/brain-seed) (2026-09-04T02:34:22Z,
  counted in Pattern 3 below),
  [`johan-socarras/Notelint`](https://github.com/johan-socarras/Notelint) (2026-09-04T01:04:00Z, a
  linter *for* markdown knowledge bases rather than a notes app itself — thin, single instance, not
  counted toward any pattern), and
  [`NROxAI/Business-Project-Manager`](https://github.com/NROxAI/Business-Project-Manager)
  (2026-09-04T03:48:07Z, self-described primarily as a "Universal AI Business & Project Governance
  Framework" with "second brain" only a parenthetical subtitle — **borderline scope**, not counted).
  No new-since-last-run Show HN posts were PKM-relevant (the `tags=show_hn` feed for
  `created_at_i>1788477064` returned 5 hits, none PKM-adjacent).

### Source coverage

- **GitHub public REST search API** — reachable, unauthenticated. **A rate-limit diagnostic worth
  recording**: GitHub tracks the Search API (`/search/repositories`) and the core API
  (`/repos/{owner}/{repo}`, `/users/{user}/repos`) as **separate buckets** — search allows 10
  req/min, core allows 60 req/hour, both unauthenticated. Early in this run, `/rate_limit` reported
  the *core* bucket at `remaining: 0` after roughly nine core-bucket calls (a handful of
  `/users/{handle}/repos` calls used to inspect the suspected spam accounts) — nine calls should not
  exhaust a 60/hour budget on their own, so the cause is not fully isolated: either this IP's core
  budget was partly pre-consumed before this session started, or `polyfetch`'s tier-escalation/retry
  behavior issues more than one underlying request per logical call. The core-bucket delta across a
  single call was not measured this run (only the search bucket's delta was checked post-reset, which
  answers a different question). Every `/search/repositories` call kept succeeding throughout
  regardless, because search draws from its own, separately-tracked, faster-resetting bucket. This
  session waited out the core-bucket reset (~8 minutes) before the three `/repos/{owner}/{repo}`
  fork-status checks; nothing was blocked on the search side at any point. Queries run this session:
  `topic:pkm`, `topic:note-taking`, `topic:personal-knowledge-management`, `"obsidian alternative"`
  (all `created:>2026-09-02`, sorted by `updated desc` rather than best-match specifically to surface
  items the prior run's default-sort search would not have shown), `topic:zettelkasten`,
  `topic:second-brain` (both `created:>2025-09-04`, full 12-month window — new query terms not run
  by the prior spec execution), `topic:hackathon topic:note-taking` (1 hit, same thin single result
  the prior run also found), `topic:hackathon topic:obsidian` (6 hits this run). The prior run's file
  recorded "`topic:hackathon+obsidian` (7 hits)" — its own shorthand is ambiguous as to whether
  `obsidian` was a second `topic:` qualifier (what this run ran) or a free-text term (broader, would
  match more), so 6 vs. 7 is not directly comparable across the two runs; not investigated further
  this run. All fetched via `polyfetch fetch <url> --show-body`,
  piped through `jq` to extract only the fields used as evidence. All genuinely found, not blocked or
  empty (except the narrow `created:>2026-09-02` slices for `topic:pkm` and `"obsidian alternative"`,
  both 0 hits — found-empty for that specific slice, not a source failure).
- **Show HN via HN's Algolia API** — reachable, unauthenticated, via `polyfetch fetch <url>
  --show-body`. This run's new query terms (not run by the prior execution): `zettelkasten`,
  `digital garden`, `local-first notes`, `markdown notes`, `obsidian mcp`, `AI memory agent`, `notes
  MCP server` (all `tags=show_hn`, `numericFilters=created_at_i>1756944000` for the 12-month window).
  Also re-ran a plain `tags=show_hn&created_at_i>1788477064` (no query term) to directly enumerate
  everything posted since the prior run's own execution — 5 hits, none PKM-relevant (found-empty for
  that specific new-since-last-run slice). All genuinely found, not blocked or empty; several hundred
  raw hits total across the seven new query terms above, all titles individually read (small enough
  result sets per query to do so), PKM-relevant ones extracted below.
- **Step-4 compliant substitute for Devpost/Lovable/Replit/hackathon-showcase gap** — re-ran per the
  spec: `query=vibe coded&tags=show_hn`, `query=lovable&tags=show_hn`, `query=Show HN
  hackathon&tags=story` (all 12-month window). Unlike the queries above, these three return far more
  than can be read title-by-title in this session's remaining budget (these three terms are broad
  enough that the top-100 cap was almost certainly hit — the prior run recorded roughly 740 raw hits
  across its whole query set, though per-query `nbHits` was not captured this run); each was capped
  at the top 100
  results by relevance and filtered by a `jq` regex over the title
  (`note|pkm|obsidian|knowledge|wiki|brain|zettelkasten|vault|journal`) rather than read in full —
  `grep` itself was denied by this session's permissions, forcing the `jq`-side filter. That regex
  would miss a PKM-relevant title framed around Notion, Roam, Logseq, or "outliner" specifically, so
  "zero PKM-relevant hits" for `Show HN hackathon` and "one hit" for each of the other two are
  regex-filtered-sample results, not exhaustive reads. `vibe coded` surfaced one hit, already known
  from the prior run (`bhackett`'s "vibe coded our team's issue tracker, knowledge base, telemetry
  board"); `lovable` surfaced one genuinely new, on-topic, but thin hit (below); `Show HN hackathon`
  surfaced none matching the regex. Consistent with the prior run's finding that this substitute
  channel yields thin PKM-specific signal — found, not blocked, but under this run's filtering
  method, not exhaustively read the way the seven terms above were.

  > "Another note-taking app? My first built app"
  > — markpGN, [Show HN post](https://news.ycombinator.com/item?id=45235716) linking
  > https://graphnotes.vercel.app/, created 2025-09-13, 3 points (built with Lovable per the query
  > match; single instance, too thin alone for a pattern)

- **Bluesky public post-search API** — **still blocked, third consecutive confirmation, with a
  caveat this run's evidence is less complete than the prior two.** `polyfetch fetch
  "https://public.api.bsky.app/xrpc/app.bsky.feed.searchPosts?q=obsidian%20alternative&limit=25"`
  raised a `FingerprintBlock` at the tool's first fetch tier, which triggered tier escalation to the
  Patchright/Chromium backend — that escalation then failed in *this* environment with
  `BrowserType.launch: Executable doesn't exist ... chrome-headless-shell` (the sandboxed Chromium
  binary for Patchright is not installed in this container). That means: the tier-1 `FingerprintBlock`
  is confirmed (inferred from the code path reaching the escalation branch — `getProfile` below never
  reaches that branch), consistent with continued blocking, but **no fresh literal HTTP status code
  was captured this run**, unlike the prior two runs' definitive 403. As a direct comparison,
  `polyfetch fetch "https://public.api.bsky.app/xrpc/app.bsky.actor.getProfile?actor=bsky.app"
  --show-body` succeeded cleanly on the first tier with a normal JSON profile — reproducing the
  spec's documented sibling-endpoint asymmetry (`searchPosts` blocked, `getProfile` fine) exactly.
  Recorded as **blocked**, not absence of signal, per the four-state discipline; flagging
  `patchright install` as a local environment fix for whoever runs this pipeline next, since it
  would let a future run capture the literal status code again rather than only the inferred block.
  (Follow-up 2026-09-04, same session: `polyfetch doctor` reports `chromium: ok` in the main session's
  own environment context — this may already be resolved for a future run; worth re-checking rather
  than assuming still broken, since the discrepancy with this run's finding wasn't root-caused.)
- **Devpost, Lovable/Replit showcases, Indie Hackers, TrustMRR, Bolt.new, v0/Vercel** —
  **excluded-by-scope**, unchanged from the prior run and from the orchestrator's instructions this
  run. Not attempted. (Bolt.new and v0/Vercel's exclusion has since been upgraded from "deferred, open
  gap" to "confirmed ruled out" — both explicitly ban scraping in their real ToS, checked
  2026-09-04; see `docs/plans/0001-concept.md` §3.)

### Ethical boundary applied

Every pattern below names an aggregate count and lists multiple independent instances; no single
project is presented as "the one to build like." Stars, HN points, and org-vs-individual ownership
appear only as neutral corroborating metadata, never as a ranking signal elevating one project above
the others in its pattern.

---

### Pattern 2 (continued) — 8 cited new independent instances (at least 8; more seen, not all cited): local-first, no-cloud, no-lock-in notes tools

The prior run's 6 instances stand (see `examples/2026-09-03T231104Z-pkm-tools/builds-findings.md`).
These 8 are new to this run, found via the `"local-first notes"` and `"markdown notes"` Algolia
terms — none overlap the prior run's citations or each other; distinct authors, all within the
12-month window, all self-describe with the literal phrase "local-first." The `topic:second-brain`/
`topic:zettelkasten` GitHub searches also surfaced further Pattern-2-shaped repos not cited here for
space (`tstapler/stelekit`, `fabriziosalmi/noted`, `ankasoft/perga` among them) — the 8 below are a
representative cited subset, not an exhaustive count of everything found.

> "Glyph, a local-first Markdown notes app for macOS built with Rust"
> — skarat, [Show HN post](https://news.ycombinator.com/item?id=47245617) linking
> https://glyphformac.com/, created 2026-03-04, 8 points

> "HelixNotes – Local-first Markdown notes app built with Rust and Tauri"
> — ArkHost, [Show HN post](https://news.ycombinator.com/item?id=46955400) linking
> https://helixnotes.com, created 2026-02-10, 6 points (this author also posted the same product
> twice more in the window — 2026-02-11 and 2026-02-23 — counted once here per the independence
> heuristic's same-author dedup, consistent with how the prior run handled GYST/PileaX)

> "Opensidian: Local-first notes in the browser with POSIX shell and sync"
> — braden-w, [Show HN post](https://news.ycombinator.com/item?id=47676461) linking
> https://opensidian.com, created 2026-04-07, 2 points

> "Local-first note taking web app powered by SQLite WASM – notem"
> — hanhan22, [Show HN post](https://news.ycombinator.com/item?id=49322163) linking
> https://notem.pages.dev/, created 2026-08-16, 2 points

> "Notvex – encrypted local-first notes with SQLCipher and XChaCha20"
> — GFrancV, [Show HN post](https://news.ycombinator.com/item?id=48946288) linking
> [GFrancV/notvex](https://github.com/GFrancV/notvex), created 2026-07-17, 2 points

> "NoteCove – local-first notes&tasks, synced via your own cloud storage"
> — drewcsillag, [Show HN post](https://news.ycombinator.com/item?id=47425200) linking
> https://notecove.io/, created 2026-03-18, 2 points

> "Slate – An Open Source Local First Note taking web app built using Rust"
> — sheerluck, [Show HN post](https://news.ycombinator.com/item?id=47250659) linking
> https://app.slate.tangentlabs.dev/, created 2026-03-04, 1 point

> "Noteika – Local-first notes that resurface before duplicate yourself"
> — annrap1d, [Show HN post](https://news.ycombinator.com/item?id=48748398) linking
> https://noteika.com, created 2026-07-01, 1 point

**What this adds to the prior run's reading:** the prior run cited 6 instances of this convergence
and called it "a structural expectation, not a feature request." This run's 8 additional instances,
surfaced purely by trying two query terms the prior run never used, suggest the prior count was a
significant undercount of the same signal rather than the pattern itself being small — a methodology
note for whoever runs this spec next: the choice of search terms materially changes the apparent size
of a pattern, independent of the underlying population.

---

### Pattern 3 (continued, broadened) — 10 new independent instances: AI-agent memory grafted onto a markdown/notes vault, not limited to Obsidian specifically

The prior run's 6 instances were scoped specifically to "graft AI-agent memory onto an *existing
Obsidian* vault." This run's new queries (`topic:second-brain`, `topic:zettelkasten`, `"obsidian
mcp"`, `"AI memory agent"`) surfaced a substantially larger and more general version of the same
convergence: builders treating a **markdown/notes vault** (Obsidian-specific or not) as the durable
memory substrate for coding agents. Scope was held deliberately tight here — pure agent-memory
infrastructure with no notes/markdown/vault layer (vector DBs, generic KV memory stores, OWASP's
agent-memory-poisoning project, etc.) was excluded as **out of scope for a PKM-tools category**, even
though it surfaced in the same searches. The 10 below all explicitly root themselves in a
markdown/notes/vault substrate. One of the 10, `sqliteai/sqlite-memory`, appears to be org-owned
rather than an individual account (based on the `sqliteai` handle itself, not a checked `owner.type`
field) — flagged, in the same spirit as the prior run's `inkeep/open-knowledge` flag, though not
independently confirmed this run. (`Obelyth/cortex`, an Organization-owned Claude-second-brain repo
seen in the same GitHub search, is a further corroborating sighting of this pattern but is not one of
the 10 cited instances below.)

> "A networked PARA knowledge brain for coding agents — documents managed by PARA and woven into one
> connected graph, held in a searchable Postgres store with a revision history per document. One
> static binary: MCP server + CLI."
> — poorants, [poorants/engram](https://github.com/poorants/engram), created 2026-09-03
> (unrelated to the prior run's `karankantaria/Engram` — same word, two independent products,
> different authors, different repos)

> "An AI-woven personal knowledge graph that links, clusters, and answers questions across your
> notes."
> — Desouki27, [Desouki27/thought-loom](https://github.com/Desouki27/thought-loom), created
> 2026-09-03

> "Turn Notion essays into a private, local-first story vault for AI retrieval through MCP."
> — notvasub, [notvasub/life-story](https://github.com/notvasub/life-story), created 2026-09-03

> "A second brain that runs itself. Self-compiling wiki, automatic session capture, and instructions
> that rewrite themselves from your corrections. Markdown all the way down."
> — boryanmarkulis, [boryanmarkulis/brain-seed](https://github.com/boryanmarkulis/brain-seed),
> created 2026-09-04 (genuinely new-since-last-run — created after the prior run's own 2026-09-03
> execution timestamp)

> "记忆核心（Memory Eternal）：自研的 DeepSeek Harness 记忆插件——对话结束自动沉淀知识卡到本地 Markdown
> Vault" (a self-built DeepSeek-Harness memory plugin that distills chat sessions into knowledge
> cards in a local Markdown vault, with dedup, CJK search, and a knowledge graph)
> — EternalNight996, [EternalNight996/memory-eternal](https://github.com/EternalNight996/memory-eternal),
> created 2026-08-31

> "Persistent memory for Claude Code — the full plugin: hooks, skill, MCP server, workflows. A
> Markdown vault you own, no server and no account."
> — SirCharan, [SirCharan/second-brain](https://github.com/SirCharan/second-brain), created
> 2026-07-21

> "Wenlan is a knowledge base for the AI-native age. Your AI agents capture what they learn, Wenlan
> keeps it current and distills it into source-cited wiki pages you can trust"
> — 7xuanlu, [7xuanlu/wenlan](https://github.com/7xuanlu/wenlan), created 2026-04-19, 62 stars

> "Thoth – Obsidian AI Research Assistant"
> — acertainKnight, [Show HN post](https://news.ycombinator.com/item?id=46968889) linking
> [acertainKnight/project-thoth](https://github.com/acertainKnight/project-thoth), created
> 2026-02-11, 2 points

> "SQLite Memory – Markdown based AI agent memory with offline-first sync"
> — marcobambini, [Show HN post](https://news.ycombinator.com/item?id=47676123) linking
> [sqliteai/sqlite-memory](https://github.com/sqliteai/sqlite-memory), created 2026-04-07, 10 points
> (repo owner `sqliteai` is an organization, not marcobambini's personal account — flagged per the
> scope's org-vs-individual caveat)

> "I built an AI agent memory engine because Obsidian wasn't cutting it"
> — perseusai, [Show HN post](https://news.ycombinator.com/item?id=48915435) linking
> https://perseus.observer/blog/built-perseus-vault-obsidian-wasnt-cutting-it/, created 2026-07-15,
> 1 point (a direct, first-person statement of the exact complaint-to-build pipeline this pipeline is
> designed to trace — cited as evidence of the pattern, not as a candidate to build toward)

**What this adds to the prior run's reading:** the prior run's framing — "builders keep choosing to
bolt AI-agent memory *onto* an existing PKM vault rather than build a new memory store from scratch"
— holds, but this run's evidence shows the substrate isn't limited to Obsidian specifically. Plain
Markdown, Postgres-backed PARA structures, and SQLite all recur as "good enough" persistence layers
for the same underlying need. The `perseusai` quote above is the clearest first-person articulation
found across either run of the complaint → build pipeline this project traces: an existing PKM tool
(Obsidian) was judged insufficient specifically as agent memory, prompting a new build.

---

### Pattern 4 (continued) — 3 new independent instances: rebuilding Zettelkasten as a named methodology

The prior run's 4 instances (including one Zettelkasten rebuild, `serudda/zettelkasten-garden`)
stand. These 3 are new, found via the `zettelkasten` Algolia term (not run by the prior execution),
distinct authors, no relationship to the prior run's citation or to each other.

> "Zettelkasten Interactive – 60KB knowledge tool for ADHD brains"
> — SlaWisni73, [Show HN post](https://news.ycombinator.com/item?id=45859573) linking
> https://zettelkasten.site/, created 2025-11-08, 5 points

> "AI-Powered Zettelkasten Using Pinecone and Claude MCP"
> — mhay, [Show HN post](https://news.ycombinator.com/item?id=45459907) linking
> [logicalicy/ai-zettelkasten-lite](https://github.com/logicalicy/ai-zettelkasten-lite), created
> 2025-10-03, 2 points

> "A self-organizing digital Zettelkasten for research"
> — bethanyhunt, [Show HN post](https://news.ycombinator.com/item?id=48297094) linking
> [ulyssestenn/funes](https://github.com/ulyssestenn/funes), created 2026-05-27, 1 point

**What this adds to the prior run's reading:** the prior run's observation — that some build effort
targets a specific predecessor's interaction model rather than "the next Obsidian" — extends past
Notational Velocity and TUI-Obsidian clones (the prior run's examples) to Zettelkasten itself, now
frequently rebuilt with an AI-extraction layer on top (Pinecone/Claude MCP, self-organizing links)
rather than as a plain digital index card system.

---

### Summary

- **Patterns addressed this run:** 4 (all carried forward from the prior run; Pattern 1 unchanged,
  Patterns 2–4 extended with new instances)
- **New independent instances cited this run (deduplicated per the heuristic above):** 21
  (Pattern 2: 8 · Pattern 3: 10 · Pattern 4: 3)
- **Genuinely new-since-last-run repos identified:** 3 (`boryanmarkulis/brain-seed`, already counted
  within the 21 above, as one of Pattern 3's 10; `johan-socarras/Notelint` and
  `NROxAI/Business-Project-Manager`, both too thin or too borderline-scope to count toward a pattern,
  cited above as two additional single instances, not part of the 21)
- **Sourced evidence items:** 21 primary new-instance citations (each with author handle, repo/post
  URL, and creation date) + 2 additional thin/borderline single instances (`Notelint`, `NROxAI` —
  `brain-seed` already included in the 21) + 1 thin step-4-substitute hit (`markpGN`/graphnotes) =
  **24 total sourced citations** in this file, plus corroborating-only/excluded references: the 2
  confirmed spam-cluster accounts, the 3 unverified-not-spam accounts, the 4 never-checked accounts,
  the 5 flagged template-repo accounts, and the `topic:hackathon topic:obsidian` recheck (6 hits,
  not directly comparable to the prior run's differently-scoped 7).
- **Source states:** GitHub — found (with a rate-limit-bucket diagnostic noted above) · Show HN
  (Algolia) — found · Bluesky — **blocked** (third consecutive confirmation; tier-1 `FingerprintBlock`
  inferred from the escalation code path and the working `getProfile` sibling-endpoint comparison,
  but no fresh literal HTTP status captured this run because the local Patchright/Chromium binary is
  missing — `patchright install` flagged as an environment fix for future runs) ·
  Devpost/Lovable-Replit showcases/Indie Hackers/TrustMRR/Bolt.new/v0-Vercel — excluded-by-scope.
- **Net read for the concept-synthesis stage:** the population of independent PKM-tool builders is
  larger than the prior run's 24-instance count suggested — not because more people started building
  in one day, but because the prior run's query set undercounted Patterns 2 and 3 specifically. All
  four convergences from the prior run remain live and, if anything, better-evidenced now.
