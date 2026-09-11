---
name: name-brand-vetter
description: Generates and vets candidate product names against the user's own naming taxonomy — a cheap filter on every generated candidate, an expensive PR-launch sweep on 2-3 finalists only. Runs standalone, against this repo's own candidates or an external concept. Not legal clearance.
---

Generates candidate product names for a concept and narrows them to one finalist through the user's
own naming taxonomy: a name is an asset people can own, say, remember, repeat. Runs standalone — not
gated on the rest of this pipeline having just run, and not hardwired to this repo's own
`candidates/` output (see Input).

## Input

Read `config/name.md` (copy from `config/name.example.md`). Exactly one of:

- **Option A**: a `candidates/*-candidate.md` path from this repo's own pipeline.
- **Option B**: a standalone concept description (concept paragraph, category, slug) — used when
  vetting names for a concept that didn't come from this repo's own `candidates/` output, including
  concepts belonging to external projects. **If the concept belongs to an external project, do not
  read or import that project's own files into this repo** — a plain-language description is enough
  to work from. See the standing sfclarity/sfsanity boundary if the run is for one of those.

Also read the config's **Output ownership** field — required, governs the Output section below —
and any Constraints (markets/languages for Travel, TLDs beyond the `.com` default, known trademark
classes).

## What to do

Work in the order below — generation and judgment first (cheap), the fast filter second (cheap),
verification last and only against survivors (expensive). Never run step 4 or step 6 against the
full generated list — see `docs/plans/0004-name-brand-vetting.md`'s two-tier design.

1. **Build** — generate 15-30 candidate names from the concept using named construction patterns
   (compound, suffix, or metaphor) — state which pattern produced each name, don't present an
   unstructured brainstorm list.
2. **Say / Picture / Travel / Market-easy** — judgment pass over every generated name, no external
   lookups:
   - **Say**: read it aloud once — would a radio host, a barista, or someone overhearing it in a
     hallway say it back correctly? Cut names that need spelling out.
   - **Picture**: concrete and sketchable (an image/logo forms easily), not an abstraction that
     resists visualization.
   - **Travel**: no accidental bad meaning in the languages/markets named in `config/name.md`'s
     Constraints (default: English-speaking only) — flag, don't silently cut, since this is a
     judgment call the run's owner may want to see even when it fails.
   - **Market-easy**: easy to say, spell, remember, and pass along without effort — the umbrella
     check the four above already mostly cover; note anything that survives the others but still
     reads as effortful to spread.
   Cut names that fail Say or Picture outright; carry Travel/Market-easy flags forward rather than
   cutting on them alone.
3. **Clear — Tier 1, cheap filter, every surviving candidate.** Two checks, both free and
   unauthenticated at this cardinality (see `discovery/2026-09-11T200013Z-naming-sources-research.md`
   §Recommendation):
   - **Category clash** — judgment: does the name already mean something else prominent in the
     concept's stated category? (Not a trademark search — that's Tier 2.)
   - **`.com` availability via RDAP** — `rdap.verisign.com/com/v1/domain/<name>.com` (no auth; a 404
     response means unregistered/available, a 200 with an `events` array means taken — read the
     `registration` event's date if taken, useful context even for a cut name).
   - **GitHub name collision** — `gh search repos <name>` / `gh api search/users -f q=<name>` (already
     authenticated in this workspace, same pattern `build-pattern-scanner` uses) — flag heavy existing
     use in the same space, don't auto-cut on any hit (a dormant unrelated repo isn't disqualifying).
   Narrow to a shortlist (target: 5-8 names) carrying every flag from steps 2-3 forward, not silently
   dropped.
4. **Shortlist to 2-3 finalists** from the survivors, by judgment against "the one you can't stop
   saying" — state the reasoning, don't just declare a winner.
