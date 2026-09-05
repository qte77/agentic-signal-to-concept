# Hackathon content as a signal source — research pass, 2026-09-05

Question tested: can hackathon content (event descriptions, sponsor challenge statements, stated
goals, judging themes, problem statements) serve as a new signal source for this pipeline, distinct
from HN/ProductHunt/GitHub? Hypothesis: a sponsor challenge is a company *directly stating demand*,
and a hackathon compresses "many independent teams converging on the same problem" into a 24-48h
window — a cleaner, faster version of the convergence signal `build-pattern-scanner` already looks
for on GitHub/Show HN over 30-day windows.

Three domains checked: Devpost, cv.inc, AGI House (`app.agihouse.org`). ToS/robots.txt verified
fresh this pass via raw fetch (`polyfetch-scrape`, `httpx`/`patchright` backends) — no claim below is
carried over from `docs/plans/0001-concept.md`'s prior Devpost finding without being re-verified at
source today.

## Source coverage

### Devpost — BLOCKED (re-confirmed)

- `devpost.com/robots.txt`: `User-agent: *` / `Disallow:` (default UA open), but explicitly disallows
  eight named crawlers including `User-agent: anthropic-ai` / `Disallow: /`.
- Terms of Service, fetched fresh at its current canonical URL
  (`devpost.com/terms` → 301 → `info.devpost.com/terms` → 301 → `info.devpost.com/legal/terms-of-service`,
  HTTP 200, fetched 2026-09-05), exact quoted clause (Section 4, "Prohibited Conduct" context):

  > "Uses manual or automated software, devices, scripts robots, or other means or processes to
  > access, "scrape," "crawl" or "spider" **the Site**, **User Content** (including User Profiles
  > and Team Pages), or any related data or information"

  This directly answers the question the team lead posed: the clause is not scoped to project/
  submission pages only. "the Site" and "any related data or information" are unqualified — a
  hackathon listing or event-description page is unambiguously part of "the Site." There is no
  narrower reading under which a listing page escapes this clause. Violation consequences (same
  document, Section 4): "removal of some or all of your User Content..., disqualification from a
  Hackathon,... and/or termination of your Devpost account."
- **Verdict: BLOCKED, unambiguous, no loophole.** No workaround attempted, per instruction. This
  matches and reinforces `docs/plans/0001-concept.md` §3's prior 2026-09-01 finding — now
  re-verified against the live document rather than carried from memory.

**Rendering cross-check (added after initial draft, per a direct request to re-verify JS-shell risk
on this specific site).** The ToS/event/gallery fetches above initially came back via `polyfetch`'s
`httpx` tier — sufficient because the content was found server-embedded in the raw response body
(Next.js streaming payload / JSON-LD), not absent — but re-fetched all three with `--tier patchright
--wait-until networkidle` (full headless-Chromium render, JS execution complete) as a direct
cross-check against the possibility of a bot-served shell or JS-hydrated content the `httpx` tier
missed: (1) `cerebralvalley.ai/legal/terms` — patchright-rendered plain text is identical to the
`httpx` version, same section list, same zero hits for scrape/harvest/acceptable-use/data-mining; (2)
the example event page (`.../e/openai-gpt-6-astra-sf`) — the patchright-rendered page's embedded
schema.org `Event`/`Hackathon` JSON-LD contains the exact same Prizes/Rules text as the `.md` export
used below, word for word; (3) the natsec hackathon gallery — the patchright-rendered page's
schema.org `ItemList` JSON-LD reports `"numberOfItems":102"` with exactly 102 `CreativeWork` entries
(Vanguard, Shadow Fleet Detector, SkillHarness all present), matching the `.md` gallery export
exactly. No additional or different content appeared under full rendering in any of the three cases
— the findings below are not an artifact of an unrendered shell.

### cv.inc (→ cerebralvalley.ai) — FOUND, clears the ToS gate

- `cv.inc` is a redirect front-door (308) to `cerebralvalley.ai` — same operator (Cerebral Valley
  Enterprises Inc.), same Terms/Privacy documents apply to both hostnames.
