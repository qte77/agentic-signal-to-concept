# 0004 — agentic-signal-to-concept: name & brand vetting

## Current status and what's next, in order

The user asked (2026-09-11) for a new capability: given a concept (this repo's own
`candidates/*-candidate.md`, or a standalone external concept — the user explicitly said this repo is
used, and will keep being used, as a research instrument for two external private projects of theirs,
`sfclarity`/`sfsanity`, whose own content must never enter this repo — see
`config/name.example.md` and the Standalone-input note below), generate and vet candidate product
names against the user's own naming taxonomy (verbatim, reproduced in full under Design below), ending
in one finalist name that is memorable, ownable, and cleared (category/trademark/domain) as far as an
unattended pass reasonably can clear it.

1. ~~Plan file 0004~~ — this file, done 2026-09-11.
2. ~~Naming-sources research pass~~ — done 2026-09-11: `discovery/2026-09-11T200013Z-naming-sources-research.md`.
   **Answer to the load-bearing question**: USPTO trademark search's public UI (`tmsearch.uspto.gov`/
   `tsdr.uspto.gov`) is confirmed **not** gated by ID.me/login for individual lookups — a materially
   different (and better) access state than USPTO's patent ODP. The separate TSDR *bulk API* does
   require a key, and whether current key issuance needs ID.me is genuinely uncertain (not resolved) —
   the new spec avoids that API entirely, using the UI only, so this doesn't block anything today. Full
   per-source verdicts (EUIPO, WIPO, RDAP, TLD spread, GitHub, social handles) in the file itself.
3. ~~`.claude/agents/name-brand-vetter.md` spec~~ — done 2026-09-11, structured around the user's own
   taxonomy headers verbatim, two-tier design (RDAP `.com` + GitHub in Tier 1; web search + USPTO UI +
   RDAP TLD spread + GitHub + EUIPO-if-credentialed + social-handles-as-owner-checklist in Tier 2; WIPO
   excluded from both), not-legal-advice section.
4. ~~`config/name.example.md`, `names/README.md`, `.gitignore` entries, `AGENTS.md` update~~ — done
   2026-09-11.
5. ~~First real test run against `Contextlint`/agent-memory~~ — done 2026-09-11:
   `examples/2026-09-11T203709Z-contextlint-names/`. Finalist **Memolint** (follows the recognizable
   `-lint` dev-tool convention; "Contextlint" itself was cut at Tier 1 for a heavy GitHub collision,
   proving the taxonomy converges independently rather than rubber-stamping the input candidate's
   working title). Real tooling gaps hit and folded back into `.claude/agents/name-brand-vetter.md`:
   most importantly, **USPTO Trademark Search is now a manual/owner checklist item, not automated** —
   the run's first pass reported false "no results" for all three finalists (a guessed deep-link URL
   silently ignores its own query parameter), caught only by a positive-control check against a term
   certain to have live marks. Eight smaller corrections (GitHub API method/rate-limit/flags, RDAP
   404 handling, undocumented TLD-lookup mechanisms) also folded in. Every finalist's USPTO status is
   correctly recorded as unverified, not cleared, in the archived output.
