# Naming/trademark/domain sources — access research pass, 2026-09-11

Question tested: for arc 0004's `name-brand-vetter` two-tier design (Tier 1 "Clear" — cheap, all
candidates; Tier 2 "PR-launch sweep" — expensive, finalists only), what is the real access state of
each source the taxonomy names — USPTO trademark search, EUIPO, WIPO Global Brand Database, RDAP,
TLD spread, GitHub, social handles, general web search? The single most load-bearing question: does
USPTO's **trademark** search share the same ID.me identity-verification gate
`examples/2026-09-07T043304Z-patent-signal-research/patent-signal-research.md` found on USPTO's
**patent** Open Data Portal (ODP)?

Same discipline as that precedent file: every ToS/robots.txt/access claim below is fetched fresh this
pass (`polyfetch-scrape` raw fetch, or the patchright/browser-render tier where a page is a JS shell)
and quoted verbatim, not paraphrased from memory or a WebSearch snippet alone — except where explicitly
marked "WebSearch-corroborated, not raw-verified" because a raw fetch could not reach the actual text
(the same limitation the precedent file hit on one USPTO redirect target).

## 1. USPTO trademark search — the single most important question

**Verdict on the public search UI: NOT gated. Verdict on the TSDR API: UNCERTAIN (see below) — not
resolved either way, not assumed.**

- **Trademark Search (`tmsearch.uspto.gov`) and TSDR (`tsdr.uspto.gov`), individual lookups, raw-fetched
  fresh**: both load in full — `tmsearch.uspto.gov` (125,660 bytes, an Angular SPA shell titled
  "Trademark search") and `tsdr.uspto.gov` (56,092 bytes, a fully server-rendered HTML search form
  titled "Trademark Status & Document Retrieval") — **neither page contains any "sign in", "login",
  "ID.me", "MFA", or "account" string** (checked via exact substring search over the saved raw body,
  zero hits on both). A page-load with no login prompt doesn't itself rule out a gate at the point of
  *use* (submitting an actual search) — the patent ODP's own gate, per the precedent file, bites at use,
  not load. To settle that distinction for trademark search, a second, independent
  source was checked: raw-fetched `www.uspto.gov/trademarks/search` (137,767 bytes, a real
  server-rendered Drupal page, not a shell) contains this **verbatim** passage: *"Log into your
  USPTO.gov account for a better search experience. Logging in using the Sign in link in the top right
  corner helps you avoid errors when the system is handling heavy traffic. It'll also give you access
  to even more features and enhancements in the future, including options to customize your search
  experience based on your preferences."* — explicitly framed as optional/recommended ("for a better
  experience," "helps you avoid errors"), never as required. WebSearch corroborates the same reading in
  its own words ("sign-in is not required. The trademark search is free and no account is required").
  **This directly contradicts nothing found on the patent side and confirms trademark search does not
  share the ODP account/MFA/ID.me gate for individual UI lookups.**
- `tmsearch.uspto.gov/robots.txt` and `tsdr.uspto.gov/robots.txt` both raw-fetched as HTTP 404 (no
  robots.txt file exists on either host) — absence, not a block; noted per this repo's own
  "state what was searched" discipline rather than treated as silence-equals-clearance.
- `www.uspto.gov/terms-use` and `www.uspto.gov/terms-service` both raw-fetched as HTTP 404, same
  dead-link finding the patent-research pass already made for the sibling `/terms-service` path — no
  separate trademark-specific ToS page found at either guessed URL.