- `cerebralvalley.ai/robots.txt`: `User-agent: *` / `Allow: /` (plus explicit `Allow` for a few OG-
  image API paths), with a short disallow list (`/auth/`, `/api/`, `/profile/create`,
  `/events/~/`, `/showcase/~/`, some query-param patterns) — none of it covers event/hackathon
  listing or description pages. The file opens with `# LLMs: https://cerebralvalley.ai/llms.txt`.
- Terms of Service, fetched fresh at `cerebralvalley.ai/legal/terms` (HTTP 200, 2026-09-05) — the
  page is a Next.js app; the ToS text is server-embedded in the streamed React payload (confirmed by
  reading the raw response body directly rather than trusting the outer HTML shell, per this run's
  SPA caution). Full document read start ("Terms of Service" H1) to end ("Contact" section, followed
  by the site footer). Sections: Acceptance of terms, Changes to the terms, Services, User
  responsibilities, Privacy, Limitation of liability, Termination, Governing law, Contact. **No
  clause anywhere bans scraping, crawling, spidering, bots, automated access, data mining, or AI
  training use** — searched for all of `scrape`/`crawl`/`spider`/`bot`/`automat`/`harvest`/
  `data mining` across the raw body; the only hits for `crawl`/`spider`/`bot` are unrelated favicon-
  detection JS boilerplate (`/bot|crawler|spider|robot|crawling/i.test(ua)`), not ToS text. Same
  null result for the Privacy Policy (`cerebralvalley.ai/legal/privacy-policy`).
- **Checked specifically for the Vercel/v0 pattern** (`docs/plans/0001-concept.md` §3 found Vercel's
  general ToS silent on scraping while a separately incorporated Acceptable Use Policy carried the
  actual ban) — searched the full raw ToS body for "incorporat[ed]"/"policy"/"guideline"/"acceptable
  use": the only incorporated-by-reference document is the Privacy Policy ("Please review our
  Privacy Policy, which is incorporated into these Terms..."), itself independently checked and
  clean of scraping language. No separate Acceptable Use Policy or Community Guidelines document
  exists on this site to carry a hidden restriction the way Vercel's did.
- The site's own `llms.txt` (`cerebralvalley.ai/llms.txt`) states directly: "robots.txt: public
  pages may be crawled," names a `llms-full.txt` "for agents that want the whole site in one fetch,"
  documents a `.md` suffix convention for machine-readable page variants
  (`/e/{slug}.md`, `/e/{slug}/hackathon/gallery.md`), and gives a sitemap. This is supporting
  context for the ToS reading above, not treated as itself authoritative — the operative check was
  the actual Terms of Service document, which independently contains no scraping bar.
- **Verdict: FOUND — no ToS or robots.txt bar identified.** This is a materially different posture
  from every other source checked in this repo's history (Devpost/Lovable/Replit/Indie Hackers/
  TrustMRR/Bolt.new/v0 all explicitly ban scraping in their own ToS); cv.inc/cerebralvalley.ai does
  not.

### AGI House (`app.agihouse.org`) — BLOCKED

- `app.agihouse.org/robots.txt` returns the SPA's `index.html` shell for any unmatched route (no
  robots.txt file actually exists at that subdomain) — absence of a robots.txt is not a ToS
  clearance, so this alone settles nothing.
- `www.agihouse.org/robots.txt` (the marketing host): `User-agent: *` / `Disallow:` (fully open) —
  again, robots.txt permissiveness only, not the controlling document.
- Terms of Service at `agihouse.org/terms` (`www.agihouse.org/terms`, "Last Updated: June 10, 2026")
  is a client-rendered, code-split Next.js page with `ssr:false` on its content component — the
  initial HTML and even the page-specific JS chunk contain no ToS text (`pageProps: {}`, content
  lazy-loaded via a further chunk). Rendered the page through `polyfetch`'s Patchright/browser tier
  (`--tier patchright --wait-until networkidle`) to get the actual displayed text rather than
  inferring "probably fine" from an empty static fetch. Section 1 ("Acceptance of Terms") defines
  scope explicitly: "the AGI House platform ("Platform"), including the website at agihouse.org
  **and the associated application**" — this unambiguously covers `app.agihouse.org`, the exact
  subdomain the example event URL uses. Section 7 ("Acceptable Use"), exact quote:

  > "You agree not to: ... Scrape, harvest, or collect data from the Platform without authorization"

