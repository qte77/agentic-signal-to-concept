# Discovery run 2026-09-04T060617Z — candidate problem-space categories

Phase 0 (`signal-discoverer`) first-ever real execution. Broad, bounded, unfiltered pull across Show
HN and ProductHunt — no category named up front, per the spec. Output is a triage list for a human to
pick from, not sourced-quote evidence the way Phase 1 findings are.

## Window and source coverage

| Source | State | What was pulled | Window |
|---|---|---|---|
| Show HN (Algolia) | **found** | All `tags=show_hn` stories, unfiltered by topic | `created_at_i` in `(1785909977, 1788501977]` = 2026-08-05T06:06:17Z – 2026-09-04T06:06:17Z (30 days, spec default; no `discovery/window-override.md` present) |
| ProductHunt (GraphQL v2) | **found, narrower window than intended** — see below | `posts(order: NEWEST, first: 50, after: $cursor)`, paged; `postedAfter`/`postedBefore` verified to work but not used for the full pull (rate-limited before a wider re-pull) | Effectively one calendar day (2026-09-03), not the full 30 days — see below |

**Show HN — how it was pulled.** `hn.algolia.com/api/v1/search` caps any single query at 1000 hits
(`paginationLimitedTo`); the 30-day window alone returns `nbHits: 3670`, so a single paged query would
silently truncate at 1000. Split the window into 6 sub-ranges (via AND'd `numericFilters=created_at_i>X,
created_at_i<Y`) sized so no bucket approached the 1000-hit cap, fetched each with `hitsPerPage=1000` via
`polyfetch-scrape`, and merged by `objectID`. The 6 buckets summed to exactly 3670 with no duplicates and
no gaps against the un-truncated total — full coverage of the window confirmed, not assumed. **3670 titles
is far above the plan's own anticipated "few hundred, not thousands"** — flagging that discrepancy plainly
rather than silently sampling. Given the volume, clustering used a two-pass method (see below) rather than
reading all 3670 titles in sequence one-by-one; the method is stated so the gap between "read every title"
(spec's phrasing) and what was actually done is visible, not hidden.

**ProductHunt — how it was pulled, corrected mid-run.** Token (`PRODUCTHUNT_API_TOKEN`) verified live
with a 3-post test call before relying on it. Paged `posts(order: NEWEST, ...)` via `after`/`endCursor`,
50 per page, up to 40 pages (800 posts). Every one of the 800 posts returned — across 40 pages, all
unique `id`s, cursor genuinely advancing (no repeated page) — carried the identical
`createdAt: "2026-09-03T07:01:00Z"`. First read as a broken/redacted field (the same class of finding
`complaint-miner.md` documents for PH commenter usernames); **that read was checked and was wrong.**
Testing `posts(postedAfter:, postedBefore:)` directly (this run's own briefing had said PH's `posts` query
"doesn't take a date-range filter directly" — untrue, it does, checked live) against a 24h window one day
earlier returned a *mix*: four posts stamped `2026-09-02T07:01:00Z` plus one, "KAI - Agentic AI Platform,"
stamped `2026-09-02T08:48:28Z`. `07:01:00Z` UTC is `00:01` Pacific — PH's standard daily-launch-cohort
timestamp, applied to most of a day's posts, with a handful of individually-timestamped exceptions. Not a
data-quality defect: the field is correct, and its correct reading is that **the unconstrained 800-post
pull never advanced past a single calendar day (2026-09-03) because that one day alone contains more than
800 posts** in this environment — consistent with Show HN's own volume in this window running roughly 3–5x
above the historical baseline the plan anticipated (see above). A follow-up exhaustive count for that one
day hit ProductHunt's own API rate limit (`rate_limit_reached`, complexity-based, resets ~7.5 min later)
before a precise per-day total could be established; that wait was not taken — burning session time on a
rate-limit sleep for a precision figure this pass doesn't need was judged not worth it. **Net effect,
stated plainly: PH's contribution to this run covers (at most) one calendar day, 2026-09-03, not the full
30-day window** — narrower than Show HN's confirmed full-window coverage above. What's reported below from
PH is real content (unique products, real names/taglines/topics) from that one day, not a fabricated or
broken sample — just a narrower time-slice than intended. Topic-tag frequency (PH's own taxonomy) was used
as the first-pass clustering signal per the spec, then taglines within frequent topics were read to judge
real cluster vs. grab-bag.

**Excluded per spec, not attempted:** broad HN comment firehose, Reddit, app-store, GitHub-issue discovery —
each requires its own bounded-query design or access/ToS check first (see `docs/plans/0003-broad-discovery.md`
§Scope).

**Clustering method, stated plainly (both sources).** HN: word/bigram frequency across all 3670 titles was
used as a first pass to surface candidate themes (not to declare a cluster on its own), then a
representative sample of matching titles per candidate theme was read to judge whether it was a coherent
problem-space cluster or a coincidental word match / grab-bag — the same discipline the spec specifies for
PH's topic-tag frequency, applied consistently to HN's keyword frequency for the same reason (3670 titles
made a title-by-title-only read impractical to do with real qualitative attention). PH: topic-tag frequency
first pass, then taglines read within frequent topics, per spec. Two clusters below (Games, general Browser
Extensions) were surfaced by this first pass and then **rejected** after reading samples — noted at the end
rather than silently dropped, so the negative finding is visible too.

## Ranked candidate categories

Ranked by combined signal strength and cross-source confirmation (cross-source ranks above single-source,
per spec). Counts are per-category keyword/topic matches, not mutually exclusive — a title can match more
than one category's pattern (e.g. an MCP-based agent-memory tool matches both #1 and #2); this is stated
so the counts aren't misread as a partition. **PH counts below all come from that single 2026-09-03 sample**
(see coverage note above) — treat "cross-source" here as "independently present in PH's one-day sample,"
not "present in PH across the full 30-day window."

### 1. Agent memory / persistent context for AI coding agents — cross-source

**Signal:** HN ~18 distinct products naming "agent memory" / "memory engine|layer" / "persistent context"
directly (e.g. MCP Memory, Mnemosyne, Remembrane, Engrava, Knowl, Vibsync, GreatArrow.ai, Decispher, a
public "Agent Memory Leaderboard"), out of 27 titles matching a looser memory+agent keyword. PH: 4–5
independent products (Organizational Memory 2.0, Sonnenfeld, Engram, MemHub).

**Rationale:** Both sources independently surface multiple *unrelated* teams solving the identical
problem — "coding agents forget everything between sessions, give them durable memory" — with distinct
technical approaches (SQLite FTS5, graph DBs, hierarchical/MCP-native, shared/reviewable). Cross-source
convergence on a narrow, well-defined pain point with a dozen-plus independent attempts is a strong
real-cluster signal, not noise from a broad keyword.

**What a `config/scope.md` here might say:** category = "agent/coding-agent memory and persistent-context
tools"; sources = HN + PH as pulled here, keyword-narrowed to memory/context/persistence; look for what
existing tools' users complain is still missing (cross-agent portability, review/audit of stored memory,
cost of storage/retrieval) rather than re-validating that the category exists.

### 2. MCP servers for niche integrations (agent-tooling infrastructure) — cross-source

**Signal:** HN 91 titles mention MCP directly. PH 8 posts. Both sources show the *pattern* (someone
wrapping an existing service — Google Ads, DOCX editing, government contract data, UI component
libraries, Postgres/Obsidian — behind an MCP server) repeated across many unrelated verticals.

**Rationale:** This is real convergence on a *shape* of build (an MCP-server wrapper), not on one
product category — the individual MCP servers found solve unrelated end-user problems. Flagging this
distinction explicitly: a `config/scope.md` built directly on "MCP servers" would be too broad to run
`complaint-miner` against meaningfully; it would need to pick one vertical (e.g., "MCP servers for X")
first. Ranked above single-source clusters but below #1 because of this internal heterogeneity.

### 3. Terminal / session environment for coding agents — cross-source

**Signal:** HN ~17 distinct products building a terminal/session layer specifically around coding agents
(Wallfacer, HUD, Saggar, terminal-code, Mole, HarnessRouter, Kit), out of 126 broader terminal/CLI
matches. PH: Grove ("One terminal for you and your AI agent"), Blume.codes, Hvelv — smaller but
independently confirms the same theme in PH's own words.

**Rationale:** A recurring, named pain point — coding-agent sessions need their own terminal/session UX
(multiplexing, history, cost tracking, cross-tool handoff) distinct from a plain shell — attempted
independently by multiple unrelated builders on both platforms.

**What a `config/scope.md` here might say:** category = "terminal/session UX for AI coding agents";
look for what these tools' launch-thread comments say is still missing (session portability across
Claude Code/Codex/Cursor came up repeatedly in titles alone).

### 4. Local-first / self-hosted / no-account personal software — cross-source

**Signal:** HN ~135 titles carry an explicit local-first/self-hosted/offline/no-account/on-device framing
across many unrelated domains (password manager, recipe keeper, LaTeX workspace, screen OCR, Markdown
notes, image API, monitoring). PH: ~12 (MarkNote, DueAhead, Fottly, AFaaS MCP Fleet, Tasbeeh Counter,
DevBite, among others).

**Rationale:** This is an ethos/positioning cluster, not a single product category — the common thread
is "no cloud, no account, your data stays local" as the *selling point*, repeated across domains that
are otherwise unrelated. Real and cross-source-confirmed, but a `config/scope.md` needs to pick one
domain (expense tracking, note-taking, etc. — see #5/#6 below) rather than scope to "local-first"
itself.

### 5. Personal finance / expense / invoicing tools for freelancers and small business — cross-source

**Signal:** HN 20 titles (Splitty, Splitright, DrakeAI, Snapquo, an EN 16931 e-invoice validator, a
"no bank sync" expense tracker, a budget app framed around daily spend). PH 11 (Autónomo Pro Gestor,
Invoice Manager for Excel, AI Expense Notebook, Peggy, ForgeBill, TaxFlow, Snaptix).

**Rationale:** Cross-source, moderate volume, and the titles/taglines repeat the same specific
complaints — no bank-account linking required, splitting expenses without forcing an account creation,
invoicing/quoting being a late-night chore for solo operators — a recognizable, narrow pain-point
cluster matching the spec's own worked example.

### 6. Native macOS Markdown/text editors — cross-source, smaller

**Signal:** HN: 12 titles matching a markdown/text-editor pattern, 11 distinct products after
normalizing titles — one resubmission ("Markdown Buddy," same title, two separate Show HN posts,
`objectID`s 49205269 and 49374127) is the only duplicate; the rest (Write.md, Marble, Nodes, Caxton,
Femto, three further unnamed-in-title "another/yet another" editors, one named "Markup") are distinct.
PH weaker but present (QuickMark, MarkNote, Fadepad) from the one-day sample.

**Rationale:** A visibly saturated, repeatedly-attempted niche — 11 distinct independent "yet another
native Markdown/text editor" launches in the same 30-day window (one of them resubmitted), several
emphasizing on-device AI or Quick-Look integration as differentiation. Worth flagging precisely
*because* of the saturation: a `config/scope.md` here would need a genuinely distinct angle, not "a
markdown editor."

### 7. Habit tracking / journaling / personal note-taking — single-source-leaning (HN)

**Signal:** HN 25 (Kadō, jeden.day, Evoboard, Mininote, a decision journal for AI agents). PH weak by
direct keyword match (2–6; likely under-counted since PH's "Productivity" topic (209 posts) and "Task
Management" topic (14) plausibly contain more that a narrow regex missed, but taglines sampled from
those topics skewed toward team/work tools rather than personal habit tracking specifically).

**Rationale:** Real, recurring HN signal matching the spec's own worked example category; PH support is
present but weaker and less specifically matched, so this is flagged as HN-leaning rather than
strongly cross-source, per the spec's discipline on not silently collapsing that distinction.

### 8. macOS menu-bar utility apps — single-source-leaning (HN)

**Signal:** HN 16 (a cron-job watchdog, a dev-server/port monitor, a system monitor, a Claude-usage
tracker, a color picker, app notarization/signing tooling). PH weak (2 direct matches).

**Rationale:** A recognizable recurring *form factor* (small, single-purpose, lives in the menu bar)
rather than one problem — genuinely convergent on HN, but the individual tools solve unrelated problems,
so this is closer to #2's "shape of build" pattern than a single scope-able category; a `config/scope.md`
would need to pick one of the underlying problems (dev-server monitoring, Claude usage tracking, etc.),
not "menu-bar apps" as a category.

## Considered and rejected (surfaced by the frequency first-pass, not promoted)

- **Games / game engines** — high raw volume (HN 103, PH 38 under the "Games" topic) but reading samples
  from both sources showed a genuine grab-bag: karaoke games, retro-console compilers, word-building
  games, board games, school-communication apps tagged "Kids/Games," a texture-pipeline tool for game
  devs. No shared underlying problem beyond "someone built a game" — stated explicitly as noise, not a
  candidate category, per the spec's instruction to say so rather than imply a trend from volume alone.
- **General browser extensions** — HN 24, PH 17 under "Chrome Extensions." Samples were similarly
  scattered (price comparison, YouTube-to-playlist, Discord quest automation, tab organization, PR
  review helper, Cloudflare detection) — a build *form factor*, not a shared problem. Rejected for the
  same reason as menu-bar apps above but weaker/more scattered, so not promoted even as a "shape of
  build" entry.

## Summary

8 candidate categories promoted, ranked above; 2 additional clusters surfaced by the frequency first-pass
and explicitly rejected as grab-bags after reading samples.

- **Show HN: found**, full 30-day window confirmed (3670 titles, 6-bucket pull summing exactly to the
  un-truncated total, no gaps/no duplicates).
- **ProductHunt: found**, but narrower than intended — the 800-post sample pulled all landed on one
  calendar day (2026-09-03), because PH's daily-launch-cohort timestamp convention (`00:01` Pacific,
  applied to most of a day's posts) means that single day alone contains more than 800 posts in this
  environment. This was diagnosed twice: first (wrongly) as a broken/redacted field, then corrected by
  testing PH's `postedAfter`/`postedBefore` filters live (they work, contrary to this run's initial
  briefing) against an adjacent day, which showed genuine per-post timestamp variation mixed with the
  batch stamp. A follow-up pull to widen PH's coverage across more of the 30-day window hit PH's API rate
  limit before completing; not retried via a sleep-wait. PH's categories/counts above reflect that one
  day's real content, not a fabricated or corrupted sample.
- **Excluded, per spec**: broad HN comment firehose, Reddit, app-store, GitHub-issue discovery.

**Volume note for the next run:** both sources ran well above the plan's anticipated density (HN: 3670
titles vs. an expected "few hundred"; PH: >800 posts in a single day). A future `signal-discoverer` run
should budget for bucketed HN pulls and a PH rate-limit-aware, multi-day-budgeted pull from the start,
rather than discovering both constraints mid-run as this one did.
