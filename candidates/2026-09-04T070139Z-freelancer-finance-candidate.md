# Candidate — Freehold Finance

Two candidates are drafted in this file because the evidence clearly separates into two distinct
product concepts, at two different confidence levels. **Candidate 1 (Freehold Finance) is
cross-source evidenced** — a complaints pattern and an independent-builds pattern converge on the
same gap, per this spec's strongest-signal rule. **Candidate 2 (Upfront Terms) is single-source
evidenced and explicitly thin-grounded** — see its own Evidence section.

---

## Concept candidate

- **Name:** Freehold Finance
- **Live URL:** Not yet built — feeds agentic-grounded-persona-eval Phases 1-2 only, not Phase 3
- **Slug:** freehold-finance

A privacy-first, no-bank-sync, data-portable invoicing and expense tracker for freelancers and
small businesses, positioned explicitly against two related risks the evidence shows freelancers
already naming out loud: (1) handing financial records to a third-party server they don't control,
and (2) being at the mercy of a vendor's pricing after an acquisition or ownership change. "Freehold"
signals owning your data and your tool outright rather than renting both from a SaaS vendor who can
change the terms later.

Core mechanic implied by the evidence: manual/local-first entry with no mandatory bank-account
linking (the recurring "no bank sync" design choice independently reached by multiple unrelated
builders), plus a first-class, no-lock-in data export/self-host path (the specific behavior several
Harvest refugees describe reaching for on their own).

## Assumed ICPs

(derived from evidence, not invented)

- **Privacy-conscious solo freelancers and self-employed people unwilling to link a bank account or
  hand financial records to a third-party server.** Directly stated in complaints Pattern 5 —
  devoid31 self-hosting Invoice Ninja after reading a privacy policy; skwee357 explicitly looking
  for "privacy focused (i.e. NON web, no sync) accounting software"; amritghimire wanting something
  that "works offline... doesn't rely on external servers."
- **Long-tenured freelancers/small agencies on legacy pricing plans of a SaaS tool that was acquired
  by a roll-up and repriced, currently or recently switching away.** Directly stated in complaints
  Pattern 4 — molf ("actively moving away from Harvest"), agsqwe (13-year ~$100/mo customer facing a
  22x list-price jump, closed the account), SyneRyder (downloaded their data and rebuilt their own
  tool rather than pay). This is the single strongest complaint pattern found this run (100+
  comments on one incident) and it names data export and independence from vendor pricing decisions
  as the thing these customers reached for, not just "a cheaper competitor."
- **Adjacent, not scoped 1:1 — general privacy-focused personal-finance users who are not
  freelancer/business-scoped but independently converge on the same "no bank sync" design choice**
  (Whisper Money, usequantive, per builds Pattern 2). Listed because it corroborates the design
  principle has demand beyond this scope's narrow ICP, not because these are the target customer.

## Research constraints

(carried over/adapted from `config/scope.md`)

- Source set for further validation, unchanged from this run: HN + ProductHunt for complaints (this
  run's ProductHunt pass was **blocked on a missing worktree credential, not absence of signal** —
  see complaints findings §Source coverage; close this gap before treating PH as validated-absent),
  GitHub + Show HN for builds (Bluesky attempted, confirmed blocked both runs). Reddit and app-store
  remain excluded per `docs/plans/0001-concept.md`.
- No jurisdiction/geography restriction is imposed by `scope.md`. Complaints Pattern 6 (tax/fee
  fragmentation) argues a product can't credibly promise general multi-jurisdiction compliance — this
  candidate sidesteps that by not competing on tax compliance at all; it competes on data ownership
  and pricing durability, which are jurisdiction-agnostic.
- Competitor set worth comparing language against: Harvest (post-Bending-Spoons-acquisition pricing,
  the specific named cautionary example), FreshBooks and Invoice Ninja (named directly by
  complainants — Invoice Ninja specifically as the self-hosted escape valve one commenter already
  uses), and spreadsheets/Excel/Google Docs (the default DIY baseline nearly every Show HN launch
  thread in the complaints findings gets compared against).
- Explicitly avoid two saturated positionings the evidence rules out: (1) "yet another all-in-one
  business-OS for freelancers" — builds Pattern 1 found 10+ independent, unrelated teams already
  building exactly this shape in the last 12 months, and complaints Pattern 3 shows freelancers
  actively bouncing off all-in-one bloat; (2) "yet another automated payment-reminder emailer" —
  complaints Pattern 1 names this shape as already well-served (e.g. Uaryn) and argues the sharper
  gap is elsewhere.

## Evidence