5. **PR-launch sweep — Tier 2, finalists only.** Never run this against more than 2-3 names:
   - **Web search** — this repo's own `WebSearch` tool, no gate.
   - **USPTO Trademark Search** — `tmsearch.uspto.gov`, one render per finalist (patchright/JS render
     required — it's an Angular SPA). **Confirmed no ID.me/login gate for this UI** (research pass
     below) — read for an identical or confusingly-close mark in a relevant class. State the search
     terms/classes used; a "no conflict found" result means exactly that, not clearance (see Not
     legal advice below).
   - **EUIPO** — only if `dev.euipo.europa.eu` API credentials are provisioned (owner-gated, free
     self-service OAuth2/OIDC — **never scrape `euipo.europa.eu/eSearch/` directly**, it's both
     robots.txt-disallowed and covered by a blanket TDM copyright opt-out). If no credential exists,
     state EUIPO as skipped, not silently omitted.
   - **WIPO** — **do not query WIPO Global Brand Database at all**, confirmed blocked (active
     technical captcha on every path, plus a PATENTSCOPE-shaped ToS clause) — leave it out of the
     sweep entirely rather than attempting and reporting a failure.
   - **Domain age + TLD spread via RDAP** — the finalist's `.com` (re-confirm from Tier 1) plus
     `.net`/`.org`/`.ai`/`.dev`/`.app`/`.xyz` (all RDAP-covered). For `.io`/`.co`/`.me` (confirmed
     absent from the RDAP bootstrap), fall back to a plain DNS lookup — this only proves
     registered/delegated, never "available" — state that asymmetry in the output every time it
     applies, don't present a DNS-fallback result with the same confidence as an RDAP one.
   - **GitHub** — re-confirm the Tier 1 check for each finalist specifically.
   - **Social handles** (X/Twitter, Instagram) — **do not automate this.** Both are confirmed blocked
     (X: robots.txt blanket disallow + explicit ToS ban on automated access "by any means"; Instagram:
     Meta Platform Terms, WebSearch-corroborated). List it as a manual/owner checklist item in the
     output with the exact handles to check by hand, never as an automated pass/fail.
6. **The finalist** — one name, with the reasoning tying back to "the one you can't stop saying,
   clear to hold, fluent to spread," and every hedge from steps 2-5 still attached in the write-up
   (never let a Travel flag or a "no conflict found in sources checked" caveat silently disappear —
   `claim-verification.md`'s hedge-decay failure mode applies here directly).

## Fetch tooling

RDAP and GitHub calls are plain unauthenticated/already-authenticated GETs — fetch via
[`polyfetch-scrape`](https://github.com/qte77/polyfetch-scrape) (`uv run --directory
../polyfetch-scrape polyfetch fetch <url> --json`) or `gh api`/`gh search` respectively, same pattern
as the rest of this pipeline. USPTO's Trademark Search UI is a client-rendered Angular SPA — use
`polyfetch`'s patchright/browser-render tier (`--wait-until networkidle`), not a plain GET, or the
page returns an empty shell. See
`discovery/2026-09-11T200013Z-naming-sources-research.md` for the full access research this spec's
Clear/PR-launch-sweep sections are built from.

## If a source is blocked or uncertain

Same four-state discipline as the rest of this pipeline (blocked / empty / found / excluded-by-scope)
plus a fifth this spec specifically needs: **uncertain** — the naming-sources research pass left the
USPTO TSDR *bulk API*'s current ID.me requirement genuinely unresolved (the UI itself is confirmed
open; only the separate bulk API is uncertain). This spec never calls the TSDR bulk API — it uses the
UI only — so this doesn't block anything today, but if a future change adds bulk API use, that
assumption must be re-verified at the point of use, not inherited from the research file.

## Not legal advice

A "no conflict found" or "available" result from this spec's output is a screening signal, not a
trademark-clearance opinion, a freedom-to-operate analysis, or a guarantee a name is safe to adopt,
file, or launch under — it means only that the specific sources checked, in the way checked, on the
date checked, did not surface a conflict. It does not check for confusingly-similar marks, only
identical/near-identical ones in the sources' own search. Before adopting, filing, or launching any
name this spec outputs, a human must independently verify current trademark status directly against
the primary USPTO/EUIPO systems (not this file's summary) and consult qualified trademark counsel.
Repeat this paragraph, not just a pointer to it, in every output document.

## Output

- **This repo's own concept** (`config/name.md` Output ownership = yes): write
  `names/<date-time-iso>-<slug>-names.md` per `names/README.md`'s Expected structure. Gitignored by
  default; archive to `examples/<date-time-iso>-<slug>-names/` if worth keeping permanently, same
  convention as every other run type in this pipeline.
- **External concept** (Output ownership = no, e.g. sfclarity/sfsanity): still write
  `names/<date-time-iso>-<slug>-names.md` (already gitignored, so this is harmless working output),
  but the file's own opening line must read **"DO NOT ARCHIVE — external concept, relocate manually"**
  and this spec must never propose or perform an `examples/` archive for it. The run's owner relocates
  the file out of this repo themselves. See the standing sfclarity/sfsanity boundary.
