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
3. **Clear — Tier 1, cheap filter, every surviving candidate.** One judgment call plus two free,
   unauthenticated checks (see `discovery/2026-09-11T200013Z-naming-sources-research.md`
   §Recommendation):
   - **Category clash** — judgment: does the name already mean something else prominent in the
     concept's stated category? (Not a trademark search — that's Tier 2.)
   - **`.com` availability via RDAP** — `rdap.verisign.com/com/v1/domain/<name>.com` (no auth; a 404
     response means unregistered/available, a 200 with an `events` array means taken). **Tooling
     note, confirmed 2026-09-11**: `polyfetch fetch ... --json` on an RDAP 404 exits non-zero and
     returns an error object, not a plain success payload — treat that as the "available" signal
     itself, don't expect a 200-shaped JSON body. Pin `--tier httpx --max-attempts 1` so a correctly-
     terminal 404 doesn't trigger a slower-tier fallback retry. Reading the `registration` event's
     date on a *taken* name is optional context, not required — skip it for candidates already cut on
     this check alone unless time allows a follow-up `--show-body` fetch.
   - **GitHub name collision** — `gh search repos <name> --match name --limit 10 --json
     fullName,description,stargazersCount,updatedAt` (the `--match name` flag matters: a bare `gh
     search repos <name>` also matches descriptions/READMEs, noisy for a common word) and `gh api -X
     GET search/users -f q=<name>` (**note the explicit `-X GET`** — `gh api` defaults `-f` to POST,
     and `search/users` only accepts GET; omitting `-X GET` returns a misleading `404 Not Found` that
     reads like "no such user" rather than "wrong HTTP method"). Both already authenticated in this
     workspace, same pattern `build-pattern-scanner` uses. Flag heavy existing use in the same space,
     don't auto-cut on any hit (a dormant unrelated repo isn't disqualifying). **Rate limit**: the
     authenticated GitHub search endpoint is 30 req/min (confirmed in
     `discovery/2026-09-11T200013Z-naming-sources-research.md` §6) — at 2 calls/candidate this is
     reachable well within the spec's own 15-30-candidate generation range. Pace calls (a short sleep
     between candidates) or batch, rather than firing all candidates' calls back to back; if the limit
     is hit anyway, judge the remaining candidates on RDAP + known-trademark/category-clash signal
     alone and say so plainly rather than silently skipping the GitHub row.
   Narrow to a shortlist (target: 5-8 names) carrying every flag from steps 2-3 forward, not silently
   dropped.
4. **Shortlist to 2-3 finalists** from the survivors, by judgment against "the one you can't stop
   saying" — state the reasoning, don't just declare a winner. **A cheap, no-gate `WebSearch` pass
   across the shortlist (not just the finalists) is allowed here** if it catches something Tier 1's
   judgment-only category-clash check would miss (e.g. an established term-of-art collision in an
   adjacent product category) — this stays within "never run the *expensive* Tier-2 legs [USPTO/RDAP
   TLD spread] against more than 2-3 names," since `WebSearch` itself is already ungated at any
   cardinality.
