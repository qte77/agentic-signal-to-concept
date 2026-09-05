# Startup-registry discovery — 2026-09-05T204416Z

One-off research pass across `startups.gallery` and three similar public registries of
startup/indie-project listings, following this repo's own horizontal-discovery discipline
(aggregate pattern signal, ToS-gated before any pull). Not a formal pipeline stage — no new
`.claude/agents/*.md` spec was created for this.

## Source coverage

Four-state discipline (found / blocked / empty / excluded-by-scope), same as
`signal-discoverer`'s convention. All four sources below came back **found** — none were blocked
by ToS this pass, a different outcome than the Devpost/Lovable/Replit/Indie Hackers/TrustMRR/
Bolt.new/v0 precedent in [issue #10](https://github.com/qte77/agentic-signal-to-concept/issues/10)
and `docs/plans/0001-concept.md` §3. Read carefully anyway — "found" here means "no clause bars a
one-off read for aggregate research," not "unrestricted for any future automated/recurring pull."

| Source | robots.txt | ToS scraping clause | Verdict |
|---|---|---|---|
| `startups.gallery` | `Allow: /` (all agents, all paths) — fetched directly | No ToS/legal page exists on the site at all (checked `/about`; only a FAQ, no legal/terms link in footer) | **Found.** No contractual bar because no contract exists. Absence of a ToS is not blanket permission — treated here as "no known restriction," not "explicitly cleared," and re-checked before any repeat/automated pull. |
| `betalist.com` | Only the boilerplate robotstxt.org comment, no `Disallow` rules — fetched directly | [betalist.com/terms](https://betalist.com/terms), fetched directly: bans commercial use, copying, and mirroring the "materials," but contains **no explicit scraping/crawling/bot clause** | **Found**, with a caveat: the no-copy/no-mirror language is broad enough that bulk republishing of listings would be out of scope — consistent with this task's aggregate-only framing anyway. |
| `uneed.best` | Allows all agents except `?sortBy=`/`?orderBy=` query variants (irrelevant here) — fetched directly | [uneed.best/terms-of-use](https://www.uneed.best/terms-of-use): Prohibited-Activities clause reads, verbatim: *"Engaging in any automated use of the system, such as using scripts to send comments or messages"* and *"Using bots, scripts, or automated tools to manipulate votes, rankings, or any other metrics."* The named examples are write-side (posting, vote manipulation), but the operative phrase — "any automated use of the system" — is not itself limited to writes; this is structurally close to the clause that ruled out Lovable in `docs/plans/0001-concept.md` §3. | **Found, but narrower than first read.** What actually clears this source is not the ToS reading above — it's [uneed.best/privacy-policy](https://www.uneed.best/privacy-policy), which discloses a first-party **public, read-only MCP server** (`mcp.uneed.best`) and API, logged only for abuse monitoring, built for exactly this kind of programmatic read access. **This pass did not use that API/MCP path** — the `/weekly` page above was pulled as a single rendered-HTML read via WebFetch, not through the sanctioned endpoint, and the MCP server's own terms of use were not separately checked. Treat the HTML pull as a one-off, not a precedent for repeat/automated access; the MCP/API is the real integration path if this source is formalized. |
| `wip.co` | Allows all agents except a few auth/settings/search paths — fetched directly | [wip.co/terms](https://wip.co/terms), fetched directly | **Found, most explicit permission of the four.** The ToS directly contemplates reuse of "data from our APIs or site," conditioned on (a) a visible backlink to WIP and (b) non-commercial republication only ("not a company/startup"). This research doc's aggregate use fits inside that — it is not itself a republished product. |

Methodology note: every ToS/robots.txt reading above except BetaList's robots.txt (re-verified
with a raw-body fetch via `polyfetch-scrape`, confirming it is genuinely just the boilerplate
comment with no `Disallow` lines) came from WebFetch, which renders the page and returns a
small-model summary of it, not raw source text. Treated as reliable enough for a one-off triage
read; a formalization pass should re-pull the load-bearing clauses (especially Uneed's, above) with
a raw fetch before depending on this reading long-term.

Note on `startups.gallery` specifically: its actual content is VC-funded early-stage companies
(funding rounds, job postings, named investors) rather than indie/solo/vibe-coded projects — a
narrower fit to this task's "indie/side projects, vibe coded products" framing than its name
suggested going in. Kept in the ranking below as market-validation signal (what's already funded =
crowded/competed-for), not as a source of feasible indie-buildable niches.

## Ethical boundary applied

Every specific project named below is cited as **market/competitive evidence for a niche**, the
same treatment `build-pattern-scanner` gives a GitHub repo — never as a build target to clone. This
matters more here than usual because the deliverable is explicitly "what should we build": no
niche below should be read as "go build a copy of the named example," only "independent builders
converging on this problem is evidence the problem is real."

## ROI / feasibility proxies used

No real financial data was available for almost any listing (registries show taglines, occasional
vote counts, occasionally a funding stage — never MRR or user counts for indie listings). Proxies,
stated explicitly per the task's instructions, not invented numbers:

- **ROI proxy** — cross-source/cross-listing convergence within the ~1-week window pulled (same
  problem shape appearing as 3+ independently-named listings = validated demand, the same reading
  `docs/plans/0001-concept.md` §3 already gives "19 independently built apps, same shape"), weighed
  against how crowded/saturated that makes the niche for a new entrant.
- **Feasibility proxy** — apparent technical complexity of the listed examples (wrapper-around-an-
  API vs. multi-system orchestration), and whether the niche's core data/integration needs are
  already known to be ToS-gated per this repo's own §1–§3 findings (e.g. LinkedIn/X scraping for
  lead-gen tools is a known-gated dependency, not a fresh assumption).

Each score below is 1–5, low to high; ROI×feasibility is the ranking column. These are directional
triage scores, not calibrated metrics.

## Ranked niches/ideas

### 1. Agentic/coding-agent infrastructure tooling — ROI 3, Feasibility 4 → **12**

A "picks and shovels" cluster riding the coding-agent wave, three independent indie builders
converging on adjacent problems (trust/discovery for MCP servers, data APIs for agents,
agent-to-agent backlinking) — the strongest same-shape convergence found in this pass:
- **MCPVault** — "Explore and trust MCP servers with grades, signals, and verification"
  ([betalist.com](https://betalist.com/), Today/Sept 5 listing).
- **Webclaw** — "The web scraper API your AI agent deserves" ([wip.co/products](https://wip.co/products), pulled 2026-09-05).
- **LinkBunny** — "Backlink exchange for your coding agents" ([wip.co/products](https://wip.co/products) and [wip.co](https://wip.co/), pulled 2026-09-05).
- **Firecrawl** — "Web data API for AI," Series A ([startups.gallery](https://startups.gallery/), pulled 2026-09-05) — cited only as evidence the *category* (data/infra APIs for AI agents) is fundable, not counted toward the convergence signal and not a target to clone; it is a funded company, not an indie listing.

ROI 3: real 3-way convergence among indie listings, but this is a developer-facing infra layer —
smaller addressable audience than a consumer or SMB niche, and Firecrawl's presence shows the
category can scale to VC-backed competition fast. Feasibility 4: build blocks are public APIs
(GitHub, npm registries, MCP spec itself) — no ToS-gated dependency identified in this pass,
consistent with this repo's own note that GitHub's API is "well-documented, public, generous rate
limits."

### 2. Vertical ops SaaS for narrow small-business categories — ROI 3, Feasibility 4 → **12**

Recurring pattern of "unify the 3–4 disconnected tools a specific small-business operator already
juggles" — narrower TAM per vertical, but the pattern itself (not any one instance) repeats. Only
two genuine same-shape hits surfaced this pass (a third candidate, My Intranet HRIS, turned out to
be horizontal HR software usable by any business, not a specific vertical — excluded from evidence
below after re-checking its own tagline):
- **Caredence** — "Run your daycare with unified check-in, billing, reports, and parent comms"
  ([betalist.com](https://betalist.com/), Today/Sept 5 listing).
- **GestorMSP** — "Unify RMM, ITSM, PSA, CRM, and SOC to run your MSP without integrations"
  ([betalist.com](https://betalist.com/), Today/Sept 5 listing).

ROI capped at 3 partly because this is a thin n=2 convergence, weaker than niche #1's n=3 — noted
explicitly, not smoothed over. Feasibility scored 4: this is CRUD/workflow software, no gated
third-party data or ToS-restricted integration required, matching this repo's own KISS bias — the
actual work is picking one underserved vertical and doing the domain research, not fighting an
access barrier.

### 3. "AI visibility" / generative-engine-optimization monitoring — ROI 3, Feasibility 3 → 9

Two listings, not three — a third candidate initially grouped here (SEO Consensus, "Develop SEO
and Google Ads strategies for any public webpage") is conventional SEO/ad-strategy tooling, not
brand-citation tracking, and was removed after re-reading its own tagline. The two that genuinely
match are from two different registries, not the same day-window on one site:
- **SEOPulse** — "Track and grow your brand citations across AI search engines"
  ([betalist.com](https://betalist.com/), Thursday Sept 3 listing).
- **Visby** — "Track and enhance artificial intelligence visibility metrics"
  ([uneed.best/weekly](https://www.uneed.best/weekly), pulled 2026-09-05, 69 votes).

ROI scored 3, not higher: two independent listings is a real but weaker signal than this doc's own
stated "3+ listings" convergence bar used elsewhere — genuine early signal, not yet validated
demand by this pass's own standard. Feasibility capped at 3: there's no clean, stable, ToS-covered
API for "what did ChatGPT/Perplexity/Gemini say about my brand" — building this well means querying
multiple AI chat surfaces at scale, an access question this pass did not resolve (would need its
own ToS check per surface before committing to build).

### 4. AI-powered outreach / lead-gen tooling — ROI 3, Feasibility 2 → 6

Recurring pattern, real willingness-to-pay category (sales tooling), but feasibility is
structurally capped by this repo's own prior findings:
- **WhoGoes** — "Turn public LinkedIn posts into outreach-ready event attendee lists"
  ([betalist.com](https://betalist.com/), Today/Sept 5 listing).
- **ReplyHey** — "Find and answer high-intent Reddit, X, and LinkedIn leads daily"
  ([betalist.com](https://betalist.com/), Yesterday/Sept 4 listing).
- **Agentic Leadsourcing** — "Everyone builds the AI sales agent. I build what it reads"
  ([wip.co/products](https://wip.co/products), pulled 2026-09-05).

Feasibility scored 2, not higher: every one of these depends on scraping or bulk-reading LinkedIn,
Reddit, and/or X. Reddit (unauthenticated) and X (cost-prohibitive since its Feb-2026 pricing
overhaul) are ToS/cost findings this repo's own `docs/plans/0001-concept.md` §1 and §3 already
verified at source. **LinkedIn is not one of those** — no LinkedIn ToS check was done in this repo
or this pass; the claim that LinkedIn scraping is barred is asserted from general knowledge, not
fetched, and is marked **unverified** here rather than treated as a confirmed gate. Feasibility 2
reflects two confirmed-gated sources (Reddit, X) plus one unverified-but-plausible one (LinkedIn),
not three confirmed gates — re-check LinkedIn's own terms before leaning on this score further.

### 5. AI content/video generation wrapper tools — ROI 2, Feasibility 3 → 6

The single most saturated cluster observed this pass — at least six near-identical listings in one
week window: **TapVid**, **Motiofy AI**, **Photo to Video**, **Molyin**, **Convia Studio**,
**explain.ink** (all [betalist.com](https://betalist.com/), Today/Yesterday/Thursday listings,
2026-09-03–09-05). ROI scored low deliberately: this is past the "many independent builders =
validated demand" signal and into "commoditized wrapper around the same 2–3 underlying gen-AI
model APIs" — the pattern this repo's concept doc warns is a race-to-the-bottom, not a moat.
Feasibility is only medium (3) because differentiation, not implementation, is the real barrier.

### 6. AI agent/employee orchestration for solopreneurs — ROI 2, Feasibility 2 → 4

Also heavily crowded — **Sistava** ("Hire AI employees to run sales, marketing, and support 24/7"),
**Ardelia** ("Spin up an AI-run company that debates, decides, and remembers"), **GoldMine**
("Autonomous marketing agent designed for solo operators"), **Zens AI** (customer-support
automation) — all [betalist.com](https://betalist.com/)/[uneed.best/weekly](https://www.uneed.best/weekly)
listings, 2026-09-03–09-05 — plus **Town** ("The unusually helpful AI assistant," Series A,
[startups.gallery](https://startups.gallery/)) showing the category is already VC-funded too.
Both ROI and feasibility scored low: oversaturated supply side despite real demand, and reliable
multi-step agent orchestration remains a hard, foundation-model-quality-dependent engineering
problem, not a KISS-buildable wrapper.

### Noted but not ranked: AI-detection-evasion tooling

**Verva** — "Humanize AI text and bypass AI detectors in seconds"
([betalist.com](https://betalist.com/), Yesterday/Sept 4 listing) — flagged, not scored. This is an
adversarial-evasion category (helping users defeat AI-content detectors), a build-ethics concern
distinct from this task's ToS-access lens. Not recommending it as a niche regardless of its
ROI/feasibility profile.

## Summary

- **Sources**: 4 checked, 4 found, 0 blocked, 0 empty, 0 excluded-by-scope this pass.
- **Niches ranked**: 6 scored + 1 flagged-and-excluded (Verva, ethics).
- **Top 3 by ROI×feasibility**: (1) agentic/coding-agent infrastructure tooling (12, n=3 indie
  convergence — the strongest signal found), (2) vertical ops SaaS for narrow small-business
  categories (12, n=2, weaker convergence than #1), (3) AI-visibility/GEO monitoring (9, n=2, below
  this doc's own "3+ listings" validated-demand bar).
- **Formalization recommendation**: not yet — see final report to team lead for the reasoning
  (source content is thin per-listing compared to HN/GitHub, and none of the four came back
  blocked, so there is no fresh ToS-navigation work to encode into a spec the way
  `build-pattern-scanner`'s GitHub/Show HN/Bluesky mix needed).
