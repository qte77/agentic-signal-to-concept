# Candidates — terminal-ux (2026-09-04T070138Z)

Two candidates are drafted from this scope, not one, because the evidence separates cleanly into
two distinct jobs-to-be-done with different triggers and different (non-identical) ICPs: **(1)**
knowing what N *already-running* sessions are doing right now, in-line, and **(2)** what happens to
one session's context the moment a vendor cap forces you off it. Real independent builds do
sometimes bundle pieces of both, but the complaint evidence and the differentiation logic behind
each are distinct enough to warrant separate write-ups. Both are traceable to the same two Phase 1
findings files:

- `findings/2026-09-04T070138Z-terminal-ux-complaints-findings.md` (HN only this run — ProductHunt
  was environment-blocked, not silent; see Research constraints below)
- `findings/2026-09-04T070138Z-terminal-ux-builds-findings.md` (GitHub Search API + Show HN;
  Bluesky blocked)

Both files exist and both contain real sourced evidence, so this run is **cross-source-evidenced**
per the concept-synthesizer precondition, not single-source. Cross-source convergence (a complaint
pattern independently echoed by an independent-builds pattern) is called out explicitly per
candidate below, and weighted above single-source signal per the synthesis spec.

---

# Candidate — Vigil

## Concept candidate
- **Name:** Vigil
- **Live URL:** Not yet built — feeds agentic-grounded-persona-eval Phases 1-2 only, not Phase 3
- **Slug:** vigil-attention-cost-cockpit

A native terminal chrome layer — not a new multiplexer to switch to, not a separate dashboard app
to remember to open — that continuously surfaces two things inline, across every coding-agent CLI
session currently running in a developer's terminal: (a) which sessions are working, done, or
stuck waiting on input, and (b) live token/cost burn per session and in aggregate. The
differentiation is narrowness of scope plus delivery as always-visible chrome rather than a
tool/tab/app the user has to context-switch to in order to check.

## Assumed ICPs
- Solo developers and 2-3 person founding teams running 3 or more concurrent coding-agent CLI
  sessions daily (often across git worktrees), who currently keep track of session state by
  manually cycling through panes.
- Cost-sensitive indie/pre-revenue developers who want running spend visibility as part of the
  terminal itself, not a bill or a rate-limit wall as the first signal, and not a separate app to
  install and check.

## Research constraints
- Carry forward `config/scope.md`'s v1 source set: HN + ProductHunt for complaints, GitHub + Show
  HN for builds; Reddit and app-store excluded (ToS/scraping-block reasons documented in the
  complaints findings file).
- ProductHunt was environment-blocked this run (missing worktree credential, endpoint itself
  confirmed live via a direct 401 test) — not evidence PH is silent on this ICP. Phase 2 should not
  treat the absence of PH quotes here as "PH has nothing to say"; rerun complaint-mining with a
  valid token before drawing that conclusion.
- Bluesky is blocked with a repeatable `FingerprintBlock`/403 across multiple runs (re-confirmed
  this run) — treat as structurally unavailable for now rather than worth re-attempting per Phase 2
  cycle.
- The space is crowded: the complaints findings' own Pattern 1 lists ten named terminal-multiplexer
  wrappers found in the same searches (amux, crmux, Herdr, Muxel, Smux, Wmux, Tenex, Architect,
  Metateam, Agentic Hive), and the builds findings' Pattern A cites 14 independently-authored
  session managers as a representative subset of a much larger sample, with Pattern C separately
  citing 12 cost-tracking dashboards. Phase 2 persona work should probe specifically *why* the
  existing tools (named in the findings files, not re-cited here as a template to copy) don't
  satisfy the ICP, rather than assuming a green field.
- No date window is bounded in `scope.md`; the builds pass applied a 12-month default
  (2025-09-04 through 2026-09-04). This is a very recent surge of build activity, not an
  established multi-year category — Phase 2 timing analysis should reflect that.

## Evidence

