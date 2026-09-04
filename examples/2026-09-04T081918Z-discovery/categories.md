# Discovery run 2026-09-04T081918Z — candidate problem-space categories

Phase 0 (`signal-discoverer`) **second** real execution. Broad, bounded, unfiltered pull across Show
HN, ProductHunt, and GitHub — no category named up front, per the spec. GitHub is a brand-new source
for this run (added to the spec after the first run, timestamp `2026-09-04T060617Z`, archived at
`examples/2026-09-04T060617Z-discovery/categories.md`); this is its first real execution. Output is a
triage list for a human to pick from, not sourced-quote evidence the way Phase 1 findings are.

**Window overlap with the first run, stated plainly.** This run's 30-day window
(`2026-08-05T08:20:18Z`–`2026-09-04T08:20:18Z`) overlaps roughly 99% with the first run's
(`2026-08-05T06:06:17Z`–`2026-09-04T06:06:17Z`) — a ~2h14m shift, not two hours of fresh activity
piled on top. Where HN/PH counts below resemble the first run's, that is the same underlying corpus
being re-measured (with a slightly different clustering regex in places, noted per-category), not
independent confirmation of growth. GitHub's numbers are genuinely new — first time this source ran.

## Window and source coverage

| Source | State | What was pulled | Window |
|---|---|---|---|
| Show HN (Algolia) | **found**, full window confirmed | All `tags=show_hn` stories, unfiltered by topic | `created_at_i` in `(1785918018, 1788510018]` = 2026-08-05T08:20:18Z – 2026-09-04T08:20:18Z (30 days, spec default; `discovery/window-override.md` absent, confirmed via Read tool error) |
| ProductHunt (GraphQL v2) | **found, bounded sample, full window touched** — see below | `posts(postedAfter:, postedBefore:, first: 50, order: NEWEST)`, one query per calendar day, 30 queries total | Full 30 calendar days, 2026-08-05 through 2026-09-03 (each day's own 24h `postedAfter`/`postedBefore` bound) |
| GitHub (`gh` search API) | **found**, first real run | `search/repositories` with `created:>{cutoff} stars:>500 sort:stars-desc`, no `topic:` qualifier, 3 pages | `created:>2026-08-05` (repos created since the window start) |

**Show HN — how it was pulled.** `hn.algolia.com/api/v1/search` caps any single query at 1000 hits;
the 30-day window alone returns `nbHits: 3671` (essentially identical to the first run's 3670 — see
window-overlap note above). Split into 6 sub-ranges (`numericFilters=created_at_i>X,created_at_i<Y`,
~5 days each), fetched each with `hitsPerPage=1000`, merged by `objectID`. Per-bucket `nbHits` values
(563, 713, 602, 475, 683, 635) summed to exactly 3671 with 3671 unique `objectID`s after merge — full
coverage confirmed, not assumed, same discipline as the first run. Given the volume, clustering used a
word/bigram frequency first-pass across all 3671 titles to surface candidate themes, then targeted
regex searches per candidate theme were read in full (not just a sample) to judge coherent cluster vs.
coincidental word match — stated per-category below whether the full regex-matched set or a
representative sample was read.

**ProductHunt — how it was pulled, and its real limits.** Token verified working via a live test
call. The spec's own correction (from the first run) was applied: `postedAfter`/`postedBefore` bound
each query to one calendar day, paced 3s apart, for all 30 days — **no rate limit was hit once across
all 30 requests**, a clean improvement over the first run's single-day stall. However: querying a
single day's *true* volume (`totalCount`) revealed this environment runs PH at 431–1331 posts/day
(sum across the 30-day window: **22,346** — see day-by-day table below), and the API's `first`
parameter silently caps at **20 per page regardless of the value requested** (50 was requested every
time; 20 came back every time) — a previously-unconfirmed API behavior worth flagging for future runs.
Net effect, stated precisely: **every one of the 30 calendar days was queried and is represented in
the sample** (a genuine improvement over the first run's single-day coverage), but each day's sample
is its 20 newest posts, not exhaustive — 600 posts total against ~22,346 real posts in the window,
roughly a **2.7% sample**, evenly spread across all 30 days rather than concentrated on one. This is
reported as what it is: a bounded, time-distributed sample sufficient for topic-frequency clustering,
not a full-window census. Topic-tag frequency (PH's own taxonomy) was used as the first-pass
clustering signal per spec, then taglines within frequent topics were read.

| Date | totalCount (real daily volume) | Sampled |
|---|---|---|
| 2026-08-05 | 874 | 20 |
| 2026-08-06 | 709 | 20 |
| 2026-08-07 | 618 | 20 |
| 2026-08-08 | 526 | 20 |
| 2026-08-09 | 452 | 20 |
| 2026-08-10 | 688 | 20 |
| 2026-08-11 | 1017 | 20 |
| 2026-08-12 | 777 | 20 |
| 2026-08-13 | 671 | 20 |
| 2026-08-14 | 613 | 20 |
| 2026-08-15 | 513 | 20 |
| 2026-08-16 | 431 | 20 |
| 2026-08-17 | 676 | 20 |
| 2026-08-18 | 1022 | 20 |
| 2026-08-19 | 786 | 20 |
| 2026-08-20 | 760 | 20 |
| 2026-08-21 | 639 | 20 |
| 2026-08-22 | 556 | 20 |
| 2026-08-23 | 491 | 20 |
| 2026-08-24 | 733 | 20 |
| 2026-08-25 | 1216 | 20 |
| 2026-08-26 | 896 | 20 |
| 2026-08-27 | 789 | 20 |
| 2026-08-28 | 718 | 20 |
| 2026-08-29 | 802 | 20 |
| 2026-08-30 | 584 | 20 |
| 2026-08-31 | 740 | 20 |
| 2026-09-01 | 1331 | 20 |
| 2026-09-02 | 883 | 20 |
| 2026-09-03 | 835 | 20 |

**GitHub — how it was pulled (first real run).** Checked `total_count` before committing to a
threshold, per spec: `created:>2026-08-05 stars:>250 sort:stars-desc` returned **510** (too high —
low-hundreds target, trending toward thousands); `stars:>500` returned **216** — settled on `500` as
the threshold. Fetched all 3 pages (100/100/16), confirmed `total_count: 216` consistent across all
three page fetches, 216 unique `full_name`s after dedup — full coverage of the thresholded set
confirmed. `topics` frequency was used as the first-pass clustering signal (85/216 repos had empty
`topics`; a description-word-frequency fallback was run on that subset per spec — it surfaced no
theme beyond what topics already showed, reinforcing rather than adding to the clusters below).

**Excluded per spec, not attempted:** broad HN comment firehose, Reddit, app-store, GitHub-issue
discovery, GH Archive (owner-gated/engineering-lift, tracked separately per spec §5).

## Ranked candidate categories

Ranked by combined signal strength and cross-source confirmation (cross-source ranks above
single-source, per spec). Counts are per-category keyword/topic matches, not mutually exclusive — a
title/repo/post can match more than one category's pattern (e.g. a memory tool built as an MCP server
matches both #2 and #3). PH counts below come from the 2.7% day-spread sample described above — real
content, correctly scaled down, not a fabricated or single-day-biased sample this time.

### 1. Terminal / session management and orchestration for coding agents — cross-source

**Signal:** HN: 107 raw `terminal|session` matches; the full list was read (not just a first-pass
sample) and at least 40 distinct products are specifically about managing, recording, resuming,
monitoring, or orchestrating AI-coding-agent sessions (Wallfacer, HUD, AgentTerm, Quack, Saggar,
Clinch, Csift, Roost, Codewindow, Kungfu, ctx, Pond, MulmoTerminal, Wolfpack, Open Session, AgentParty,
Herdr, TheGitAI, Remotevibe, VT Code, Wattage, Supafork, Shed, Podiom, Ava, Ration, Aurict, Aidcrew,
codex-remote-control, Zoetrope, Pisesh, among many others) — the rest of the raw matches are unrelated
generic-terminal apps (a Tetris clone, a torrent client, weather-in-terminal). GitHub: the same shape
appears independently under the `claude-code`/`ai-agents` topics (`sodiumsun/agenttrail` — "watch
Claude Code, Codex, and Cursor sessions... in real time"; `furkankly/zoetrope` — "Watch a Claude Code
session as a live flow graph"; `damejan84/tokentab` — reads Claude Code/Codex/Gemini CLI session logs
for cost). PH: weak in this run's 2.7% sample (2 direct keyword hits) — expected given the sample
fraction, not a real absence.

**Rationale:** This is now the densest, most repeatedly-attempted cluster in the pull — dozens of
unrelated builders independently building session-recording, session-resuming, session-monitoring, and
multi-agent-terminal-orchestration tools, a noticeably larger and denser cluster than the first run's
equivalent (~17 distinct products then vs. ~40+ now) — though given the ~99% window overlap noted
above, this size difference likely reflects this run's broader regex and full-list read rather than
genuine two-day growth; stated so the reader doesn't over-read it as fresh momentum.

**What a `config/scope.md` here might say:** category = "session/terminal UX and orchestration for AI
coding agents"; look at what launch comments say is still missing — cross-tool session portability
(Claude Code ↔ Codex ↔ Cursor) and cost/token visibility across sessions came up repeatedly by name.

### 2. Agent memory / persistent context for AI coding agents — cross-source

**Signal:** HN: 63 titles matched `memory|persistent context|context engine` — full matched set was
read directly (regex returned a manageable 63, no first-pass-then-sample needed for this one).
Distinct independent products: Remembrane, GreatArrow.ai, NexusMem, Memoars, MCP Memory, Verity,
Engrava, Knownbase, Cogni, Seahorse, Mnemosyne, Knowl, Itsuki, Rafter, Contextual, Decispher, Mex,
Engram, among ~40+ more. GitHub: 4 genuine matches after filtering false positives (a debugger's
"read memory" feature, a world-model project) — `memcode-in/memcode` ("#1 Memory Layer for AI
agents"), `xzf-thu/VoiceMem`, `NxcoreAI/EverRoom`, `jaredrhod/fullstack-agent`. PH: 5 direct matches
in the sample (`MemoryBridgeAI v2.0` — "One private memory, shared by ChatGPT, Claude, and Cursor";
`Continuum` — "Remember what you know about the people you manage"; `Kindmemo`; `Rulip`; `Actx0` —
"Memory infrastructure for AI agents").

**Rationale:** Same as the first run's #1 finding — independently, across all three sources, many
unrelated teams solve the identical narrow problem ("coding agents forget everything between
sessions") with genuinely distinct technical approaches (SQLite FTS5, graph DBs, shared/cross-tool
memory, permission-aware memory). A recurring, well-defined pain point with dense independent
attempts is the strongest kind of real-cluster signal this pipeline looks for.

**What a `config/scope.md` here might say:** category = "agent/coding-agent memory and
persistent-context tools"; look for what existing tools' users say is still missing (cross-agent
portability, audit/review of stored memory, cost of retrieval) rather than re-validating the category
exists — it clearly does, confirmed twice now across two runs.

### 3. AI agent tooling infrastructure: MCP servers, agent skills, harness routers — cross-source, heterogeneous

**Signal:** HN: 91 titles mention MCP directly. GitHub: `mcp` (15), `ai-agents` (28), `claude-code`
(20), `agent-skills` (12) topics — after removing the DeepSeek Harness plugin ecosystem (see rejected
section below, a distinct and much larger phenomenon), these topics still show independent builders
wrapping unrelated services (x64dbg, phone-number verification, product comparison) behind MCP
servers, and independent builders packaging "skills" for Claude Code/Codex/Grok Build (`sepia`,
`scroll-craft`, `autoprompt-skill`, `website-rebuild-skill`, `appllama-skills`). `HarnessRouter` is a
notable cross-harness-routing example ("Run Codex, Claude Code, Hermes, PI, DSH... through one API").
PH: present within the broad "Developer Tools" topic (99 matches in-sample) but not cleanly separable
from that topic's grab-bag without name-level reading — MCP/agent-skill-shaped products (`Firecrawl
MCP`, `Skillselion` — "Ranked directory of Claude Code, Codex, Grok, Cursor skills") appear but sparse
in the 2.7% sample.

**Rationale:** Real convergence on a *shape of build* (wrap an existing capability as an MCP server, or
package a workflow as an installable agent skill), not on one end-user problem — the individual
servers/skills solve unrelated problems. Flagging this explicitly, same as the first run: a
`config/scope.md` built directly on "MCP servers" or "agent skills" would be too broad to run
`complaint-miner` against meaningfully; it needs one vertical first. Ranked below #1/#2 for that
internal heterogeneity, not for weaker signal.

### 4. Job search, resume, and interview-prep tooling for individual applicants — cross-source, new this run

**Signal:** HN: 21 titles (`job search|job hunt|resume|interview prep`) — distinct products: Job
Seeker, LiminalML, RoleSweep, Interspectr, ResumeSkip, Filiz, MyPRs, Apply AI, Seisin, Hindcast
(tangential — session-resume, not job-resume), agent-hop. PH: 12 in the "Career" topic — ApplyIn ("AI
agent that applies to jobs from your own browser"), Job Trawlers, WeKIT, AuthBuild, Job Sentry, Yoxon,
RoleTect, PivotPartner, Elan ("AI voice mock interviews"), TechNavigator.

**Rationale:** This category did not appear in the first run's ranked list at all — it is a genuine
new finding this run, not a re-measurement of the same signal (unlike several categories above where
the ~99% window overlap means "new" numbers are really the same corpus). Independent teams across both
sources are converging on the same underlying complaint: the mechanics of applying to jobs (tailoring
resumes per posting, tracking applications across many ATS systems, interview prep) are tedious enough
that many unrelated builders are automating pieces of it with AI agents.

**What a `config/scope.md` here might say:** category = "AI-assisted job search and application
tooling"; look for what users say about trust/accuracy of auto-applying (several titles frame this as
a differentiator — "with a Human in the Loop," "deletes listings when employers close them" — implying
the unassisted-automation version already has a bad reputation worth investigating).

### 5. Local-first / self-hosted / no-account personal software — cross-source

**Signal:** HN: 142 titles carry an explicit local-first/self-hosted/offline/no-account framing across
many unrelated domains. PH: 14 in-sample (`local-first|self-hosted|no account|offline|on-device|no
signup|no upload`). GitHub: 7 under the `local-first` topic, itself a mix of unrelated domains (a
Hook+Skill Guard, an MCP server, a 3D CAD tool).

**Rationale:** Same as the first run — an ethos/positioning cluster, not a single product category.
Real and cross-source-confirmed, but a `config/scope.md` needs to pick one domain rather than scope to
"local-first" itself.

### 6. Personal finance, expense, invoicing, and budgeting tools for individuals and freelancers — cross-source

**Signal:** HN: 30 titles (Spltty, DrakeAI — "no bank sync," Splitright — "without anyone making an
account," Snapquo, an EN 16931 e-invoice validator, Budget Guard, Masareef). PH: 31 unique posts
across "Personal Finance" (11) and "Fintech" (26) topics (6 overlap both) — Kurio, Lumio ("Link none
of your accounts"), coZify, TaxReclaim, Vyapaar Saathi, TradingPlan.

**Rationale:** Cross-source, moderate volume, and the recurring specific complaint (no bank-account
linking required, splitting expenses without forcing account creation, invoicing being a late-night
chore for solo operators) matches the spec's own worked example and the first run's #5 finding closely
— consistent signal across both runs.

### 7. Native macOS Markdown / text editors — cross-source, weaker PH this run

**Signal:** HN: 45 raw `markdown|text editor` matches; distinct products include Nodes, Write.md,
Marble, Caxton, Femto, Lilo, Notes Plus, MarkUp, HyperMarkdown. Notably, `Markdown Buddy`
(`objectID`s 49205269 and 49374127) is the same resubmission-duplicate the first run flagged — same
posts, same window overlap noted above, not a new resubmission. PH: only 3 in-sample this run (down
from a stronger showing last time) — attributable to the 2.7%-sample fraction rather than a real
decline; flagged as weaker-this-round rather than silently reported as a drop in real signal.

**Rationale:** A visibly saturated, repeatedly-attempted niche, confirmed again on HN; PH's weaker
showing this run is a sampling artifact, stated plainly rather than smoothed over.

### 8. Habit tracking, journaling, and personal note-taking — cross-source

**Signal:** HN: 21 titles (Kadō, jeden.day, Evoboard, a decision journal for AI agents). PH: 8
in-sample (`habit|journal|notes app|diary`) — includes a five-year journal ("LifeLogs") and an
offline-first tasks/habits app referenced on HN (Kagelin).

**Rationale:** Consistent, moderate cross-source signal matching the spec's own worked example and the
first run's #7 finding.

### 9. macOS menu-bar utility apps — single-source-leaning (HN)

**Signal:** HN: 15 (`menu bar|menubar`) — a cron-job watchdog, dev-server/port monitor, Claude-usage
trackers (two independent ones), a color picker, a crypto-price ticker. No distinct PH or GitHub
confirmation found this run.

**Rationale:** A recognizable recurring *form factor* rather than one problem, same as the first run's
#8 finding — the individual tools solve unrelated problems, so a `config/scope.md` would need to pick
one underlying problem, not "menu-bar apps" as a category.

## Considered and rejected (surfaced by first-pass signals, not promoted)

- **DeepSeek Harness (`dsh`) plugin ecosystem — GitHub, 33 repos under `dsh-plugin`/`deepseek-harness`/
  `dsh` topics.** This is the clearest ethical-boundary case this pipeline has hit: every one of these
  33 repos exists *because* one specific upstream project (`deepseek-ai/deepseek-harness`, 211,588★,
  itself in the pull) has a plugin architecture — they are plugins, skins, desktop wrappers, and
  handbooks for that one tool, not independent teams converging on a shared problem from different
  angles. Per the spec's own ethical boundary (§6: never name one specific project as the reason a
  category looks promising), this is excluded outright rather than reframed as an aggregate "plugin
  ecosystem" category — the aggregate here *is* one project's userbase, which is a different thing
  from the aggregate clusters above.
- **Games** — HN 115 raw matches. Reading a sample confirmed the same conclusion as the first run: a
  genuine grab-bag (retro-console compilers, board games, karaoke games, a texture pipeline) with no
  shared underlying problem beyond "someone built a game."
- **General browser extensions** — HN 17 (down from 24 last run — same conclusion, weaker signal).
  Samples remained scattered (price comparison, YouTube tools, a Cloudflare-Worker dev extension) — a
  build form factor, not a shared problem.
- **ProductHunt "GitHub" topic tag** — 57 in-sample matches, but reading samples showed this is PH's
  generic catch-all tag for anything with developer/open-source relevance, not a coherent
  problem-space cluster (a Verilog-to-DNA simulator, a fax service, and a GitHub-avatar generator all
  carry this tag).
- **ProductHunt "Productivity" topic tag** — 167 in-sample matches, far too broad to be its own
  cluster; sampling it showed the real signal inside it is already captured by the narrower categories
  above (career/job-search, personal finance, habit-tracking, agent memory all appear as sub-slices of
  this tag).
- **Browser automation / web-scraping tooling for agents** — HN 10 (`browser automation|web
  automation|scraping|scraper`) — real and coherent (Browser Agent, PyScrappy, Puppetflow) but too
  small this run to promote as its own category; closer to a minor variant of #3's shape-of-build
  pattern than a standalone problem-space cluster.

## Summary

9 candidate categories promoted, ranked above; 5 additional clusters/tags surfaced by first-pass
signals and explicitly rejected, including one new ethical-boundary case (a single project's plugin
ecosystem, not an aggregate cluster).

- **Show HN: found**, full 30-day window confirmed (3671 titles, 6-bucket pull summing exactly to the
  un-truncated total, no gaps/no duplicates). Window overlaps ~99% with the first run's — most
  category counts here re-measure the same corpus, not fresh two-day growth; stated per-category above
  where that matters.
- **ProductHunt: found**, and — unlike the first run — genuinely touched all 30 calendar days this
  time (the spec's own corrective was applied successfully, no rate limit hit across 30 paced daily
  queries). Each day's sample is bounded to its 20 newest posts (an API-side cap on `first`, newly
  confirmed this run) against real daily volumes of 431–1331 — a ~2.7% window-spread sample, reported
  as exactly that rather than implied to be exhaustive.
- **GitHub: found**, first real run of this step. `stars:>500` (216 repos, vs. 510 at `stars:>250`)
  kept the pull in the low hundreds as the spec requires. Topics cleanly surfaced themes; the 85
  no-topic repos were checked via a description-frequency fallback per spec and added no new theme.
- **Excluded, per spec**: broad HN comment firehose, Reddit, app-store, GitHub-issue discovery, GH
  Archive.

**Note for the next run:** PH's real per-day volume (431–1331) and the newly-confirmed `first`-capped-
at-20 API behavior mean a day-by-day paced pull (as done here) is now the known-working pattern —
worth carrying forward rather than rediscovering. HN's window-overlap issue (two runs 12h apart cover
~99% the same corpus) suggests spacing future discovery runs further apart, or explicitly diffing
against the prior run's `objectID`s, to avoid re-reporting the same signal as if it were fresh.
