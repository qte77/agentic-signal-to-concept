# Candidate — Signoff

One candidate is drafted here, not several. The builds findings describe five distinct product
shapes for this scope, but only two of them have a matching pattern on the complaints side — resume
tailoring/ATS-scoring (builds Pattern 1, 10 instances), tracking dashboards (builds Pattern 3, 7
instances), and interview prep (builds Pattern 4, 6 instances) total 23 independently-built
instances, including two viral repos independently verified at 71,287 and 41,844 GitHub stars, with
no corresponding complaints-side pull toward any of them in this run. That volume of independent
build activity with zero complaints-side demand signal is read here as a saturation warning against
building in those shapes, not as a second (or third, fourth, fifth) candidate — see Research
constraints. The two shapes that do have a cross-source match — human-gated application submission
(complaints Patterns 2/3/4/7 + builds Pattern 2) and job-listing integrity (complaints Pattern 6 +
builds Pattern 5) — are drafted as **one** candidate below because the evidence reads as the same
underlying complaint viewed from two ends of one pipeline (what the agent sends out on your behalf;
what the agent shows you as worth applying to), not two separate products, mirroring how a prior
candidate in this repo (Freehold Finance) merged privacy and pricing-durability complaints under one
"ownership" principle rather than drafting them separately.

## Concept candidate

- **Name:** Signoff
- **Live URL:** Not yet built — feeds agentic-grounded-persona-eval Phases 1-2 only, not Phase 3
- **Slug:** signoff

Signoff is a job-search and application agent whose core claim is that nothing leaves under your
name, and nothing you're shown is taken on faith. The primary mechanism is a submission gate that is
structural and unbypassable, not a marketing tagline: every application requires an explicit,
visible, per-item approval step before it is sent, with no setting that removes it. This is a direct
response to the specific failure the evidence documents rather than a generic "human in the loop"
pitch: complaints Pattern 7 shows that Job Compass branded itself "help you find jobs, not replace
you" and was still read by a commenter as functionally automated spam once its actual mechanism was
described — a stated intention, unverified in the product's own behavior, did not establish trust.
Signoff's gate has to be checkable, not just claimed.

The secondary mechanism extends the same "verify, don't assert" principle to what the agent shows the
user before they spend effort: postings are checked for basic signs of being fake, already closed, or
reposted to look fresh before being surfaced or queued (complaints Pattern 6). This is deliberately
kept secondary — the corresponding builds evidence (Pattern 5) is the smallest pattern in the builds
findings, 4 cited instances against Pattern 2's 8, and is treated here with that same thinness, not
inflated to equal standing with the submission gate.

Complaints Pattern 3's own analysis names the positioning choice this candidate is making explicit:
differentiating by "opting out of the arms race" (verifiable human involvement, rate-limited/
quality-gated submission) rather than by "helping you win the arms race faster" — which the pattern
identifies as the untried direction against an AI-vs-AI escalation that both sides of the hiring
funnel already describe as making things worse. That ties the job-seeker-side evidence to the
hiring-manager side of the same complaints file (Pattern 1): a commenter on the Obsess Jobs thread
said flatly it is "better for us to not hire at all than to hire someone who applied while they were
sleeping." A product whose applications can survive that scrutiny is addressing trust from both ends
of one pipeline, not building a faster on-ramp into the same distrust.

## Assumed ICPs

(derived from evidence, not invented)

- **Job seekers who want search/application automation but withhold trust from any agent that
  submits without a visible, unbypassable approval step.** Directly stated in complaints Pattern 2 —
  quaunaut asking "Do I have the ability to select who things are going to?" on the original 2017
  auto-apply Show HN; lolinder calling mass-applying-while-asleep "something entirely different that
  will almost certainly lead to lying on the submitted resumes"; devoutsalsa preferring no cover
  letter over "generic AI generated garbage." Reinforced in Pattern 4 by a builder with 18 months of
  direct operating experience who chose not to ship auto-apply at all.
- **Job seekers who've wasted effort on fake, closed, or reposted-to-look-fresh listings and want the
  listing itself checked before they invest in applying.** Directly stated in complaints Pattern 6 —
  more_corn ("fake job postings pollute the pool and make real hiring impossible"), the
  josephd79/kstrauser exchange calling a since-filled-but-still-live posting "an enormous red flag."
- **Hiring managers and recruiters — not the paying customer, but the audience whose trust
  determines whether the product's output is viable at all.** Directly stated in complaints Pattern
  1 — jeswin, lolinder, fhd2, and peab each independently describing inboxes overwhelmed by
  automated applications they say they can already spot and discard. A candidate whose output cannot
  be told apart from what these hiring managers describe discarding is self-defeating at the point of
  delivery, regardless of how the job seeker feels about it.

## Research constraints

(carried over/adapted from `config/scope.md`)