- **The TSDR API (`tsdrapi.uspto.gov`) is a real, separate gate — but its current strength is
  genuinely uncertain, not simply "the same as ODP" or "lighter than ODP."** Facts established this
  pass:
  - A direct, unauthenticated probe of a real TSDR endpoint
    (`tsdrapi.uspto.gov/ts/cd/casestatus/sn88888888/info.xml`) returned a raw-fetched **HTTP 401
    AuthRequired** — confirming an API key is required, not open access. Even `tsdrapi.uspto.gov/robots.txt`
    itself returned 401 — this host gates every path behind the key, unlike ODP's public-but-empty shell.
  - WebSearch (corroborated across independent results, not a single source): "TSDR requires a
    TSDR-specific key (the ODP key does not work)... hosted on a separate server with a separate API
    key from the rest of ODP" — administratively distinct from the patent-side ODP `X-API-Key`.
  - Raw-fetched, from the patent-research precedent's own primary source
    (`blog.patentriff.com/p/not-so-open-uspto-to-require-registration`), the FAQ's own enumeration of
    what migrated into the ID.me-gated ODP key (quoted verbatim): *"The FAQ lists the inclusion of
    Patent Trial and Appeal Board (PTAB) proceeding data, Patent File Wrapper (PFW) datasets previously
    found in the Patent Examination Data System (PEDS), Final Petitions Decisions (FPD), and the Bulk
    Data Storage System (BDSS) (FAQ, ¶ 6)."* — **TSDR/trademark is not named in this enumeration.** The
    same article's opening line does say ODP "has functioned as the unified data platform... providing
    publicly accessible patent **and trademark** datasets" generically, but every specific, itemized
    fact in the article (the June 18/Aug 18 2026 dates, the ¶16/¶79 FAQ citations, the PTAB/PFW/FPD/BDSS
    list) is patent-specific — trademark is never independently confirmed as inside the same gate by
    this source.
  - The official **TSDR API Key Manager user guide** (PDF, `data.uspto.gov/documents/documents/
    TSDR-API-Key-Manager-user-guide.pdf`, read directly, not OCR'd from a summary) describes the
    registration flow as: sign in to `account.uspto.gov/api-manager/` with a plain **USPTO.gov account**
    (create one if needed), select "TSDR (Trademark Search and Data Retrieval) API," press "Request API
    key" — **no ID.me, no MFA, no identity-verification step of any kind mentioned anywhere in this
    document.** This is a real primary source, but it is dated **September 2020** — nearly six years
    before the 2026 ID.me rollout the patent-research pass documented — so it cannot be treated as
    current authority on its own.
  - **This pass could not raw-verify the *current* (2026) TSDR key-request flow.** Both
    `account.uspto.gov/api-manager/` and the newer `data.uspto.gov/apikey` were fetched, including with
    patchright rendering (`--wait-until networkidle`) to let client-side JS run — both returned an
    Angular app that never mounts past an empty `<app-root></app-root>` under this session's fetch
    tooling, the same class of limitation the patent-research precedent hit on a different USPTO
    redirect target and declined to force further. WebSearch results here are genuinely mixed and not
    trustworthy as a tiebreaker: one summary states generally "accessing USPTO APIs requires... ID.me,"
    but every citation trace leads back to the same patent-specific FAQ language quoted above, not a
    TSDR-specific confirmation — this is the exact "search artifact, tool answered a different question"
    failure mode this repo's `claim-verification.md` rule warns against, and is called out here rather
    than resolved by picking the more alarming-sounding answer.
  - **Answer to the team lead's question, stated plainly: UI search — confirmed NO ID.me gate (same
    confidence level as the patent-research precedent's strongest findings). TSDR bulk/API access — an
    API key is confirmed required (401 probe), but whether that key's *issuance* currently requires
    ID.me is UNCERTAIN — evidence leans toward "no" (2020 guide, absence from the ID.me FAQ's own
    itemized list) but is not primary-source-confirmed for 2026. Do not build anything that assumes
    either answer; if the spec ever needs the TSDR API, that assumption must be verified again at
    the point of use, not inherited from this file.**

## 2. EUIPO eSearch plus

**Verdict: website — blocked for automated use (robots.txt + a broad ToS opt-out). Official API — found,
owner-gated, free self-service, low friction.**

- `euipo.europa.eu/robots.txt`, raw-fetched: explicitly disallows the search surface itself —
  *"disallow: /eSearch/"* (also `/eSearchCLW/`, `/sim/`, `/eurolocarno/`, `/designclass/`,
  `/orphanworks/` — every EUIPO database frontend, the same shape as this repo's prior finding that a
  site's own database paths get individually named in robots.txt while the rest of the site stays open).
- EUIPO's own Legal Notices page (`euipo.europa.eu/en/info/legal-notices`), raw-fetched (288,697 bytes,
  server-rendered, not a shell), contains this **verbatim** clause: *"except as specifically and
  explicitly authorised by the EUIPO in writing, the EUIPO expressly reserves and opts out of any
  copyright exception for text or data mining, web scraping or similar reproductions, extractions or
  uses for whatever purpose other than scientific research ('TDM') of any Information. This reservation
  applies to any purposes, including the training, development or commercialisation of any artificial
  intelligence system, and by any means, including bots, scrapers or other automated processes, to the
  fullest extent permitted by applicable law in all relevant jurisdictions, including for the purposes
  of Article 4(3) of Directive (EU) 2019/790..."* — **this is a blanket EU-copyright-law TDM opt-out,
  not conditioned on volume the way WIPO's clause below is** — a single automated fetch of one name
  would fall under it exactly as a bulk crawl would, unless "specifically and explicitly authorised... in
  writing." That carve-out is exactly what the official API (next bullet) provides.
- **The official EUIPO Trademark Search REST API is a separate, explicitly-provisioned door**:
  `dev.euipo.europa.eu`, raw-fetched (real, server-rendered "API Portal," `user-registry-oidc`, anonymous
  role by default) — confirms this is EUIPO's genuine OAuth2/OIDC developer portal, not a placeholder.
  WebSearch corroborates (not raw-verified past the portal's own landing page, deprioritized since
  registration wasn't completed): free registration "in under 2 minutes," subscribe to the Trademark
  Search API. **One caveat on a specific number**: a "1,000 free EUIPO requests/month" figure appeared
  in these WebSearch results, but tracing it back shows it was stated as a *third-party reseller's* own
  tier (Signa/Apify-style wrapper services), not EUIPO's own quota — EUIPO's own rate limit on
  `dev.euipo.europa.eu` was not independently confirmed this pass, and this file does not assert a
  number for it. **This is the compliant path** — registering for and
  using this API is the "written authorisation" the Legal Notices clause itself carves out as an
  exception to the TDM opt-out, the same free-self-service shape as EPO OPS in the patent-research
  precedent (lower friction than USPTO's ID.me gate, comparable to `PRODUCTHUNT_API_TOKEN`).
- **Do not scrape `euipo.europa.eu/eSearch/` directly, even for a single finalist name** — both the
  robots.txt disallow and the TDM opt-out cover it regardless of query volume. If EUIPO coverage is
  wanted, it must go through `dev.euipo.europa.eu`'s API, an owner-gated (but low-friction) credential.

## 3. WIPO Global Brand Database

**Verdict: blocked — both by ToS (near-identical clause to PATENTSCOPE's) and by an active,
technical bot-detection captcha confirmed on every path tried, including `robots.txt` itself.**

- `branddb.wipo.int/robots.txt`, raw-fetched: did **not** return a robots.txt file at all — it returned
  an ALTCHA (proof-of-work CAPTCHA widget) HTML challenge page instead, embedding
  `challengeurl="https://api.branddb.wipo.int/captcha"` and a client-side redirect back to the
  originally-requested path only after the captcha's `statechange` event reports `"verified"`. The exact
  same challenge page (1,700 bytes) was returned for `branddb.wipo.int/en/`, the site's own homepage —
  **this is an active, technical, whole-site block on any non-captcha-solving client**, the same shape
  this repo's history already established for Lens.org in the patent-research pass, not a policy-only
  restriction.
- WIPO's own **Terms and Conditions for the Use of the WIPO Global Brand Database** (raw-fetched,
  `wipo.int/en/web/global-brand-database/terms_and_conditions`, 96,267 bytes, server-rendered) contains
  this **verbatim** clause — near-identical in structure to the PATENTSCOPE clause the patent-research
  pass already quoted, confirming WIPO reuses the same template across its database products:
  > "the Global Brand Database is provided only for public information purposes and the User is
  > forbidden to: (a) use the service excessively to the detriment of other Users (for that matter, more
  > than 10 search related actions per minute from a single IP address can be considered excessive); (b)
  > perform automated queries; (c) perform bulk acquisition, bulk downloading, and bulk storing of data;
  > (d) perform bulk copying, bulk reformatting, bulk sharing and bulk redistributing of data; (e)
  > perform web scraping; (f) perform any other abusive use degrading or circumventing the service."
- Unlike EUIPO's clause above, this one *is* explicitly volume-conditioned in its own text (the ">10
  search actions/minute" framing, and "bulk" qualifying most of the list) — but it doesn't matter here,
  because the technical captcha block (confirmed directly, not inferred from the ToS) already forecloses
  any automated access regardless of volume, the same "no workaround on a confirmed block" standing rule
  this repo already applies to WIPO PATENTSCOPE.
- **No separate Global Brand Database API was found or searched for this pass** — the site's own ToS
  frames it as a public-information website, not an API product (distinct from PATENTSCOPE, which has a
  paid Web Service API); not independently confirmed absent, just not pursued given the technical block
  already settles the access question for this pipeline's purposes.
- **Verdict: BLOCKED, both layers.** Note for the spec: EUIPO's own **TMview** (`tmdn.org`), which
  aggregates EUIPO + national EU offices + USPTO + WIPO marks into one search front-end, is a possible
  alternate door for multi-jurisdiction coverage if WIPO GBD's absence is felt — not checked this pass
  (its terms are unverified), named here only as a candidate for a future pass, not a recommendation to
  use it today.

## 4. RDAP

**Verdict: found, free, unauthenticated, and genuinely low-friction for this pipeline's low-volume use
case — with a real, TLD-specific coverage gap (see §5).**

- `data.iana.org/rdap/dns.json` (the IANA RDAP bootstrap registry), raw-fetched fresh: `version: "1.0"`,
  `publication: "2026-09-09T23:00:03Z"` (two days before this research pass — genuinely current), 590
  service entries covering roughly 1,200 TLDs.
- A live, real lookup — `rdap.verisign.com/com/v1/domain/example.com`, raw-fetched, unauthenticated, no
  API key — succeeded and returned a full RDAP JSON record. **The creation-date/"domain age" field is
  confirmed to live exactly where expected**: `events: [{"eventAction": "registration", "eventDate":
  "1995-08-14T04:00:00Z"}, {"eventAction": "expiration", ...}, {"eventAction": "last changed", ...}]`.
- **ToS/rate-limit language, raw-fetched from the link the RDAP response's own `notices` array points
  at** (`verisign.com/domain-names/registration-data-access-protocol/terms-service/index.xhtml`, which
  301-redirects to `verisign.com/legal-center/rdap-terms/` — followed and fetched, 101,911 bytes,
  server-rendered): *"you agree that you will use this data in Verisign's RDAP database only for lawful
  purposes and that under no circumstances will you use the data to: (1) allow, enable, or otherwise
  support the transmission of mass unsolicited, commercial advertising or solicitations...; or (2)
  enable high volume, automated, electronic processes that send queries or data to the systems of
  Verisign or an ICANN-accredited registrar, except as reasonably necessary to register domain names or
  modify existing registrations."* — the restriction is explicitly conditioned on **"high volume"**
  automated querying, not automated querying as such. A handful of lookups per naming run (Tier 1: one
  `.com` check per generated candidate; Tier 2: a small TLD spread for 2-3 finalists) reads as squarely
  outside this clause, not a gray area.
- No separate rate-limit number is stated anywhere in the notice or the RDAP response itself — noted
  plainly as absent rather than invented; a real production integration should still add polite spacing
  between requests as a courtesy, not because a specific limit was found.

## 5. TLD spread

**Verdict: RDAP covers most, but not all, of the TLDs a naming run would care about — a real,
now-quantified gap, not a hypothetical one.**

- Checked against the same fresh IANA bootstrap fetched in §4 for a realistic startup-naming TLD set:
  **present** (has a bootstrap RDAP server): `.com`, `.net`, `.org`, `.ai`, `.dev`, `.app`, `.xyz`.
  **Absent** (no entry anywhere in the bootstrap's 590 service groups, checked by exact match, not a
  substring/case bug): `.io`, `.co`, `.me` — three TLDs commonly used by exactly the kind of
  small/dev-tool product this pipeline's candidates skew toward (`Contextlint`-style names, per the
  plan's own first-test-run target).
- **For an RDAP-covered TLD**, the same low-friction, unauthenticated, per-domain query from §4 applies
  directly — just substitute the registry's own RDAP base URL from the bootstrap entry.
- **For an RDAP-gap TLD** (`.io`/`.co`/`.me` confirmed, likely others), the compliant fallback is a
  plain DNS query for the domain's NS/SOA records (standard DNS resolution, no ToS surface at all,
  the same class of "core internet infrastructure protocol" as RDAP itself) — this proves the domain
  **is delegated/registered**, but proves nothing about availability or age if it *isn't* found (a
  domain can be registered-but-unused, showing no NS records, while still being taken). **This asymmetry
  must be stated plainly in the new spec, not glossed over**: RDAP-covered TLDs get a real
  registered/available + age answer; RDAP-gap TLDs only ever get a weaker
  "found-in-DNS-so-definitely-taken" signal, never a confident "available."
- Registrar bulk-check APIs (Domainr, Namecheap, GoDaddy) were not researched in depth per the
  team lead's scope instruction — named here only as the owner-gated alternative if full TLD-spread
  coverage (including `.io`/`.co`/`.me`) is wanted without the DNS-fallback asymmetry above.
  **Lowest-friction compliant option overall: RDAP first, DNS-delegation fallback second, both free** —
  no registrar credential needed for the coverage this pipeline actually needs (a bounded, named TLD
  list, not exhaustive bulk-registrar-grade coverage).

## 6. GitHub

**Verdict: already clear, no new work.** Confirmed live this pass: `env -u GH_TOKEN -u GITHUB_TOKEN gh
auth status` succeeds in this workspace (account `qte77`, scopes `gist`, `read:org`, `repo`,
`workflow`) — the same authenticated `gh` CLI `build-pattern-scanner.md` step 1 and `signal-discoverer.md`
step 3 already use, at the same documented rate limits (10 req/min unauthenticated, 30 req/min
authenticated for the search endpoint). Checking whether a candidate name is already in wide use as a
GitHub org or repo name (`gh search repos <name>`, `gh api search/users -f q=<name>` for orgs) raises no
new ToS question and needs no new credential.

## 7. Social-handle availability

**Verdict: confirmed blocked for X/Twitter (raw-verified); strongly indicated blocked for Instagram
(WebSearch-corroborated, not raw-verified — this session's fetch tooling could not reach the actual
terms text, see below). Both become a manual/owner step in the new spec, as the plan's design already
assumed — this section confirms rather than merely repeats that assumption.**

- **X/Twitter, `x.com/robots.txt`, raw-fetched in full (2,678 bytes)**: named bots get narrow,
  specific allow/disallow rules (Googlebot/Bingbot, facebookexternalhit), several AI-specific crawlers
  are explicitly and fully blocked (`User-agent: Google-Extended` / `Disallow: *`; same for
  `FacebookBot`, `Discordbot`, `meta-webindexer`, `meta-externalagent`, `meta-externalads`,
  `meta-externalfetcher`), and the file ends with a catch-all for every other client, quoted verbatim:
  *"# Every bot that might possibly read and respect this file\nUser-agent: *\nDisallow: /"* — a
  one-off automated GET to check a handle's existence falls squarely under this blanket disallow, not
  under any of the named-bot carve-outs above it.
- **X/Twitter ToS**, raw-fetched (`x.com/en/tos`, 288,243 bytes, server-rendered), verbatim: *"access or
  search or attempt to access or search the Services by any means (automated or otherwise) other than
  through our currently available, published interfaces that are provided by us... (NOTE: crawling or
  scraping the Services in any form, for any purpose without our prior written consent is expressly
  prohibited)"* — explicit, unconditional (no volume threshold, unlike WIPO's clause), and specifically
  names "automated or otherwise," which covers a single handle-availability check exactly as much as a
  bulk crawl.
- **Instagram** (a Meta product — a different company from X/Twitter above; note §7's own X robots.txt
  quote shows X *blocking* Meta's crawlers, not the reverse, so no "sibling posture" is being inherited
  here): two independent raw-fetch attempts this pass —
  `instagram.com/robots.txt` and `instagram.com/legal/terms/` — both returned Instagram's own
  client-rendered web-app shell (621,580 and 621,590 bytes respectively, near-identical generic HTML,
  no actual robots.txt syntax or terms text present in either body) rather than the requested content;
  a third attempt at `help.instagram.com/581066165581870` returned a generic Meta "Sorry, something went
  wrong" error page. This is the same class of tooling limitation the patent-research precedent hit on
  a USPTO redirect target and explicitly declined to force further — **marked here as WebSearch-corroborated, not raw-verified**, per this file's own stated labeling discipline, rather than silently treated as equivalent to a raw-verified quote. WebSearch corroboration (Meta's own widely-documented Platform
  Terms language, consistent across independent search results): *"You may not access or collect data
  from our Products using automated means (without our prior permission) or attempt to access data you
  do not have permission to access."* Combined with this pipeline's own standing "assume blocked,
  don't chase a technical workaround" discipline, this is treated as **effectively blocked** for
  planning purposes — but flagged explicitly as the one verdict in this file resting on
  WebSearch-corroboration rather than a raw-fetched primary quote, should a future pass want to try
  harder (e.g., a heavier patchright render) to settle it definitively.
