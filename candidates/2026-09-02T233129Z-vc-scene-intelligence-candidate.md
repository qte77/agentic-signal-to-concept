# Candidate — Scene-Intelligence Layer (working name, not a product name)

## Concept candidate
- **Name:** Scene-Intelligence Layer — a "who's genuinely active in this space right now, and why"
  view for allocators (VC/FO/corp-dev), layered on an already-live, continuously-refreshed
  multi-city events/jobs graph, rather than a standalone ecosystem map or deal-CRM built from
  scratch.
- **Live URL:** Not yet built — feeds `agentic-grounded-persona-eval` Phases 1-2 only, not Phase 3.
- **Slug:** `vc-scene-intelligence`

## Assumed ICPs
(derived from evidence below, not invented)

- **Primary evidence gap, explicitly unresolved by this run**: the run's actual target
  ICP — VCs, family offices, corp-dev — **did not directly surface as complainants** in this run's
  sources (see Evidence, "what this run could NOT confirm" below). Treat any ICP framing below as
  *inferred from adjacent and build-side evidence*, not as directly-sourced VC/FO/corp-dev pain.
- **Founders/job-seekers** (sfclarity's existing free-tier ICP) are the ICP with **directly sourced**
  HN complaint evidence about scene-signal noise (adjacent-ICP echo, see Evidence) — worth
  considering whether this concept's real near-term ICP is closer to sfclarity's *existing* free
  tier than to the capital-side bet this run set out to test.
- **Builders of the 20 independent instances found** (Evidence, Patterns A/B) are themselves
  evidence of a *third* ICP: individual technologists building tools in this space for their own or
  a small audience's use — several explicitly naming VCs as their tool's *user* (not necessarily its
  *payer*): the Peru ecosystem map lists "VC funds" as a mapped category; the Founders-to-VCs
  matcher states "hundreds of VCs...use the tool to receive deal flow." This is indirect evidence
  that VCs will *engage* with tooling in this space when it's offered to them, which is weaker than,
  but not nothing against, evidence that they're asking for it unprompted.

## Research constraints
(carried over from `config/scope.md`)

- Problem-space: VCs/FOs/corp-dev tracking a local tech/startup scene (who's building, hiring,
  raising; genuinely emerging vs. hype) to source deals, scout, or decide where to spend time.
  Framed against `sfclarity/docs/plans/0014-roadmap-gtm.md`'s own explicitly-unvalidated "Paying
  (capital)" ICP hypothesis — this run exists to find evidence for or against that bet, not to
  justify it.
- Date window: last 12 months (default, unmodified).
- Sources: v1 defaults (HN + ProductHunt for complaints; GitHub + Show HN + Bluesky for builds).
  ProductHunt blocked (token unset); Bluesky not independently re-verified this run (inherited a
  1-day-old confirmed-blocked finding from the tool's own spec — see builds-findings.md).

## Evidence

**Source coverage summary**: cross-source. Both Phase 1 passes produced real findings.
- Complaints: `findings/2026-09-02T233129Z-vc-scene-intelligence-complaints-findings.md` — 2
  patterns (below spec's 4-8 target, honestly reported as such by that pass), plus a separate
  adjacent-ICP-echo section and a cross-reference-for-builds section.
- Builds: `findings/2026-09-02T233129Z-vc-scene-intelligence-builds-findings.md` — 2 patterns, 23
  independent instances total (10 GitHub repos + 3 HN launches in Pattern A; 10 GitHub repos in
  Pattern B), plus 2 further independent Show HN launches logged separately.

**The load-bearing cross-source convergence** (per this spec's own weighting rule — convergence
above single-source):

1. Complaints Pattern 1 (one founder manually assembling "who's active in my space" from
   Crunchbase + Tracxn + spreadsheets, because no tool answers it directly —
   [hgaddipa001](https://news.ycombinator.com/item?id=44112008)) **converges directly** with Builds
   Pattern B (10 independent builders across 8+ geographies building regional/city
   startup-ecosystem-mapping tools, e.g.
   [Amarendar112/hyderabad-startups-map](https://github.com/Amarendar112/hyderabad-startups-map),
   [RikepilB/peru-tech-map](https://github.com/RikepilB/peru-tech-map)). Same gap, named
   independently from both the complaint side and the build side. This is the strongest single
   finding this run produced.
2. Builds Pattern A's cross-referenced Show HN launches echo the SAME specific incumbent complaint
   in their own marketing copy — "[Show HN:
   VCBacked](https://news.ycombinator.com/item?id=47226533)": *"Crunchbase has data but no
   contacts... PitchBook and CB Insights are $5–25K/year and overkill"* — independently naming the
   identical incumbents (Crunchbase, Tracxn-adjacent tools) as inadequate that Complaints Pattern 1
   named. Self-serving marketing copy, not third-party complaint evidence, so weighted lower than
   (1), but real corroboration of the same specific gap from a third independent source.
3. **A real, unresolved tension, not a convergence**: Complaints Pattern 2 (deal-sourcing outreach
   is itself experienced as noise by founders receiving it —
   [gyanchawdhary](https://news.ycombinator.com/item?id=46704853)) sits in direct tension with
   Builds Pattern A (tools built to help the *sourcing* side reach founders faster/more). A
   scene-intelligence product that only serves the allocator's sourcing need risks reproducing the
   exact noise problem founders already complain about — worth checking explicitly against
   `0014-roadmap-gtm.md`'s own "forward-to-source only" invariant before treating Pattern A's
   existence as pure validation.

**What this run could NOT confirm** (stated plainly, per this spec's own hedge-preservation rule —
this is the single most important caveat and it must survive into any downstream use of this
candidate): **no direct evidence was found of VCs, family offices, or corp-dev people themselves
publicly complaining about this problem.** `complaint-miner`'s own conclusion: HN's complaint signal
for this specific ICP is thin because that ICP largely doesn't post complaints on HN at all — the
adjacent "signal vs. noise in a scene" pain that IS visible on HN comes from founders/job-seekers,
not allocators. ProductHunt (blocked, token unset) is flagged as the most likely source to actually
carry allocator-side complaint signal, and was not reached this run. **This run does not validate
the paying-capital-product bet from `0014-roadmap-gtm.md`; it validates that a real, adjacent
"scene visibility" gap exists and that many independent small builders are already attacking pieces
of it — the specific question of whether VCs/FOs/corp-dev will pay for it remains open.**

**Where sfclarity may differ from all 20+ found independent instances** (an observation, not a
recommendation to build — read every instance's own description again before treating this as
settled): none of the found GitHub repos or HN launches describe being built on top of an
already-live, continuously-refreshed, multi-city events/jobs feed — most read as standalone,
largely static or manually-curated maps/CRMs. sfclarity's own already-shipped free-tier
infrastructure (a live, daily-refreshed events feed already spanning SF with a multi-city roadmap)
is a resource none of the 20+ found instances appear to have. Whether that's a real, defensible
differentiator or just an artifact of what a GitHub/HN search can see is **not established by this
run** — it would need its own verification, not an inference from absence-of-mention in a repo
description.

**Thin-grounding flags carried forward** (from source findings, restated here so they don't
disappear): Complaints Pattern 1 is single-source, secondhand, and a success-story write-up (not a
raw complaint) — directional only. Complaints Pattern 2's founder account is single-source. Both
build patterns rest on real fetched evidence (23 independent, non-fork instances, author/date
verified) but low engagement was observed on directly-relevant Show HN posts (1-2 points each) —
noted as an unfavorable data point on reach, not omitted.