- **ProductHunt status is a genuine open gap, not a settled absence — two conflicting accounts exist
  and neither is independently verified here.** The complaints findings record `PRODUCTHUNT_API_TOKEN`
  as absent from the process environment, and every attempt to read `.env` directly (Read tool,
  Python `open()`, `uv run --env-file`) refused by this sandbox's own permission layer — stated in
  that file as "the net effect is the same as the prior runs that found it genuinely unset" but
  explicitly *not* confirmed as an absent token. Separately, the team lead relaying this task states
  the token "is genuinely configured in `.env` but didn't propagate into that subagent's shell," i.e.
  a subagent-environment issue, not owner-gating — but this synthesis pass cannot read `.env` either
  and has not independently verified that claim. Both accounts are recorded here rather than one
  silently adopted; either way, PH must be closed out with a real pass before this candidate's
  complaints-side evidence is treated as anything but HN-only, mirroring this repo's prior treatment
  of the same gap in the Freehold Finance candidate.
- Builds-side source set for further validation: GitHub Search API and Show HN both found and
  queried live this run; Hugging Face Spaces found, used as aggregate corroboration only (no
  per-item author text in that API's response shape); Bluesky **blocked** (`FingerprintBlock`, HTTP
  403, re-confirmed this run, consistent with prior runs); cv.inc/cerebralvalley.ai
  **attempted-but-incomplete** (the spec's documented `.md` convenience path 404s; base HTML site
  reachable but not parsed this run — a future run should locate the current path before concluding
  this source is empty). Reddit and app-store review mining excluded by scope default, not attempted.
- **Saturation warning, explicitly not evidence for this candidate:** resume-tailoring/ATS-scoring
  (builds Pattern 1, 10 instances, including `career-ops-hq/career-ops` at 71,287 stars and
  `MadsLorentzen/ai-job-search` at 41,844 stars, both independently verified against the GitHub repo
  API, not just trusted from the search response), tracking dashboards (builds Pattern 3, 7
  instances), and interview prep (builds Pattern 4, 6 instances) are heavily built-out shapes. Note
  this run's complaints pass was itself scoped toward the trust/auto-apply signal per `scope.md`'s
  own discovery note, so the absence of a complaints-side pull toward these three shapes in this
  specific run is not proof no such demand exists anywhere — but combined with that scale of
  independent build saturation, this candidate should not compete head-on in any of the three; if any
  of that functionality is added later it belongs as a secondary feature under the gate/integrity
  core, not as this candidate's primary positioning.
- **Baseline to differentiate against, not emulate:** the full-autonomy tools already named and
  criticized within the complaints evidence itself — the original 2017 "Webapp to Auto Apply for
  Jobs" (complaints Pattern 2), Obsess Jobs (complaints Patterns 1/2), and Jobber (complaints Pattern
  7). These are cited because commenters in the sourced threads already criticized them by name, not
  because this synthesis is naming them as competitors to study.
- **Near-neighbors, cited only in aggregate per the ethical boundary re-applied at this synthesis
  stage:** the human-gated auto-apply builders cataloged in builds Pattern 2, and the
  listing-verification/anti-ghosting builders cataloged in builds Pattern 5, corroborate that both
  mechanisms are independently, repeatedly attempted design responses — not a template for this
  candidate to copy, and none of them individually named here as a reference design to model
  positioning language on.
- **Marketing/proof constraint, carried forward as thin and single-incident (do not overweight):**
  complaints Pattern 5 documents one incident (Job Compass's testimonial images) where fabricated-
  looking marketing proof generalized into distrust of the product's core function — the source
  findings file itself discloses this as "a single-incident case study... not independent sightings
  of a recurring behavior," and that hedge is preserved here rather than treated as a validated
  pattern. It applies to this candidate only as a constraint on Signoff's own marketing (avoid any
  unverifiable claim or synthetic-looking proof), not as demand evidence for a feature.

## Evidence

**Source coverage summary:** Both Phase 1 findings files exist for this scope
(`findings/2026-09-11T220526Z-job-search-tooling-complaints-findings.md`, HN-only — ProductHunt
blocked this run, see Research constraints above, 7 patterns, 24 sourced quotes;
`findings/2026-09-11T220526Z-job-search-tooling-builds-findings.md`, GitHub + Show HN + HF Spaces
corroboration — Bluesky blocked, cv.inc incomplete — 5 patterns, 35 cited independent instances).
Independently reconfirmed via `os.listdir('findings')` that these are the only files matching this
scope slug (first-ever run for `job-search-tooling`) before starting this synthesis. This candidate
is **cross-source evidenced** on two separate convergent pattern-pairs, weighted per this spec's
strongest-signal rule above any single-source pattern.

**Cross-source convergence #1 (primary mechanism) — a structural, checkable submission gate, not
human-in-the-loop as positioning language:**

