# Patent databases as an idea/signal source — research pass, 2026-09-07

Question tested: can patent data (old, current, expired) from open-source databases, MCP servers, or
public APIs serve as a new idea/signal source for this pipeline, alongside the five sources
`signal-discoverer.md`/`build-pattern-scanner.md` already use (Show HN, ProductHunt, GitHub, cv.inc
hackathon listings, Hugging Face Spaces)? Two hypotheses tested separately:

1. **Expired/lapsed patents as a public-domain idea source** — once a patent expires, lapses (unpaid
   maintenance fees), or is abandoned, the specific claimed invention becomes free to build without a
   license. Tests whether this is a genuinely novel signal type this pipeline lacks.
2. **Recent/active patent filings as demand/investment evidence** — a company filing a patent is
   spending real money on a stated problem. Tests whether this is a novel demand signal or is largely
   redundant with GitHub/Show HN build-volume evidence `build-pattern-scanner` already reads.

An earlier attempt at this exact task was dispatched 2026-09-06 but its work was lost to a session
interruption before it reported back or wrote output — this is a fresh pass, not a resume; no prior
patent research exists elsewhere in this repo.

## Legal/status framing — this file's own operating rules, stated first

Patent legal status (in-force/active vs. expired vs. lapsed vs. abandoned vs. pending-application) is
a hard-gated field, more serious than a ToS block, because presenting an ACTIVE patent's claimed
invention as "buildable" risks real patent-infringement liability for whoever acts on it — actual
legal exposure, not a policy violation. These rules govern every section below, not just this one:

- **Never describe an in-force/active patent's claims as "buildable" or "an idea to pursue" without
  prominently flagging its active status immediately next to the description, every single time** —
  no exceptions, no relying on one caveat stated once at the top of this file.
- **For an expired/lapsed/abandoned patent**, the specific claimed invention may be noted as public
  domain — but every such note must also flag: (a) a patent family (continuations,
  continuations-in-part, divisionals) can mean related claims are still active elsewhere even when one
  specific patent number expired, and (b) products built "around" the idea may implicate other patents
  not checked here. Never imply "this one expired, so the whole product space is clear."
- **Patent status is verified from the actual database record's expiration/legal-status field, not
  inferred from filing date alone** — patents can lapse early for unpaid maintenance fees or be
  extended via patent term adjustment. Every status claim below cites which system's field was read.
- **This repo's existing ethical boundary applies in addition to, not instead of, the above**:
  aggregate pattern citation only, never "clone this one specific patent/inventor's work as the build
  target."
- **Explicit disclaimer, repeated at the end of this file**: this research is signal for further
  investigation, not legal clearance to build anything. A human must independently verify current
  patent status (including the full patent family) and consult qualified counsel before building or
  launching anything informed by patent content.

## Source coverage

Four-state discipline (found / blocked / empty / excluded-by-scope), same convention as
`signal-discoverer` and every prior research pass in this repo. Every clause below was fetched fresh
this pass (`polyfetch-scrape` raw fetch + a Python offset search over the saved body, or a live
patchright/browser-equivalent render where a site is a JS shell) — no clause is carried from a
WebSearch summary without a primary-source check, except where explicitly marked "WebSearch-only,
not raw-verified" below.

### MCP servers wrapping patent data — FOUND (exist), but launder no ToS

A `GitHub` search for "patent MCP server" surfaces a real, active ecosystem: `riemannzeta/patent_mcp_server`,
`jordanburke/uspto-mcp-server`, `john-walkoe/uspto_pfw_mcp` (USPTO Patent File Wrapper API),
`john-walkoe/uspto_fpd_mcp` (PTAB Final Petition Decisions), `smythmyke/patent-search-mcp-server`
(patent dossiers, prosecution history, citation graphs, **and Google Patents search**), and several
more, all community-maintained (no official USPTO/EPO/WIPO/Google MCP server found). **Key finding:
an MCP server does not launder ToS or access requirements — it is a thin client; the backing API's own
terms and credential requirements still govern.** Concretely:
- Every USPTO-backed server (`uspto_pfw_mcp`, `uspto-mcp-server`, the "ODP"/PTAB-wrapping ones)
  ultimately calls the same USPTO Open Data Portal (ODP) endpoints documented below — which now
  require a personal USPTO.gov account with MFA and, for an API key, "strict ID.me verification" (see
  the PatentsView/ODP entry below). The MCP server does not remove that requirement; it just means
  whoever runs the server needs to supply that credential.
