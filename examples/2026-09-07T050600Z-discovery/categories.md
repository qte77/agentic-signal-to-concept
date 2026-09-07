# Discovery run 2026-09-07T050600Z — candidate problem-space categories

Phase 0 (`signal-discoverer`) **third** real execution, and the first to exercise all five sources
for real in one pass: Show HN, ProductHunt, GitHub (all three run before), plus **cv.inc
(cerebralvalley.ai) hackathon listings and Hugging Face Spaces, both new this run**. No category
named up front, per the spec. Output is a triage list for a human to pick from, not sourced-quote
evidence the way Phase 1 findings are.

**Window overlap with the second run, stated plainly.** This run's 30-day window
(`2026-08-08T05:06:00Z`–`2026-09-07T05:06:00Z`, epoch `1786165560`–`1788757560`) overlaps the second
run's (`2026-08-05T08:20:18Z`–`2026-09-04T08:20:18Z`, epoch `1785918018`–`1788510018`) by **90.4%**
(2,344,458 of 2,592,000 seconds) — materially less overlap than the ~99% between runs 1 and 2, because
this run was taken ~3 days later rather than ~2h14m later. The genuinely fresh tail (created after run
2's window closed, epoch `>1788510018`) is **~2.87 days** (2026-09-04T08:20Z onward). Every HN and PH
count below is split into "total" vs. "fresh tail" so re-measurement isn't misread as new growth. cv.inc
and Hugging Face Spaces are new sources — all their signal is new-by-construction, stated as such rather
than compared against a run-2 baseline that doesn't exist for them. GitHub's re-run carries a real
caveat this time (see GitHub section): run 2's raw 216-repo list was not archived anywhere in this repo,
so a precise repo-level diff against run 2 isn't possible — only the aggregate shape (threshold, count,
topic distribution) can be compared, not a same-repo-vs-new-repo split.

## Window and source coverage

| Source | State | What was pulled | Window |
|---|---|---|---|
| Show HN (Algolia) | **found**, full window confirmed | All `tags=show_hn` stories, unfiltered by topic | `created_at_i` in `(1786165560, 1788757560]` = 2026-08-08T05:06:00Z – 2026-09-07T05:06:00Z (30 days, spec default; `discovery/window-override.md` confirmed absent via Read-tool check) |
| ProductHunt (GraphQL v2) | **found**, bounded sample, full window touched | `posts(postedAfter:, postedBefore:, first: 20, order: NEWEST)`, one query per calendar day, 30 queries | Full 30 calendar days, 2026-08-08 through 2026-09-06 (2026-09-07 excluded — only ~5 hours old at pull time, would be a misleadingly incomplete "day") |
| GitHub (`gh` search API) | **found**, third run | `search/repositories` with `created:>{cutoff} stars:>500 sort:stars-desc`, no `topic:` qualifier, 3 pages | `created:>2026-08-08` |
| cv.inc (cerebralvalley.ai) | **found**, new source | `llms.txt` → `llms-full.txt` (index + Showcase) + `sitemap-md.xml` (per-event `lastmod`), event pages fetched via `.md` suffix, one public gallery pulled | Filtered to event pages with `lastmod` inside this run's window |
| Hugging Face Spaces | **found**, new source, thin signal this window | `api/spaces?search={keyword}` and `api/spaces?filter=hackathon`, `sort=createdAt&direction=-1` | Filtered to `createdAt` inside this run's window |

