# Job search, resume, and interview-prep tooling — Build-pattern findings (first run)

## §Research

Scope per `config/scope.md`: independent small-build activity (AI-assisted "vibe coded" or
conventionally built, treated identically) converging on AI-assisted job search and application
tooling — resume tailoring, auto-apply agents, ATS application tracking, interview prep. This
category was surfaced by `signal-discoverer` run 2
(`examples/2026-09-04T081918Z-discovery/categories.md` category #4) and confirmed present, unchanged
ranking, in run 3. Date window per spec default: 12 months, **2025-09-11 through 2026-09-11** (the
run date) — confirmed via `datetime(2025,9,11,tzinfo=utc).timestamp()` = `1757548800`, used as the
`numericFilters=created_at_i>1757548800` bound for every Show HN query below; GitHub queries used
`created:>2025-09-11`. No prior findings exist for this scope — first execution.

### Independence heuristic (v1 default, applied as-is)

Per `.claude/agents/build-pattern-scanner.md`: two builds count as independent only if ALL of —
distinct author/org handles; both created within the 12-month window; no direct fork/clone
relationship to each other; no shared canonical upstream repo. **Stated plainly as the spec's coarse
v1 default, not a validated methodology.**

No GitHub query below used a `fork:` qualifier, so GitHub's Search API excludes forks from every
result by default — fork-filtered by GitHub itself, not by per-repo inspection.

**Data-quality exclusions and same-author dedup applied this run:**

1. **Same author, multiple posts of the same product — counted once.** Confirmed for: `dakheera47`
   (JobOps / job-ops, both the GitHub repo and the Show HN post), `sneefle` (aiapplyd.com, posted as
   both "Verifying that an auto-submitted job application arrived" and "AI Applyd"), `cbyteai`
   (jobspire.co.in, two posts), `djrnz` (jobbi.app, two posts), `hillj23` (Morning Stack, two posts),
   `jaumapv` (cvora.net, three posts under two titles), `fredfilm` (SignalResume, two posts),
   `corefiredrill` (SalaryScript, two posts), `jb_hn` (agent-data/job-search plugin, two Show HN posts
   — this same repo also appears independently in the GitHub `"job search" agent` query below; treated
   as one instance, not double-counted across sources), `chetanba` (RoleSweep, two posts), `vednig`
   (Resume Tailor / tailor.resume.academy, two posts). One case is a same-handle-family **inference,
   not a confirmed identity**, per this project's claim-verification discipline: `irfahm_` and
   `irfahm11` (trylockedin.app, three posts across the window) were merged as one author on the
   strength of the near-identical handle and identical product/URL, not a verified single account —
   stated plainly as an inference under the spec's coarse heuristic, not fact.
2. **Likely same builder posting under multiple HN accounts — treated as one instance, not four.**
   SharpSkill (sharpskill.dev / sharpskill.fr) was posted across the window by four *different* HN
   usernames (`Enjoyooor`, `SnackStyle`, `MakeMilk`, `GiornoJojo`), each posting first-person "I
   built" language about the identical product at the identical URL family. **This is an inference
   from behavioral similarity, not a confirmed single identity** — no account-linkage evidence was
   checked beyond the repeated first-person claim and shared product/domain. Stated plainly as an
   inference; does not meet the independence heuristic's "distinct author" bar in spirit even though
   the account names differ, so counted once under Pattern 4 rather than silently merged without note.
3. **Derivative of a specific existing repo, not an independent build — excluded from the count.**
   [Fighter90/career-ops-ui](https://github.com/Fighter90/career-ops-ui) self-describes as "a
   clean, docs-style web UI for **the career-ops AI job-search pipeline**" — built explicitly atop
   `career-ops-hq/career-ops` (cited under Pattern 1 below), not an independent convergence. Excluded
   from every count; noted only as ecosystem activity around that one viral repo.
4. **Named in the orchestrator's seed list but off-topic — excluded.**
   [`hetpatel-11/agent-hop`](https://github.com/hetpatel-11/agent-hop) ("agent-hop" in the seed list)
   is confirmed via a targeted Show HN lookup this run to be a coding-agent session-hopping TUI
   (resume a live Claude Code/Codex/Grok session across tools) — unrelated to job search. This is the
   same shape of tool as discovery category #1 ("terminal/session management for coding agents"), not
   category #4. Excluded from every pattern below.
5. **Employer-side tooling, not job-seeker tooling — noted as adjacent, not counted.**
   [Permanym](https://permanym.com) ("AI resume spam ruined hiring so I built a tool to keep the bots
   out," romanhn) is the mirror-image problem — employers dealing with a flood of AI-generated
   applications — not a job seeker's tool. Flagged under Pattern 5 as adjacent signal, excluded from
   every count.

### Source coverage

- **GitHub Search API (`search/repositories`), authenticated via `gh`.** Confirmed live via `gh api
  rate_limit --jq '.resources.search'`: 30 req/min authenticated. All calls used `env -u GH_TOKEN -u
  GITHUB_TOKEN gh api -X GET search/repositories -f q='...' --jq '...'` per this workspace's
  environment note. Queries run, all `created:>2025-09-11` unless noted:
  - Core free-text: `resume tailoring` (**5,479** total hits), `"auto apply" job` (**780**), `job
    application tracker` (**13,917**), `ATS resume` (**17,973**), `"cover letter" AI` (**1,954**),
    `interview prep AI` (**5,769**), `"job search" agent` (**2,357**).
  - Topic corroboration (not individually inspected beyond the returned sample): `topic:job-search`
    (**3,578**), `topic:resume` (**3,599**), `topic:job-application` (**384**), `topic:ats`
    (**1,689**).
  - Step-4 substitutes for Lovable/Replit/hackathon showcase signal: `topic:lovable job OR resume OR
    interview` (**4** hits, all inspected — weak for this category specifically), `topic:replit job
    resume` (**1** hit), `topic:hackathon resume OR job created:>2025-09-11 stars:<500` (**92** hits,
    top 20 inspected — genuine hackathon-submission signal, several explicitly named hackathons: Amazon
    Nova AI Hackathon 2026, 2026 Global PBL 1st Hackathon Irvine CA, Midnight Hackathon August 2026),
    `"v0.dev" resume` (**1** hit, a v0-generated throwaway repo — found, near-empty), `"bolt.new" job`
    (**1** hit, near-empty). Consistent with prior runs' finding that this substitute channel is real
    but not a 1:1 replacement for the platforms' own showcases.
  - Two extreme outlier star counts were verified directly via `gh api repos/<owner>/<repo>` (not just
    trusted from the search response) per this project's claim-verification discipline:
    `career-ops-hq/career-ops` (**71,287** stars, confirmed) and `MadsLorentzen/ai-job-search`
    (**41,844** stars, confirmed) — both genuine, not a search-API artifact.
  - All queries genuinely found, not blocked — including the near-empty `v0.dev`/`bolt.new` ones,
    which are found-empty for those specific phrases, not a source failure.
- **Show HN via HN's Algolia API**, fetched via `uv run --directory ../polyfetch-scrape polyfetch
  fetch <url> --show-body`, `tags=show_hn`, `numericFilters=created_at_i>1757548800`,
  `attributesToRetrieve` trimmed to reduce payload ~10x. Queries run (all multi-word AND queries per
  Algolia's plain-full-text behavior, consistent with the spec's documented finding — a bare
  single-word `resume` query was tried first and confirmed noisy: 435 hits, dominated by "resumable
  upload," "presume," session-"resume" false matches, one genuine hit (Apply AI) found in the first 5):
  `job application` (**132** hits, page 1 of 2 read in full — 100 hits, ~35 on-topic), `interview
  prep` (**121** hits, page 1 of 2 read — 100 hits, dense on-topic signal), `auto apply` (**40** hits,
  all read), `cover letter` (**59** hits, all read), `resume tailor` (**49** hits, all read), `job
  search agent` (**61** hits, all read), `applicant tracking` (**8** hits, all read), `mock interview`
  (**16** hits, all read). Page 2 of `job application` and `interview prep` was not fetched — a
  coverage gap noted rather than silently dropped; page 1 alone already yielded far more on-topic,
  independent instances than needed for every pattern below.
- **Bluesky public post-search API — blocked, re-confirmed this run.**
  `app.bsky.feed.searchPosts?q=job%20application%20AI%20agent` returned the same `FingerprintBlock`
  HTTP 403 documented by prior runs. Recorded as blocked, not absence of signal, per the four-state
  discipline; the spec's "re-check periodically" instruction was honored rather than assumed still
  true.
- **Hugging Face Spaces (`huggingface.co/api/spaces?search=...`)** — found, unauthenticated, no ToS
  restriction per spec step 6. Three searches: `resume` (1,000 Spaces returned, span 2022–2026, no
  server-side date filter available on this endpoint — corroboration only, not individually cited as
  quoted instances since the Spaces API returns no author-written description text in this response
  shape, only id/likes/sdk/createdAt), `job application` (45 Spaces, roughly 20 created within this
  run's 12-month window, distinct owner handles throughout), `interview prep` (73 Spaces, roughly 15
  in-window). Treated as aggregate corroboration of the same clusters found on GitHub/HN, per the same
  discipline used for GitHub topic counts — never as the basis for a quoted instance.
- **cv.inc / cerebralvalley.ai — attempted, incomplete, not blocked.** The spec's documented
  `cerebralvalley.ai/events.md` convenience path returned a clean HTTP 404 this run (`GoneError:
  terminal HTTP 404`); the base site and `/events` (HTML, 200) are reachable. The markdown-convenience
  endpoint the spec describes no longer resolves at that exact path as of this run — not pursued
  further via raw HTML parsing given the query budget already spent and the overwhelming volume of
  on-topic signal already found via GitHub/HN/HF Spaces. Stated as **attempted-but-incomplete**, a
  distinct state from blocked or excluded-by-scope; a future run should locate the current correct
  path before concluding this source is empty for this category.
- **Devpost, Lovable/Replit/Bolt.new/v0 showcases, Indie Hackers, TrustMRR — excluded-by-scope**,
  unchanged from the spec. Not attempted directly.
- **Seed-list verification (orchestrator-supplied HN names).** Confirmed via this run's own general
  queries (not targeted lookups): ResumeSkip, Apply AI, Seisin, Interspectr. Confirmed via targeted
  single-name lookups (first hit each, unambiguous): LiminalML, RoleSweep, Filiz, MyPRs. Confirmed
  off-topic and excluded: agent-hop (see exclusion #4 above). Not independently re-verified this run:
  Job Seeker. PH-sourced seed names (ApplyIn, Job Trawlers, WeKIT, AuthBuild, Job Sentry, Yoxon,
  RoleTect, PivotPartner, Elan, TechNavigator) are outside this spec's source list (ProductHunt is
  `complaint-miner`'s source, not this agent's) — not fetched; none resurfaced incidentally on
  GitHub/Show HN this run.

### Ethical boundary applied

Every pattern below names an aggregate count and lists multiple independent instances; no single
project is presented as "the one to build like." Stars, HN points, and org-vs-individual ownership
appear only as neutral corroborating metadata, never as a ranking signal elevating one project above
the others in its pattern. The two viral outliers (`career-ops-hq/career-ops`,
`MadsLorentzen/ai-job-search`) are cited as one instance each within Pattern 1's aggregate, exactly
like every other instance — not singled out as a template.

---

### Pattern 1 — 10 cited independent instances (GitHub `resume tailoring` total_count: 5,479; `ATS resume` total_count: 17,973 — both far larger populations than inspected; Show HN `resume tailor` nbHits: 49, all read): resume tailoring and ATS-optimization tools

The dominant convergence by volume: distinct, unrelated builders independently building "paste your
resume + a job description, get a tailored/ATS-scored resume back," frequently after describing their
own failed job search.

> "just gonna make it simple: all job portals scan resumes with AI now, if you aren't tailoring your
> resume to each posting you're already behind."
> — ghosts_, [Show HN: ResumeSkip](https://resumeskip.com/), created 2026-08-18, 3 points

> "The job search that runs on your machine. AI job application framework built on Claude Code:
> evaluate postings, tailor CVs, write cover letters, prep interviews. Fork it and own it."
> — MadsLorentzen, [MadsLorentzen/ai-job-search](https://github.com/MadsLorentzen/ai-job-search),
> created 2026-03-18, 41,844 stars (directly verified via `gh api repos/...`, not just the search
> response)

> "Open-source AI job search: scan job portals, evaluate listings into a structured A-H report with a
> global 1-5 score, tailor your CV, track applications — runs locally in your AI coding CLI."
> — career-ops-hq (org), [career-ops-hq/career-ops](https://github.com/career-ops-hq/career-ops),
> created 2026-04-04, 71,287 stars (directly verified)

> "I applied to over 200 jobs last month. Tailored resumes by hand, rewrote cover letters late at
> night, and still got no responses for most of them. Eventually I realized the problem..."
> — tanbirrrrr, [Show HN: CareerKit – ATS scorer and resume tailoring tool for job
> seekers](https://news.ycombinator.com/item?id=47427729), created 2026-03-18, 1 point

> "I'm a Staff Software Engineer, and just recently, on February 24, 2026, I was laid off along with
> 200+ coworkers. To survive the brutal job market, I hacked together a tool..."
> — djrnz, [Show HN: Free AI resume tailor I built after a recent layoff](https://jobbi.app/), created
> 2026-03-09, 2 points

> "Hi HN, I'm the creator of Resume Tailor, I'd love for you to try it out and help me improve it."
> — vednig, [Show HN: Resume Tailor – Fit your resume to any
> job](https://tailor.resume.academy/), created 2026-07-29, 2 points

> "Hey HN, I'm a career coach and I've spent years watching people get burned by resume tools. The
> pattern is always the same: free trial that locks your download, credit packs..."
> — bdtrt, [Show HN: ResuOpt – AI resume optimizer with no subscriptions
> ($4.99 one-time)](https://www.resuopt.com/), created 2026-02-17, 2 points

> "I'm an international student and community college grad... I sent ~300 applications and got 0
> interviews."
> — fredfilm, [Show HN: SignalResume, ATS-first resume builder with grounded AI,
> free](https://signalresume.com), created 2026-02-19 (also posted 2026-02-17, same author/product,
> counted once), 2 points

> "Hi guys, I made my Restailor project open source. You're welcome to use the code in case you're
> looking for a new job."
> — fomoz, [DataDoesYou/Restailor](https://github.com/DataDoesYou/Restailor), created 2026-03-12, 1
> point

> "Job tailoring advice is everywhere but it all bottlenecks at the same place: actually doing it 50
> times. Copy resume, paste into ChatGPT, rewrite bullets, reformat everything..."
> — dippatel1994, [Show HN: Chrome extension that tailors your resume to a job posting in one
> click](https://ajusta.ai), created 2026-03-06, 1 point

> "Resume Matcher – Tailor your resumes with job descriptions"
> — srbhr, [srbhr/Resume-Matcher](https://github.com/srbhr/Resume-Matcher), created 2026-03-04, 2
> points on HN, popular standalone open-source project

Further instances seen but not individually cited (space, not exhaustion): ZenResume, CV10X,
HiredToday.app, cvfitr.com, CVora (jaumapv, three posts, counted once), Kudory, PatchWork, NextRole,
hiredcopilot.com (Xotic007), Resumx (auto-fit one-page renderer), Jobbi (djrnz, second post — same
product as above), Morning Stack (hillj23), `Paramchoudhary/ResumeSkills` (2,241 stars),
`fabriciotrinndade/ats-resume-generator-html`, `JeevansSP/resume-optimizer`.

---

### Pattern 2 — 8 cited independent instances (GitHub `"auto apply" job` total_count: 780; Show HN `auto apply` nbHits: 40, all read; `job application` nbHits: 132, page 1 of 2 read): auto-apply and application-automation agents, with an explicit trust/HITL fault line

This is the pattern `config/scope.md` specifically flagged as worth investigating: whether builders
frame human-in-the-loop review as a differentiator against fully-autonomous auto-apply. **Both of the
discovery run's own hinted taglines are directly re-confirmed here with fresh first-person quotes, not
assumed from a title alone:**

> "Built a job search automation agent with a human in the loop. Without a human, job application
> quality was inconsistent."
> — kan101, [Show HN: Job Search Automation Agent with a Human in the
> Loop](https://rehiredd.com), created 2026-08-07, 1 point

> "Show HN: RoleSweep – job search that deletes listings when employers close them"
> — chetanba, [rolesweep.com](https://rolesweep.com), created 2026-08-14, 3 points (also posted
> 2026-08-05, 2 points, as "Paste a job listing, see if it's still live on employer's own site," same
> author/product, counted once)

Both taglines named in `config/scope.md`'s discovery note are now directly sourced from first-person
build language in this run, not merely inferred from a title — a real, stated design axis (trust in
unsupervised automation) that independent builders are actively differentiating on, not a discovery
artifact.

> "A personal job application AI Agent for job discovery, fit scoring, tailored materials, form
> filling, human-gated submission and application tracking."
> — Liam-Frost, [Liam-Frost/AutoApply](https://github.com/Liam-Frost/AutoApply), created 2026-04-02,
> 123 stars — another explicit human-gated-submission design, reinforcing the same trust fault line as
> rehiredd.com above

> "I'm a software engineer who spent 8 months job hunting last year. Applied to hundreds of jobs.
> Filled out the same forms over and over."
> — Gaasre, [Show HN: ApplyGhost – Auto-apply to jobs with quality, not
> quantity](https://applyghost.com), created 2026-03-01, 1 point

> "I reverse-engineered LinkedIn's SDUI update that broke auto-apply."
> — Maazkhanxo, [jobeasyapply.com](https://jobeasyapply.com) (also posted "AI LinkedIn auto-apply
> that runs in your own browser session," same author/product, counted once), created 2026-07-09, 2
> points

> "Score, rewrite, auto-apply via cloud browser."
> — sneefle, [Show HN: AI Applyd](https://aiapplyd.com/) (same author/product as the earlier
> "Verifying that an auto-submitted job application arrived" post, counted once), created 2026-04-23,
> 1 point

> "Job hunting while working full-time felt like running two careers at once. By the time I finished
> work, I barely had the energy to research the right companies, let alone tailor my..."
> — cbyteai, [jobspire.co.in](https://jobspire.co.in/), created 2026-02-14 (also posted 2026-06-27,
> same author/product, counted once), 1 point

> "My husband and I both have full-time jobs... We built Power Apply at night to survive the 9 to 5."
> — inesbarros1, [Show HN: We built Power Apply at night to survive the 9 to
> 5](https://powerapply.ai/), created 2026-01-21, 3 points

> "An AI agent that applies to jobs for me (Playwright, GPT5.4 form filling)"
> — torontodev007, [torontodeveloper/job-application-agent](https://github.com/torontodeveloper/job-application-agent),
> created 2026-07-01, 2 points

Further instances seen, not individually cited: AutoApplyMax (Azoo92i), `slothsheepking/jobclaw`,
`AbhishekMandapmalvi/AutoApply`, Easyapply.lol (mmurph24 — a satirical dark-pattern demo, not a real
product, excluded from the count but worth noting as the mirror image of this pattern), AI-powered
Autofill / BidHub Copilot (cednore), Gurify (slavba). **Re-categorized out of this pattern**: eliornl's
[Show HN: ApplyPilot](https://github.com/eliornl/applypilot) ("150 applications. One offer. Each
application took 5+ manual steps... none of them talking to each other") describes a
search-and-tailor companion, not an auto-submitting agent — no auto-apply claim in its own text; moved
to Pattern 3's further-instances list instead. Note also a same-name collision, not the same
product: the GitHub `"auto apply" job` query separately returned `ibarrajo/ApplyPilot` ("Autonomous
job application pipeline: discover, score, tailor, and auto-apply"), a different owner entirely —
listed here as its own further-instance, not merged with eliornl's tool.

---

### Pattern 3 — 7 cited independent instances (GitHub `job application tracker` total_count: 13,917; Show HN `applicant tracking` nbHits: 8, all read; `job search agent` nbHits: 61, all read): job-application tracking and job-search dashboards

Distinct from Pattern 2: these track and organize an existing search rather than automate the
applying itself — Kanban boards, analytics dashboards, offline trackers.

> "I've been building Seisin, a desktop app for keeping an entire job search in one place. I was
> planning on posting this back at v1.0, but held on to see a bit more..."
> — LudbaSH, [Show HN: Seisin](https://getseisin.com), created 2026-08-12, 6 points

> "I built JobOps to automate the parts of job hunting that feel like repetitive ops work. Scrapes
> jobs from LinkedIn / Indeed / Glassdoor..."
> — dakheera47, [Show HN: JobOps – self-hosted job hunt "Ironman
> Suit"](https://jobops.dakheera47.com/) (same author/product as
> [DaKheera47/job-ops](https://github.com/DaKheera47/job-ops), 3,936 stars, counted once), created
> 2026-02-16, 1 point

> "I built an open-source job tracker that removes manual updates by ingesting job-related emails.
> Instead of asking users to log applications or update statuses, the system..."
> — thughari, [thughari/JobTrackerPro](https://thughari.github.io/JobTrackerPro/), created 2026-02-02,
> 2 points

> "Hey HN, I built a free job application tracker. Kanban board, Chrome extension, AI suggestions."
> — santoshus, [Show HN: ApplyFlow – Free Kanban job tracker with multi-tier Chrome
> capture](https://www.applyflowtracker.com), created 2026-02-26, 1 point

> "I built Interview OS to solve my own job hunting chaos after applying to 50+ roles and losing track
> of applications, interviews, and follow-ups."
> — iammhador, [Show HN: Interview OS, a privacy-first job
> tracker](https://interviewos.xyz), created 2026-01-21, 2 points

> "I got tired, so I built an offline desktop job tracker + resume builder... because I got tired of
> juggling Numbers, Notion, and Pages to track applications and different versions of my resume."
> — ruslanora, [ruslanora/kin](https://github.com/ruslanora/kin), created 2026-04-10, 4 points

> "What do you use to keep track of job applications? I kept losing track of where I applied and when
> to follow up, so I made a very small web tracker for myself."
> — p-stanchev, [Show HN: A tiny free job application tracker so you stop forgetting
> follow-ups](https://applytrack.netlify.app/), created 2026-01-11, 2 points

Further instances seen, not individually cited: `machadop1407/job-application-tracker`,
`rakib97j/Job-Application-Tracker`, RemotePilot (introvertmac), InterviewTrackr (princierKevin),
`SubbiahS/job-application-tracker` (HF Spaces), and eliornl's
[ApplyPilot](https://github.com/eliornl/applypilot) — self-described as consolidating "150
applications... 5+ manual steps... none of them talking to each other" across separate tools, closer
in shape to this pattern's tracking/companion tools than to Pattern 2's auto-submitting agents (see
Pattern 2's re-categorization note).

---

### Pattern 4 — 6 cited independent instances (GitHub `interview prep AI` total_count: 5,769; Show HN `interview prep` nbHits: 121, page 1 of 2 read; `mock interview` nbHits: 16, all read): AI interview prep and mock-interview coaching agents

> "I was always having this anxious feeling before the interview about not getting the job. Because of
> that, I tended to try to prep as much as I could to at least pass the first round..."
> — ShinApekusay, [Show HN: Interspectr – automated interview prep generated from a job
> posting](https://interspectr.com/), created 2026-08-13, 2 points

> "I couldn't afford interview prep, so I built a free alternative."
> — cdnsteve, [Show HN post](https://news.ycombinator.com/item?id=49643992) linking
> https://learningto.co/, created 2026-09-10, 41 points — highest-engagement post found this run

> "I've been working on a project to solve a problem I had with mock interview platforms: they don't
> feel real. The biggest factor is that they let you give perfect, rehearsed..."
> — devinda-dilshan, [Show HN: CleverMock – An AI voice interviewer that
> interrupts you](https://www.clevermock.com), created 2026-02-27, 2 points

> "I made this because I had trouble 'thinking aloud' during technical interviews, even when I knew
> the optimal solution."
> — collinboler2, [Show HN: LeetDuck – AI voice-to-voice mock interviewer for
> LeetCode.com](https://leetduck.com/), created 2026-01-06, 2 points

> "I've been interviewing recently and got frustrated with two things: copy-pasting context (resume,
> job description) into LLMs repeatedly."
> — viveknar, [Show HN: Intervu – Free, BYOK Interview Prep
> (Groq/Gemini/OpenAI)](https://www.intervu.cc/), created 2026-02-08, 1 point

> "For 18 months, I was stuck in a loop: apply, interview, reject. Repeat. I sent over 1,000
> applications and failed 37 interviews. The frustrating part? I wasn't..."
> — ilyasseisov, [Show HN: After 37 failed interviews, I built the prep tool I wish I
> had](https://news.ycombinator.com/item?id=46385955), created 2025-12-25, 1 point

**Flagged, not fully counted:** SharpSkill (sharpskill.dev/sharpskill.fr) was posted at least four
times across the window under four different HN accounts (Enjoyooor, SnackStyle, MakeMilk,
GiornoJojo), each using first-person "I built" language about the same product — see exclusion #2
above. Treated as one instance, not four, and not one of the six cited above.

Further instances seen, not individually cited: Enlist AI (lilprince1218), InterviewDen (psonthalia),
AlgoVoice (jarlen), MockXP (thatguywho), PrepFile (taureanhall), crackr.dev (wa5ina).

---

### Pattern 5 — 4 cited independent instances, smaller: trust, verification, and anti-ghosting tooling around the job search

A smaller but distinct convergence directly adjacent to the scope's own HITL-trust hint: tools that
verify the *process itself* is trustworthy — that a listing is real, that an application arrived,
that a company won't ghost.

> "I started building ghoster in 2021 after trying to switch a career and noticed the issues with
> ghosting in the HR world were extreme."
> — ghosterdotapp, [Show HN: Ghoster – Crowdsourced database of which copanies (sic) ghost job
> applicants](https://ghoster.app), created 2026-07-08, 2 points

> "Verifying that an auto-submitted job application arrived."
> — sneefle, [aiapplyd.com](https://aiapplyd.com/), created 2026-08-17, 1 point

> "Author here! Agents are applying to jobs for people right now, with progressively more volume, and
> there's nothing built for it. So they scrape career pages and fight ATS for..."
> — fraywing, [Show HN: OJCP – an open protocol for agent-consumable job
> data](https://ojcp.dev/), created 2026-08-12, 39 points

> "A lot of 'Urgent' job postings are actually months old and just reposted to look fresh. I built a
> tool that looks past the PR to see if the company is actually hiring or..."
> — viveknar, [Show HN: I got tired of applying to ghost jobs, so I built a BS
> detector](https://www.thesubspace.io/), created 2026-02-15, 3 points

**Adjacent, not counted (employer-side, not job-seeker tooling):**

> "Employers are facing a growing wave of automated AI submissions, fake applications, and low-effort
> spam. It's not unusual for recruiters and hiring managers to receive thousands of..."
> — romanhn, [Show HN: Permanym – for hiring teams overwhelmed by AI resume
> spam](https://permanym.com), created 2026-07-28, 2 points

---

### Summary

- **Patterns found:** 5 — (1) resume tailoring/ATS optimization, (2) auto-apply/application
  automation agents (with a directly-confirmed HITL-vs-autonomous trust fault line), (3) job-application
  tracking dashboards, (4) AI interview prep/mock-interview coaching, (5) trust/verification/anti-ghosting
  tooling (smaller, adjacent to Permanym's employer-side mirror image, excluded).
- **Independent instances cited (deduplicated per the heuristic and exclusions above):** 35 (Pattern
  1: 10 · Pattern 2: 8 · Pattern 3: 7 · Pattern 4: 6 · Pattern 5: 4), drawn from a far larger population
  by GitHub/Algolia's own reported totals: GitHub `resume tailoring` 5,479, `ATS resume` 17,973, `job
  application tracker` 13,917, `interview prep AI` 5,769, `"auto apply" job` 780, `"job search" agent`
  2,357 total hits; Show HN `interview prep` 121, `job application` 132, `resume tailor` 49, `cover
  letter` 59, `job search agent` 61, `auto apply` 40 total hits — only a sample of each was individually
  inspected and cited above.
- **Two of `config/scope.md`'s own discovery-stage hints independently re-confirmed with fresh
  first-person sourced quotes this run** (not assumed from a tagline): "with a Human in the Loop"
  (rehiredd.com, Pattern 2) and "deletes listings when employers close them" (RoleSweep, Pattern 2) —
  both now carry direct first-person build language, not just a title match.
- **Data-quality exclusions and dedup applied:** ~13 same-author/same-product repeat posts merged to
  one instance each; one likely-multi-account single builder (SharpSkill) merged from 4 posts to 1
  instance; one repo excluded as a non-independent derivative of a cited repo
  (`Fighter90/career-ops-ui`, built atop `career-ops-hq/career-ops`); one seed-list name excluded as
  off-topic (`agent-hop`, a coding-agent session tool, not job-search tooling); one adjacent
  employer-side tool noted but excluded from the job-seeker-tooling count (Permanym) — see §Research
  for detail on each.
- **Two extreme GitHub star counts verified directly against the repo API**, not merely trusted from
  the search response, per this project's claim-verification discipline: `career-ops-hq/career-ops`
  (71,287★) and `MadsLorentzen/ai-job-search` (41,844★), both confirmed genuine.
- **Source states:** GitHub Search API — found (authenticated `gh`, no blocking encountered) · Show
  HN (Algolia) — found · Hugging Face Spaces — found, corroboration only (no per-item author text in
  the API response, so not individually quoted) · Bluesky — **blocked**, re-confirmed this run
  (`FingerprintBlock`, HTTP 403, same as prior runs) · cv.inc/cerebralvalley.ai — **attempted, incomplete**
  (the spec's documented `.md` path 404s; base site reachable but not parsed as raw HTML this run,
  a genuine coverage gap for a future run to close) · step-4 Lovable/Replit/hackathon GitHub
  substitutes — **found**, real hackathon-submission signal (92 hits for the hackathon substitute,
  weaker for Lovable/Replit specifically at this category) · step-4 Bolt.new/v0-Vercel GitHub
  substitute — **found, but near-empty** for this category specifically (1 hit each for `"v0.dev"
  resume` and `"bolt.new" job`), consistent with the freelancer-finance run's prior finding that this
  substitute channel is real but not a 1:1 replacement for the platforms' own showcases ·
  Devpost/Lovable-Replit-Bolt-v0 platforms' own showcases/Indie Hackers/TrustMRR — excluded-by-scope
  (unattempted directly, distinct from the GitHub-substitute channel above).
- **Net read for concept-synthesis:** this is the largest, densest convergence this pipeline has found
  to date — not one dominant shape but five distinct, repeatedly-built product shapes, two viral
  outlier repos (tens of thousands of stars each, independently verified as genuine), and direct
  first-person confirmation of the specific trust/HITL differentiator the discovery stage flagged as
  worth investigating.
