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
2. **Next**: dispatch a research pass (same rigor/format as
   `examples/2026-09-07T043304Z-patent-signal-research/`) answering the one fact that changes this
   spec's shape most: does USPTO's **trademark** search (Trademark Search / TSDR / Trademark Center)
   share the same ID.me identity-verification gate the patent-signal-research pass found on USPTO's
   *patent* Open Data Portal (ODP)? Also check EUIPO eSearch, WIPO Global Brand Database, RDAP (domain
   existence + creation-date "age" lookup), TLD-spread checking, GitHub (expected already-clear, reuses
   `build-pattern-scanner`'s existing `gh` CLI auth), and social-handle-availability checking (expected
   ToS-blocked on most platforms — confirm, don't assume).
3. **Then**: write `.claude/agents/name-brand-vetter.md` per the two-tier design below, structured
   around the user's own taxonomy headers verbatim (not reinvented), with a not-legal-advice disclaimer
   mirroring the patent-research precedent.
4. **Then**: `config/name.example.md` (standalone input template), `names/README.md` +
   `.gitignore` entry (working-output convention, same shape as `discovery/`/`findings/`), `AGENTS.md`
   update (new phase, runnable standalone).
5. **Then**: first real test run against the strongest existing candidate (agent-memory /
   `Contextlint` — confirmed across all 4 relevant sources over three discovery runs, see
   `docs/plans/0003-broad-discovery.md` item 16).
6. **Owner gate throughout**: any run whose concept input is sfclarity/sfsanity-owned must not have its
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
| Naming-sources research pass (USPTO Trademark/TSDR ID.me question, EUIPO, WIPO Global Brand Database, RDAP, TLD spread, GitHub, social handles) | agent | `discovery/<ts>-naming-sources-research.md` written and archived, same found/blocked/owner-gated rigor as the patent-research precedent; the USPTO trademark-vs-patent ID.me question explicitly answered, not assumed either way. |
| `.claude/agents/name-brand-vetter.md` spec | agent | File exists: Input (repo candidate OR standalone config), taxonomy-mapped sections (Build/Say/Picture/Travel/Market-easy/Clear/PR-launch sweep/Finalist), two-tier design encoded, not-legal-advice disclaimer, ethical boundary re-stated. |
| `config/name.example.md` | agent | Copy-and-fill template exists, mirrors `config/scope.example.md`'s shape. |
| `names/README.md` + `.gitignore` entry | agent | File exists; `names/*.md` gitignored, matching `findings/`/`discovery/` treatment. |
| `AGENTS.md` update | agent | New phase documented, positioned after `concept-synthesizer`, explicitly runnable standalone. |
| First real test run (against `Contextlint`/agent-memory candidate) | agent | Real tier-1 + tier-2 pass run end-to-end, output written and reviewed, spec corrections (if any) folded back in, same discipline as every prior first-real-run in this repo's history. |
| Docs audit (CHANGELOG/README/CONTRIBUTING/issues) | agent | Updated once the above ships, per this repo's per-milestone discipline. |