6. **Next**: docs audit close-out (CHANGELOG/README, already mostly done inline as this arc shipped —
   confirm nothing's stale) and mark this arc closed once confirmed.
7. **Owner gate throughout**: any run whose concept input is sfclarity/sfsanity-owned must not have its
   output committed to this repo's tracked `names/`/`examples/` — see the `sfc-sfs-boundary` memory
   and the Output section below. This repo's own naming runs (against its own `candidates/`) follow the
   normal gitignore-then-archive-to-`examples/` path.

## Why this arc exists

No naming/branding phase exists anywhere in this repo's own pipeline or in either downstream repo
(confirmed 2026-09-11 by reading `agentic-market-research-to-gtm/README.md` in full: its Phase 3 GTM
Strategy covers value propositions/channels/launch plan but explicitly not product naming). The user
wants naming treated with the same sourced, verifiable discipline this repo already applies to concept
discovery — not a creative-brainstorm free-for-all, and not a legal-clearance guarantee either.

## Design

### The user's taxonomy, verbatim intent (headers for the new spec map onto this, not reinvented)

A name is an asset people can own, say, remember, repeat.

- **Ownable** — clear the category, trademark databases, domain reality, *before* falling in love with
  a name.
- **Market-easy** — easy to say, spell, picture, remember, and pass along without effort.
- **Travel** — native ears (no accidental meaning in other languages/markets), bad-meaning check,
  border test.
- **Picture** — concrete, sketchable, sticky (a name that can become an image/logo, not an abstraction).
- **Build** — compound, suffix, or metaphor construction patterns (how candidate names get generated).
- **Say** — radio test, barista test, hallway test (said aloud, once, understood/repeated correctly).
- **Clear** — category clash, trademark, `.com` availability (the cheap fast-filter, applied to every
  generated candidate).
- **PR-launch sweep** — web search, USPTO/EUIPO/WIPO, domain age, TLD spread, GitHub, social handles
  (the expensive pass, applied only to finalists).
- **The finalist** — the one you can't stop saying, clear to hold, fluent to spread.

### Two-tier verification (advisor guidance, confirmed sound against the taxonomy above)

Running the full PR-launch sweep against every generated candidate is wasteful and mirrors this
pipeline's own existing discipline of bounding expensive steps (`signal-discoverer`'s bounded pull,
`concept-synthesizer`'s "don't invent evidence" gate):

- **Tier 1 — cheap filter, all candidates.** Build → Say → Picture → Travel → Market-easy criteria
  (judgment, cheap/no external lookups) plus a fast category-clash + `.com`-availability check (RDAP or
  equivalent), applied to the full generated candidate list. Narrows to a shortlist.
- **Tier 2 — PR-launch sweep, finalists only (2–3 names).** The expensive, source-backed checks: web
  search, USPTO/EUIPO/WIPO trademark search, RDAP domain age, TLD spread, GitHub, social handles. Only
  ever run against the shortlist survivors, never the full candidate list — same "don't run the
  expensive step at the wrong cardinality" discipline as the rest of this pipeline.

### Standalone input (not hardwired to this repo's own candidates)

This capability is used both for this repo's own `candidates/*-candidate.md` output and for external
concepts (sfclarity/sfsanity, per the `sfc-sfs-boundary` memory) — the spec's Input section must accept
either a `candidates/*-candidate.md` path **or** a standalone `config/name.md` concept description
(mirroring `config/scope.example.md`'s copy-and-fill pattern), never assuming this repo's own pipeline
just ran.

### Output-location-follows-ownership

- This repo's own naming runs: gitignored working output (`names/<ts>-<slug>-names.md`, same treatment
  as `findings/*-findings.md`/`discovery/*.md`), archived to `examples/` if worth keeping permanently.
- Runs on behalf of sfclarity/sfsanity: **never committed to this repo's tracked output at all** — the
  owner relocates that output manually (e.g. into the sfclarity repo itself). See the `sfc-sfs-boundary`
  memory for the precedent (`run/vc-scene-intelligence`).

### Not legal advice (mirrors the patent-signal-research precedent, `examples/2026-09-07T043304Z-patent-signal-research/`)

A trademark/domain sweep here is a screening step, not legal clearance. "Clear the category" implies a
confusingly-similar-mark check with genuine legal risk if skipped or misrepresented as authoritative.
The new spec must carry a disclaimer at least as prominent as the patent-research file's, every output
document must repeat it, and no output may declare a name "clear" in an absolute sense — only "no
conflict found in the sources checked this pass," sources named.

### Ethical boundary carried forward

Same standing rule as the rest of this pipeline: aggregate/pattern signal only. Never present one
specific candidate name as "the answer" without the hedges above surviving into the output document
itself (not just stated in conversation — `claim-verification.md`'s hedge-decay failure mode applies
here as much as anywhere else in this pipeline).

## Code / file / source map

**New files this arc creates:**
```
.claude/agents/name-brand-vetter.md   (new agent spec — not yet written)
config/name.example.md                 (standalone input template, mirrors config/scope.example.md)
names/README.md                        (mirrors discovery/README.md's shape)
discovery/<ts>-naming-sources-research.md   (research pass, not yet run — see remaining-work table)
```
**Modified:**
- `AGENTS.md` — add the new phase, documented as runnable standalone (not gated on the rest of the
  pipeline having just run), positioned after `concept-synthesizer`.
- `.gitignore` — add `names/*.md` (same treatment as `discovery/*.md`/`findings/*-findings.md`).
- `CONTRIBUTING.md` / `README.md` — status pointers once the new phase is real.

**Reference precedents to reuse, not re-derive:**
- `examples/2026-09-07T043304Z-patent-signal-research/patent-signal-research.md` — exact rigor/format
  for the sources-research pass (found/blocked/owner-gated per source, raw-verified ToS quotes, not
  WebSearch-summary-only, explicit not-legal-advice section).
- `.claude/agents/concept-synthesizer.md` — precondition-check + "don't invent evidence" pattern to
  mirror in the new spec's own precondition section.
- `config/scope.example.md` — copy-and-fill input template shape for `config/name.example.md`.

## Remaining work (single table)

| Item | Gate | Done-when |
|---|---|---|
| Plan file 0004 | **shipped** (2026-09-11) | This file exists with status, design, source map, remaining-work table. |
| Naming-sources research pass (USPTO Trademark/TSDR ID.me question, EUIPO, WIPO Global Brand Database, RDAP, TLD spread, GitHub, social handles) | **shipped** (2026-09-11) | `discovery/2026-09-11T200013Z-naming-sources-research.md` written, same found/blocked/owner-gated rigor as the patent-research precedent. USPTO trademark UI confirmed not ID.me-gated (the load-bearing question); TSDR bulk API's current gate strength left explicitly uncertain, not guessed. EUIPO website blocked/API owner-gated-free; WIPO GBD blocked (technical + ToS); RDAP found free (with a confirmed `.io`/`.co`/`.me` coverage gap, DNS fallback documented); GitHub already clear; X blocked (raw-verified), Instagram blocked (WebSearch-corroborated only, flagged as such). Archived below. |
| `.claude/agents/name-brand-vetter.md` spec | **shipped** (2026-09-11) | File exists: Input (repo candidate OR standalone config), taxonomy-mapped sections (Build/Say/Picture/Travel/Market-easy/Clear/PR-launch sweep/Finalist), two-tier design encoded per the research pass's recommendation, not-legal-advice section, ethical boundary re-stated. |
| `config/name.example.md` | **shipped** (2026-09-11) | Copy-and-fill template exists, mirrors `config/scope.example.md`'s shape, includes required Output ownership field. |
| `names/README.md` + `.gitignore` entry | **shipped** (2026-09-11) | File exists; `names/*.md` and `config/name.md` gitignored, matching `findings/`/`discovery/` treatment. |
| `AGENTS.md` update | **shipped** (2026-09-11) | New phase documented in "Two modes, plus a standalone naming phase" + a new "Naming a concept" section, explicitly runnable standalone, external-output-ownership rule stated. |
| First real test run (against `Contextlint`/agent-memory candidate) | **shipped** (2026-09-11) | Real Tier-1 + Tier-2 pass run end-to-end (24 candidates → 19 through Tier 1 → 6 shortlisted → 3 finalists swept). Finalist **Memolint**. Output archived at `examples/2026-09-11T203709Z-contextlint-names/`. Nine real spec corrections folded back into `.claude/agents/name-brand-vetter.md`, most importantly demoting USPTO Trademark Search from an automated Tier-2 check to a manual/owner checklist item after a positive-control test proved its automated render silently fabricated "no results" regardless of query. |
| Docs audit (CHANGELOG/README/CONTRIBUTING/issues) | **shipped** (2026-09-11) | `CHANGELOG.md`, `examples/README.md`, and this plan file all updated in the same PR as the spec corrections; `README.md`/`CONTRIBUTING.md` already current from the prior PR, no further change needed. No new GitHub issue opened — the USPTO gap is fully resolved in-spec (manual step), not a tracked open item. |