- **Conclusion for the spec**: social-handle availability checking is a manual/owner step, not an
  automated one, for both platforms checked — exactly the design assumption `docs/plans/0004-name-brand-vetting.md`
  already carried into its design section. This file's job was to confirm, not merely repeat, that
  assumption, and it does: X is confirmed via two independent raw-fetched primary sources
  (robots.txt + ToS); Instagram is WebSearch-corroborated pending a raw-fetch retry if ever needed.

## 8. General web search

**Already available, no gate.** This repo's own `WebSearch` tool already covers the "web search" leg of
the PR-launch sweep — no research, credential, or access work needed here; listed only so the synthesis
below is complete.

## Synthesis

### Accessibility, summarized

| Source | State | Gate |
|---|---|---|
| USPTO Trademark Search UI (`tmsearch.uspto.gov`/`tsdr.uspto.gov`) | **found, no credential** | none — confirmed no ID.me/MFA/login requirement for individual lookups |
| USPTO TSDR API (`tsdrapi.uspto.gov`) | found, owner-gated | API key confirmed required (401 probe); whether current key issuance needs ID.me is **UNCERTAIN**, not resolved this pass |
| EUIPO eSearch plus (website) | **blocked** for automated use | robots.txt `disallow: /eSearch/` + a broad TDM copyright opt-out banning bots/scrapers for any non-scientific-research purpose |
| EUIPO Trademark Search API (`dev.euipo.europa.eu`) | found, owner-gated, low friction | free self-service OAuth2/OIDC registration; a "1,000/month" figure found in search results is a third-party reseller's tier, not a confirmed EUIPO quota |
| WIPO Global Brand Database | **blocked** | active ALTCHA captcha on every path (confirmed directly, including on `robots.txt` itself) + a ToS clause near-identical to PATENTSCOPE's |
| RDAP (IANA bootstrap + registry endpoints, e.g. Verisign for `.com`) | **found, no credential** | none for this pipeline's low-volume use — ToS bans only "high volume, automated" querying |
| TLD spread via RDAP | found for most TLDs, **gap confirmed** for `.io`/`.co`/`.me` | none for RDAP-covered TLDs; DNS-delegation fallback (weaker signal) for gaps; registrar bulk APIs are the owner-gated alternative, not pursued |
| GitHub (`gh` CLI) | **found, no credential** | already authenticated in this workspace, reuses existing pattern |
| X/Twitter handle check | **blocked** | robots.txt blanket `Disallow: /` for unnamed clients + ToS bans automated access "by any means" |
| Instagram handle check | **blocked** (WebSearch-corroborated, not raw-verified) | Meta Platform Terms bans automated data collection without prior permission |
| General web search | **found, no credential** | this repo's own `WebSearch` tool already covers it |