- `patent-search-mcp-server`'s stated "Google Patents search" capability, if it hits Google Patents'
  own internal search/XHR surface (as opposed to individually-known `/patent/<number>` URLs), would
  be querying a path `patents.google.com/robots.txt` does **not** allow (see below) — this repo's own
  discipline (never trust a tool's README claim over the actual target's terms) applies to MCP-server
  claims exactly as it does to any other secondary source. Not independently verified against that
  specific server's source code this pass (out of scope — the point stands regardless of this one
  server's actual implementation).
- None of these servers were installed or run this pass (would require registering the underlying
  credentials anyway); assessed by README/description only, consistent with "MCP server exists,
  maintained-appearing, wraps API X" triage, not a functional test.

**Verdict: found (the access pattern exists), but not a shortcut around any gate below** — whichever
underlying source is chosen, its own gate (credential, ToS, or both) still applies.

### USPTO PatentsView / Open Data Portal (ODP) — FOUND, but owner-gated with high friction (ID.me)

- `patentsview.org` (the PatentsView marketing/docs site) 301-redirects to
  `data.uspto.gov/support/transition-guide/patentsview` — **confirmed via WebSearch, corroborated by
  the live redirect**: PatentsView fully migrated into the USPTO Open Data Portal on **March 20,
  2026**; the legacy `api.patentsview.org` was retired May 1, 2025 (HTTP 410 Gone since); the
  intermediate `search.patentsview.org/api/v1` PatentSearch API (which required only a free `X-Api-Key`
  header, no identity verification) is itself now superseded — a direct fetch to
  `search.patentsview.org` failed to resolve in this session, consistent with the host being retired
  in favor of ODP-hosted endpoints.
- `patentsview.org/robots.txt` (raw-fetched fresh): `User-agent: ClaudeBot / Disallow: /` — explicit,
  named block, the same pattern already established for SourceHut/Codeberg elsewhere in this repo's
  history.
- **The live, current gate is USPTO ODP's own access policy, not a ToS ban.** WebSearch-corroborated,
  cross-checked against a direct fetch of the cited source
  (`blog.patentriff.com/p/not-so-open-uspto-to-require-registration`, quoted verbatim below): as of
  **June 18, 2026**, a valid USPTO.gov account (with MFA) is required to access ODP at all (previously
  anonymous access was permitted); effective **August 18, 2026**, four additional profile fields are
  required or access is lost; and obtaining an **API key** specifically requires **"strict ID.me
  verification"** — USPTO's own stated reason, quoted from the same source: to "improve site security,"
  "[b]lock harmful automated bot traffic," "[r]educe infrastructure costs," and (the blog's
  characterization of USPTO's own framing) "identify and potentially restrict users extracting bulk
  data for AI training without authorization." The same ODP sign-in requirement also folded in what
  used to be separately-hosted free bulk data (`bulkdata.uspto.gov` — patent maintenance-fee events,
  patent assignment records — both previously no-login; a direct fetch to `bulkdata.uspto.gov` this
  pass failed to resolve, consistent with consolidation into the gated ODP "BDSS" bulk-data system a
  `data.uspto.gov/apis/transition-guide/bdss` result names).
- **License/attribution, if access is obtained**: PatentsView's own data license is CC BY 4.0
  (WebSearch-corroborated: "Users are free to use, share, or adapt the material... subject to the
  standards of the Creative Commons Attribution 4.0 International License," with a requested
  attribution form "Source: PatentsView or www.patentsview.org") — not raw-verified against the
  now-redirected terms page (the redirect target is a JS shell that did not render usable text under
  this session's fetch tooling; not re-attempted with a heavier render given the access gate above
  makes the license question moot without ID.me verification anyway).
- **ID.me is a real-identity verification service** (government ID + biometric/selfie match, the same
  system used for IRS.gov and many state unemployment portals) — a materially higher-friction gate
  than this pipeline's existing owner-gated credentials (`PRODUCTHUNT_API_TOKEN` is a free
  self-service signup; GH Archive/BigQuery needs a Google Cloud project). This is not a simple "ask
  the owner to paste a key into `.env`" step; it requires the owner to personally complete identity
  verification.
