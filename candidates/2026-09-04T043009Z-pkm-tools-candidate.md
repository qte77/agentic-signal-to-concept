# Candidate — Recallect

## Concept candidate

- **Name:** Recallect
- **Live URL:** Not yet built — feeds agentic-grounded-persona-eval Phases 1-2 only, not Phase 3
- **Slug:** recallect
- **Description:** A local-first search-and-memory layer that sits on top of a note-owner's
  *existing* plain-markdown / Obsidian-format vault — not a new editor, not a vault migration. It
  gives both the human owner and their coding/AI agents fast retrieval over notes they already have,
  built on a local-first, no-lock-in architecture that keeps notes in plain files the owner controls,
  not behind any vendor's paywall or proprietary sync service. (Kept intentionally aggregate — see
  the ethical-boundary notes under Evidence for why no single builder's specific feature list is
  used as the spec here.)

## Assumed ICPs

(derived from evidence, not invented)

- **Long-time paying PKM customers who've watched core functionality or data access get moved behind
  a paywall, and who no longer trust that what they put into a tool stays theirs.** Grounded in
  Complaints Pattern 3 (Evernote's welcome-page paywall; a maker's own complaint about two other paid
  products that wouldn't hand back raw files) and architecturally corroborated by Builds Pattern 2's
  14 independent local-first/no-lock-in instances. This is this run's strongest, most cross-source-
  grounded ICP.
- **Existing Obsidian/plain-markdown vault owners who can capture notes easily but can't find them
  again.** Grounded in Complaints Pattern 2 (Evernote, Joplin/Obsidian OCR gap, Amplenote search
  request). Corroborated by Builds Pattern 3 (16 instances), but only as **mechanism** convergence —
  see the hedge under Convergence B below; the build-side evidence does not confirm this same human
  population is who those builders are serving.
- **Developers using coding agents (Claude Code, Cursor, and similar) who want an existing notes
  vault to serve as durable agent memory**, rather than re-explaining context every session. Grounded
  directly in Builds Pattern 3 (16 instances, explicitly agent-facing: "for coding agents,"
  "Persistent memory for Claude Code," "AI agent memory engine"). Treated as a **distinct** ICP from
  the one above, not the same population, per the hedge.
- **[Thin, complaint-only] PKM users who have suffered catastrophic, unrecovered sync data loss and
  no longer trust their tool at all.** Grounded only in Complaints Pattern 1 (4 independent users,
  4 different sync backends) and reinforced by Pattern 5's unprompted stability complaints from
  otherwise-satisfied Logseq fans. **No build-side pattern in this run corroborates a response to
  this specific ICP** — flagged explicitly below as an uncrowded gap rather than a validated
  opportunity; carry the hedge into Phase 2 rather than treating this ICP as market-tested.

## Research constraints

(carried over/adapted from `config/scope.md`, corrected against what this run's two Phase 1 passes
actually did)

- **Source set that actually yielded, this run:** HN (Algolia comment/story search) — found, used for
  complaints Patterns 1–4. ProductHunt (GraphQL v2 API) — **found and used for the first time this
  scope**, per today's complaints file (`PRODUCTHUNT_API_TOKEN` was present in `.env` but not the
  shell environment; verified live with an authenticated `viewer { user { username } }` call before
  use). PH author handles are redacted by a content-based filter in this fetch environment — every PH
  quote below is cited by comment-permalink URL and date instead of a handle, consistent with the
  complaints findings file's own disclosed practice. Reddit — excluded-by-scope (unauthenticated
  scraping confirmed fingerprint-blocked elsewhere; OAuth terms unverified — a deliberate v1
  exclusion). GitHub public search API — found, unauthenticated, for the builds pass. Show HN via
  Algolia — found. Bluesky — **blocked, third consecutive confirmation**, though this run's evidence
  is an inferred `FingerprintBlock` (tier-1 escalation path reached, but the local Patchright/Chromium
  binary needed to capture a literal HTTP status is missing in this container) rather than a
  fresh-captured 403 like the prior two runs.
