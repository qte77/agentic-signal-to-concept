# Freelancer finance — Build-pattern findings (first run)

## §Research

Scope per `config/scope.md`: independent small-build activity (AI-assisted "vibe coded" or
conventionally built, treated identically) converging on the personal finance / expense tracking /
invoicing problem space for freelancers and small businesses. This category was surfaced by
`signal-discoverer`'s first real run (`discovery/2026-09-04T060617Z-categories.md`, category #5). No
date window is bounded in `scope.md`; this run applies `build-pattern-scanner`'s own v1 default — a
12-month window, **2025-09-04 through 2026-09-04** (the run date). No prior findings exist for this
scope — this is the first execution of this spec against it.

### Independence heuristic (v1 default, applied as-is)

Per `.claude/agents/build-pattern-scanner.md`: two builds count as independent only if ALL of —
distinct author/org handles; both created within the 12-month window; no direct fork/clone
relationship to each other; no shared canonical upstream repo. **Stated plainly as the spec's coarse
v1 default, not a validated methodology.**

**Basis for the "no fork" clause below:** none of this run's GitHub Search API queries used a `fork:`
qualifier, and GitHub's Search API excludes forks from results by default without one — so every
GitHub-sourced item below is fork-filtered by GitHub itself, not by per-repo inspection of the `.fork`
field. No individual repo's fork status was separately checked this run.

**Two data-quality exclusions applied, in the same spirit as prior runs' spam/template flags:**