- **Verdict: found, owner-gated, high-friction** — the single richest structured source for
  hypothesis 1 (a documented legal-status field exists, see below) and hypothesis 2 (filing-volume
  counts by CPC/assignee), but not accessible in this session and not a turnkey credential the way
  this pipeline's other owner-gated sources are.

### USPTO Patent Public Search (`ppubs.uspto.gov`) — UI-only, confirmed

The team lead's specific question: does this have its own API, or is it UI-only? **Confirmed UI-only.**
WebSearch across USPTO's own API catalog (`developer.uspto.gov/api-catalog`) and ODP's API docs
(`data.uspto.gov/apis/patent-file-wrapper/*`) turns up "Search," "Documents," and "Status Codes" APIs —
all under the same ODP umbrella, all subject to the same June/August 2026 sign-in + ID.me gate above.
Patent Public Search itself (the UI that replaced PatFT/AppFT/PubEAST/PubWEST) has no separate,
lighter-weight API of its own; its `Legal Status` field (which, per WebSearch, states expiration
reasons explicitly, e.g. "Expired Due to Failure to Pay Maintenance Fees") is a UI-displayed value
backed by the same gated ODP data underneath — not independently fetchable through a different, open
door. Fetch attempts to `ppubs.uspto.gov/robots.txt` and `www.uspto.gov/terms-service` both failed
(no file / 404) — inconclusive on their own, superseded by the ODP-wide policy finding above regardless.

### Google Patents Public Datasets (BigQuery) — FOUND (CC BY 4.0), owner-gated on credentials

- License, WebSearch-corroborated (not raw-verified — the authoritative source is the dataset's own
  GitHub `LICENSE` file, not fetched this pass given the credential gate below makes it moot without
  BigQuery access anyway): "Google Patents Public Data" (IFI CLAIMS Patent Services and Google) is
  licensed **CC BY 4.0** — genuinely open, both commercial and non-commercial reuse permitted with
  attribution. This is the most permissively-licensed source found this pass.
- **Access requires a Google Cloud Platform project with BigQuery enabled** — the same owner-gated
  shape this repo's plan already tracks for GH Archive ("needs BigQuery credentials... or raw
  hourly-JSON parsing, a real engineering lift; not attempted in v1"). Not available in this session.
- **Verdict: found, owner-gated on GCP/BigQuery credentials** — a real next-tier candidate if the
  pipeline owner is willing to provision a GCP project, structurally identical to the still-deferred
  GH Archive item already in this plan's remaining-work table.

### `patents.google.com` (individual patent detail pages) — FOUND for single-page reads; bulk search NOT permitted

This required correcting an initial WebSearch-based read. A live raw fetch of Google's **current**
general Terms of Service (`policies.google.com/terms`, fetched fresh, not summarized) shows the
"Don't abuse our services" list's automated-access clause is explicitly conditioned on robots.txt, not
an unconditional ban — quoted verbatim from the fetched body:

> "using automated means to access content from any of our services in violation of the
> machine-readable instructions on our web pages (for example, robots.txt files that disallow
> crawling, training, or other activities)"

No other bullet in the same list bans automated access generally (the list is otherwise about malware,
phishing, fake accounts/reviews, IP-rights violation, reverse-engineering ML models, and misusing
AI-generated output to train other models — none of which cover reading a public patent page).
`patents.google.com/robots.txt` (raw-fetched):

```
User-agent: *
Disallow: /*
Allow: /$
Allow: /advanced$
Allow: /patent/
Allow: /sitemap/
```

**Reading**: individual patent detail pages (`patents.google.com/patent/<number>/en`) are explicitly
`Allow`-listed and therefore not "in violation of the machine-readable instructions" — compliant to
fetch one at a time, given a patent number already in hand. **Everything else is `Disallow`-listed,
including the site's own search/results surface** — a bulk or keyword-driven search sweep across
Google Patents (the kind `signal-discoverer` would want to *discover* candidate patent numbers by
topic) is **not** permitted by this same robots.txt, only individual-page lookups once a patent number
is already known from elsewhere. This is a real, load-bearing scope boundary, not a technicality —
see Synthesis.