**Show HN — how it was pulled.** Full-window `nbHits` (`hitsPerPage=0` probe) came back **3533**,
above Algolia's 1000-hit `paginationLimitedTo` cap, so the window was split into 6 evenly-sized
~5-day buckets (`numericFilters=created_at_i>X,created_at_i<Y`, boundaries at
1786165560/1786597560/1787029560/1787461560/1787893560/1788325560/1788757560), each fetched with
`hitsPerPage=1000` via `polyfetch-scrape`. Per-bucket `nbHits`: 602, 567, 644, 603, 554, 563 — summed
to **exactly 3533**, matching the un-truncated total with **3533 unique `objectID`s** after merge, no
gaps, no duplicates — full coverage confirmed, not assumed, same discipline as runs 1 and 2. Of the
3533, **273 were created after run 2's window closed** (epoch `>1788510018`) — the genuinely fresh
tail. Clustering used a word/bigram frequency first pass (python, not read title-by-title) to surface
candidate themes, then targeted regex per theme, with samples/full matched sets read to judge coherent
cluster vs. coincidental word match, and each candidate theme's matches split into total vs.
fresh-tail counts.

**ProductHunt — how it was pulled.** Token (`PRODUCTHUNT_API_TOKEN`) verified live via a 3-post test
call before committing to the full pull (verified without ever printing the token or its length, per
this session's own guidance on not letting a secret-shaped value reach any script's output).
`postedAfter`/`postedBefore` bound each query to one UTC calendar day, `first: 20`, paced 3 seconds
apart, run synchronously (an earlier attempt to background the 30-query pull via `nohup ... &` was
silently killed when the shell session was torn down between Bash calls in this sandbox — re-run
synchronously with a longer timeout instead, and it completed cleanly). **No rate limit was hit across
all 30 requests.** `first` is still silently capped at 20 regardless of request value (confirmed again,
same as run 2). Real per-day volume (`totalCount`) ranged **431–1,329** across the 30 days, summing to
**21,968** real posts in the window against **600** sampled (20/day × 30) — a **2.73% sample**, evenly
spread across all 30 days, reported as exactly that. Per-day totals for the **27 days** that overlap
run 2's own day-by-day table (2026-08-08 through 2026-08-31, plus 2026-09-01 through 2026-09-03 — 24 +
3 = 27 days) are within 0–2 posts of run 2's figures for the same calendar dates — e.g. 2026-08-27: 788
here vs. 789 in run 2; 2026-09-01: 1329 vs. 1331 — consistent with the same underlying corpus being
re-measured with normal count-at-query-time drift, not two-day growth. **2026-09-04, 2026-09-05, and
2026-09-06 are genuinely new calendar days** not present in run 2's table at all (totals 700, 602, 534
respectively).

**GitHub — how it was pulled.** Checked `total_count` first: `created:>2026-08-08 stars:>500
sort:stars-desc` returned **214** (run 2's equivalent query, 3 days earlier, returned 216 — consistent,
kept the same threshold rather than re-deriving it). Fetched all 3 pages (100/100/14), `total_count:
214` consistent across all three fetches, **214 unique `full_name`s** after dedup — full coverage of
the thresholded set confirmed. **Caveat this run doesn't have a way around:** run 2's raw 216-repo list
was never saved to this repo (only its categories.md narrative survives), so there is no way to compute
a precise "how many of this run's 214 are genuinely new repos vs. repos that simply crossed the 500-star
threshold since run 2" split — a repo appearing in this run's set is not automatically new-since-run-2.
Only the aggregate shape (threshold held steady at 500, count stayed in the same low-200s band, topic
distribution shape) is comparable, stated as that rather than implying a repo-level diff was done.