5. **PR-launch sweep — Tier 2, finalists only.** Never run this against more than 2-3 names:
   - **Web search** — this repo's own `WebSearch` tool, no gate.
   - **USPTO Trademark Search — do not automate this; manual/owner checklist item, same treatment as
     social handles below.** `tmsearch.uspto.gov` is confirmed not ID.me/login-gated (research pass
     below), but a real first run (2026-09-11) confirmed the tooling available to this spec **cannot
     actually execute a search on it**: `tmsearch.uspto.gov`'s own page is a client-rendered Angular
     SPA with no URL-only search route (a plausible-looking deep link,
     `/search/search-results?query=<term>`, silently ignores the `?query=` parameter and always
     renders the same empty-results shell — confirmed via a positive control against `query=nike`, a
     term certain to have live marks, which rendered byte-identical to every finalist). `polyfetch`
     has no click/type capability to drive the SPA's own search box. **This failure is silent, not an
     error** — the false render is indistinguishable from a genuine "no results found" without a
     positive control, so a "no conflict found" claim built on it would be actively wrong, not merely
     unverified. List it in the output as a manual step with the exact search terms to run: "check
     `tmsearch.uspto.gov` directly for `<name>` before treating USPTO as cleared." If a future session
     adds real browser click/type capability and wants to automate this leg again, it must run a
     positive-control check (a term certain to have live marks) before trusting any "no results"
     render — never re-enable this as an automated check without that guard.
   - **EUIPO** — only if `dev.euipo.europa.eu` API credentials are provisioned (owner-gated, free
     self-service OAuth2/OIDC — **never scrape `euipo.europa.eu/eSearch/` directly**, it's both
     robots.txt-disallowed and covered by a blanket TDM copyright opt-out). If no credential exists,
     state EUIPO as skipped, not silently omitted.
   - **WIPO** — **do not query WIPO Global Brand Database at all**, confirmed blocked (active
     technical captcha on every path, plus a PATENTSCOPE-shaped ToS clause) — leave it out of the
     sweep entirely rather than attempting and reporting a failure.
   - **Domain age + TLD spread via RDAP** — the finalist's `.com` (re-confirm from Tier 1) plus
     `.net`/`.org`/`.ai`/`.dev`/`.app`/`.xyz` (all RDAP-covered). Find each TLD's RDAP base URL from
     the IANA bootstrap registry, `data.iana.org/rdap/dns.json` (fetch fresh, don't hardcode — it's
     free/unauthenticated and current as of the naming-sources research). For `.io`/`.co`/`.me`
     (confirmed absent from that bootstrap), fall back to a plain DNS lookup via Google's
     DNS-over-HTTPS endpoint (`dns.google/resolve?type=NS&name=<name>.<tld>`, fetched via
     `polyfetch`, no other DNS tool confirmed available in this sandbox) — this only proves
     registered/delegated (an NXDOMAIN/Status-3 response means no NS records found, not "available"
     in the RDAP sense), never a confirmed "available" the way an RDAP 404 is — state that asymmetry
     in the output every time it applies, don't present a DNS-fallback result with the same confidence
     as an RDAP one.
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
../polyfetch-scrape polyfetch fetch <url> --json`, pinning `--tier httpx --max-attempts 1` for RDAP
so a correctly-terminal 404 doesn't escalate to a slower fallback tier) or `gh api`/`gh search`
respectively, same pattern as the rest of this pipeline. **USPTO's Trademark Search UI is not fetched
by this spec at all** — confirmed 2026-09-11 that `polyfetch` (even with a patchright/browser-render
tier) cannot submit a real query against its client-rendered Angular SPA, and a plausible-looking
deep-link URL silently ignores its own query parameter rather than erroring — see the manual-step
instruction in step 5 above; do not attempt to re-automate this leg without first adding real
click/type browser capability and a positive-control check. See
`discovery/2026-09-11T200013Z-naming-sources-research.md` for the full access research this spec's
Clear/PR-launch-sweep sections are built from, and
`names/2026-09-11T203709Z-contextlint-names.md`'s "Spec friction" section for the first real run that
surfaced the USPTO gap and the other tooling corrections folded into this spec.

## If a source is blocked or uncertain

Same four-state discipline as the rest of this pipeline (blocked / empty / found / excluded-by-scope)
plus a fifth this spec specifically needs: **uncertain** — the naming-sources research pass left the
USPTO TSDR *bulk API*'s current ID.me requirement genuinely unresolved (the UI itself is confirmed
open; only the separate bulk API is uncertain). This spec never calls the TSDR bulk API — it uses the
manual-checklist step above for the UI — so this doesn't block anything today, but if a future change
ever adds bulk API use, that assumption must be re-verified at the point of use, not inherited from
the research file. **A sixth state for this spec specifically: verified-false-positive** — a check
that appears to return a real result but is confirmed (via a positive control) to be silently
answering a different question than asked, e.g. the USPTO deep-link finding above. Treat a
verified-false-positive the same as blocked (state it plainly, never report the fabricated result),
but flag it more loudly, since it's actively misleading rather than merely absent.

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