- **Verdict: BLOCKED, unambiguous.** No workaround attempted.

## Content richness (cv.inc / cerebralvalley.ai only — the one source that cleared the gate)

Sampled the team-lead-given example page plus five more current/recent listings (an upcoming
sponsor hackathon, a completed sponsor hackathon with a public gallery, and several more pulled
from the site's own `llms-full.txt` full-text export):

- **`cerebralvalley.ai/e/openai-gpt-6-astra-sf`** (OpenAI-hosted, upcoming): full prize structure
  ($50k/$25k/$15k credits + DevDay tickets + 1yr ChatGPT Pro), an explicit build constraint ("All
  work must be built during the hackathon and submitted as a public open source repository -
  existing product code does not qualify"), team-size cap, and a stated goal ("Show us what you can
  ship with GPT-6 Astra's new capabilities"). Logistics-only fields (date/location/format) are also
  present but not the whole of the page.
- **`cerebralvalley.ai/e/3rd-annual-natsec-hackathon`** (Army xTech via Shield Capital, completed
  May 2026): the event page states the problem domain at a category level ("command & control,
  sensor integration, edge compute, cybersecurity, drones") and names the sponsor RFI relationship
  explicitly. Its **public project gallery**
  (`cerebralvalley.ai/e/3rd-annual-natsec-hackathon/hackathon/gallery.md`) lists **102 submitted
  projects** from one ~31-hour window (May 2, 9am – May 3, 4pm PDT), each with a written problem/
  solution description, team handles, GitHub link, and demo video. Several project descriptions
  cite the sponsor's own named problem-statement codes directly (e.g. "Problem statements addressed:
  PS2 (Edge Deployments and Drone Operation) primary... PS1 (Sensor Analysis and Integration)
  secondary") — direct evidence of a sponsor-issued problem-statement taxonomy that independent teams
  converged on, in writing, within a single compressed window.
- **A Vercel × Google Gemini hackathon ("Zero to Agent")**, sampled via `llms-full.txt`: an explicit
  "What we're looking for" section — "Agents that solve real problems–not just demos, but tools
  people actually want to use... Creative use of Gemini's multimodal and reasoning capabilities...
  Projects that take full advantage of Vercel's infrastructure" — this is a sponsor stating demand
  in its own words, not inferred from a complaint or a launch post.
- **A Rosewood/luxury-hospitality hackathon**, sampled via `llms-full.txt`: "Real-world problem
  statements tied to luxury hospitality and guest experience," with a "What you'll build" list
  (AI concierge/personalization, autonomous hotel operations, voice agents).
  the-`llms-full.txt` export also carries a numeric, weighted judging rubric for at least one
  hackathon (Impact Potential 25%, Technical Demo 50%, Creativity 15%, Presentation & Pitch 10%) plus
  submission requirements (GitHub link, demo video, "Deploy to production, no localhost allowed") —
  i.e. judging criteria are sometimes published in full, not just prize amounts.
- **An Anthropic "Built with Opus 4.6" virtual hackathon** and a **Gladstone/"Claude Science"
  biomedical hackathon**, both sampled via `llms-full.txt`: both carry an explicit sponsor
  invitation framed as demand ("we want you to push the boundaries of what's newly possible...
  solving problems we haven't even imagined") plus concrete constraints (participant caps, credit
  grants, prize pools).

**Conclusion on richness**: event pages on this platform are not logistics-only. Sponsor-stated
"what we're looking for" language, explicit problem-statement taxonomies, weighted judging criteria,
and submission constraints are present across the majority of sampled listings, not just the one
example given. Public hackathon galleries (where a host has made one public) are a genuine
many-independent-teams-in-one-window corpus, structurally the same shape as the pipeline's existing
GitHub/Show HN convergence evidence but denser: 102 teams in ~31 hours vs. GitHub's own
`build-pattern-scanner` pulls over a 12-month independence window.

## Synthesis

**Does this add something HN/PH/GitHub don't?** Yes, on cv.inc specifically, in two ways the
existing sources don't: (1) a sponsor's own "what we're looking for" text is first-party stated
demand, categorically different from inferring demand from a complaint thread or a Show HN launch;
(2) a hackathon gallery is a much more compressed convergence window (24-48h, sometimes fewer) than
the 30-day/12-month windows `signal-discoverer`/`build-pattern-scanner` currently use, and it is
explicitly organized around a named problem/theme by construction (the sponsor set it), reducing the
"is this a coincidental word match or a real cluster" ambiguity that `signal-discoverer.md` step 6
already flags as a real failure mode for keyword-clustered GitHub/HN pulls.

**Is it accessible at all?** Only partially, and this matters for how the recommendation should be
scoped. Of the three domains checked, only **cv.inc/cerebralvalley.ai** clears the ToS gate. Devpost
— almost certainly the largest hackathon-listing platform by volume — is unambiguously blocked, same
as prior findings. AGI House, a comparable curated-AI-hackathon operator, is also unambiguously
blocked, with a ToS clause naming "scrape, harvest, or collect data" and defining "Platform" to
include the exact subdomain in question. So the hypothesis holds for the *type* of signal cv.inc
carries, but does **not** generalize to "hackathon content" as a broad category — it is one specific
platform's compliant door, not a rule that hackathon platforms in general are open.

**Does the core hypothesis hold up against real content?** Yes, on cv.inc: sponsor-stated demand
language is real and common across sampled listings (not just the one given example), and at least
one completed hackathon's public gallery shows genuine compressed convergence (102 independent teams,
one ~31-hour window, several explicitly tagged against the sponsor's own problem-statement codes).

**Recommendation.** Worth adding as a new, narrowly-scoped source — not a general "hackathon
scraping" capability, but specifically `cerebralvalley.ai` (`cv.inc`) event, hackathon-listing, and
public-gallery pages, fetched via the same `polyfetch-scrape` discipline already used elsewhere in
this pipeline (the site's own `.md`-suffix and `llms-full.txt` conventions make this cheap: no HTML
parsing needed, `<slug>.md` and `.../gallery.md` return clean markdown directly). Suggested shape,
sized like the rest of this pipeline's incremental additions rather than a new phase:

- **Not a new subagent.** A single new step inside `signal-discoverer.md` (Phase 0, unfiltered) and
  a corresponding step inside `build-pattern-scanner.md` (Phase 1, scoped to `config/scope.md`'s
  category) is enough — this is the same shape GitHub/Show HN already take in both specs, not a
  reason to add a fourth subagent to a 4-spec pipeline (`AGENTS.md`'s "what this deliberately doesn't
  do" section already argues against premature machinery).
  - `signal-discoverer` addition: pull `cerebralvalley.ai/events.md` or the relevant city-feed page,
    cluster by stated theme/sponsor the same way GitHub `topics`/PH `topics` are clustered today.
  - `build-pattern-scanner` addition: for a chosen category, search recent/upcoming hackathon event
    pages for sponsor "what we're looking for"/problem-statement language relevant to the scope, and
    for any hackathon with a public gallery in-window, treat the gallery as convergence evidence —
    same independence heuristic (distinct author/org handles, no fork/shared-upstream relationship)
    already defined in `build-pattern-scanner.md` step 5, applied to gallery entries instead of
    GitHub repos.
- **Ethical boundary carries over unchanged, not re-derived**: a gallery's aggregate count and named
  problem-statement categories are fine to cite ("102 independent teams at the Army xTech hackathon,
  N of them addressing sponsor problem-statement PS2"); naming one specific team's specific gallery
  project as "the one to clone" is exactly the boundary `build-pattern-scanner.md`'s existing
  Ethical-boundary section already bars, unchanged by this new source.
- **Do not extend this to Devpost or AGI House.** Both are confirmed, unambiguous ToS bars — this
  finding closes that door for those two specifically; re-check only if either platform's own terms
  change, not as a standing todo.

If the team decides this is worth building, it is a small, additive change to two existing specs —
not new pipeline machinery — but that decision is left to the team lead, not assumed here.