### Recommendation for the new spec

**Tier 1 — "Clear" (cheap, all candidates)**: RDAP `.com` lookup (free, unauthenticated, §4) + GitHub
org/repo name search (free, already authenticated, §6). Both are genuinely cheap and unauthenticated at
whatever cardinality the generated-candidate list reaches. **Do not** put the USPTO trademark UI or
EUIPO in Tier 1 — the UI is a JS-heavy page requiring a patchright render per lookup (fragile and slow
at "all candidates" cardinality, the same "wrong cardinality for an expensive/fragile step" discipline
this pipeline already applies elsewhere), and EUIPO's compliant path is an owner-gated API credential
that shouldn't be assumed provisioned by default.

**Tier 2 — "PR-launch sweep" (finalists only, 2-3 names)**: web search (§8, no gate) + USPTO Trademark
Search UI read via a patchright render, one finalist at a time (§1 — confirmed open, no ID.me; do not
call the TSDR bulk API given the unresolved ID.me question). **One hedge that must survive into the
spec itself, not just this file**: "no robots.txt found" and "no separate ToS page found at two guessed
URLs" (§1) is an absence-of-restriction finding, not an affirmative "ToS permits automation" finding —
treat the UI as open based on the positive evidence (optional-sign-in language, full page render with no
login gate), not on the absence checks alone. + RDAP TLD spread across the finalist names,
including the DNS-fallback for `.io`/`.co`/`.me` (§4–5) + GitHub, re-confirmed (§6) + EUIPO, **only if**
the pipeline owner provisions the free `dev.euipo.europa.eu` API credential — otherwise state EUIPO as
skipped, not silently omitted (§2) + social handles as an explicit **manual/owner** checklist item, never
an automated step (§7). **Leave WIPO Global Brand Database out of both tiers entirely** — it is
confirmed blocked at the technical layer, the same "no workaround on a confirmed block" rule this repo
already applies to WIPO PATENTSCOPE and Lens.org; TMview (§3) is a named-but-unverified candidate for a
future pass if multi-jurisdiction coverage beyond USPTO/EUIPO is ever wanted.

## Not legal advice — read this before acting on anything above

This file is signal for building an unattended screening pass, not legal clearance. A "no conflict
found" or "available" result anywhere this file's findings eventually feed into means only that the
specific sources checked, in the way checked, on the date checked, did not surface a conflict — it is
**not** a trademark-clearance opinion, a freedom-to-operate analysis, or a guarantee that a name is safe
to adopt, file, or launch under. Before adopting, filing, or launching any name informed by output built
on this research, a human must independently verify current trademark status (via the primary USPTO/EUIPO/WIPO
systems directly, not a downstream summary), check for confusingly-similar marks (not just identical
ones — a check this file's sources support only partially and this pass did not attempt to validate),
and consult qualified trademark counsel. Nothing in this file, or in any output the resulting
`name-brand-vetter` spec produces, should be read as legal clearance to use a name.