**Source coverage summary:** cross-source — this candidate is grounded in both the complaints pass
(HN only, PH blocked) and the builds pass (GitHub + Show HN), with the strongest single piece of
evidence in the whole run being that both passes independently converge on near-identical language
("which one needs you" / "couldn't easily tell which agent was stuck").

**Pattern 1 (complaints) × Pattern A + Pattern E (builds) — attention-routing, the strongest
cross-source convergence in this run:**

> "I work on a 300k line monorepo, just me and my co-founder. At any given moment I have 3-6 CLI agents (Claude Code, Codex, Aider) running simultaneously across git worktrees. The throughput is great. Managing it is not. [...] How are others handling multi-agent workflows?"
>
> [Show HN: "Is anyone else drowning in terminal tabs running AI coding agents?", parsak, 2026-03-05](https://news.ycombinator.com/item?id=47268777)

> "I run 5+ Claude Code sessions in parallel throughout the day. Switching between tmux panes to find which one is waiting for approval, typing a prompt, then jumping back to another pane — it gets old fast."
>
> [Show HN: "Crmux – A Vim-like TUI to manage multiple Claude Code sessions in tmux", maedanao, 2026-03-02](https://news.ycombinator.com/item?id=47217461)

Independently, on the builds side, "know which one needs you" is described in the findings file as
*the* recurring core feature across fourteen cited, independently-authored session-manager repos
(desktop apps, TUIs, an Emacs package, a Neovim plugin, a Raycast extension, a Telegram bridge, a
macOS menu-bar app) — see `builds-findings.md` Pattern A. Show HN corroborates in first-person
builder language, not just repo descriptions:

> "I ran them in a split terminal for a few weeks, and quickly spotted two main problems. The first is that I couldn't easily tell which agent was stuck waiting on me and which was still working..."
>
> [Show HN post, igor_nast, 2026-07-19](https://news.ycombinator.com/item?id=48966140)

(A second, independent Show HN launch in the same findings-file pattern opens with the identical
pain framing before describing its own fix — not quoted again here to avoid the write-up reading as
"here's the template"; see `builds-findings.md` Pattern E for the full citation.)

This is a complaint pattern and an independent-builds pattern pointing at the same gap — per the
synthesis spec, this is the strongest signal the method produces, and it is treated as such here.

**Pattern 4 (complaints) × Pattern C (builds) — cost/burn visibility, second convergence, folded
into the same candidate because both findings files independently frame the differentiation the
same way (native/inline vs. a separate app to switch to):**

> "They're great at writing code, but I have no idea what they're doing while they work. Tokens burn with no visibility, context fills up silently, and when a session ends everything the agent learned is gone."
>
> [Show HN: "Sidekick – See what your AI coding agents are doing", cal_lopez, 2026-02-26](https://news.ycombinator.com/item?id=47164432)

> "How much are you spending before you even see a $1 of revenue? Nice tool, but the agentic workflow doesn't sound cost efficient."
>
> [comment on "Is anyone else drowning in terminal tabs running AI coding agents?", nis0s, 2026-03-05](https://news.ycombinator.com/item?id=47268777)

The complaints file's own "what this means" note for this pattern reads: existing tools solving this
(named in `complaints-findings.md` Pattern 4) do so "as an additional app/extension to install and
switch to, not as native terminal chrome" — twelve independently-authored cost-tracking dashboards
are cited in `builds-findings.md` Pattern C confirming the category is real and actively being
built, several explicitly self-described as "htop for AI coding agents," which corroborates demand
while also confirming this specific sub-space (cost tracking) is comparatively crowded on its own —
the differentiation for Vigil is bundling it as inline chrome together with attention-routing, not
cost tracking alone.

**Why bundled into one candidate rather than two:** real independent builds already converge on
this bundling — e.g. one cited Pattern A repo pairs "multi-session resume" with "API usage
dashboards" in the same tool, and another pairs a "live status sidebar" with running multiple
sessions in one terminal (see `builds-findings.md` Pattern A for the specific citations; not
re-named here as a template — several other builds do the same pairing independently, which is the
point). Splitting attention-routing and cost visibility into two separate candidate products would
not reflect a distinction the evidence itself draws.

**Thin-grounding flag:** none for the core convergence (both attention-routing and cost-visibility
have direct cross-source support). The one soft spot: no single build cited in either findings file
is confirmed to deliver *both* as native inline chrome specifically (as opposed to a separate
dashboard/app, or attention-routing without cost, or cost without attention-routing) — this specific
combination is inferred as the gap from reading the complaint framing against the build citations,
not read verbatim from any one quote. This inference should be checked, not assumed, in Phase 2.

---

# Candidate — Overflow

## Concept candidate
- **Name:** Overflow
- **Live URL:** Not yet built — feeds agentic-grounded-persona-eval Phases 1-2 only, not Phase 3
- **Slug:** overflow-ratelimit-handoff

A session-continuity layer that detects a vendor rate-limit or subscription-cap event as it happens
and automatically exports the current coding-agent session's context so it can be resumed in a
different harness or account — as opposed to every found tool in this space today, which requires
the user to notice the limit themselves and manually invoke a handoff. Because the mechanism is
"export and resume a session elsewhere," it inherits Pattern 6's secret-leak caveat directly (not
adjacently) and treats automatic secret redaction as a first-class, non-optional part of the export
step, not a README disclaimer.

## Assumed ICPs
- Developers on capped subscription tiers (commonly cited at ~$20/mo) who run heavy daily CLI usage
  and juggle 2+ AI-vendor accounts to route around individual caps.
- Developers who prioritize context continuity over a specific vendor/tool — willing to switch
  mid-task if the switch doesn't cost them re-explaining what the agent was doing.

## Research constraints
- Same v1 source-set carryover as Vigil above (HN+PH complaints, GitHub+Show HN builds; PH
  env-blocked this run, not silent; Bluesky structurally blocked).
- Pattern D (the build-side evidence for this candidate) is the narrowest pattern found in the
  builds pass — 4 independent instances, versus 11-14 for the other build patterns. Small-n,
  though all 4 are confirmed independent (distinct authors, no fork relationship, span
  March-August 2026 of the window).
- Phase 2 should specifically test whether "automatic rate-limit detection" is something users would
  trust running unattended (auto-exporting a session on a trigger they didn't invoke) versus wanting
  a manual-but-fast confirm step — the complaint evidence establishes the *want* for less manual
  effort, not that fully automatic is the right trust level.

## Evidence

**Source coverage summary:** cross-source, though the build-side population (Pattern D, n=4) is
smaller than Vigil's. Still counted as cross-source convergence per the synthesis spec because the
complaint pattern and the independent-builds pattern point at the same underlying gap
(harness-locked sessions), even though neither source directly evidences the specific "automatic
trigger" differentiation — that piece is inferred from absence, and is flagged as such below.

**Pattern 2 (complaints) — rate limits force a manual, context-losing tool switch:**

> "i kept hitting rate limits in Claude Code mid-debugging, then hopping to Gemini or Codex. the annoying part wasn't switching tools (copy-pasting terminal output doesn't bring tool-use context with it) — it was losing the full conversation and spending 10 minutes re-explaining what i was doing."
>
> [Show HN: "`npx continues` – resume same session Claude, Gemini, Codex when limited", yigitkonur35, 2026-02-19](https://news.ycombinator.com/item?id=47075089)

> "most of us are juggling between $20 chatgpt and $20 claude subscriptions, so being able to hop between them by copying stuff over properly is clutch."
>
> [reply from the same builder, yigitkonur35, 2026-02-19](https://news.ycombinator.com/item?id=47075089)

> "I run Claude Code across multiple projects daily. Two problems kept getting in the way: being stuck in the terminal waiting for agents to finish, and burning through rate limits midweek while manually switching between accounts."
>
> [Show HN: "Claude-Nonstop – Auto Account Switching and Slack Remote in Claude Code", rchaz, 2026-02-20](https://news.ycombinator.com/item?id=47082232)

The complaints file's own analysis of this pattern notes that even the one dedicated tool found
(`npx continues`) has its author stating "rate limit detection is manual for now" — i.e. the closest
existing attempt to this problem confirms, in the builder's own words, that automatic detection is
the unaddressed part.

**Pattern D (builds) — cross-harness portability is a real, independently-attempted build target,
not just a complaint:**

`builds-findings.md` Pattern D cites four independent, distinct-author builds (spanning March, July
x2, and August 2026 of the 12-month window) whose sole purpose is exporting a session from one
coding-agent harness and resuming it in another — described there as "the most direct, sourced
confirmation of the specific signal discovery flagged as worth checking." None of the four
descriptions in that pattern mention an automatic rate-limit trigger; all read as user-invoked
export/resume actions. This is treated as an aggregate confirmation that 4 independent teams
consider harness-locked sessions worth solving — not as any single one of those four projects being
the template to build like or extend, consistent with the ethical boundary the build-pattern-scanner
pass applied and which this synthesis pass does not undo.

**Pattern 6 (complaints) — folded in as a direct design constraint, not an adjacent one, because
Overflow's core mechanic (export a session, resume it elsewhere) is the exact action this pattern's
quotes are about:**

> "IMPORTANT CAVEAT: Unless you have a very good security hygiene, your Claude Code sessions are likely full of sensitive information such as environment secrets. Use with caution and avoid using on public repositories."
>
> [Show HN: "Ccgs – Collaborative Claude Code sessions, stored in Git branches", scrollaway, 2026-06-06](https://news.ycombinator.com/item?id=48426297)

> "Everyone knows you shouldn't be pasting env secrets and customer data into Claude, but many do it anyway because the perceived productivity gains offered by tools like Claude Code are just too high to ignore [...] the secrets are already sitting in the transcripts on their laptop - this just moves them somewhere shared and permanent."
>
> [comment on the ccgs thread, AG342, 2026-06-06](https://news.ycombinator.com/item?id=48426297)

**Thin-grounding flag (explicit, per the synthesis spec's rule 5):** the "automatic rate-limit
trigger" is this candidate's core differentiation, but it is not directly evidenced by any quote in
either findings file — no complainant explicitly asks for "automatic detection" in those words, and
no cited Pattern D build's description mentions one. It is inferred by combining (a) the complaint
pattern's emphasis on the manual-switch pain plus the one dedicated tool's own admission that
detection is "manual for now," and (b) the absence of any automatic-trigger language across all four
Pattern D build descriptions. This is a reasonable but *inferred* gap, not a directly-quoted one —
treat it as the part of this candidate most in need of validation (e.g. a targeted follow-up
complaint-mining pass specifically searching "automatic" + "rate limit" + "handoff") before treating
it as confirmed demand in Phase 2.

---

## Patterns considered, not drafted as standalone candidates

- **Pattern 3 (complaints, crash/reset context loss; "bookmark the relevant parts" ask)** — the
  complaints file's own read is that full-session resume is a saturated build space (at least six
  wrapper tools it names), and the differentiated ask (selective, curated bookmarking rather than
  full-transcript resume/search) has no corroborating build pattern in `builds-findings.md` — every
  cited Pattern A build describes full resume, not curated bookmarking. No cross-source convergence
  on the differentiated part of this pattern specifically, so it is not drafted as a standalone
  candidate here; it could be a secondary feature explored for Vigil in a later iteration, but the
  evidence does not support it as its own concept yet.
- **Pattern 5 (complaints, team-to-team session handoff)** — a distinct persona (team lead /
  colleague inheriting context) from the solo-developer focus of Vigil and Overflow, with real
  complaint evidence (Grov, Ccgs quotes in `complaints-findings.md`), but only one build citation
  in `builds-findings.md` touches team-scale memory directly (the `codecast` entry within Pattern A,
  "Team memory... line-level agent attribution") — a single instance within a pattern about general
  session management, not an independently-populated pattern of its own. Thin cross-source support;
  not drafted as a standalone candidate, flagged here rather than silently dropped.