1. **API-surface documentation profiles, not competing builds (excluded).** The `org-owned
   `api-evangelist` account published four repos this window —
   [invoice-ninja](https://github.com/api-evangelist/invoice-ninja),
   [freshbooks](https://github.com/api-evangelist/freshbooks),
   [freeagent](https://github.com/api-evangelist/freeagent), and
   [zoho-invoice](https://github.com/api-evangelist/zoho-invoice) — each self-described as "an
   independent third-party profile of a public API surface" for an *existing commercial product*.
   These document other companies' APIs; they are not independent builds of a competing tool and are
   excluded from every count below.
2. **Portfolio/demo sample, not a working tool (excluded).**
   [peterwkdev-creator/sample-freelancer-expense-invoice-tracker](https://github.com/peterwkdev-creator/sample-freelancer-expense-invoice-tracker)
   self-describes as "a formula-driven Excel expense and invoice tracker for freelancers - portfolio
   sample (fictional data)." Excluded as a coding-portfolio artifact rather than a build intended for
   real use.

### Source coverage

- **GitHub Search API (`search/repositories`), authenticated via `gh`** — reachable. Confirmed live
  via `gh api rate_limit --jq '.resources.search'`: 30 req/min authenticated (10 req/min
  unauthenticated), a separate bucket from the 5,000/hour core API. All calls used `env -u GH_TOKEN -u
  GITHUB_TOKEN gh api -X GET search/repositories -f q='...' --jq '...'` per this workspace's
  environment note (a stray `GH_TOKEN` env var otherwise shadows the valid stored login). Queries run,
  all `created:>2025-09-04` unless noted: `invoicing freelancer` (1,516 total hits), `expense tracker
  freelancer` (90 total hits), `"no bank sync" expense` (0 hits — found-empty for that exact phrase;
  see Pattern 2's `"no bank"` Show HN finding below for the same complaint sourced differently),
  `topic:invoicing` (766 hits, corroboration only, not individually inspected), `topic:expense-tracker`
  (2,468 hits, corroboration only, not individually inspected), `topic:lovable invoice OR expense OR
  freelancer OR finance` (16 hits, no date filter — Lovable-ecosystem corroboration per step 4),
  `topic:bolt invoice OR expense OR freelancer` (0 hits), `"v0.dev" invoice freelancer` (0 hits),
  `"bolt.new" invoice expense` (0 hits), `"v0.dev" invoice` (0 hits), `"v0.dev" expense` (0 hits),
  `"bolt.new" freelancer OR invoicing OR "expense tracker"` (1,706 hits, no date filter — but every
  top-ranked result was a bolt.new *clone/tooling* repo, not a finance product; found, but empty of
  on-topic signal). All genuinely found, not blocked — including the deliberately-narrow 0-hit
  queries, which are found-empty for that specific phrase/topic combination, not a source failure.
- **Show HN via HN's Algolia API** — reachable, unauthenticated, fetched via `uv run --directory
  ../polyfetch-scrape polyfetch fetch <url> --show-body`. Queries run, all `tags=show_hn` +
  `numericFilters=created_at_i>1725436800` (the 12-month window, epoch for 2025-09-04) unless noted:
  `freelancer` (286 total hits, most recent ~20 read in full), `invoice` (very high hit count — the
  bare term also stem-matches "invite"/"involve"/"invoke", consistent with the spec's warning that
  Algolia's `query` is plain full-text; the first ~15 results read in full, 2 on-topic), `expense`
  and `invoicing` as bare single-term queries were also tried and found **noisy in the same way**
  ("expense" matched "experience," "invoicing" matched "involving" via Algolia's stemming/typo
  tolerance) — previews showed no additional on-topic hits beyond what the other queries already
  surfaced, so these two were not read in full past the preview (a coverage gap, noted rather than
  silently dropped). `"no bank"` (17 total hits across 2 pages, both pages fetched and all 17 read in full — directly
  confirms the discovery run's "no bank-account linking required" complaint with a sourced quote, see
  Pattern 2; of the 17, only 2 are freelancer/small-business-scoped — DrakeAI and Chargenda — the
  remaining 15 are general personal-finance apps, see Pattern 2's adjacent-signal note). All genuinely
  found, not blocked or empty. **Caveat on all Show HN dates used below:** the post's `created_at` is
  used as a within-window-proxy for the product's own creation date; the underlying repo/site's actual
  creation date was not separately fetched for any Show HN-sourced instance. All dates used are
  2026-02 or later, well inside the window's 2025-09-04 start, so this is a low-risk assumption here
  (unlike a run where a post sits close to the window boundary).
- **Bluesky public post-search API** — **blocked, confirmed this run.** `uv run --directory
  ../polyfetch-scrape polyfetch fetch
  "https://public.api.bsky.app/xrpc/app.bsky.feed.searchPosts?q=freelancer%20invoicing"` returned a
  `FingerprintBlock` at HTTP 403 (`patchright fetch failed after 3 attempts (status=403)`), consistent
  with the spec's documented finding. Not re-tested against the sibling `getProfile` endpoint this
  run (already established by prior runs; not repeated here to conserve query budget). Recorded as
  **blocked**, not absence of signal, per the four-state discipline.
- **Devpost, Lovable/Replit showcases, Indie Hackers, TrustMRR** — **excluded-by-scope**, unchanged
  from the spec. Not attempted directly.
- **Bolt.new / v0-Vercel GitHub substitute (step 4)** — **attempted this run for the first time
  against a real category** (per the orchestrator's instruction, since it "hasn't been run for real
  yet anywhere in this repo"). Result: **found, but empty of on-topic signal** for this specific
  category. `topic:bolt` combined with finance keywords returned 0 hits; `"v0.dev"`/`"bolt.new"`
  combined with `invoice`/`expense`/`freelancer` returned 0 hits each; the broadest untargeted
  `"bolt.new"` free-text query (1,706 hits) surfaced only bolt.new clone/tooling/prompt-template
  repos, none a finance product for this category. This is a genuine negative result for
  freelancer-finance specifically, not a re-confirmation of the general Bolt.new/v0.dev signal — the
  substitute channel (Show HN) carried this category's signal instead (Even Keel, ApeiroCraft, etc.,
  below), consistent with the spec's own caution that GitHub topic search is "not a 1:1 substitute."

### Ethical boundary applied

Every pattern below names an aggregate count and lists multiple independent instances; no single
project is presented as "the one to build like." Stars, HN points, and org-vs-individual ownership
appear only as neutral corroborating metadata, never as a ranking signal elevating one project above
the others in its pattern.

---

### Pattern 1 — 10 cited independent instances (GitHub's own `total_count` for the query was 1,516; the top 30 by `updated desc` were inspected, all on-topic — a representative subset, not an exhaustive read of all 1,516): all-in-one invoicing / business-OS apps for freelancers and small businesses

The dominant convergence by volume: distinct, unrelated builders each independently building a
"manage clients, send invoices, get paid" tool scoped explicitly to freelancers/small businesses,
frequently self-described as a FreshBooks/Invoice-Ninja alternative.

> "Freelancer Invoicing Software & Business OS for India"
> — solostackweb, [solostackweb/Solostack](https://github.com/solostackweb/Solostack), created
> 2026-05-06

> "Self-hosted admin panel for freelancers and small agencies: projects with milestones and time
> tracking, CRM, invoicing and quotes with PDF output, support tickets, wiki and a client portal.
> Plain PHP and MySQL, no framework, no build step."
> — immarketing-dev, [immarketing-dev/admin-dashboard](https://github.com/immarketing-dev/admin-dashboard),
> created 2026-09-03

> "BillBuddy is a fast, intuitive, and modern mobile and web application built with Expo and React
> Native to help small business owners and freelancers easily generate, manage, and share professional
> invoices."
> — sanjanb, [sanjanb/billbuddy-app](https://github.com/sanjanb/billbuddy-app), created 2026-09-04

> "Professional invoicing for freelancers and small businesses."
> — warrengalyen, [warrengalyen/BillFoundry](https://github.com/warrengalyen/BillFoundry), created
> 2026-08-22

> "LedgerPilot — invoicing & client management SaaS for freelancers. Vanilla JS, zero dependencies,
> freemium business model (3 free invoices/mo → Pro $12/mo)."
> — umutseve4, [umutseve4/ledgerpilot](https://github.com/umutseve4/ledgerpilot), created 2026-08-19

> "I built Invovanta to help freelancers and small businesses manage clients, create professional
> invoices, track payments, and keep their business information organized in one place."
> — jitukedir, [Show HN post](https://news.ycombinator.com/item?id=49501845) linking
> https://invoiceflowdesign.app/, created 2026-08-30, 1 point

> "I spent years working on the tools in my family's business, doing tiling, painting and decorating
> ... That was the reason I started building Snapquo ... Get the quote done, send it to the customer,
> let them accept and sign it, take a deposit if you want to, and then turn it into an invoice."
> — maclinz, [Show HN post](https://news.ycombinator.com/item?id=49511368) linking
> https://snapquo.com/, created 2026-08-31, 3 points (also named in discovery's own HN product list —
> re-surfaced here independently via a general query, not a targeted lookup, corroborating the
> discovery run's finding with a fresh sourced quote)

> "Show HN: BillDesk – Offline invoicing for freelancers (Windows and Mac)"
> — afif1710, [Show HN post](https://news.ycombinator.com/item?id=49135745) linking
> https://getbilldesk.vercel.app, created 2026-08-01, 3 points

> "I'm 15 and built this solo. Freelancers typically juggle 5-6 disconnected tools to run what is
> essentially a one-person business. Helios puts CRM, contracts, invoices, proposals, time tracking
> and client outreach in one place."
> — Koran37, [Show HN post](https://news.ycombinator.com/item?id=48683765) linking
> https://www.helios.today/, created 2026-06-26, 2 points

> "I recently built LoveInvoice, a pleasant invoicing tool for freelancers and small businesses ...
> wanted to build something simple that doesn't require a subscription or a bunch of features you
> don't need when you're just getting started."
> — rkn7, [Show HN post](https://news.ycombinator.com/item?id=48669531) linking
> https://loveinvoice.com, created 2026-06-25, 2 points

Further instances seen in the same GitHub search but not individually cited (space, not exhaustive):
`BobPChand/AIInvoiceGenerator`, `Mishi-TKK/Sovereign-Ledger`, `shashmit/scaly`,
`titaniumcoder/pocket-cfo`, `manishkumar0013/FreelanceFlow`, `chubbunny78-design/taskflow-ai`,
`TatiShayo/billflow`, `srikanthraj9/billflow-invoicing`, `murdahlob/tallysheet`, `Ishtiaq5/flowdesk`,
`rudreshsankpal21/ClientFlow` — all distinct owners, all created within the window, all matching the
same convergence.

---

### Pattern 2 — 8 cited independent instances: freelancer expense/income tracking and profit-visibility dashboards

A related but distinct convergence: tools scoped to tracking money *coming in and going out* and
visualizing profit, rather than the invoice-generation/send-to-client workflow of Pattern 1. One of
the eight cited instances (DrakeAI) explicitly builds in "no bank sync" as a design principle —
directly confirming the complaint discovery surfaced ("no bank-account linking required") with a
fresh sourced quote rather than assuming it from a tagline.

> "Show HN: DrakeAI – expense tracker you log by voice or text, no bank sync"
> — a_protsyuk, [Show HN post](https://news.ycombinator.com/item?id=49187481) linking
> https://drakeai.app/, created 2026-08-05, 2 points (named in discovery's own HN product list;
> re-confirmed here via a general `"no bank"` query, not a targeted lookup — the discovery-stage
> tagline holds up as a real, sourced, first-person design claim. **Caveat**: the post's own text
> says "expense tracker," not "freelancer expense tracker" — it does not itself name freelancers or
> small businesses. It is treated as in-scope here because discovery placed it in this category, not
> because this run's own reading of the Show HN post demonstrates freelancer-specific scoping.)

**Adjacent signal, not freelancer-scoped, sourced separately from the 8 cited instances above:** the
same `"no bank"` Show HN search surfaced 17 total hits across the window; only DrakeAI and one other
(Chargenda, a business-subscription tracker) are business-scoped at all. The remaining 15 are general
personal-finance apps with no freelancer or small-business framing — a much larger convergence on
"no bank sync" as a design principle than this category alone would suggest, worth flagging to
concept-synthesis as context even though it sits outside this run's category boundary:

> "It's intentionally simple and privacy-friendly (manual tracking, no bank connections) ... managing
> subscriptions for teams or businesses."
> — brokeceo7, [Show HN post](https://news.ycombinator.com/item?id=46367385) linking
> https://www.chargenda.com/, created 2025-12-23, 2 points (business-subscription tracking —
> adjacent to, not the same as, freelancer expense/invoicing, but the only other business-scoped
> "no bank" instance found)

> "I built Whisper Money for three reasons ... It's local-only: no accounts, no server, no cloud, no
> bank sync."
> — brauhaus, [Show HN post](https://news.ycombinator.com/item?id=48957761) linking
> https://play.google.com/store/apps/details?id=com.pluckd.cashzilla, created 2026-07-18, 2 points
> (general personal finance, not freelancer/business-scoped — cited only as corroboration that
> "no bank sync" is a broader design convention, not a freelancer-specific one)

> "I didn't want to hand my bank logins to a third-party aggregator ... without bank connections,
> syncing the same dashboard on every device."
> — pedromlsreis, [Show HN post](https://news.ycombinator.com/item?id=48659054) linking
> https://usequantive.app/demo, created 2026-06-24, 1 point (general net-worth tracker, same caveat)

> "The expense, income, and invoice tracker is built for self-employed people and freelancers. Replace
> the spreadsheet, not your accountant."
> — nicojuhari, [Show HN post](https://news.ycombinator.com/item?id=49282790) linking
> https://simple-trackr.com, created 2026-08-13, 1 point

> "Show HN: A revenue dashboard for US freelancers in a single HTML file"
> — saltypixel, [Show HN post](https://news.ycombinator.com/item?id=48461693) linking
> https://taliivue.com/, created 2026-06-09, 2 points

> "FinSight AI is an AI-powered smart invoicing and expense intelligence platform for freelancers and
> SMEs. Extract invoice data using OCR, organize expenses, generate AI-driven financial insights,
> track cash flow, automate reminders, and visualize business analytics."
> — Genofogu, [Genofogu/FinSight-AI](https://github.com/Genofogu/FinSight-AI), created 2026-08-07

> "Full-stack invoicing & expense tracker for freelancers — client management, PDF invoice generation,
> Stripe billing, and a real-time analytics dashboard."
> — hemapriya137, [hemapriya137/Ledgerly](https://github.com/hemapriya137/Ledgerly), created
> 2026-08-28

> "Smart finance, tax, expense, income and invoice management platform built for Indian freelancers
> and small businesses."
> — kartikbansode, [kartikbansode/finwise](https://github.com/kartikbansode/finwise), created
> 2026-06-16

> "A premium, cinematic command center for freelancers, consultants and founders. Finora combines
> project management, income/expense tracking and auto-calculated analytics in one dark, gold-accented
> workspace."
> — kaydeva, [kaydeva/Finora](https://github.com/kaydeva/Finora), created 2026-08-09

> "A modern profit and expense tracking dashboard for small businesses and freelancers. Log income and
> expenses, visualize trends with interactive charts, and get a real-time snapshot of net profit."
> — MrMikeAde, [MrMikeAde/Profit-tracker](https://github.com/MrMikeAde/Profit-tracker), created
> 2026-07-29

Further instances seen, not individually cited: `BobPChand/AIBookkeeperPro` (note: the same author,
BobPChand, also built the AI Invoice Generator cited in Pattern 1 — one builder, two separate finance
tools within the window, counted once per pattern, not double-counted as two independent authors),
`MeAkash77/TaxHacker-UK...`, `asunnyboy861/SnapLedger`, `s12101406-sys/Expenso`.

---

### Pattern 3 — 6 cited independent instances: time-tracking that ends in an invoice/bill, for freelance consultants

A narrower, product-shape-distinct convergence: track billable hours first, generate the invoice/PDF
bill as the output, rather than starting from an invoice form.

> "Show HN: I built a time-tracking app for freelancers and contractors"
> — mufasa159, [Show HN post](https://news.ycombinator.com/item?id=49248154) linking
> https://github.com/paracosmos-studio/tallier, created 2026-08-10, 2 points (repo lives under the
> `paracosmos-studio` org handle rather than mufasa159's personal account — flagged, not independently
> confirmed as org- vs. individual-owned this run)

> "Show HN: Time tracking for solo consultants that ends in a PDF invoice"
> — Stackedboost, [Show HN post](https://news.ycombinator.com/item?id=49497476) linking
> https://hourtobill.com/demo, created 2026-08-30, 4 points

> "Hi, HN. I built Closed Rings. A developer-friendly, AI-agent-first time tracker ... This is
> primarily for consultants or freelance developers who want to start tracking their time right away."
> — samacs, [Show HN post](https://news.ycombinator.com/item?id=48189544) linking
> https://closedrings.sh/en, created 2026-05-19, 6 points

> "Self-hosted time tracking, invoicing, and expense management"
> — AKolenda, [AKolenda/timetracker](https://github.com/AKolenda/timetracker), created 2026-05-08

> "Offline-first time tracking & invoicing Flutter app for freelance developers."
> — OleksSobol, [OleksSobol/CodeLedger](https://github.com/OleksSobol/CodeLedger), created 2026-02-13

> "TimeTrack Freelance work timer & Invoice Machine"
> — OhHeyItsJason, [OhHeyItsJason/TimeTrack](https://github.com/OhHeyItsJason/TimeTrack), created
> 2026-02-20

---

### Pattern 4 — 4 cited independent instances: narrow, free, no-signup point-solution tools for a single freelance business task

The smallest-scope convergence: rather than a full invoicing/expense SaaS, a single free tool solving
one specific piece of freelance business admin (pricing, proposal tracking, contract generation).

> "Show HN: Even Keel – free, no-signup freelance pricing calculators"
> — evenkeelhq, [Show HN post](https://news.ycombinator.com/item?id=48933692) linking
> https://evenkeelhq.com/, created 2026-07-16, 3 points (also posted 2026-07-10, same author, same
> product — counted once per the independence heuristic's same-author dedup)

> "I built ApeiroCraft after getting tired of sending proposals and having zero visibility into what
> happened next ... Turn the accepted proposal into an invoice with one click."
> — lolllll, [Show HN post](https://news.ycombinator.com/item?id=49158160) linking
> https://apeirocraft.tech, created 2026-08-03, 2 points (also posted 2026-07-30, same author, same
> product — counted once)

> "Walks through deliverables, payment schedule, revision caps, IP ownership, and mutual termination
> terms, then generates a formatted PDF. No signup, no login."
> — varunKvK, [Show HN post](https://news.ycombinator.com/item?id=48874361) linking
> https://www.clauseapp.space/tools/sow-generator, created 2026-07-11, 1 point

> "Calculator to help freelancers find their ideal hourly or daily rate"
> — alvarojmza, [alvarojmza/freelancer-rate-calculator](https://github.com/alvarojmza/freelancer-rate-calculator),
> created 2026-04-23 (surfaced via `topic:lovable`, the step-4 GitHub substitute for
> Lovable-ecosystem showcase signal — built with Lovable per the topic tag)

---

### Summary

- **Patterns found:** 4 — (1) all-in-one invoicing/business-OS apps, (2) expense/income tracking &
  profit dashboards, (3) time-tracking-to-invoice for consultants, (4) narrow free point-solution
  tools (pricing calculators, proposal tracking, SOW generation).
- **Independent instances cited (deduplicated per the heuristic above):** 28 (Pattern 1: 10 ·
  Pattern 2: 8 · Pattern 3: 6 · Pattern 4: 4), plus 3 adjacent (not freelancer-scoped) "no bank sync"
  citations noted separately in Pattern 2. Drawn from a much larger population by GitHub/Algolia's own
  reported totals — not independently verified item-by-item beyond the subsets actually inspected:
  1,516 GitHub `total_count` for "invoicing freelancer" (top 30 inspected), 90 for "expense tracker
  freelancer" (top 20 inspected), 286 Show HN "freelancer" hits (top 20 inspected), 17 Show HN
  "no bank" hits (all inspected), within the 12-month window.
- **Two discovery-stage complaints independently re-confirmed with fresh sourced quotes this run**
  (not assumed from taglines): "no bank-account linking required" (DrakeAI, Pattern 2) and the
  late-night quotes/invoicing chore for solo trades operators (Snapquo, Pattern 1) — both were named
  in discovery's product list and both re-surfaced via this run's own general queries, not targeted
  lookups.
- **Data-quality exclusions applied:** 4 `api-evangelist` third-party API-documentation repos
  (not competing builds) + 1 portfolio/demo sample repo (fictional data, not a working tool) — see
  §Research.
- **Source states:** GitHub Search API — found (authenticated `gh`, 30 req/min, no blocking
  encountered) · Show HN (Algolia) — found · Bolt.new/v0-Vercel GitHub substitute (step 4, run for
  the first time against a real category this run) — found, but empty of on-topic signal for this
  category specifically · Bluesky — **blocked** (HTTP 403 `FingerprintBlock`, consistent with the
  spec's documented finding) · Devpost/Lovable-Replit showcases/Indie Hackers/TrustMRR —
  excluded-by-scope.
- **Net read for concept-synthesis:** this is a large, high-volume, multi-shaped convergence — not
  one dominant tool shape but (at least) four distinct product shapes independently and repeatedly
  built by unrelated individuals within the same 12-month window, with two of the discovery run's
  specific complaint taglines directly corroborated by first-person build quotes.
