# Candidate — Recallect

## Concept candidate

- **Name:** Recallect
- **Live URL:** Not yet built — feeds agentic-grounded-persona-eval Phases 1-2 only, not Phase 3
- **Slug:** recallect
- **Description:** A local-first search-and-memory layer that sits on top of a note-owner's
  *existing* plain-markdown / Obsidian-format vault — not a new editor, not a vault migration.
  It gives both the human owner and their coding/AI agents fast, trustworthy retrieval over notes
  they already have, without third-party plugin code executing against that vault, and without
  requiring the owner to switch note-taking tools. (Kept intentionally aggregate — see the ethical-
  boundary note under Evidence for why no specific feature list is given here.)

## Assumed ICPs

(derived from evidence, not invented)

- **PKM power users who have already cycled through 2+ tools without settling** — Obsidian, Logseq,
  Roam, Notion, Joplin, Org-mode — and for whom "will this still be worth using in two years" is a
  live, previously-broken expectation, not a hypothetical objection. Grounded in Complaints Pattern 5.
- **Existing Obsidian/plain-markdown vault owners who can capture notes easily but can't find them
  again**, and separately, whose accumulated notes don't convert into thinking or action. Grounded in
  Complaints Patterns 2 and 3.
- **Developers using coding agents (Claude Code, Cursor, Codex, Gemini CLI, OpenCode) who want their
  existing notes vault to serve as durable agent memory**, rather than re-explaining context every
  session. Grounded in Builds Pattern 3 — **note this ICP is agent-facing in the evidence itself**,
  not the same population as the human-retrieval complaint above; see the Convergence 1 hedge below.
- **Users wary of third-party plugin code running against their personal vault data.** Grounded in
  Complaints Pattern 4 only — no build-side evidence in this run corroborates this specific concern
  (see the trust-conflation flag below).

## Research constraints

