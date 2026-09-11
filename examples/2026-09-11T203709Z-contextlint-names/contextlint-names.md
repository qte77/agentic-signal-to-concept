# Naming run — agent-memory candidate (Contextlint)

**Run timestamp:** 2026-09-11T203709Z
**Output ownership:** this repo's own concept (yes) — normal gitignore-then-archive-to-`examples/` path applies.

## Input

- **Source:** `candidates/2026-09-04T070136Z-agent-memory-candidate.md` (Option A, this repo's own pipeline).
- **Concept, one line:** a hygiene/audit layer that sits on top of the memory and instruction files a
  team already has (CLAUDE.md, AGENTS.md, MCP/local-storage memory stores) — flags stale/contradictory/
  poisoned entries, treats instruction files as versioned model-scoped artifacts, runs a before/after
  regression check on edits. Deliberately not a new storage backend.
- **Constraints (from `config/name.md`):** Travel check = English-speaking markets only (default).
  TLDs: `.com` for the Tier-1 cheap filter; `.io`/`.dev`/`.ai` required at Tier 2 (this run also ran
  the spec's own full default TLD set — `.net`/`.org`/`.ai`/`.dev`/`.app`/`.xyz` plus `.io`/`.co`/`.me`
  DNS fallback — a superset that covers the config's required TLDs). Trademark classes: developer
  tooling/software (Class 9/42), inferred not hard-locked.
- **Important methodological note:** "Contextlint" (the candidate file's working title) was treated as
  one ordinary generated candidate, not a pre-selected winner, per this run's instructions — the point
  was to test whether the taxonomy would surface it, or something better, independently. It did surface
  the same naming *pattern* (the `-lint` suffix) but landed on a different, cleaner name — see The
  Finalist below.

## Tier 1 — generated candidates, cheap filter

24 names generated across three named construction patterns. Step 2 (Say/Picture/Travel/Market-easy)
cuts 5 outright; the remaining 19 went through the Tier-1 `.com` RDAP + GitHub cheap filter.

| # | Name | Pattern | Say/Picture/Travel/Market-easy | Category clash (judgment) | `.com` (RDAP) | GitHub | Result |
|---|---|---|---|---|---|---|---|
| 1 | Contextlint | compound (context+lint) | Say OK, Picture OK, Travel OK, Market-easy OK | — | **TAKEN** | **Heavy, on-point**: GitHub org `contextlint` exists; `contextlint/contextlint` repo pitches "Audit LLM prompt and context spend"; 5+ more repos named `contextlint`/`ContextLint` in adjacent linting space | **CUT — heavy same-space collision** |
| 2 | Memoprune | compound (memo+prune) | All OK | none found | AVAILABLE | Clean — no repos, no orgs | Tier-1 PASS |
| 3 | Rulerot | compound (rule+rot) | Say OK but blunt/negative-sounding word (noted, not disqualifying); Picture OK; Travel OK | — | AVAILABLE | **Flag**: `AlexPixelAP/rulerot` — "Find duplicate rules, contradictions, broken paths and token waste in CLAUDE.md/AGENTS.md" — near-identical positioning to this concept, dormant (0 stars) | Tier-1 PASS, carried with heavy flag |
| 4 | Driftguard | compound (drift+guard) | All OK | — | **TAKEN** | **Heavy**: org `DriftGuard` (GitOps drift detection, active), plus `DriftGuard-sh`, `DriftGuardAI` orgs and multiple 8-11★ repos in the general "drift detection" space | **CUT — heavy collision + domain taken** |
| 5 | Staleflag | compound (stale+flag) | All OK | — | AVAILABLE | Light, unrelated (`staleflags` — dead feature-flag detection) | Tier-1 PASS (see Tier-2 category-clash finding below — later dropped before full Tier-2 sweep) |
| 6 | Instructwatch | compound (instruct+watch) | All OK | none found | AVAILABLE | Clean — no repos, no orgs | Tier-1 PASS |
| 7 | Contextgroom | compound (context+groom) | **Cut at step 2** — "groom" carries a strong unrelated negative connotation in modern usage; Picture/Market-easy fail on that basis | — | not run | not run | **CUT at step 2 (Say/Picture)** |
| 8 | Rulehealth | compound (rule+health) | **Cut at step 2** — weak Picture (abstract "health score"), reads as a different category (health-tech) | — | not run | not run | **CUT at step 2** |
| 9 | Rulify | suffix (rule+-ify) | **Cut at step 2** — weak Picture, abstract verb form, nothing to sketch | — | not run | not run | **CUT at step 2** |
| 10 | Memolint | suffix (memo+lint) | All OK | none found | AVAILABLE | Clean — no repos; one unrelated username (`memolintei`) | Tier-1 PASS |
| 11 | Driftly | suffix (drift+-ly) | All OK, flagged Market-easy: generic "-ly" SaaS-suffix pattern, saturated style | — | **TAKEN** | **Heavy**: `Driftly` org/user (119★ attendance app), `Driftly-AI`, `Driftly-net` orgs, a dozen+ unrelated users on the exact name | **CUT — saturated namespace + domain taken** |
| 12 | Contextops | suffix (context+-ops) | **Cut at step 2** — weak Picture, abstract "-ops" suffix, no concrete image | — | not run | not run | **CUT at step 2** |
| 13 | Rulekit | suffix (rule+-kit) | All OK | — | **TAKEN** | **Heavy**: org `RuleKit` (190★ rule-based learning suite), `rulekit-dev`, `rulekitlabs` orgs, an active rules-engine (13★) — adjacent category (rules engines) | **CUT — established adjacent-category org + domain taken** |
| 14 | Instructlint | suffix (instruct+lint) | All OK, Market-easy note: slightly longer/clunkier than Memolint | — | AVAILABLE | **Flag**: two dormant repos, both near-identical positioning — `Neohu-ceo/instructlint` ("Lint the instructions that guide your coding agents — conflicts, stale paths, unsafe commands, scope errors, context bloat") and `BahirHakimy/instructlint` | Tier-1 PASS, carried with heavy flag |
| 15 | Memops | suffix (memo+-ops) | **Cut at step 2** — weak Picture, abstract "-ops" | — | not run | not run | **CUT at step 2** |
| 16 | Weedline | metaphor (gardening) | Picture OK (concrete); **Travel flag**: "weed" carries cannabis-slang connotation even in English markets — flagged, not silently cut, per spec | — | **TAKEN** | Light, unrelated (Inkscape algorithm) | **CUT — domain taken + weak fit given Travel flag** |
| 17 | Groundskeeper | metaphor (caretaker) | All OK, Market-easy note: long (4 syllables) | — | **TAKEN** | **Heavy, on-point**: 215★ `groundskeeper` npm package (console-statement removal), plus `zvoque/groundskeeper` — "Skill-usage tracking and cleanup for Claude Code — flags cold/unused skills" (same ecosystem, same "agent hygiene" space) | **CUT — heavy same-space collision + domain taken** |
| 18 | Litmus | metaphor (chemistry test) | All OK, strong Picture (litmus paper) | Flag: real risk of adjacent-category clash | **TAKEN** | **Severe**: `litmuschaos/litmus` (5,609★, major CNCF project), `google/litmus` (LLM testing/eval tool — directly adjacent category), `microsoft/Litmus` | **CUT — severe established-brand collision** |
| 19 | Compost | metaphor (decay/recycling) | All OK | — | **TAKEN** | Moderate, mostly unrelated, but `compost` org exists and namespace is fairly saturated | **CUT — domain taken + moderate saturation** |
| 20 | Sentinel | metaphor (guard) | All OK | Flag: extremely common security-tool name | **TAKEN** | **Severe**: `alibaba/Sentinel` (23,146★), `Sentinel-One`/`SentineLabs` orgs (real trademark, SentinelOne) | **CUT — severe established-brand collision** |
| 21 | Ledger | metaphor (accounting/provenance) | All OK | Flag: strong fintech/crypto brand association (Ledger SAS hardware wallets) | **TAKEN** | **Not fully re-checked** — GitHub search hit the authenticated rate limit (30 req/min) partway through this run's Tier-1 pass; not retried given the domain-taken + strong known trademark signal already sufficient to cut | **CUT — domain taken + known trademark collision (Ledger SAS)** |
| 22 | Anchor | metaphor (stability) | All OK | Flag: Anchor.fm (podcasting) and Anchor (Solana dev framework) are established products | **TAKEN** | **Not fully re-checked** — same rate-limit gap as above | **CUT — domain taken + known collisions** |
| 23 | Tripwire | metaphor (security trigger) | All OK, strong Picture | Flag: **Tripwire, Inc.** is a real, longstanding file-integrity-monitoring / config-drift-detection commercial product — direct same-space collision | **TAKEN** | **Not fully re-checked** — same rate-limit gap | **CUT — direct established-competitor collision** |
| 24 | Custodian | metaphor (caretaker) | All OK | Flag: `cloud-custodian` (Capital One, well-known AWS policy/governance OSS project) is an adjacent-category collision | **TAKEN** | **Not fully re-checked** — same rate-limit gap | **CUT — domain taken + adjacent-category collision** |

**Tier-1 shortlist (6 names carried forward, all flags attached):** Memoprune, Instructwatch, Memolint,
Staleflag, Rulerot (flagged), Instructlint (flagged).

**Shortlist-to-finalists judgment ("the one you can't stop saying"):** Rulerot and Instructlint were not
carried to Tier 2 — both collide with dormant-but-directly-on-point GitHub repos already doing this
exact CLAUDE.md/AGENTS.md audit pitch under the same name; real risk even though `.com` is free. A quick
`WebSearch` pass (cheap, no gate) on the remaining four surfaced a category-clash problem for
**Staleflag**: "stale flag" is an established term of art in *feature-flag* management (Amplitude,
Datadog, Flagsmith, Reflag all ship "stale flag detection" as a named feature; a GitHub repo
`stale-flag-detector` exists) — this risks users mistaking the product for a feature-flag hygiene tool,
not an AI-agent-memory hygiene tool. Dropped before the expensive Tier-2 sweep (USPTO render + full TLD
spread), consistent with the spec's "never run Tier 2 against more than 2-3 names."

**Finalists (3): Memolint, Memoprune, Instructwatch.**

## Tier 2 — PR-launch sweep, finalists only

Sources per finalist: web search (no gate), USPTO Trademark Search UI (patchright render, term-only —
see caveat below), RDAP TLD spread (`.net`/`.org`/`.ai`/`.dev`/`.app`/`.xyz`) + DNS-delegation fallback
for `.io`/`.co`/`.me`, GitHub re-check, social handles (manual). EUIPO and WIPO handled per spec — see
below.

### Memolint

- **Web search:** no existing product/trademark/project found under this exact name (checked 2026-09-11).
- **USPTO Trademark Search UI: UNVERIFIED — blocked by tooling, not a real result.** See the USPTO
  caveat below the three finalist subsections for the full positive-control finding; do not read the
  earlier "No results found" text as a search outcome for this or any finalist.
- **RDAP `.com`** (re-confirmed from Tier 1): **AVAILABLE**. **`.net`/`.org`/`.ai`/`.dev`/`.app`/`.xyz`:
  all AVAILABLE** (RDAP 404 on every registry checked, 2026-09-11).
- **`.io`/`.co`/`.me` (DNS fallback, `dns.google/resolve?type=NS`):** all three returned DNS Status 3
  (NXDOMAIN) — no NS records found. Per the spec's stated asymmetry: this shows **no evidence the
  domain is delegated/registered**, it does **not** confirm availability the way an RDAP 404 does —
  stated explicitly here, not glossed over.
- **GitHub** (re-confirmed): no repos, no matching orgs; one unrelated username (`memolintei`).
- **EUIPO:** skipped — no `dev.euipo.europa.eu` credential provisioned in `.env` (confirmed absent).
- **WIPO Global Brand Database:** not queried — confirmed blocked at the technical layer (per
  `discovery/2026-09-11T200013Z-naming-sources-research.md` §3), left out of the sweep entirely per spec.
- **Social handles (manual/owner checklist — not automated, per spec):**
  - `x.com/memolint` — check by hand
  - `instagram.com/memolint` — check by hand

### Memoprune

- **Web search:** no existing product/trademark/project found under this exact name (checked 2026-09-11).
- **USPTO Trademark Search UI: UNVERIFIED — blocked by tooling.** See caveat below.
- **RDAP `.com`:** AVAILABLE. **`.net`/`.org`/`.ai`/`.dev`/`.app`/`.xyz`: all AVAILABLE.**
- **`.io`/`.co`/`.me` (DNS fallback):** all NXDOMAIN — no evidence of delegation (same asymmetry caveat
  as above applies).
- **GitHub** (re-confirmed): no repos, no orgs.
- **EUIPO:** skipped, no credential. **WIPO:** not queried, confirmed blocked.
- **Social handles (manual):** `x.com/memoprune`, `instagram.com/memoprune`.

### Instructwatch

- **Web search:** no directly-colliding product; one adjacent mention of an AI-model-naming artifact
  ("Llama-3.2-1B InstructWatch" on a third-party AI-model platform, OXMIQ) — not a product-name
  collision in this concept's category, noted rather than dropped.
- **USPTO Trademark Search UI: UNVERIFIED — blocked by tooling.** See caveat below.
- **RDAP `.com`:** AVAILABLE. **`.net`/`.org`/`.ai`/`.dev`/`.app`/`.xyz`: all AVAILABLE.**
- **`.io`/`.co`/`.me` (DNS fallback):** all NXDOMAIN — same asymmetry caveat applies.
- **GitHub** (re-confirmed): no repos, no orgs.
- **EUIPO:** skipped, no credential. **WIPO:** not queried, confirmed blocked.
- **Social handles (manual):** `x.com/instructwatch`, `instagram.com/instructwatch`.

**USPTO search caveat — corrected after a positive-control check (applies to all three finalists
above).** The deep-link URL `tmsearch.uspto.gov/search/search-results?query=<term>` was initially
recorded as returning "No results found" for all three finalists and treated as a real (if unscoped)
search result. **That was wrong, caught only by running a positive control after the fact:** the same
URL pattern with `query=nike` — a term certain to have many live, registered marks — also rendered "No
results found," with a response byte-length (294,537) identical to every finalist's render. A follow-up
attempt with a longer timeout and a `--wait-for-function` predicate waiting for "NIKE" or "results for"
to appear in the page text never resolved (predicate timed out) — ruling out a slow post-load XHR as the
explanation. **Conclusion: the `?query=` parameter is silently ignored by this route; every render in
this run — for `nike` and for all three finalists — is the same default empty-results shell, not an
executed search.** This is a real capability gap in the tooling available for this run (`polyfetch` can
fetch/render a page but cannot type into the page's own search box and submit it, and no working
URL-only search route was found), not a finding about any finalist's trademark status. **Every finalist's
USPTO row above is UNVERIFIED, not "no conflict found."** A human must run these three searches directly
in a browser at `tmsearch.uspto.gov` before treating USPTO as cleared for any of them. See Not legal
advice below.

## The finalist

**Memolint.**

All three finalists cleared every source that actually executed a check — RDAP clean across `.com` plus
six more TLDs, GitHub clean — but **USPTO is unverified for all three** (see caveat above; the tooling
could not actually submit a search), so the decision came down to the qualitative "one you can't stop
saying, clear to hold, fluent to spread" test from step 6, applied only to the sources that did run:

- **Memolint** follows the `-lint` naming convention developers already recognize instantly (ESLint,
  Pylint, markdownlint, actionlint) — the name self-explains "a tool that checks memory/instruction
  files" without further pitch. It is short, two clear morphemes, and accurately matches the candidate's
  actual positioning (flags and audits, doesn't silently mutate anything).
- **Memoprune** was a close second, but "prune" implies autonomous deletion, which overstates what the
  concept does — it flags and versions, a human/agent decides whether to act. This is a real
  positioning-accuracy mismatch, not just a style preference.
- **Instructwatch** is clean everywhere checked but reads as more generic/passive ("watch" is one of the
  most overused suffixes in monitoring-tool naming); it was the weakest "can't stop saying" candidate of
  the three despite being equally clear on the clearance checks.

**Every hedge from the sweep stays attached, not dropped:**
- `.com`/`.net`/`.org`/`.ai`/`.dev`/`.app`/`.xyz` are RDAP-confirmed available; `.io`/`.co`/`.me` are only
  DNS-fallback "not found" — a weaker signal than the RDAP results, not equivalent to "confirmed
  available."
- **USPTO Trademark Search was never actually executed this run** — the render technique used could not
  submit a real search (confirmed by a failed positive control against `nike`); this is unverified, not
  "no conflict found." A human must run this search directly before treating it as cleared.
- EUIPO was skipped (no credential), not silently omitted. WIPO was never queried, per the confirmed
  block already established for this pipeline.
- GitHub re-check is clean as of 2026-09-11; a live namespace can change after this date.

**No domain/GitHub conflicts found for Memolint in the sources actually checked. USPTO trademark status
is unverified, not cleared** — see below.

## Not legal advice

A "no conflict found" or "available" result from this spec's output is a screening signal, not a
trademark-clearance opinion, a freedom-to-operate analysis, or a guarantee a name is safe to adopt,
file, or launch under — it means only that the specific sources checked, in the way checked, on the
date checked, did not surface a conflict. It does not check for confusingly-similar marks, only
identical/near-identical ones in the sources' own search. Before adopting, filing, or launching any
name this spec outputs, a human must independently verify current trademark status directly against
the primary USPTO/EUIPO systems (not this file's summary) and consult qualified trademark counsel.
Repeat this paragraph, not just a pointer to it, in every output document.

## Spec friction encountered during this run (for `.claude/agents/name-brand-vetter.md` correction)

This was the first real end-to-end execution of this spec. Real gaps hit while executing it, not design
review:

1. **Step 3's own header/body mismatch.** It says "Two checks, both free and unauthenticated" but then
   lists three sub-bullets (category clash, `.com` RDAP, GitHub). Category clash is a judgment call with
   no fetch involved — the "two checks" framing is probably intentional (excluding the judgment step from
   the count) but reads as an inconsistency on first pass. Suggest rewording to "one judgment call plus
   two free/unauthenticated checks."
2. **`gh api search/users -f q=<name>` as literally written in the spec's Fetch-tooling section fails.**
   Confirmed empirically: `gh api` defaults `-f` to a POST request, and `search/users` only accepts GET,
   so the exact command in the spec returns `404 Not Found` (a misleading error — reads like "no user
   found," not "wrong HTTP method"). The working form is `gh api -X GET search/users -f q=<name>`. This
   should be corrected in the spec verbatim, since the error message actively misleads about what went
   wrong.
3. **GitHub search API rate limit is not mentioned anywhere in the spec, and this run hit it.** At
   19 Tier-1 candidates × 2 calls each (repos + users search), the authenticated 30-req/min search limit
   (documented in `discovery/2026-09-11T200013Z-naming-sources-research.md` §6, but not carried into the
   spec itself) was hit on the last 4 candidates (`ledger`, `anchor`, `tripwire`, `custodian`), which had
   to be judged on RDAP + known-trademark signal alone rather than a fresh GitHub search. At the spec's
   own stated cardinality ceiling (30 generated candidates), this will recur reliably. Suggest the spec
   add explicit pacing guidance (e.g., a short sleep between GitHub calls, or batching repos+users search
   into fewer calls) for Tier 1.
4. **`polyfetch`'s handling of a "domain available" RDAP 404 is not what a literal reading of the spec
   implies.** The spec's Tier-1 bullet says "a 404 response means unregistered/available" as if the tool
   call simply returns a 404 status in a normal JSON payload. In practice, `polyfetch fetch ... --json`
   on an RDAP 404 exits non-zero and returns an *error* object (`{"error_type": "GoneError", "status":
   404, ...}`), not a plain success payload — and without pinning `--tier httpx --max-attempts 1`, the
   tool's fallback chain may escalate to slower tiers on what is actually a correctly-terminal 404.
   Worth stating explicitly in the Fetch-tooling section.
5. **The RDAP base URLs for TLDs beyond `.com` are not given anywhere in the spec.** The spec's Tier-2
   bullet names the TLD list (`.net`/`.org`/`.ai`/`.dev`/`.app`/`.xyz`) but not how to find each
   registry's RDAP endpoint. This run fetched and parsed `data.iana.org/rdap/dns.json` itself (documented
   in the research file, but not pointed to operationally from the spec's own Tier-2 step). Suggest the
   spec name this bootstrap URL directly as the lookup mechanism.
6. **The `.io`/`.co`/`.me` "plain DNS lookup" fallback names no actual tool or technique.** This sandbox
   has no confirmed `dig`/`nslookup` binary; this run used Google's DNS-over-HTTPS endpoint
   (`dns.google/resolve?type=NS`) via `polyfetch`, which fits the existing toolchain but is a specific
   choice the spec doesn't make. Worth naming explicitly so the next run doesn't have to invent it.
7. **The most important gap: the USPTO Trademark Search UI cannot actually be searched with the tooling
   this spec names, and the failure mode is silent, not an error.** Fetching `tmsearch.uspto.gov` alone
   renders only the search *form* with no query executed. Guessing a deep link
   (`/search/search-results?query=<term>`) looked like it worked — it renders a real results-shell page
   and shows "No results found" — but this was a **false positive**, caught only by running a positive
   control (`query=nike`, a term certain to have many live marks) after the fact: `nike` produced the
   identical byte-length page and the identical "No results found" text, and a longer-timeout
   `--wait-for-function` probe for "NIKE" appearing anywhere in the page text never resolved. **The
   `?query=` parameter is silently ignored by this route — every render (finalists and the `nike`
   control alike) is the same default empty-results shell, not an executed search.** `polyfetch` has no
   click/type capability to drive the SPA's own search box, and no working URL-only search route was
   found. This means: (a) the spec's step 5 instruction to read USPTO for "one render per finalist" is
   currently **not achievable** with the tooling named in Fetch tooling — either a different tool
   (real browser automation with click/type) needs to be added for this one leg, or the spec must say
   USPTO is a manual/owner step like social handles; (b) the spec should require a positive-control check
   (a term certain to have live marks) before treating any "no results" render as real, since a silently
   ignored query parameter produces a page that is otherwise indistinguishable from a genuine empty
   result set — exactly the "tool answered a different question" failure mode `claim-verification.md`
   warns about, caught here only by an advisor review, not by the run's own first pass.

8. **Two deviations from the spec's literal wording, logged rather than silently made.** The spec's
   Fetch-tooling section names bare `gh search repos <name>`; this run used
   `gh search repos <name> --match name --limit 10 --json fullName,description,stargazersCount,updatedAt`
   instead, because the bare form also matches descriptions/READMEs (noisy for a common word like
   "lint") and returns unstructured text. Recommend the spec name `--match name` explicitly. Separately,
   step 4's "no external lookups" framing is for the judgment pass (step 2); this run used one cheap,
   no-gate `WebSearch` pass (already permitted for Tier 2) across all 4 remaining shortlist survivors
   *before* narrowing to the final 2-3, to catch a category-clash problem (Staleflag vs. "stale flag
   management," an established feature-flag term of art) ahead of committing to the expensive USPTO/RDAP
   sweep. This stayed within "never run Tier 2 [the expensive legs] against more than 2-3 names," but the
   spec doesn't explicitly bless a cheap-leg-only pre-check across a slightly wider set — worth naming as
   an allowed pattern if the spec agrees it's a good idea.
9. **Step 3's own instruction to read the `registration` event date for a taken `.com`** (useful context
   even for a cut name, per the spec) was skipped this run for the 13 candidates whose `.com` came back
   taken — `--json` doesn't include the response body (see gap 4 above), so it would have required a
   second `--show-body` fetch per taken name; not done here for time, noted rather than silently omitted.

Not legal advice. This file's Not-legal-advice section above governs the naming output; this section is
process feedback on the spec, not part of the naming result itself.