**Source coverage summary**: Both Phase 1 findings files exist for this scope
(`findings/2026-09-04T070139Z-freelancer-finance-complaints-findings.md`, HN-only — ProductHunt
blocked, 6 patterns, 25 sourced quotes; `findings/2026-09-04T070139Z-freelancer-finance-builds-findings.md`,
GitHub + Show HN, 4 patterns, 28 cited independent instances). This candidate is **cross-source
evidenced**: a complaints pattern (Pattern 5) and an independent-builds pattern (Pattern 2) converge
on the same gap — the strongest signal this method produces, per this spec's own weighting rule —
and is reinforced by the strongest single complaint pattern found this run (Pattern 4, single-source
but very high volume/acuteness).

**Cross-source convergence — privacy / no-bank-sync / data ownership:**

> there isnt any way i would submit anything to this company after reading their privacy policy...
> every single thing that has to do with my freelance business AND my invoices is pretty personal. no
> thanks. ill self host invoice ninja. easy peasy.
>
> — devoid31, complaints Pattern 5. [https://news.ycombinator.com/item?id=40228179](https://news.ycombinator.com/item?id=40228179)

> I'm looking for a privacy focused (i.e. NON web, no sync) accounting software for personal use...
>
> — skwee357, complaints Pattern 5. [https://news.ycombinator.com/item?id=41044973](https://news.ycombinator.com/item?id=41044973)

> Show HN: DrakeAI – expense tracker you log by voice or text, no bank sync
>
> — a_protsyuk, builds Pattern 2. [https://news.ycombinator.com/item?id=49187481](https://news.ycombinator.com/item?id=49187481)

The complaints-side pattern (people explicitly rejecting bank-linked/cloud-synced tools on privacy
grounds) and the builds-side pattern (multiple independent, unrelated builders — DrakeAI and
Simple-trackr freelancer/self-employed-scoped; Chargenda, Whisper Money, and usequantive adjacent but
not freelancer-scoped, per builds findings' own caveat — each independently choosing "no bank sync"
as a stated design principle) point at the same gap from two different signal types. This candidate
does not treat any one of these builds as a template to replicate — they are cited only as aggregate
corroboration that the design principle recurs independently, consistent with the ethical boundary
`build-pattern-scanner.md` applied and which this synthesis re-applies rather than undoes.

**Reinforcing, single-source pattern — pricing shock / vendor lock-in from a PE roll-up acquisition:**

> Similar story here. They took my ~$100/yr Harvest time-tracking Solo plan, increased the price by
> 2.5x for a more restricted plan than I had... So I downloaded my data, and had Claude vibecode a
> fully-featured clone in a single evening.
>
> — SyneRyder, complaints Pattern 4. [https://news.ycombinator.com/item?id=48849810](https://news.ycombinator.com/item?id=48849810)

> But it's not enterprise SaaS, is it? It's just a time tracking and invoicing tool... Source: am
> actively moving away from Harvest due to this ridiculous price hike.
>
> — molf, complaints Pattern 4. [https://news.ycombinator.com/item?id=49375595](https://news.ycombinator.com/item?id=49375595)

This is the single highest-volume, most-acute pattern found across either findings file (100+
comments on one incident), but it has **no corresponding builds-side pattern** — no independent
product in the builds findings was found positioned specifically against acquisition-driven
repricing, only individual freelancers' one-off, unshipped, personal LLM-vibecoded clones (SyneRyder
above; agsqwe similarly). It is folded into this candidate as reinforcing evidence for the
"ownership/portability" positioning rather than presented as its own cross-source-evidenced pattern,
since thematically it is the same underlying complaint (control over your own financial data and
tool, not being at a vendor's mercy) as Pattern 5. **Flag**: the counter-signal in this same evidence
(freelancers solving it themselves with an LLM clone in an evening) should be weighed against a
commercial product's viability — several of the exact people most acutely affected already
self-served rather than needing a new vendor.

**Saturation warning used to shape negative-space positioning:** builds Pattern 1 (10 cited
independent instances, all-in-one invoicing/business-OS apps) cross-references complaints Patterns 2
and 3 (spreadsheet-is-good-enough objection; fragmentation/bloat complaints) as a warning against,
not a validation for, an all-in-one shape — see Research constraints above.

**Thin-grounding note applying to this candidate specifically:** the ProductHunt leg of complaints
coverage did not run this pass (blocked on a missing worktree credential — see complaints findings
§Source coverage). This candidate's evidence base is HN-only on the complaints side; it should be
treated as provisional until a PH pass exists to check whether the privacy/ownership pattern holds up
outside HN's specific audience.

---

## Concept candidate

- **Name:** Upfront Terms
- **Live URL:** Not yet built — feeds agentic-grounded-persona-eval Phases 1-2 only, not Phase 3
- **Slug:** upfront-terms

A tool that makes deposit/upfront-payment terms a structural part of sending a quote or invoice
(e.g., an invoice literally cannot be marked "sent" or work "started" without a deposit rule attached,
with the deposit collection built into the send flow) rather than a manual habit the freelancer has
to enforce themselves. This targets the "chasing late payment" pain named repeatedly in the
complaints findings, but takes the "enforce better terms" angle the complaints thread itself surfaced
as freelancers' actual workaround, rather than the "send nicer reminder emails" angle the complaints
findings flag as already saturated.

**Grounding note (must be read before acting on this candidate): this candidate is
single-source-evidenced and thin.** It is traceable to real quotes, but:

1. It has **no corresponding pattern in the builds findings** — no independent build in either
   findings file was found targeting deposit/upfront-payment enforcement specifically. The four
   builds patterns found (all-in-one suites, expense/income dashboards, time-tracking-to-invoice,
   narrow point-tools) do not include this shape.
2. The complaint-miner's own analysis of Pattern 1 explicitly argues the existing fixes in the source
   thread are "mostly behavioral/contractual, not tooling gaps" and names commenters
   (`andrewstuart`, `dboreham`, `WhyComboNadir`) arguing the fix is a policy change, not software —
   i.e. the source finding itself is skeptical this is a tooling opportunity, not just this synthesis
   pass being cautious.
3. The evidence is **adjacent rather than directly on-point**: the quotes below describe freelancers
   who *already solved* late payment by unilaterally changing their own contract terms (upfront
   deposits, full payment upfront) — none of them describe wanting software to help *enforce* a
   deposit policy; that leap from "I personally changed my terms" to "a tool should structurally
   enforce this" is this synthesis's inference, not something a quote states directly.

## Assumed ICPs

(derived from evidence, not invented)

- Solo freelancers and small service/trades businesses who invoice after work is delivered and
  report spending significant recurring time on collections/chasing (arsene94: "20% of my time on
  Shadow Admin work... chasing invoices"; river_otter's contractor friend spending "a lot of evenings
  on a laptop chasing invoices").
- Freelancers who have already informally solved this via unilateral contract-term changes and might
  want the enforcement built into their tooling rather than manually policed (SteveStavros: "switched
  to requiring 50% upfront for any new client work... completely eliminated late payment issues").

## Research constraints

(carried over/adapted from `config/scope.md`)

- Same source set and exclusions as Candidate 1 above (HN + PH for complaints, PH blocked this run;
  GitHub + Show HN for builds, Bluesky blocked; Reddit/app-store excluded).
- Given point 2 of the grounding note, **any Phase 2 work on this candidate should treat "is this
  actually a tooling gap or a behavior-change problem" as an open question to test directly with
  personas**, not as a settled premise — the source complaint thread's own commenters already argue
  the latter.
- Explicitly avoid the "reminder emailer" framing complaints Pattern 1 flags as saturated (e.g.
  Uaryn already exists in that shape); the differentiation has to be structural (deposit required
  before work/invoice can proceed), not cadence/tone of reminders.

## Evidence

**Source coverage summary**: single-source — complaints findings only
(`findings/2026-09-04T070139Z-freelancer-finance-complaints-findings.md`, Pattern 1). No builds-side
pattern corroborates this candidate; see Grounding note above, which is the authoritative statement
of this candidate's evidentiary weakness and must not be dropped in any downstream use of this file.

> After 7 years of freelancing and building enterprise platforms, I realized I was spending 20% of my
> time on "Shadow Admin" work—drafting contracts, compliance, and chasing invoices.
>
> — arsene94, complaints Pattern 1. [https://news.ycombinator.com/item?id=46839109](https://news.ycombinator.com/item?id=46839109)

> My buddy runs a general contracting business... he spends a lot of evenings on a laptop chasing
> invoices and scheduling follow-ups instead of hanging out with his family and friends.
>
> — river_otter, complaints Pattern 1. [https://news.ycombinator.com/item?id=47545486](https://news.ycombinator.com/item?id=47545486)

> Switched to requiring 50% upfront for any new client work. Lost a couple of prospects but
> completely eliminated late payment issues... The clients worth keeping never push back on it.
>
> — SteveStavros, complaints Pattern 1. [https://news.ycombinator.com/item?id=47639650](https://news.ycombinator.com/item?id=47639650)

**Per-pattern link**: complaints Pattern 1
(`findings/2026-09-04T070139Z-freelancer-finance-complaints-findings.md#1-chasing-late-payment-consumes-freelancer-time-and-is-the-most-repeated-invisible-tax`)
is the sole source pattern. Its own "what this means for a concept candidate" note already
identifies the deposit/upfront-payment angle as the sharper opportunity versus reminder cadence —
this candidate follows that lead, but the lead itself is downstream inference within the complaints
findings, one hop removed from a quote that directly asks for enforcement tooling. No quote in either
findings file directly states "I wish there were a tool that enforced deposits for me."