(carried over/adapted from `config/scope.md`, corrected against what the two Phase 1 runs actually
did — `scope.md`'s "Independence heuristic override: Not applicable" line predates `build-pattern-
scanner` and was superseded, per the builds findings file's own note)

- **Source set that actually yielded, this run:** HN (Algolia comment/story search) for complaints;
  HN Show HN + GitHub public search API for builds. ProductHunt blocked (no `PRODUCTHUNT_API_TOKEN`
  configured). Reddit excluded-by-scope (unauthenticated scraping confirmed fingerprint-blocked
  elsewhere; OAuth terms unverified — a deliberate v1 exclusion, not a gap). Bluesky confirmed
  blocked as of 2026-09-03 (HTTP 403 FingerprintBlock at the fetch-tooling layer).
- **Independence heuristic actually applied (builds pass only):** 12-month window, 2025-09-03 to
  2026-09-03 — `build-pattern-scanner`'s own v1 default, not a `scope.md`-specified constraint. All
  "N independent instances" counts cited below inherit this window and the heuristic's distinct-
  author/no-fork/no-shared-upstream test.
- **Explicitly excluded from this candidate's evidence base:** the five `topic:hackathon+obsidian`
  repos built under one hackathon's mandated Obsidian-vault checklist — the builds findings file
  flags these as compliance with a shared brief, not independent convergence, and does not count them
  as a pattern. Not used here either.
- **For downstream Phase 2 persona work:** compare language against Obsidian, Notion, Logseq, Roam
  Research, and Joplin users specifically — the tools actually named across both findings files —
  not a generic "note-taking app" comparison set.
- **Scope stays category-level**, per `scope.md`'s original framing: this is not "Obsidian, but
  better," it is a response to a cross-vendor pattern (see Considered-and-not-drafted section below
  for why a direct Obsidian-replacement reading of the evidence was set aside).

## Evidence

### Source coverage summary

Both Phase 1 passes exist and both contain real sourced content: `findings/2026-09-
03T231104Z-pkm-tools-complaints-findings.md` (5 patterns, 14 sourced HN quotes) and `findings/2026-
09-03T231104Z-pkm-tools-builds-findings.md` (4 patterns, 24 independent build instances). This
candidate is therefore **dual-source-evidenced overall** — but individual sub-claims within it carry
different evidentiary weight, flagged inline below per the synthesizer spec's rule 5. Nothing below
is invented; every claim traces to a specific pattern and, where quoted, a specific HN comment/story
or GitHub/Show HN repo listing already cited in one of the two findings files.

### Cross-source convergence (primary grounding)

**Convergence 1 — retrieval, not capture, is the unsolved problem** (Complaints Pattern 2 + Builds
Pattern 3)

> "Been using Notion for over 2 years now, and unfortunately I must say I am not going to be using
> them moving forward. You end up with a soup of documents each with their own structure, search is
> terrible, creating is easy sure - but finding the right content sucks."
> — sergiotapia, comment on ["Notion Acquires
> Automate.io"](https://news.ycombinator.com/item?id=28459099), 8 Sep 2021 (Complaints Pattern 2)

> "Standalone MCP server that gives AI agents access to your Obsidian vault — hybrid search, memory,
> tasks, files, OAuth 2.1."
> — aliasunder, [aliasunder/vault-cortex](https://github.com/aliasunder/vault-cortex), created
> 2026-05-01 (Builds Pattern 3, one of six independent instances)

Six independent builders (Builds Pattern 3) are, right now, building semantic-search/agent-memory
tools that graft onto an *existing* Obsidian vault rather than replacing it — directly on point for
the "capture is easy, finding is the unsolved problem" complaint (Complaints Pattern 2, two
independent users) and the related "hoarded notes don't get reused" complaint (Complaints Pattern 3,
three independent users). **Hedge that must survive: this is mechanism-convergence, not user-
convergence.** The complaints are humans failing to find their own notes; all six Builds Pattern 3
instances are explicitly agent-facing ("for Claude Code," "for AI agents," "gives AI agents access").
No quote in either findings file shows a builder framing this as a human-retrieval tool first. Treat
the read that "the same underlying gap serves both audiences" as this candidate's own inference, not
as evidence the two source sets independently confirmed the same user need.

**Convergence 2 — chronic tool migration alongside a saturated "build the next Obsidian" build
pattern** (Complaints Pattern 5 + Builds Pattern 1)

> "Switched to Obsidian for faster startup time, which is at the top of my feature list for such
> apps. Joplin got worse over time with more notes. I considered Roam and Notion, but having to pay
> AND slow startup made no sense, although Notion features are quite nice. Now thinking about adding
> Logseq to work with my Obsidian."
> — repple, comment on ["Obsidian 1.5 Desktop
> (Public)"](https://news.ycombinator.com/item?id=38775725), 26 Dec 2023 (Complaints Pattern 5)

Complaints Pattern 5 (three independent users) shows chronic, unresolved tool-switching — including
one user ("flkiwi") who gave up on switching entirely and wrote a personal replacement. Builds
Pattern 1 independently shows eight distinct builders, in one year, positioning a new tool explicitly
against Obsidian ("Obsidian alternative" appears in the self-description of all eight). Read
together: a wide, unconnected population keeps building — and a separate population of users keeps
switching — without the market converging on a tool that sticks. See "Considered and not drafted"
below for how this candidate resolves that tension rather than adding a ninth alternative.

### Single-source or adjacent patterns folded in as context, not features

**Complaints Pattern 1 (cross-vendor slowness) — thin build-side match, flagged.** Complaints
Pattern 1 has three independent users describing felt slowness across Roam, Logseq, and "the whole
category." The build-side echo is thin: one Builds Pattern 1 instance uses "blazingly fast" in its
self-description (`remcostoeten/skriuw`), and the repple quote above cites startup speed as a
switching trigger. That is not a second independent build-side *pattern* about speed — it is one
description plus one complaints-side quote already counted under Convergence 2. Speed is carried into
this candidate as a bar to clear ("must not be slow"), not as a differentiator with cross-source
grounding of its own.

**Complaints Pattern 3 (note-hoarding doesn't convert to thinking/action) — used as context, not as a
feature source.** No build-side pattern in this run addresses converting captured notes into action;
Builds Pattern 3's agent-memory bolt-ons are about giving coding agents context, not about prompting
a human to act on old notes. Pattern 3's own quotes argue against a "resurface your old notes"
feature specifically — chermi's quote states that *checking whether he'd already had a thought* was
itself the problem ("I spend too much time checking if I already had a thought rather than just
thinking"), and nicbou's and Barrin92's quotes both frame organizing/reviewing notes as a
procrastination substitute for action. A resurfacing or reminder feature built on this pattern could
reproduce the exact complaint rather than solve it. This candidate therefore takes Pattern 3 as
justification for *not* investing in capture-side or review-prompting features — not as license to
invent an action-conversion feature no builder has attempted.

**Flagged separately per claim-verification discipline:** the Ask HN thread quoted for Complaints
Pattern 3 (nicbou, Barrin92) was itself posted by a founder soft-validating a product idea in this
space, not a neutral prompt — the quotes are other users' organic first-person answers, not
manufactured, but that origin is disclosed here per the complaints findings file's own caveat, so the
hedge doesn't decay at this second remove.

**Complaints Pattern 4 vs. Builds Pattern 2 — two distinct trust concerns, not one converged
pattern.** These initially look like a convergence (both are about "trust") but are not the same gap.
Complaints Pattern 4 (three independent users) is about **third-party-code risk**: an Obsidian plugin
used to deploy a remote-access trojan, a highly-rated plugin with concerning permission disclosures,
plugins silently breaking each other. Builds Pattern 2 (six independent instances) is about **data-
residency trust**: no cloud, no telemetry, self-hosted, end-to-end encrypted. Zero Builds Pattern 2
quotes mention plugin vetting, code review, or permission scoping. **This candidate's "no arbitrary
plugin code execution against the vault" design constraint is therefore single-source — grounded only
in Complaints Pattern 4, with no independent build-side corroboration in this run.** It is included
because a candidate positioning itself around vault access needs an answer to "how do you vet what
runs against my data" (per Complaints Pattern 4's own framing), not because builders are independently
converging on that specific answer.

### Considered and not drafted: a full-replacement "next Obsidian" editor

The single largest raw cross-source volume in this run is actually Builds Pattern 1 (8 instances) +
Complaints Patterns 1 and 5 combined — a plain reading says "build a fast, local-first Obsidian
replacement." That reading is not wrong on the evidence; it is the option with the most independent
instances behind it. This candidate deliberately does **not** draft that as the concept, for a
strategic reason that is this document's own inference, not itself evidence: Builds Pattern 1's own
"what this means" note observes that eight independent reimplementations already exist and that a
directory (`awesome-obsidian-alternatives`) exists to catalog them — the space is populated, not
empty — while Complaints Pattern 5 shows that switching to yet another tool has not historically
resolved this ICP's dissatisfaction (three users switched tools for exactly the triggers a ninth
alternative would target, and remained unsatisfied). A reader weighing the same evidence could
reasonably conclude the opposite — that a better-executed ninth alternative is still the higher-
volume bet — and that disagreement should be visible to whoever picks this candidate up next, not
resolved silently in this document's favor.

### Thin-grounding flags (consolidated)

- Convergence 1 (the candidate's core mechanism) is **mechanism-convergence with a user-population
  divergence**: complaints are human-retrieval-facing, all six corroborating build instances are
  agent-facing. Treat "this also serves human retrieval" as this candidate's inference.
- The "no arbitrary plugin execution" design constraint is **single-source** (Complaints Pattern 4
  only); Builds Pattern 2's local-first/no-cloud pattern is a related but distinct trust concern, not
  corroboration of the plugin-vetting concern specifically.
- Speed/performance as a differentiator is **thin on the build side** — one descriptive phrase in one
  Builds Pattern 1 instance, not an independent build-side pattern of its own.
- The "full-replacement editor" alternative was set aside by inference (saturation + unresolved churn
  reading), not by evidence showing that path fails — flagged above as a judgment call a future
  session could reasonably overturn.
