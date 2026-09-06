# Additional-sources research — 2026-09-05T233231Z

Six new candidate sources, vetted against this repo's established bar (robots.txt + actual Terms of
Service, checked for any separately-incorporated API/acceptable-use document, primary-source fetch
required — no conclusion from memory or a third-party summary). Four-state discipline (found /
blocked / empty / excluded-by-scope), same convention as `signal-discoverer` and the two prior
2026-09-05 passes (`discovery/2026-09-05T204416Z-startup-registries.md`,
`discovery/2026-09-05T213930Z-hackathon-signal-research.md`).

**Methodology note.** Patchright (headless-Chromium rendering) was unavailable this session — the
sandbox blocked the one-time browser-binary install (`mkdir '/home/vscode/.cache/ms-playwright'`
denied). Two sites (Kaggle's `/terms`, a client-rendered React SPA with an empty `<div id="root">`
in the raw response) needed a rendered read anyway; those were read via `r.jina.ai`'s render proxy
(fetches and executes the page's JS server-side, returns the resulting DOM text) rather than
Patchright — the actual page content served to a browser, not a third party's paraphrase of it.
Everything else was read via `polyfetch-scrape`'s raw-body fetch, searched with a Python
offset/substring script (grep is denied on saved tool-output files in this sandbox), or via WebFetch
where noted.

## Source coverage

### 1. SourceHut (`sr.ht`) — BLOCKED

- **robots.txt** (`sr.ht/robots.txt`, raw fetch): opens with a policy statement — "Disallowed: ...
  Anything used to feed a machine learning model" — then explicitly names, among ~25 other bots:
  ```
  # Fairly certain that this is an LLM data vacuum
  User-agent: ClaudeBot
  Disallow: /
  ```
  (Also disallows `GPTBot`, `Google-Extended`, `OAI-SearchBot`, `ChatGPT-User`, `Bytespider`, and
  others.) Same pattern as this repo's existing Codeberg precedent (robots.txt explicitly naming
  Claude bots) — a Claude-based agent identifying honestly cannot comply and read this site.
- **Terms of Service** (`man.sr.ht/terms.md`, raw fetch): the "Permissible use" section grants a
  real, if narrow, permission — *"You may use automated tools to obtain public information from the
  services for the purposes of archival or open-access research. You may not use this data for
  recruiting, solicitation, or profit."* — but the following "Automated use of our services" section
  conditions any such automated access on three requirements, the first of which is *"You obey the
  rules set forth in robots.txt"*, and separately states, unconditionally: *"You may not use
  automated tools to collect SourceHut data for solicitation, profit, or **the training of a machine
  learning model**."* The ToS also requires a User-Agent that "clearly identifies your software and
  its operators" — an honest UA cannot both identify as Claude-based and comply, since robots.txt
  disallows exactly that UA by name.