**cv.inc — how it was pulled.** Entered via `cerebralvalley.ai/llms.txt` (confirmed the 308 redirect
from `cv.inc` reaches this host, per the ToS-clearance research archived at
`examples/2026-09-05T213930Z-hackathon-signal-research/hackathon-signal-research.md` — not re-verified
from scratch this run, per that research's own recommendation to re-check only if terms change) rather
than guessing an events path — it pointed at `llms-full.txt` (238KB: site overview, a 49-event curated
"Showcase" newest-first starting 2026-07-18 — all 49 fall before this run's window, so none qualify as
in-window completed showcase events — and a live "Upcoming events" index) and `sitemap-md.xml` (159
`/e/{slug}.md` entries, each with a `lastmod` timestamp). Filtered `sitemap-md.xml` to the **12** event
pages with `lastmod` inside this run's window; two were clearly test/placeholder pages
(`sample-hackathon`, `qr-check-in-dev-demo`) and one a logistics-only meetup
(`joyco-meetup-e73c18e`) — excluded. Read the remaining 5 substantive event pages in full (`.md`
suffix): `persistent-context-sprint-hackathon`, `mongodb-nyc-hackathon`, `vultr-the-agent-arena`,
`glm-5-3-flash-lightning-hackathon`, `openai-hackathon-brasil`. Pulled the public gallery for the one
completed, in-window hackathon with a gallery
(`persistent-context-sprint-hackathon/hackathon/gallery.md`).

**Hugging Face Spaces — how it was pulled, and its real limit this run.** ToS/robots.txt clearance is
from `examples/2026-09-05T233231Z-additional-sources-research/additional-sources-research.md`
(§4, "Hugging Face (Spaces / Hub) — FOUND, clean") — not re-verified from scratch this run, same
carry-forward discipline as cv.inc above. `api/spaces` confirmed to accept `search`, `filter`, `limit`
(honored up to at least 1000, no silent cap the way PH's `first` is), `sort=createdAt`, and
`direction=-1`; `createdAt` is present on every record, letting results be bounded to the window
directly. Confirmed the search index is live and current (a zero-filter, `createdAt`-desc query
returned Spaces created minutes before the check, ruling out index staleness as an explanation for what
follows). `search=hackathon` (limit 1000, newest-first) returned exactly 1000 hits spanning 2026-05-10
to 2026-09-04, of which only **7** fell inside this run's window, and none carried the structured
`sponsor:`/`track:`/`achievement:` tags the prior research pass found on a 2025 example org. The
exact-tag route, `filter=hackathon`, returned only **174** Spaces total (that literal tag is rare),
newest dated 2026-07-07 — **zero in this run's window**. Seven further keyword variants tied to this
window's actual cv.inc hackathon themes (`buildathon`, `agentathon`, `genai hackathon`, `mcp hackathon`,
`vultr`, `gemini 3 hackathon`, `gpt-6 astra`) each returned 0–100 total hits and **0 in-window matches**
— nine distinct queries checked in total (the two base methods plus these seven). **Stated plainly:
Hugging Face Spaces is found — clean ToS/robots.txt posture, a working and current API — but carries
negligible hackathon-convergence signal for this specific 30-day window**, a real, multiply-verified
finding (not a search-syntax failure) rather than the rich 603-Space-single-org corpus the prior
research pass sampled from 2025 data. That richer corpus may exist for other windows; it wasn't present
in this one.

**Excluded per spec, not attempted:** broad HN comment firehose, Reddit, app-store, GitHub-issue
discovery, GH Archive, Devpost, AGI House, SourceHut, Kaggle, Y Combinator company directory, Stack
Overflow/Stack Exchange, GitLab, Codeberg — all previously confirmed blocked or excluded-by-cost; no
re-check attempted since none of their terms are known to have changed.

## Ranked candidate categories

Ranked by combined signal strength and cross-source confirmation (cross-source ranks above
single-source, per spec). Every category names exactly which sources confirmed it and which stayed
silent, rather than a bare "cross-source" label. Counts are per-category keyword/topic matches, not
mutually exclusive.

### 1. Agent memory / persistent context for AI coding agents — HN + GitHub + PH + cv.inc (four sources, strongest signal of any run to date)

**Signal:** HN: 63 titles matched a widened memory/remember/recall/persistent-context regex (6 in the
fresh tail); reading the full set, roughly 35+ are real, distinct agent-memory products after
discarding false positives (RAMGuard Pro's RAM optimizer, a memory-safe SSH server, a formal
memory-model proof, a calendar app, a photo-import tool) — NexusMem (x2 submissions), MCP Memory,
Cogni, Knownbase, Knowl (x2), Mnemosyne, Engrava, Seahorse, CommitLore, Memanto, Contextual, Decispher,
Itsuki, Rta-Smriti, Rafter, Memnest, Mex, Engram, Verity, Vibsync, Anansi, 1Presence, Feral, CueMap,
Hillock, Sillage, Agent Mesh, among others; fresh-tail real matches include **Engrim** ("universal,
local-first SQLite memory engine for AI CLIs") and **DeepMem** ("persistent semantic memory via MCP
for Claude Code"). GitHub: **3** genuine non-DSH matches after filtering 2 false positives
(`duty1g/x64dbg-mcp-server`'s literal "read memory" debugger feature, a travel-photo "memory sticker"
app) — `memcode-in/memcode` ("#1 Memory Layer for AI agents"), `xzf-thu/VoiceMem`,
`jaredrhod/fullstack-agent`. This is **one fewer** than run 2's 4 genuine matches: run 2's
`NxcoreAI/EverRoom` no longer appears in this run's `created:>2026-08-08` set — a clean illustration of
the window-shift effect (a repo created before 2026-08-08 ages out of the query entirely as the cutoff
moves forward), not a sign the product disappeared. PH: weak in the 2.73% sample as expected (`Actx0` —
"Memory infrastructure for AI agents"). **cv.inc, new this run:** two independent MongoDB-sponsored
hackathons, both explicitly themed around this exact category — `persistent-context-sprint-hackathon`
(completed Aug 13, in-window; "build AI applications powered by agent memory... intelligence grounded
in persistent memory") drew a **94-team public gallery** (3 placed), and `mongodb-nyc-hackathon`
(upcoming Sep 26; "the next chapter of agentic infrastructure is memory, persistence, and self-evolving
systems... building harnesses that can adapt, remember, and operate reliably over the long haul") — a
sponsor stating the identical demand twice, in its own words, independent of any launch-post or repo
count.

**Rationale:** This is the third consecutive run in which this category is the single strongest signal
in the corpus, and the first time a source outside the product-launch shape (HN/PH/GitHub) confirms it
— a company (MongoDB) is willing to fund two separate hackathons around exactly this problem and drew
94 independent teams to one of them in under 8 hours. That is first-party stated demand plus compressed
multi-team convergence, on top of three runs of consistent unprompted-launch signal. This is now about
as strong as the evidence gets in this pipeline's own vocabulary.

**What a `config/scope.md` here might say:** category = "agent/coding-agent memory and
persistent-context tools"; this no longer needs re-validating that the category exists — confirmed
three times over five sources. Look instead at what the 94-team gallery's non-winning entries and the
newest HN products (Engrim, DeepMem) say is still unsolved (cross-agent portability, audit of stored
memory, retrieval cost) now that the basic "give an agent memory" problem has many competent answers.

### 2. Terminal / session management and orchestration for coding agents — HN + GitHub + PH

**Signal:** HN: 76 raw `terminal|session` matches; narrowing to titles that also mention an
agent/coding/AI/tool-name term yields 28 matches (0 in the fresh tail), of which ~25 are genuinely
about managing, recording, resuming, or orchestrating AI-coding-agent sessions — Compactdiff, Hindcast,
Agent-exchange, captain-miao, CrewCoder, Porcupine, Claude Fleet, Pier, Mole, Clinch, KeepNow, Open
Session, TheGitAI, Zoetrope, AgentParty, VT Code, Aurict, Aidcrew, among others (Kith — a therapy
ambient-audio notes app — Kaden Terminal, and Namo_complete are tangential, not about session
management, despite matching the regex). GitHub (non-DSH, 178 repos): the same shape appears under the
`ai-agents`/`claude-code`/`local-first` topics — `furkankly/zoetrope` ("Watch a Claude Code session as
a live flow graph"), `damejan80/tokentab` (reads Claude Code/Codex/Gemini CLI session logs for cost),
`iAmCorey/Wake` ("all your AI agent sessions in one place — browse, search, resume"). PH: weak in-sample
as expected given the 2.73% sample fraction — `hax` ("minimalist, terminal-native coding agent"),
fresh-tail `Termish` ("mobile terminal for remote AI coding on your own machine").

**Rationale:** A recurring, named pain point — coding-agent sessions need their own recording, resuming,
and orchestration layer distinct from a plain shell — attempted independently across all three sources
that carry it, consistent with both prior runs (run 1's ~17 products, run 2's ~40+ on a broader
full-read regex). This run's narrower agent-specific regex naturally yields a smaller count than run
2's broader one; the size difference is a methodology artifact, not a claim the cluster shrank.

**What a `config/scope.md` here might say:** category = "session/terminal UX and orchestration for AI
coding agents"; look at what launch comments say is still missing — cross-tool session portability
(Claude Code ↔ Codex ↔ Cursor) and cost/token visibility across sessions came up repeatedly by name in
both this run and run 2.

### 3. AI agent tooling infrastructure: MCP servers, agent skills, harness routers — HN + GitHub + PH, heterogeneous

**Signal:** HN: 89 titles mention MCP directly (9 in the fresh tail). GitHub (non-DSH, 178 repos): `mcp`
(12), `agent-skills` (10), `claude-code` (18), `ai-agents` (19) topics — independent builders wrapping
unrelated services (a debugger, a product-comparison tool, cloud-status feeds) behind MCP servers, and
independent Claude Code/Codex "skill" packages (`sepia`, `scroll-craft`, `autoprompt-skill`,
`website-rebuild-skill`). PH: weak in-sample (`MCPanel` — "Developer first local MCP servers manager").

**Rationale:** Same shape-of-build finding as runs 1 and 2 — real, dense, repeated convergence on *how*
to build (an MCP wrapper, a packaged Claude Code/Codex skill), not on one shared end-user problem; the
individual outputs solve unrelated problems. A `config/scope.md` built directly on "MCP servers" or
"agent skills" would be too broad for `complaint-miner` to run meaningfully against — it needs one
vertical first, same caveat both prior runs raised. Ranked below #1 and #2 for this internal
heterogeneity, not for weaker raw signal (89 HN mentions is the largest raw count of any category this
run).

### 4. Local-first / self-hosted / no-account personal software — HN + PH + GitHub

**Signal:** HN: 276 titles carry an explicit local-first/self-hosted/offline/no-account/open-source
framing (18 in the fresh tail) across unrelated domains. PH: 11 in-sample (DoughCue, PulseDeck, Folio
V1, ToolWise, MoneyBoss Home, fresh-tail Sablefold). GitHub: 5 under the `local-first` topic (a
Hook+Skill Guard, an MCP server, others) — itself a mix of unrelated domains, same as run 2.

**Rationale:** Unchanged from runs 1–2 — a positioning/ethos cluster, not a scopeable product category
on its own; a `config/scope.md` needs to pick one domain (see #5, #6, #7) rather than scope to
"local-first" itself.

### 5. Personal finance, invoicing, and expense tools for individuals and freelancers — HN + PH

**Signal:** HN: 20 raw regex matches (`invoic|expense|budget|tax|payroll|bookkeep|freelanc`), 0 in the
fresh tail; **17** real after excluding 3 clearly unrelated hits (a Minecraft-clone rewrite's $120 dev
budget, a Harberger-tax ad-slot experiment, a "close the federal deficit yourself" toy) — TimeM8,
Inkvoice ("self-hosted invoicing in a single SQLite file"), Budget Guard, Splitright, an
invoice-fetching CLI, Snapquo, Masareef, Invovanta, two independent budget apps, UseCOS calculators. PH:
10 in-sample (Selpx, Invoice Compare, Family Budget Ledger, Vyapaar Saathi, Axix IDP Engine, Invoicara,
Invoice Manager for Excel, MoneyBoss Home, fresh-tail Higxel — "turn receipt photos into expenses you
can check and edit").

**Rationale:** Third consecutive run confirming this cluster at comparable volume, same specific
recurring complaints (no bank-account linking, no forced account creation to split a bill, invoicing
as a late-night chore for solo operators) as runs 1 and 2.

**What a `config/scope.md` here might say:** category = "invoicing and expense tracking for freelancers
and small operators"; the recurring complaint across all three runs is specifically "no bank-account
linking required" and "no forced account creation" — narrow `complaint-miner` toward that framing
rather than personal finance broadly.

### 6. Job search, resume, and interview-prep tooling for individual applicants — HN + PH

**Signal:** HN: 18 titles, 0 in the fresh tail (Seisin, "find the jobs that fit your resume,"
Interspectr, an MCP interface for personal job-search data, ApplyWise, RoleSweep — "deletes listings
when employers close them"). PH: 7 in-sample (WeKIT, Candid, AuthBuild, Resume Score, Job Sentry,
BestCV.in, ZenResume).

**Rationale:** Consistent with run 2's new finding (this category did not appear at all in run 1); now
confirmed a second time at comparable volume, same recurring trust/accuracy framing around
auto-application tooling.

**What a `config/scope.md` here might say:** category = "AI-assisted job search and application
tooling"; both runs that surfaced this category found titles framing trust/accuracy as a differentiator
("with a Human in the Loop," "deletes listings when employers close them") — worth investigating
whether the unassisted-automation version already has a bad reputation.

### 7. Habit tracking, journaling, and personal note-taking — HN + PH

**Signal:** HN: 14 titles, 3 in the fresh tail — Kadō (a second, differently-worded Kadō submission —
"with non-binary habit score" — distinct from an earlier non-fresh Kadō post, likely a relaunch or
resubmission), CocoCut ("zero-cloud, 1-second video diary engine"), and Hitomaki ("a diary that
advances when you finish a toilet roll"). Non-fresh matches include Meridian (local-first journal that
drafts project updates), a decision journal for AI agents, and a work journal for developers. PH: 6
in-sample, 1 fresh (DoughCue's sourdough log, TradingPlan, Lumicent, LifeLogs, fresh-tail Vifo — "AI
travel journal your agent writes").

**Rationale:** Consistent, moderate cross-source signal matching all three runs' findings.

**What a `config/scope.md` here might say:** category = "personal habit tracking and journaling
tools"; the fresh-tail entries lean toward niche framing devices (a diary tied to a physical habit, a
1-second daily video) rather than a single dominant angle — worth sampling launch comments for which
framing gets the most engagement before narrowing further.

### 8. Native macOS / cross-platform Markdown and text editors, with a new agent-interface sub-pattern — single-source (HN)

**Signal:** HN: 42 raw `markdown|text editor` matches, 3 in the fresh tail (MRDown — "diffs what your
AI just rewrote", MarkFlowy, Md2pdf). As in both prior runs, a visibly saturated niche of standalone
editors (Write.md, Caxton, Marble, Notes Plus, Lilo, "Another Markdown editor", "Another Text Editor")
— but this run's matched set also surfaces a distinct, smaller sub-pattern not named explicitly in runs
1–2: **Markdown positioned as the interchange format between a human and an AI agent**, not just a
human-facing editor — Notula ("where humans edit the Markdown your AI agents read"), Markdown
Gatekeeper ("one current source per topic for AI agents"), MRDown, and a Job Seeker tool built as
"open-source Markdown skills." PH: 0 matches in this run's narrow name/tagline regex over the 2.73%
sample (down from a weak-but-present showing in both prior runs) — attributable to the sample fraction,
not a real absence, same caveat runs 1–2 both raised for this category. **Single-source this run** (PH
0, no GitHub match) — ranked below the cross-source categories above per the spec's own ranking rule,
even though its raw volume exceeds several of them.

**Rationale:** The core editor-saturation finding repeats a third time; the agent-interchange sub-thread
is new and small (4 items) but worth flagging distinctly rather than folding silently into "yet another
editor," since it points at a different underlying need (structured, diffable, agent-readable state)
than a human-facing writing tool.

**What a `config/scope.md` here might say:** category = "Markdown as an agent-readable interchange
format" (narrower than "another Markdown editor," which is already visibly saturated) — scope
`complaint-miner` to the agent-interchange framing specifically (Notula, Markdown Gatekeeper, MRDown)
rather than the broader editor niche.

### 9. macOS menu-bar utility apps — single-source (HN)

**Signal:** HN: 14 (`menu bar|menubar`), 1 in the fresh tail (Nocturne — "hide the Mac menu bar clock,
or hide everything but the clock"). Non-fresh matches: p0rt (dev-server/port monitor), AirStats (system
monitor, appears twice — a resubmission or near-duplicate), Claude subscription-usage trackers, HN-karma
trackers, a cron-watchdog app. No PH or GitHub confirmation this run, same as prior runs.

**Rationale:** A recurring form factor rather than one shared problem, unchanged from runs 1–2 — a
`config/scope.md` would need to pick the underlying problem (dev-server monitoring, usage tracking),
not "menu-bar apps" itself.

## Considered and rejected (surfaced by first-pass signals, not promoted)

- **DeepSeek Harness (`dsh`) plugin ecosystem — GitHub, 36 repos under `dsh-plugin`/`deepseek-harness`/
  `dsh`/`cordis` topics (up from 33 in run 2).** Same ethical-boundary case as run 2, larger this time:
  every one of these repos exists because one upstream project (`deepseek-ai/deepseek-harness`) has a
  plugin architecture — not independent teams converging on a shared problem. Excluded outright per
  spec, not reframed as an "ecosystem" category.
- **Games** — HN 122 raw matches; a sample confirms the same grab-bag conclusion as both prior runs
  (a C# game engine, a multiplayer trading game, a racing game, tic-tac-toe, a puzzle game, a bot-
  detection training game) — no shared problem beyond "someone built a game."
- **General browser extensions** — HN 16 (down slightly from run 2's 17). Samples remained scattered
  (a dark-mode toggle, a tech profiler, an ad blocker, a Gmail attachment manager, a price-comparison
  tool) — a build form factor, not a shared problem, same conclusion as both prior runs.
- **Browser automation / web-scraping tooling for agents** — HN 10, same size as run 1's finding and
  still too small to promote on its own; closer to a minor variant of category #3's shape-of-build
  pattern than a standalone cluster.
- **"Video" as a keyword cluster** — HN 57 raw matches, mostly a grab-bag (`[video]` used as a generic
  post-format tag on unrelated posts, human-facing video editors). One small (~3-item) real sub-pattern
  surfaced within it — AI-agent-controllable video editors (Velorn, "an open-source video editor built
  for coding agents", "open source video editor that you can control with an LLM") — too small this run
  to promote standalone; noted here rather than silently folded into the video grab-bag.
- **cv.inc: GLM-5.3 Flash Lightning Hackathon, OpenAI Hackathon Brasil** — both real, in-window events,
  but both are generic "build anything ambitious with this model" formats with no narrow problem
  statement — read and explicitly not promoted as a category, rather than silently omitted.
- **cv.inc: Vultr "The Agent Arena Hackathon"** (upcoming, Sep 26) — themed around "agent infrastructure
  ... VM backends, serverless inference, the compute layer agents run on" — real sponsor-stated demand,
  but a single mention from a single source this run; noted as a weak, single-source lean toward
  category #3's infrastructure angle rather than promoted as its own category.
- **Hugging Face Spaces hackathon-tag/keyword searches** — see Source coverage above: multiply-verified
  as thin for this specific window (7 of 1000 free-text "hackathon" matches in-window, 0 of 174 exact-
  tag matches in-window, 0 across seven further keyword variants) — a real finding about this window,
  not a rejected cluster in the usual sense, stated here for completeness rather than only in the
  coverage table.

## Summary

**9 candidate categories promoted**, ranked above — the same count as run 2, with every one of run 2's
categories still present here (terminal/session, memory, MCP, job search, local-first, finance,
markdown, menu-bar). 7 additional clusters/patterns surfaced by first-pass signals and explicitly
rejected or flagged-but-not-promoted, including one new instance of the DeepSeek-Harness ethical-
boundary case, one new sub-pattern noted but not promoted (AI-agent-controllable video editors), and
three cv.inc event themes read and explicitly not promoted.

- **Show HN: found**, full 30-day window confirmed (3533 titles, 6-bucket pull summing exactly to the
  un-truncated total, no gaps/no duplicates). Overlaps run 2's window by 90.4%; 273 of 3533 titles
  (7.7%) fall in the genuinely fresh ~2.87-day tail. Fresh-tail counts by category: #1 memory 6/63,
  #2 terminal/session 0/28, #3 MCP 9/89, #4 local-first 18/276, #5 finance 0/20, #6 job search 0/18,
  #7 habit/journal 3/14, #8 markdown 3/42, #9 menu-bar 1/14.
- **ProductHunt: found**, all 30 calendar days touched, no rate limit hit. Real volume 431–1,329
  posts/day (21,968 total across the window) against a 600-post (2.73%) evenly-spread sample. Per-day
  totals for the 27 days overlapping run 2's table match within 0–2 posts — re-measurement of the same
  corpus, not fresh growth; 2026-09-04/05/06 are the three genuinely new calendar days.
- **GitHub: found**, third run, threshold held at `stars:>500` (214 repos, vs. run 2's 216 at the same
  threshold 3 days earlier) — consistent aggregate shape. A repo-level new-vs-re-measured diff against
  run 2 was not possible because run 2's raw repo list was never archived in this repo; stated as a real
  limitation rather than assumed away. One concrete instance of the window-shift effect: run 2's
  `NxcoreAI/EverRoom` (an agent-memory match) no longer appears in this run's `created:>2026-08-08` set.
- **cv.inc: found, new source.** Entered via `llms.txt` → `sitemap-md.xml` (per-event `lastmod`) rather
  than a guessed events-listing path; ToS clearance carried forward from
  `examples/2026-09-05T213930Z-hackathon-signal-research/hackathon-signal-research.md`, not re-verified
  from scratch. 12 event pages last-modified in-window; the strongest single finding of this run came
  from here — two independent MongoDB-sponsored hackathons naming "agent memory / persistent context"
  as their explicit theme, one of them (completed, in-window) drawing a 94-team public gallery in under
  8 hours.
- **Hugging Face Spaces: found, new source, but thin for this window.** ToS clearance carried forward
  from `examples/2026-09-05T233231Z-additional-sources-research/additional-sources-research.md`, not
  re-verified from scratch. A live and current API (verified independently of the thin result) — but
  negligible hackathon-tag or hackathon-keyword convergence signal for this specific 30-day window,
  checked nine different ways (one broad keyword pull, one exact-tag filter, seven further keyword
  variants). A real, verified finding about this window, not a tooling failure.
- **Excluded, per spec, unchanged from prior runs**: broad HN comment firehose, Reddit, app-store,
  GitHub-issue discovery, GH Archive, Devpost, AGI House, SourceHut, Kaggle, Y Combinator company
  directory, Stack Overflow/Stack Exchange, GitLab, Codeberg.

**Note for the next run.** Two carry-forwards worth acting on rather than rediscovering: (1) archive
each run's raw per-source data (or at minimum the GitHub repo `full_name` list and HN `objectID` list)
somewhere durable, so the next run can do a precise new-vs-re-measured diff instead of only a narrative
one — this run had to state that gap rather than close it. (2) Background processes started via
`nohup ... &` inside a Bash tool call do not survive to the next Bash call in this sandbox (the shell is
torn down between calls) — run long external-API pulls synchronously with a generous `timeout` instead
of backing them off; this run lost one full PH pull cycle discovering that the hard way before
re-running synchronously.