> On the one hand, this is super cool and I know some people/fields this would be great. On the
> other, I worry that without knowing exactly who and how these are getting sent out, that I'll
> seriously consider one of these companies soon after using this, and then be in the unfortunate
> position of having one low-quality application, and a followup high quality one. Do I have the
> ability to select who things are going to?
>
> — quaunaut, complaints Pattern 2. [https://news.ycombinator.com/item?id=14545601](https://news.ycombinator.com/item?id=14545601)

> Using ChatGPT to require part of your resume and looking over it before submitting it to a
> recruiter you're already in touch with is one thing. Mass applying for hundreds of jobs while you
> sleep is something entirely different that will almost certainly lead to lying on the submitted
> resumes.
>
> — lolinder, complaints Pattern 2. [https://news.ycombinator.com/item?id=42562392](https://news.ycombinator.com/item?id=42562392)

> We are completely overrun by automated applications right now... it's better for us to not hire at
> all than to hire someone who applied while they were sleeping.
>
> — lolinder, complaints Pattern 1. [https://news.ycombinator.com/item?id=42559279](https://news.ycombinator.com/item?id=42559279)

> Surely this now means now hiring managers (and people your code incorrectly identifies as hiring
> managers) now get spammed with loads of bullshit generated messages... So you've put a effort in to
> build a product just to make the world slightly worse on net.
>
> — almost, complaints Pattern 7 (reacting to Job Compass despite its "not replace you" tagline). [https://news.ycombinator.com/item?id=44270246](https://news.ycombinator.com/item?id=44270246)

> Built a job search automation agent with a human in the loop. Without a human, job application
> quality was inconsistent.
>
> — kan101, builds Pattern 2. [https://rehiredd.com](https://rehiredd.com)

> A personal job application AI Agent for job discovery, fit scoring, tailored materials, form
> filling, human-gated submission and application tracking.
>
> — Liam-Frost, builds Pattern 2. [https://github.com/Liam-Frost/AutoApply](https://github.com/Liam-Frost/AutoApply)

The complaints-side pattern (job seekers and hiring managers both distrusting unsupervised
submission, independent of each other) and the builds-side pattern (multiple independent, unrelated
builders — rehiredd.com, Liam-Frost/AutoApply, and RoleSweep among them — each independently choosing
explicit human-gated submission as a stated design axis) converge on the same gap from two different
signal types, reconfirming both taglines `config/scope.md` flagged at discovery time with fresh
first-person build language rather than a title match. Per the ethical boundary in
`build-pattern-scanner.md`, re-applied here rather than undone at synthesis: these builds are cited
as aggregate corroboration that the design axis recurs independently, never as a template — no single
one of them is presented as the tool to build like.

**Cross-source convergence #2 (secondary mechanism, explicitly the thinner of the two) — listing and
pipeline integrity:**

> What people fail to understand is that fake job postings pollute the pool and make real hiring
> impossible. We're back to a referrals only job market.
>
> — more_corn, complaints Pattern 6. [https://news.ycombinator.com/item?id=42751237](https://news.ycombinator.com/item?id=42751237)

> if you create a posting that you know will never be filled or for a job that doesn't exist, then to
> me that's unethical and should be against the law.
>
> — josephd79, complaints Pattern 6. [https://news.ycombinator.com/item?id=40837621](https://news.ycombinator.com/item?id=40837621)
>
> It's also an enormous red flag. I wouldn't work for a place that starts lying to candidates before
> they even apply.
>
> — kstrauser, reply to the above, complaints Pattern 6. [https://news.ycombinator.com/item?id=40848891](https://news.ycombinator.com/item?id=40848891)

> I started building ghoster in 2021 after trying to switch a career and noticed the issues with
> ghosting in the HR world were extreme.
>
> — ghosterdotapp, builds Pattern 5. [https://ghoster.app](https://ghoster.app)

> A lot of "Urgent" job postings are actually months old and just reposted to look fresh. I built a
> tool that looks past the PR to see if the company is actually hiring or...
>
> — viveknar, builds Pattern 5. [https://www.thesubspace.io/](https://www.thesubspace.io/)

Builds Pattern 5 self-describes as "smaller" in its own findings file — 4 cited instances against
Pattern 2's 8 — and that thinness is carried forward here rather than dropped: this mechanism is
real, cross-source, and directly on-point, but proportionally weaker evidence than the primary
submission-gate mechanism, which is why it is positioned as secondary in the concept description
above rather than as an equal second pillar. (Pitchly Hire, cited within complaints Pattern 6 as a
single-person, ~40-listing side project with 2 HN comments, is noted here with that same
disclosure — corroborating evidence, not a reference design.)

**Thin, single-incident supporting note (design constraint, not core evidence):**

> The headshots of the customer testimonials are all stock images and not the people they say are
> giving the testimonials. Makes me very skeptical that the testimonials are real or any of the stats
> on the page are real.
>
> — ryan_j_naughton, complaints Pattern 5. [https://news.ycombinator.com/item?id=44271170](https://news.ycombinator.com/item?id=44271170)

As the source findings file states explicitly, this is "a single-incident case study... not
independent sightings of a recurring behavior across products" — carried into this candidate only as
a constraint on Signoff's own marketing (per Research constraints above), not as demand evidence.

**Saturation evidence excluded from this candidate's core, per the opening note above:** builds
Pattern 1 (resume tailoring/ATS, 10 instances, `resume tailoring` GitHub total_count 5,479), Pattern
3 (tracking dashboards, 7 instances, `job application tracker` total_count 13,917), and Pattern 4
(interview prep, 6 instances, `interview prep AI` total_count 5,769) are acknowledged but not folded
into this candidate — see Research constraints for why this is read as a warning rather than an
opportunity.