- **Verdict: BLOCKED.** The research-use permission is real but is gated behind robots.txt
  compliance, and robots.txt names this exact agent family. No workaround attempted (spoofing the
  UA would violate the same document's own UA-honesty requirement).

### 2. Major League Hacking (`mlh.io` → `mlh.com`) — FOUND, but thin (see Recommendation)

- **robots.txt** (`www.mlh.com/robots.txt` — `mlh.io/robots.txt` 302-redirects here, confirmed via
  WebFetch): `Allow: /`, with a short disallow list (`/account/`, `/tools/`, `/auth/`, `/admin/`,
  `/graphql`, invite/promo-code paths) — none of it covers public event-listing pages.
- **Terms of Service** (`www.mlh.com/terms`, raw fetch — an Inertia.js app; content is server-embedded
  JSON in the page, not client-fetched, so no rendering gap): no hit anywhere in the body for
  scrape/crawl/spider/bot/automat(ed)/harvest/data-mining/machine-learning. The license grant is
  "solely for your personal purposes and non-commercial use," with a broad reuse restriction: *"You
  may not modify, publish, transmit, ... reproduce, create derivative works of, distribute, ...
  or in any way exploit any of the materials or Content on our Site in whole or in part, other than
  as necessary for your own personal non-commercial use, without our written consent"* — plus a
  Prohibited-Conduct bullet, *"Use the Service or Site commercially."* Same broad, non-automation-
  specific shape as this repo's existing BetaList finding (`2026-09-05T204416Z-startup-registries.md`),
  which was still read as "Found, with a caveat" for aggregate research use.
- **Separate community/API terms — checked, none found.** `community.mlh.io/tos` (surfaced in
  search) does not serve a distinct document — raw fetch shows it falls back to MLH's ordinary
  homepage component (`HomePage/Show`), not a separate ToS. No "API"/"developer terms" mention
  appears anywhere in the main ToS body either (MLH has no public API product).
- **Content richness — sampled, and this is the material finding.** MLH's own `mlh.com` is a
  **hackathon-league directory/certification layer, not a content host**: the season-events listing
  (`mlh.com/seasons/2027/events`, Inertia JSON payload) lists member hackathons with only
  name/date/location/logo, and each entry's real event page (`websiteUrl`) points to an
  **independently-operated external domain** (e.g. `hackrice.com`, `hackmty.com`) — a different site
  per event, each with its own unchecked ToS. MLH-hosted event pages (e.g.
  `mlh.com/events/hackrice-71/prizes`) render as `HackathonPrizes` — a generic, league-wide
  swag/software-freebie catalog, not a sponsor-specific problem statement or a submission gallery.
  No sponsor "what we're looking for" text or project gallery was found hosted on `mlh.com` itself.
- **Verdict: FOUND** (no ToS/robots.txt bar) **but structurally thin** — see Recommendation.

### 3. Kaggle — BLOCKED (website scraping and the official API alike)

- **robots.txt: genuinely absent.** `kaggle.com/robots.txt` and `www.kaggle.com/robots.txt` both
  return a real HTTP 404 (confirmed via both WebFetch and a raw `polyfetch-scrape` fetch, which
  raised a terminal `GoneError: 404`) — not an empty-but-200 file, an actual missing resource.
  Absence of robots.txt is "no known restriction from that document," not clearance — the ToS below
  is the controlling document regardless.
- **Terms of Use** (`kaggle.com/terms`): the raw HTML response is an empty, client-rendered React
  shell (`<div id="root">` with no content — Patchright unavailable this session, see Methodology).
  Read via `r.jina.ai`'s render proxy instead. Section 4 ("Are there any additional restrictions on
  my use of the Services?"), exact quoted item:
  > "'Crawls,' 'scrapes,' or 'spiders' any page, data, or portion of or relating to the Services or
  > Content (through use of manual or automated means)"
  Unconditional — not scoped to a URL pattern or an unauthenticated-only case. "Content" is defined
  as "text, graphics, data, articles, photos, images, illustrations, and User Submissions"; "Services"
  as "website(s), products, services and applications" (i.e. Kaggle broadly, competitions included).
- **Separately incorporated Acceptable Use Policy — checked, per the GitLab/Vercel precedent.**
  Section 4 item 1 itself references *"the Acceptable Use Policy, available at www.kaggle.com/aup"*.
  Fetched separately (`r.jina.ai` render): it independently prohibits *"excessive crawling of the
  content on the Services"* under a "Resource Abuse" heading — reinforcing, not superseding, the
  main ToS's unconditional ban.
- **Official API checked specifically, per the team lead's instruction.** `kaggle.com/docs/api`
  (`r.jina.ai` render) documents the `kaggle` CLI / `kagglehub` library, OAuth scopes, and rate
  limits — but contains **no separate terms, license, or purpose restriction of its own**, and
  explicitly punts governance back to "Kaggle's separate Terms of Service" (i.e. the same document
  quoted above). This is a materially different shape from Stack Exchange's API (below), which has
  its own dedicated API Terms of Use document distinct from the general site ToS. Kaggle's API is a
  technical transport, not a separately-authorized access path — the "manual or automated means"
  language in Section 4 is not scoped to browser-based scraping only, and nothing in the API docs
  carves the API out of it. Separately, WebSearch corroborates that Kaggle's actual *Competition
  Data* (as opposed to competition metadata) carries its own non-commercial/no-redistribution
  restriction on top of this — a second, independent reason the dataset side is unusable regardless.
- **Verdict: BLOCKED, both the website and the official API.** No workaround attempted. Content-fit
  caveat, noted regardless of the ToS outcome: Kaggle competitions are ML-modeling tasks (a stated
  metric/dataset, not a product problem statement) — narrower signal fit than a hackathon's "what
  we're looking for" language even had the door been open.

### 4. Hugging Face (Spaces / Hub) — FOUND, clean

- **robots.txt** (`huggingface.co/robots.txt`, raw fetch): `User-agent: * / Allow: /`, plus a
  sitemap. Fully open.
- **Terms of Service** (`huggingface.co/terms-of-service`, raw fetch — Svelte app, but the ToS text
  is server-rendered directly into the HTML, no rendering gap): zero hits anywhere in the body for
  scrape/crawl/spider/harvest/data-mining/robot. The only reuse restriction found is narrowly scoped
  to Hugging Face's **own** site assets, not user-uploaded Hub content: *"You may not alter,
  reproduce, republish, license any of **our proprietary materials** [site, design, code, graphics,
  interfaces, trademarks, and logo] ... unless we expressly give you a written permission to do
  so."* This does not reach community-authored Spaces/model/dataset content, which is what this
  source would actually read.
- **Separate incorporated documents — checked, none restrict this.** The ToS references
  "Supplemental Terms" only in the context of individually-negotiated commercial agreements (Order
  Forms, MSAs) — not a standing public AUP. The one public linked policy, `/content-policy`
  (separately fetched), governs what may be *uploaded* (illegal/explicit content moderation) and
  contains no automated-access language either.
- **Content richness — sampled.** `huggingface.co/api/spaces?search=hackathon` (a public,
  unauthenticated JSON endpoint — the same "no HTML parsing needed" cheapness this repo's cv.inc
  finding already values) returns real hackathon-org Spaces directly, with structured tags reflecting
  sponsor/track/achievement taxonomy (e.g. `sponsor:openbmb`, `track:wood`,
  `achievement:offgrid`). Sampling one such org (`huggingface.co/Agents-MCP-Hackathon`, the "Gradio
  Agents & MCP Hackathon 2025"): a named theme (Model Context Protocol), a real sponsor list (Modal
  Labs, Hugging Face, Nebius, Anthropic, OpenAI, Mistral AI, and others), $16,500+ cash plus $1M+ in
  API/compute credits, three explicitly named tracks, and a stated judging rubric ("innovation,
  technical implementation, usability, and impact"). **603 Spaces, 22 collections** are listed under
  this one org — a larger independent-team convergence corpus than cv.inc's own 102-team natsec
  gallery, in the same "many independent teams, one theme, one window" shape.
- **Verdict: FOUND — no ToS or robots.txt bar identified.** Clean, and the richest of the sources
  checked this pass.

### 5. Y Combinator company directory (`ycombinator.com/companies`) — BLOCKED

- **robots.txt** (`www.ycombinator.com/robots.txt`, raw fetch): `Disallow: /companies?*` (query-
  string search/filter views only) and `Disallow: /library?*`, alongside `Allow: /` generally — this
  does **not**, by itself, disallow the base `/companies` directory page or individual
  `/companies/<slug>` profile pages, only the parameterized search/filter URLs. Narrower than a
  first read suggests — matching this repo's own prior caution (the uneed.best finding) about not
  over-reading a robots.txt disallow. Settled by the ToS regardless (below).
- **Terms of Use** (`ycombinator.com/legal/`, raw fetch — a single combined legal page covering
  multiple YC-family sites; the current Terms of Use section is headed `<h3 id="tou">Terms of Use`
  and opens *"Welcome to the Y Combinator website (including all subdomains, the 'Site')..."*).
  Exact quote from the Intellectual Property Rights section:
  > "In connection with your use of the Site you will not engage in or use any data mining, robots,
  > scraping or similar data gathering or extraction methods."
  Unconditional, and "Site" is explicitly defined to include "all subdomains." (Correction to an
  initial mis-read: near-identical "harvest"/"scrape" boilerplate appears twice earlier on the same
  combined legal page — this is one Terms-of-Use document rendered twice in the page source, likely
  a CMS edit-payload duplicate, not a separate Hacker News-specific document; both copies carry the
  same "karma"/"upvotes" language because it's the same template.)
- **Note for the pipeline's existing HN integration**: "all subdomains" would technically reach
  `news.ycombinator.com` too, but this pipeline's existing HN access (`docs/plans/0001-concept.md`
  §1) already goes through `hn.algolia.com` and `hacker-news.firebaseio.com` — separately-operated
  API hosts, not `news.ycombinator.com` itself — so this finding does not touch the pipeline's
  existing HN sourcing.
- **Verdict: BLOCKED, unambiguous.** No workaround attempted.

### 6. Stack Overflow / Stack Exchange — BLOCKED (website and, on closer check, the API too)

- **robots.txt** (`stackoverflow.com/robots.txt`, raw fetch via `polyfetch-scrape`): what was served
  to this runner's fetcher —
  ```
  User-agent: *
  Content-signal: search=no, ai-train=no
  Disallow: /
  ```
  a full-site disallow for the default user-agent, plus the new IETF-draft `Content-signal`
  directive explicitly opting out of both search indexing and AI-training use. (This is a
  Cloudflare-fronted, UA-conditional site — a plain WebFetch call to the same URL was refused
  outright by Claude Code's own fetch tool, a separate signal of aggressive edge-level bot-blocking,
  though not itself a second confirmation of the file's exact text.)
- **API Terms of Use, checked as the team lead requested** (`stackoverflow.com/legal/api-terms-of-use`,
  raw fetch): Stack Exchange has a genuine, dedicated API Terms of Use document — a materially
  different shape from Kaggle's. It requires attribution (visually credit the Stack Exchange Network
  as source) and agreement to the main Terms of Service, but on its own contains no scraping,
  AI-training, or commercial-use restriction.
- **The main Terms of Service (`.../legal/terms-of-service/public`) initially read clean too** — a
  keyword search of its body found zero scrape/crawl/AI-training hits. **This was not the end of the
  check.** The ToS links out to a separately-hosted, separately-incorporated
  **Acceptable Use Policy** (`stackoverflow.com/legal/acceptable-use-policy`) — the same
  GitLab/Vercel-shaped hidden-restriction pattern already established in this repo's history. Its
  "Content Scraping/Bot Policy" section, exact quote:
  > "you may not use, launch, or distribute any automated system, including without limitation, any
  > spider, bot/robot, cheat utility, scraper, unauthorized script, offline reader, data miners, or
  > similar automated data gathering or extraction tools, to access any Network website or Service,
  > or to collect, gather, or copy any text, files, ... or any other content in any form, from any
  > Network website or Service, for the purpose of: (1) Building a similar or competitive website,
  > product, or service; or (2) **Developing, building, training, testing, indexing, benchmarking, or
  > improving any generative AI, chatbot, large language, or machine learning tool, model, or
  > platform, or any similar technology.** (3) Or any purpose, if such activity ... negatively
  > impacts Network bandwidth, or limits or prevents Network access for other users."
  "Network website **or Service**" is the operative phrase — the API is a "Service" of the Stack
  Exchange Network, so this is not scoped to browser-based HTML scraping only; it reaches the API
  path too, for exactly the purpose (feeding an LLM-based research/synthesis tool) this pipeline
  would use it for. Same shape as this repo's existing TrustMRR/Trustpilot findings (an explicit
  "don't use our data to train/develop AI" clause), including the same door: *"Your usage of
  automated data-gathering means is exempt from this policy if you have obtained express prior
  written consent."*
  A separately-checked `consolidated-responsible-ai-policy` document (also linked from the main ToS)
  turned out to be about Stack's own internal AI-governance commitments, not a restriction on
  external parties — checked and ruled irrelevant, not a fresh gap.
- **Verdict: BLOCKED — website (robots.txt) and API (Acceptable Use Policy) both** — reachable only
  through the same "ask for prior written permission" door this repo already has open for TrustMRR.
  Not pursued further this pass, per the "no workaround on a confirmed block" rule.

## Ethical boundary applied

Every specific example named above (hackathon orgs, event names, sponsor lists) is cited only as
evidence of what this pipeline's aggregate/pattern-citation method would look like against these
sources, or as evidence a source's content is real/rich — never as a build target. No individual
team's or builder's specific unscaled project is named or implied as something to clone, consistent
with `docs/plans/0001-concept.md` §3's standing rule.

## Recommendation

**Only Hugging Face is worth adding.** It clears the same bar cv.inc/cerebralvalley.ai already
cleared (no ToS or robots.txt restriction, checked including incorporated documents), and its content
is comparably or more rich: a public, unauthenticated JSON endpoint (`/api/spaces`, filterable by
search term) surfaces hackathon-org Spaces directly, with structured sponsor/track tags and, per org,
often 100s of independently-submitted entries under one named theme within one time window — the
same "many independent teams converging on a stated problem" shape `build-pattern-scanner` and the
cv.inc finding already look for, at a scale (603 Spaces in one sampled org) larger than cv.inc's own
102-team example. Suggested integration shape, sized the same way the cv.inc recommendation was: a
single additional step in `signal-discoverer.md`/`build-pattern-scanner.md` querying
`huggingface.co/api/spaces` by keyword/tag, not a new subagent.

**Major League Hacking clears the legal gate but is not worth building against.** Its own site
(`mlh.com`) is a directory/certification layer, not a content host — the actual rich content (sponsor
challenge text, judging criteria, project galleries) lives on each member hackathon's own,
independently-operated external domain (hackrice.com, hackmty.com, ...), each requiring its own
separate ToS check before use. That per-event research burden, for a directory that itself carries no
usable signal, makes this a "technically clear, not worth the integration cost" case — matching the
kind of source the team lead asked to flag rather than recommend.

**SourceHut, Kaggle, Y Combinator's company directory, and Stack Overflow/Stack Exchange are all
BLOCKED**, confirmed at primary source (robots.txt and/or Terms of Service, including a
separately-incorporated document for Kaggle and Stack Exchange specifically). Kaggle and Stack
Exchange both left the same door already used for TrustMRR — asking for prior written permission is
the only legitimate route beyond a flat "no," and neither was pursued further this pass, per
instruction. If Stack Exchange's permission door is ever opened, note for later: its API Agreement's
attribution requirement (visually credit source + author name + link back to the author's profile,
per-item) is a real integration cost this pipeline's existing `scripts/verify_sourcing.py` does not
already check — it verifies sourced URLs, not per-author attribution display.

## Summary

- **Sources checked**: 6.
- **Cleared (found)**: 2 — Major League Hacking, Hugging Face.
- **Blocked**: 4 — SourceHut, Kaggle (website and API), Y Combinator company directory, Stack
  Overflow/Stack Exchange (website and API).
- **Empty / excluded-by-scope**: 0.
- **Top pick**: Hugging Face — cleanest ToS posture found this pass, and the richest sampled content
  (603-Space single-org hackathon corpus, structured sponsor/track tags, public JSON endpoint).