- **Independence heuristic and window actually applied (builds pass only):** 12-month window,
  2025-09-04 through 2026-09-04 (`build-pattern-scanner`'s own v1 default, one day later than the
  prior run's window) — distinct-author/no-fork/no-shared-upstream test, same as before.
- **Explicitly excluded from this candidate's evidence base:** two GitHub accounts confirmed as a
  topic-tag spam/farm cluster (`Pitangakeratocele862`, `Rhizomatous-rutherford327`); five
  template-repo self-descriptions ("use this template," not "I use this myself":
  `eborjaa/synapse`, `gokhanarkan/minimal-second-brain`, `lemoncloud-io/2nd-brain`,
  `quanb6311/second-brain-setup`, `lowqualityloey/loey_space`); the `topic:hackathon`-scoped
  Obsidian-vault cluster carried forward from the prior run's "shared brief, not independent
  convergence" exclusion. None of these are used as evidence anywhere in this candidate.
- **For downstream Phase 2 persona work:** compare language against Obsidian, Logseq, Roam Research,
  Evernote, Joplin, and Amplenote users specifically — the tools actually named across this run's
  findings — not a generic "note-taking app" comparison set.
- **Scope stays category-level**, per `scope.md`'s original framing: this is not "Obsidian, but
  better." See "Considered and not drafted" below for why a direct full-editor-replacement reading of
  the evidence was set aside again this run.

## Evidence

### Source coverage summary

Both Phase 1 passes for this run's timestamp exist and both contain real sourced content:
`findings/2026-09-04T043009Z-pkm-tools-complaints-findings.md` (5 patterns, 13 sourced quotes, now
genuinely **two sources** — HN and ProductHunt, the first run where PH actually contributed) and
`findings/2026-09-04T043009Z-pkm-tools-builds-findings.md` (4 patterns, 21 new independent instances
this run / 24 total citations, explicitly framed by its own file as an undercount-correction against
the prior run's narrower query set, not new build activity in one day). This candidate is therefore
**dual-source-evidenced overall**, with individual sub-claims flagged inline below where their actual
evidentiary weight is thinner than that headline suggests, per synthesizer rule 5.

### Continuity with the prior candidate (`candidates/2026-09-03T231104Z-pkm-tools-candidate.md`)

Read fresh against today's evidence, this candidate lands on **essentially the same core mechanism**
as yesterday's Recallect — a local-first retrieval/memory layer sitting on an existing markdown vault,
not a new editor. But the grounding for its central "trust" pillar has **shifted and is now better
evidenced**, and one design element from yesterday has been **dropped** for lack of support in today's
data:

- **Better-grounded than yesterday:** yesterday's Convergence 2 (chronic tool migration + a saturated
  "next Obsidian" build pattern) is reinforced here by Complaints Pattern 1's generalization of
  sync-loss from Obsidian-only (both prior runs) to four independent tools/backends this run — a
  materially stronger complaint-side case for why "yet another editor" doesn't fix the actual pain.
  Builds Pattern 2 (local-first/no-lock-in) also roughly doubled in cited instances (6 → 14), making
  Convergence A below meaningfully more solid than a single-source claim.
- **Dropped, not refuted:** yesterday's candidate included "no arbitrary plugin code execution against
  the vault" as a design constraint, grounded in a complaints pattern about third-party-plugin risk
  (a remote-access trojan via a plugin, a highly-rated plugin with concerning permissions). **Today's
  complaints findings file contains no such pattern at all** — its five patterns are sync-loss,
  retrieval, paywall/portability trust, parallel-tool-use, and unprompted stability complaints. This
  is not evidence the plugin-risk concern is wrong; it simply isn't in this run's evidence base, so it
  is removed here rather than carried forward on yesterday's authority. A future run that re-surfaces
  it should restore it.
- **New this run:** Complaints Pattern 1 (sync-loss) and Pattern 5 (unprompted stability complaints)
  are new pattern topics not referenced by yesterday's candidate. Both are folded in below as
  **complaint-only, thin-grounded** context — real and vivid, but without build-side corroboration in
  this run's evidence.

### Cross-source convergence (primary grounding)

**Convergence A — paywalled/withheld data portability is a trust problem, and a large, active build
cohort is responding with local-first, non-lock-in architecture** (Complaints Pattern 3 + Builds
Pattern 2)

> "Maybe this is just me getting old, but I feel like Evernote has only gotten worse since I started
> using it over 10 years ago... The icing on the cake is that they changed the welcome page of the app
> to no longer show the list of notes - and if you want to edit the page to get that list back, you
> have to sign up to their premium subscription! And I'm already paying too, just not for the right
> level of subscription apparently."
> — DangerousPie, comment on ["Ask HN: What Happened to
> Evernote?"](https://news.ycombinator.com/item?id=30975586), 10 Apr 2022 (Complaints Pattern 3)

> "I built Tenoa because I was paying for mymind and Recall and neither one would give me my saves as
> files."
> — Tenoa's maker (self-identified in the comment body; handle redacted in this fetch environment),
> comment on [Tenoa — Product Hunt
> launch](https://www.producthunt.com/products/tenoa?comment=5793459), 18 Aug 2026 (Complaints Pattern
> 3). **Cited for the complaint only** — the source quote continues into a description of Tenoa's own
> feature set and specific product pledges ("two commitments..."), which are deliberately not
> reproduced or adopted here; per the ethical boundary, this candidate does not hold up any one active
> builder's project as the thing to replicate.

> "Notvex – encrypted local-first notes with SQLCipher and XChaCha20"
> — GFrancV, [Show HN post](https://news.ycombinator.com/item?id=48946288) linking
> [GFrancV/notvex](https://github.com/GFrancV/notvex), created 2026-07-17 (Builds Pattern 2)

> "NoteCove – local-first notes&tasks, synced via your own cloud storage"
> — drewcsillag, [Show HN post](https://news.ycombinator.com/item?id=47425200) linking
> https://notecove.io/, created 2026-03-18 (Builds Pattern 2)

Fourteen independent builders (Builds Pattern 2: 6 from the prior run + 8 new this run, found only
because this run tried the query terms "local-first notes" and "markdown notes") are, right now,
building tools whose core pitch is "your notes stay in files you own, independent of any vendor's
sync/subscription." That is a direct architectural answer to Complaints Pattern 3's users — a
long-time paying customer pushed toward a higher tier for something that used to be free, and a
builder who was personally burned twice by tools that wouldn't hand back raw files. This is this run's
best-evidenced convergence: a real complaint pattern (2 independent instances, one of them a builder's
own testimony) matched against a large, independently-growing build response (14 instances) that
directly targets it.

**Note the boundary this convergence does NOT cross:** "local-first / files you own" addresses data
**portability and lock-in**, not sync **integrity**. None of the 14 Builds Pattern 2 instances mention
conflict resolution, versioning, or crash-safety — "synced via your own cloud storage" is an ownership
claim, not an integrity guarantee. See the next section for why sync-loss itself is treated separately.

**Convergence B — retrieval, not capture, is still the unsolved problem, but on two different user
populations** (Complaints Pattern 2 + Builds Pattern 3)

> "The new Evernote desktop app is terrible. It's slow as molasses (Electron?), search is not
> reliable, and without connectivity it doesn't work..."
> — panta, comment on ["How Notion pulled itself back from the brink of failure
> (2019)"](https://news.ycombinator.com/item?id=27540471), 17 Jun 2021 (Complaints Pattern 2)

> "I'm an Evernote refugee struggling with Joplin and Obsidian this morning... the latest Joplin
> includes OCR out of the box... But it hardly works at all on Obsidian... I'm about to settle for
> Joplin."
> — delichon, comment on ["Joplin is an open source note-taking
> app"](https://news.ycombinator.com/item?id=39581855), 3 Mar 2024 (Complaints Pattern 2)

> "Need better options for serach results. For example, I would like to be able to find all tasks
> with 'xyz' in the description, and not just the files that have 'xyz' in them somewhere. I need
> fine-grained search results, like those of Typora."
> — ProductHunt commenter (handle redacted), comment on [Amplenote — Product Hunt
> launch](https://www.producthunt.com/products/amplenote?comment=1035527), 7 May 2020 (Complaints
> Pattern 2)

> "I built an AI agent memory engine because Obsidian wasn't cutting it"
> — perseusai, [Show HN post](https://news.ycombinator.com/item?id=48915435) linking
> https://perseus.observer/blog/built-perseus-vault-obsidian-wasnt-cutting-it/, created 2026-07-15
> (Builds Pattern 3) — the clearest first-person articulation across either run of the exact
> complaint-to-build pipeline this project traces.

> "Persistent memory for Claude Code — the full plugin: hooks, skill, MCP server, workflows. A
> Markdown vault you own, no server and no account."
> — SirCharan, [SirCharan/second-brain](https://github.com/SirCharan/second-brain), created
> 2026-07-21 (Builds Pattern 3)

Builds Pattern 3 grew sharply this run — 6 prior instances (Obsidian-specific) plus 10 new (broadened
to "any markdown/notes vault as agent-memory substrate") — and Complaints Pattern 2 independently
shows retrieval failing across the category, from a declining incumbent (Evernote) to a modern,
well-regarded alternative (Obsidian's OCR gap). **Hedge that must survive, carried forward unchanged
from yesterday's candidate: this is mechanism-convergence, not user-population convergence.** Every
Complaints Pattern 2 quote is a human failing to find their own notes. Nearly every Builds Pattern 3
instance is explicitly agent-facing — "for coding agents" (`poorants/engram`), "Persistent memory for
Claude Code" (`SirCharan/second-brain`), "AI agent memory engine" (`perseusai`), "gives AI agents
access" (pattern description itself). Only one or two instances this run
(`Desouki27/thought-loom` — "links, clusters, and answers questions across your notes," phrased
without an explicit agent frame) read as possibly human-facing. Treat "the same underlying retrieval
gap serves both a human owner and their coding agent" as this candidate's own inference, not as
something the two source sets independently confirmed.

### Complaint-only pattern, no build-side corroboration this run (flagged, not folded into a
convergence)

**Complaints Pattern 1 — cross-vendor sync/versioning data loss (4 independent users, 4 backends) —
and Pattern 5 — unprompted stability complaints from satisfied local-first fans.** These generalize a
finding both prior runs saw only on Obsidian to Roam Research (via PH), Logseq (twice, via HN), and
Apple Notes/iCloud (via HN) — real, catastrophic, unrecovered data loss, "and if I don't trust the
tool, I'm not going to use it."

> "Search for 'Logseq data loss $current_year' and you'll see horror stories of people who did
> everything exactly right and still lost a bunch of data from it... Logseq is so very close to being
> exactly what I want, but there are way too many tales like that for my comfort... I've conceded that
> I don't trust the tool. And if I don't trust the tool, I'm not going to use it."
> — kstrauser, comment on ["Why I Like Obsidian"](https://news.ycombinator.com/item?id=39027154), 17
> Jan 2024 (Complaints Pattern 1)

> "Be careful keeping important things in Apple Notes and not backing it up elsewhere... All of my
> notes disappeared everywhere... And no warning in the UI that disabling the iCloud sync would delete
> any data."
> — ashdksnndck, comment on ["Alto turns Apple Notes into a
> website"](https://news.ycombinator.com/item?id=44678112), 25 Jul 2025 (Complaints Pattern 1)

> "Just returned to supporting LogSeq financially as well... only issue is stability, Obsidian
> compatibility would be nice..."
> — ProductHunt commenter (handle redacted), comment on [Logseq — Product Hunt
> launch](https://www.producthunt.com/products/logseq?comment=1490654), 7 Sep 2021 (Complaints Pattern
> 5)

**Why this is NOT folded into Convergence A above:** local-first/no-lock-in (Builds Pattern 2)
addresses *who controls the files*, not *whether sync silently corrupts or drops them* — Logseq itself
is local-first and markdown-backed, and it is a named victim of Pattern 1's data-loss complaints and
Pattern 5's unprompted stability complaints from its own fans. None of Builds Pattern 2's 14 instances
describe conflict resolution, crash-safety, or tested sync integrity as a feature. **This is a real,
vivid, cross-vendor complaint pattern with zero visible build-side response in this run's evidence —
an uncrowded gap, not a validated one.** It is carried into the thin-grounding ICP above rather than
into either convergence, and should not be presented to Phase 2 as market-tested just because the
underlying pain is well-evidenced.

### Single-source or adjacent patterns folded in as context, not features

**Complaints Pattern 4 (parallel paid-tool sprawl) — thin, single-source, not incorporated as a
feature driver.**

> "I have used dozens of software the last 30 years for my small one man business... I have 5 on the
> go now, Evernote, notion, supernotes, obsidian, etc,. I also end up having the same problem. Too
> much mucking around, I would end up too disorganized lost, too much."
> — ProductHunt commenter (handle redacted), comment on [Amplenote — Product Hunt
> launch](https://www.producthunt.com/products/amplenote?comment=1268148), 11 Feb 2021 (Complaints
> Pattern 4)

No build pattern in this run addresses cross-tool consolidation or federation across heterogeneous
PKM apps (Evernote + Notion + Supernotes + Obsidian at once); Builds Patterns 1–4 are all
single-vault-substrate stories. This pattern is real but adjacent — it argues against adding *another*
tool to the pile, which is a reason for this candidate to stay a layer on an existing vault rather than
a new destination app, but it is not evidence for any specific feature.

**Builds Pattern 1 (Obsidian alternatives, 8 instances, unchanged this run) — referenced in aggregate
only.** Today's builds findings file does not re-quote this pattern's instances (it stands from the
prior run's file), so no quotes are reproduced here; see "Considered and not drafted" below for how it
factors into this candidate's reasoning.

**Builds Pattern 4 (Zettelkasten-methodology rebuilds, 7 instances total, 3 new this run) — not
incorporated.** This pattern targets a specific predecessor's interaction model (index-card linking)
rather than retrieval-layer or portability concerns, and has no corresponding complaints-side pattern
in this run's evidence. Noted for completeness, not used as grounding for this candidate.

### Considered and not drafted: a full-replacement "next Obsidian" editor

Builds Pattern 1 (8 instances, unchanged) plus Complaints Pattern 1 (now cross-vendor, 4 instances)
could support a different reading: "build a more reliable, local-first Obsidian replacement." That
reading is stronger this run than last, since Pattern 1's data-loss evidence is no longer
Obsidian-specific — a new editor doesn't obviously fix a failure mode that recurs across editors and
sync backends. This candidate again deliberately does not draft that as the concept: the space is
already populated (8 independent reimplementations, a catalog directory referenced in the builds
findings), and Complaints Pattern 1/5 both show that even a local-first, markdown-based incumbent
(Logseq) hasn't solved the trust problem architecture alone — suggesting the gap is in execution/
reliability engineering more than in "yet another editor's" feature list. A reader weighing the same
evidence could reasonably disagree and treat a better-executed editor as the higher-volume bet; that
disagreement is left visible here rather than resolved silently.

### Thin-grounding flags (consolidated)

- **Convergence B (retrieval)** is mechanism-convergence with an unconfirmed user-population overlap:
  complaints are human-retrieval-facing; nearly all corroborating Builds Pattern 3 instances are
  agent-facing. "This also serves human retrieval" is this candidate's inference, not confirmed by
  either source set independently.
- **The sync-data-loss ICP (Complaints Pattern 1 + 5) is complaint-only** — no build-side pattern in
  this run corroborates a response to it. Treat as an uncrowded-gap hypothesis for Phase 2, not a
  validated opportunity.
- **The Tenoa quote (Convergence A) is maker-authored first-person testimony**, disclosed as such in
  the source findings file and again here; it is cited for the complaint it documents (two other paid
  tools wouldn't return raw files) and the general architecture it illustrates, never as "the project
  to build like" — Tenoa's own specific product pledges are deliberately not reproduced as this
  candidate's design spec, per the ethical boundary re-applied at this synthesis stage.
- **Complaints Pattern 4 (parallel-tool sprawl)** is single-source with no build-side echo — folded in
  as a reason to stay a layer rather than a new app, not as a feature driver.
- **Dropped from yesterday's candidate:** "no third-party plugin code execution against the vault" is
  not carried forward — today's complaints findings file contains no pattern about plugin/trust risk.
  This is an absence-of-evidence-this-run flag, not a refutation; see "Continuity with the prior
  candidate" above.
- **The "full-replacement editor" alternative** was set aside by inference (population saturation +
  Pattern 1/5 showing architecture-alone hasn't solved trust), not by evidence that path fails —
  flagged as a judgment call a future session could reasonably overturn.