**Verdict: found for known-patent-number single-page reads; blocked (by robots.txt, and therefore by
the ToS's own conditional clause) for search/discovery use.**

### EPO Open Patent Services (OPS) API — FOUND, owner-gated (free tier, OAuth2 registration)

WebSearch-corroborated (not raw-verified against the Fair Use Charter document itself — deprioritized
given the registration gate makes exact wording moot without a developer account regardless): free
"Non-paying" tier via OAuth2 Consumer Key/Secret from `developers.epo.org/user/register`, roughly a
4,000,000-request/month quota (~30 req/min throttle), explicitly for **non-commercial and evaluation
use** — sustained/commercial volume needs a paid plan (€2,800/year for unlimited traffic).
Registration is free self-service (email + form), a materially lower-friction gate than USPTO's ID.me
requirement above, closer in shape to this pipeline's `PRODUCTHUNT_API_TOKEN` pattern. INPADOC legal
status (the authoritative multi-jurisdiction expiration/status service) is available through OPS —
not independently confirmed this pass which specific OPS endpoint exposes it, since registration
wasn't completed.

**Verdict: found, owner-gated (free, self-service, lower friction than USPTO)** — not accessible in
this session (no credentials), but the most turnkey-comparable-to-existing-pattern path of the
credential-gated options if the pipeline owner wants full INPADOC-grade legal status and a real search
API rather than one-page-at-a-time Google Patents lookups.

### EPO European Publication Server (`data.epo.org/publication-server`) — FOUND, no registration needed

Distinct from OPS (the API) — this is EPO's public document server, and it cleared cleanly with **no
credential**. `data.epo.org/robots.txt` (raw-fetched): disallows only `/pise-server/`, `/qual_lod/`,
and several `/linked-data/*` paths; the publication-server path itself is not disallowed, and the file
explicitly documents an *optional* enhanced-crawler registration ("By registering with us you may be
able to have your web crawler re-directed deep into the European Publication Server...") — registration
improves the experience, it is not gating baseline access. A live, unauthenticated fetch of a real
document (`.../patents/EP4457715NWA1/document.html`) succeeded and returned full bibliographic data
(see Content richness). **Verdict: found, no registration required for individual document
front-page reads** — narrower in scope than OPS (bibliographic front-page data only in the page
sampled this pass, not confirmed to include abstract/claims text — see Content richness caveat), but
a genuinely open door, the EPO analogue to Google Patents' individual-page allowance.

### WIPO PATENTSCOPE — BLOCKED (website); paid-only (Web Service API)

Raw-fetched `wipo.int/en/web/patentscope/data/terms_patentscope` (a Liferay/CMS page; the ToS text is
server-embedded, no rendering gap). Exact quoted clause (section numbering as in the source):

> "the PATENTSCOPE Database is provided only for public information purposes and the User is forbidden
> to: (a) use the service excessively to the detriment to other Users (for that matter, more than 10
> search related actions per minute from a single IP address can be considered excessive); (b) perform
> automated queries; (c) perform bulk acquisition, bulk downloading, and bulk storing of data; (d)
> perform bulk copying, bulk reformatting, bulk sharing and bulk redistributing of data; (e) perform
> web scraping; (f) perform any other abusive use degrading or circumventing the service."

Unconditional, unambiguous — no robots.txt-conditioning language of the kind found in Google's ToS
above. The separate PATENTSCOPE **Web Service** (the real API, SOAP-based) is not free: WebSearch-
corroborated at **600 Swiss Francs/calendar year** subscription. **Verdict: BLOCKED for any automated
website use; the API alternative is paid, not owner-gated-free.** No workaround attempted, per this
repo's standing "no workaround on a confirmed block" rule.

### Lens.org — effectively BLOCKED (active bot-detection) and owner-gated (subscription/trial application)

A raw fetch of `about.lens.org/lens-api-terms-of-use/` returned a live bot-detection captcha redirect
(`/.well-known/sgcaptcha/...`) rather than any page content — an active edge-level block on automated
fetch tools, the same shape as this repo's prior confirmed Bluesky WAF-block finding. Separately,
WebSearch confirms the Lens API itself is **institutional-subscription-only**; free "trial access" is
offered only for non-commercial/academic use and requires an application (official email, stated use
case, affiliation, project timeline) rather than self-service signup. **Verdict: blocked at the
technical layer (captcha) and gated at the policy layer (subscription or an approved application) —
neither cleared this pass.**

### FreePatentsOnline / other aggregators — not attempted (excluded-by-scope)

Per the team lead's instruction, only pursued "if the above don't give enough coverage." They did:
Google Patents individual-page reads plus the EPO Publication Server together already supply real,
richly-structured, credential-free sample content (see below), so this tier was not spent effort on
this pass. Not checked for ToS one way or the other — genuinely untested, not a "found" or "blocked"
claim.

## Content richness

Sampled from the two credential-free sources that cleared the gate: `patents.google.com` individual
pages (two real records, one expired and one active, per the team lead's instruction to span
statuses) and the EPO Publication Server (one real record, bibliographic front page).

### Sample 1 — EXPIRED: US8046721B2, "Unlocking a Device by Performing Gestures on an Unlock Image" (Apple Inc.)

Fetched live from `patents.google.com/patent/US8046721B2/en` (2026-09-07).

- **Legal status, exact field read**: `<dd itemprop="legalStatusIfi"><span itemprop="status">Expired -
  Lifetime</span></dd>`, with an anticipated-expiration event dated **2025-12-23** (marked
  `critical="true"`, `type="legal-status"`) — i.e. per this record, the patent's full term has already
  run out as of today's date in this session (2026-09-07).
- **This status is Google's own secondary aggregation, not the USPTO's primary record** — the same
  page states its own disclaimer verbatim, directly above the status field: *"the legal status is an
  assumption and is not a legal conclusion. Google has not performed a legal analysis and makes no
  representation as to the accuracy of the status listed."* Per this file's own operating rules
  (Legal/status framing, above), this status is reported here as **Google/IFI CLAIMS's aggregated
  reading**, not independently confirmed against USPTO's own Patent Public Search record (which this
  session could not reach — see the ODP/ID.me finding above). A human verifying this before building
  anything must re-check the primary USPTO record directly, not rely on this file's citation of
  Google's field.
- **Concrete, on-page evidence of the patent-family caveat this file is required to state**: the same
  page's family/legal-events section lists related applications across jurisdictions with their own,
  independently-tracked legal status — e.g. an EP counterpart application shown with
  `legalStatusCat="not_active"`, and a separate related U.S. patent, **US7657849B2**, shown in the
  same list with `legalStatusCat="active"` / `legalStatus="Active"`. This is a real, sourced instance
  of exactly the risk the team lead flagged: **one patent number's expiration does not mean the whole
  family or the whole product idea is clear** — a related patent in the same family/citation network
  can carry a different, still-active status. This file does not claim products in this space are
  clear to build; it only notes the expired status of this one specific patent number, with this
  family caveat immediately attached.
- **Abstract (quoted, extractable, plain-language)**: *"A device with a touch-sensitive display may
  be unlocked via gestures performed on the touch-sensitive display. The device is unlocked if
  contact with the display corresponds to a predefined gesture for unlocking the device..."* — a
  clean, non-boilerplate statement of a specific stated problem (accidental input / unintentional
  unlocking) and a specific claimed solution (gesture-based unlock), genuinely usable at the level of
  detail this pipeline's other sources (HN titles, PH taglines) provide, arguably richer.
- **CPC classification**: present, hierarchical, with human-readable descriptions at every level (see
  Sample 2 below for the exact markup shape — identical structure on this page).

### Sample 2 — ACTIVE: US11556230B2, "Data detection" (Apple Inc.) — ACTIVE, do not read as buildable

**Legal status, exact field read**: `<span itemprop="status">Active</span>, expires
<time itemprop="expiration" datetime="2035-03-18">2035-03-18</time>` — same `legalStatusIfi` field and
same Google/IFI-aggregation caveat as Sample 1 applies here too. **This patent is presented as
in-force; nothing below is a suggestion to build it — flagged here, at the point of description, per
this file's hard-gate rule, not only once at the top of this file.**

- **Abstract (quoted)**: *"In some embodiments, a device determines a current context of a plurality
  of terms, in accordance with a determination that the current context is a first context, selects
  one or more terms in the plurality of terms based on a first word list as actionable terms..."* —
  describes what shipped in iOS as "Data Detectors" (turning phone numbers, addresses, dates in text
  into tappable actions), context-dependent term selection.
- **Background section present and narrative, not pure boilerplate** (quoted): *"Communication between
  people using electronic devices has increased significantly in recent years... These communications
  often consist of user-provided content... being transmitted from one electronic device to another.
  Enhancing these communications improves the user's experience with the device and decreases user
  interaction time..."* — a real, if generic, problem-framing paragraph, more narrative than the
  abstract.
- **Claim 1 (quoted, full)**: *"A method comprising: at an electronic device with one or more
  processors and memory in communication with a second electronic device: receiving a plurality of
  terms from the second electronic device that corresponds to a communication with another user...;
  displaying, via a display device, the plurality of terms; and before receiving user input directed
  to the plurality of terms: [context-dependent selection logic]..."* — legally-structured
  (single-sentence, heavily nested) but not so dense as to be unreadable; a careful read extracts the
  same mechanism the abstract states, in more precise/narrower form. **This is the claimed, currently
  enforceable invention — again, not a build suggestion.**
- **CPC classification, exact markup**: `<span itemprop="Code">G06</span>—COMPUTING OR CALCULATING;
  COUNTING` → `<span itemprop="Code">G06F</span>—ELECTRIC DIGITAL DATA PROCESSING` →
  `<span itemprop="Code">G06F3/00</span>—Input...` (truncated in the sample; the hierarchy continues to
  more specific subgroups). **This is structurally the direct analogue of GitHub `topics` or PH
  `topics`**: a real, standardized, hierarchical taxonomy attached to every record, present at multiple
  granularities (class → subclass → group → subgroup), well-suited to the same topic-frequency
  clustering method `signal-discoverer.md` already uses for GitHub/PH.

### Sample 3 — EP4457715A1, "Collaboration Software Development Kit" (Salesforce, Inc.), bibliographic only

Fetched live from `data.epo.org/publication-server/rest/v1.2/patents/EP4457715NWA1/document.html`
(2026-09-07), no credential. Published 2024-11-06; filed as PCT/US2022/052680 (2022-12-13), priority
US 17/566,947 (2021-12-31); Applicant Salesforce, Inc.; CPC `G06Q 10/00`, `G06Q 10/10`, `G06Q 10/101`,
`G06Q 10/103` — a real, current (2024), software/collaboration-tooling example directly in this
pipeline's own subject-matter wheelhouse. **This particular front-page fetch did not surface embedded
abstract or claims text** (no `(57)` abstract block found in the returned HTML — likely on a separate
page or only in the PDF rendition, not explored further this pass) — so EPO's Publication Server is
confirmed rich for **bibliographic/classification** data without a credential, but not confirmed this
pass for abstract/claims text the way Google Patents' individual pages are. No legal-status field was
present on this page either (INPADOC legal status is a separate OPS-API service, per the OPS entry
above — not reachable without registration).

### Answering the two content-level questions directly

- **Are abstracts/background/claims usable, or too legally-dense to extract signal from?** Usable.
  All three samples above read as genuine, specific problem/solution statements — closer to a Show HN
  title-plus-body than to opaque legal boilerplate. Claims are denser than abstracts but still
  extractable with care; background sections, where present, are the most narrative and closest in
  spirit to a complaint-miner-style "stated problem."
- **Do CPC/IPC codes offer a GitHub-`topics`/PH-`topics`-style clustering signal?** Yes, and more
  standardized than either — CPC is a fixed, hierarchical, globally-maintained taxonomy (not
  free-text tags a project author chose), present on every sampled record at multiple granularities.
  This is arguably a *stronger* clustering primitive than GitHub topics (which are free-text and
  sparse on many repos, per `signal-discoverer.md` step 3's own noted fallback). Citation-count
  clustering was not independently sampled this pass (would need either PatentsView/ODP's citation
  fields or OPS's citation service, both gated) — not confirmed either way.

## Synthesis

### Hypothesis 1 — expired/lapsed patents as a public-domain idea source

**Holds up as a real, distinct signal type** the sampled content confirms it: Sample 1
(US8046721B2) shows exactly the shape the team lead hypothesized — "someone already engineered and
legally protected a specific solution to a stated problem, and the legal barrier to building it is
now gone" — with a real, dated, sourced expiration and a genuinely readable problem/solution abstract.
This is categorically different from every existing source in this pipeline: HN/PH/GitHub/cv.inc/HF
Spaces all show *current, unpatented* build activity or demand language; a patent record instead shows
a *specific, once-legally-exclusive, now-free* engineered solution, at a level of technical
specificity (claims) none of the other five sources provide.

**But it is not compliantly automatable at scale without a credential**, and the credential that
would make it scalable (USPTO ODP) is now the most expensive one to obtain of any source this
pipeline has evaluated to date (ID.me identity verification, not a self-service API key) — a genuinely
new category of gate for this pipeline, higher-friction than every existing owner-gated source
(`PRODUCTHUNT_API_TOKEN`, GH Archive/BigQuery). What **is** compliantly available without any
credential (Google Patents individual-page reads) requires already knowing a specific patent number —
it cannot itself discover *which* expired patents are relevant to a category, only confirm/enrich a
patent number surfaced some other way (e.g. named in an HN/PH complaint thread, a news article, a
`freepatentsonline`/aggregator search not attempted this pass). **Read plainly: hypothesis 1 is a real
signal type, but not compliantly automatable as a discovery mechanism today — only as a one-off
enrichment step once a specific patent is already named elsewhere**, unless the pipeline owner
provisions either EPO OPS credentials (free, self-service, lower friction, but Espacenet/OPS search
was not tested this pass for whether it can search *by problem/keyword* the way this pipeline needs)
or USPTO ODP's ID.me-gated access (the richer, more authoritative option, but a real identity-
verification step the owner would need to personally complete).

### Hypothesis 2 — recent/active patent filings as demand/investment evidence

**Structurally plausible, but reads as a different signal than GitHub/Show HN build-volume, not a
redundant re-measurement of it** — two verified structural facts support this, not asserted from
memory: (a) utility patent applications publish ~18 months after the earliest priority filing date,
per 35 U.S.C. § 122(b)(1)(A) (statute cited directly, not inferred) — meaning "recent" published
filings actually reflect problems a company committed real money to **1.5–3+ years ago** (accounting
for prosecution/grant lag beyond publication), a materially different, slower-moving demand signal
than a GitHub repo created last month; (b) Sample 3 (Salesforce's 2024-published "Collaboration
Software Development Kit," originally filed by Salesforce in Dec. 2021) is direct evidence of exactly
this: an established company, not an indie builder, spending real prosecution cost on a stated
problem — a different filer population than this pipeline's existing GitHub/Show HN/HF-Spaces sources,
which skew toward individual/small-team builders. **This reads less as "redundant with GitHub build
volume" and more as a distinct, slower, incumbent-weighted competitive-landscape signal** — closer in
spirit to `startups.gallery`'s "what's already funded = crowded/competed-for" framing
(`discovery/2026-09-05T204416Z-startup-registries.md`) than to a fresh demand proxy. Whether that
framing (competitive crowding, not raw demand) is actually useful to this pipeline's stated goal
(finding *buildable* candidate categories) is a judgment call for the team lead, not settled by this
research pass — the content confirms the signal is real and distinct, not that it's the right signal
for this pipeline's purpose.

**Also not compliantly automatable at scale as a discovery mechanism**, for the identical access
reason as hypothesis 1 — CPC/keyword-driven bulk filing search needs a gated credential (ODP or OPS),
not the credential-free individual-page path.

### Accessibility, summarized

| Source | State | Gate |
|---|---|---|
| MCP servers (community) | found (exist) | none of their own — inherits whichever backing API below is used |
| USPTO PatentsView / ODP | found | owner-gated, high friction — USPTO.gov account + MFA + **ID.me identity verification** for an API key |
| USPTO Patent Public Search | found (UI only) | same ODP gate above; no separate lighter API |
| USPTO bulk data (`bulkdata.uspto.gov`) | found | folded into the same ODP gate above (as of June 18, 2026) |
| Google Patents Public Datasets (BigQuery) | found (CC BY 4.0) | owner-gated — GCP/BigQuery credentials, same shape as this plan's still-deferred GH Archive item |
| `patents.google.com` individual pages | **found, no credential** | none — but robots.txt permits only known-number lookups, not search/discovery |
| EPO OPS API | found | owner-gated, low friction — free self-service OAuth2 registration |
| EPO Publication Server | **found, no credential** | none — bibliographic/CPC data only (abstract/claims not confirmed reachable this pass) |
| WIPO PATENTSCOPE | **blocked** (website); paid-only (Web Service API, 600 CHF/yr) | — |
| Lens.org | **blocked** (active bot-detection) + owner-gated (subscription/trial application) | — |
| FreePatentsOnline / aggregators | not attempted (excluded-by-scope, coverage already sufficient) | — |

### What a minimal integration would look like, if the team lead decides to pursue this

**Not a new subagent** — sized the same way this repo's own precedent (cv.inc, Hugging Face Spaces)
was sized: a single additional step, if pursued at all. But unlike those two additions, **this one
cannot be wired in the same way** (a `signal-discoverer`/`build-pattern-scanner` step that runs
unattended against a public, unauthenticated endpoint) — every discovery-capable path here (ODP, OPS,
BigQuery) is owner-gated, and the one credential-free path (`patents.google.com`) cannot discover, only
enrich a patent number already in hand. Two realistic shapes, not one:

1. **Do not build a discovery step.** Leave patents out of `signal-discoverer`'s unattended pull
   entirely — there is no compliant, credential-free way to *find* candidate patents by category/topic
   today, the same practical conclusion this plan already reached for GH Archive.
2. **If the pipeline owner provisions EPO OPS credentials** (the lower-friction of the two real
   discovery-capable gates), a scoped `build-pattern-scanner`-style enrichment step becomes viable:
   given a category already chosen via `config/scope.md`, search OPS/Espacenet by keyword/CPC for
   patents relevant to that category, fetch each hit's INPADOC legal status, and only ever cite an
   **expired** result as a candidate idea (with the family caveat inline, every time, per this file's
   framing rules) — an active result would be cited only as competitive/demand evidence (hypothesis 2),
   flagged active every time, never as something to build. This is a real engineering lift (OPS's
   query syntax, response parsing, and the INPADOC legal-status sub-call were not tested this pass) —
   comparable in size to the still-deferred GH Archive item, not a small add.
3. **USPTO ODP (the ID.me-gated path) is the richer option** if the owner is willing to complete
   identity verification, but that is a meaningfully bigger ask of the owner than any credential this
   pipeline has requested before — flag this explicitly to the team lead as a real decision point, not
   a rubber-stamp "just add another `.env` var."

### MCP server vs. direct API — recommendation

**Direct API access, not an MCP server, is the better path if this is pursued.** None of the community
MCP servers found this pass are officially maintained by USPTO/EPO/WIPO/Google; they are thin clients
over the same gated APIs above, and using one adds a maintenance/trust dependency on a third party's
code without removing any of the actual access gates documented above. If the team lead does decide to
provision EPO OPS or USPTO ODP credentials, calling those APIs directly (the same `polyfetch-scrape`/
authenticated-HTTP-client pattern this pipeline already uses for ProductHunt's GraphQL API) is more
consistent with this repo's existing tooling discipline than adopting an unaudited third-party MCP
server.

## Not legal advice — read this before acting on anything above

This file is signal for further investigation only. It is **not** legal clearance to build, launch, or
market anything described above. Before building or launching anything informed by patent content in
this file, a human must independently verify: current legal status of the specific patent(s) cited
(direct from USPTO Patent Public Search or an equivalent primary system, not this file); the full
patent family (continuations, continuations-in-part, divisionals, and any related filings in other
jurisdictions); and whether any other, unrelated patent might cover the same product space. Consult
qualified patent counsel before proceeding. Nothing in this file should be read as freedom-to-operate
analysis.
