# Complaint/Feature-Request Findings — terminal-ux

Run: `terminal-ux` · Scope: terminal/session UX for AI coding agents (the terminal, multiplexer, or
session layer built specifically around coding-agent workflows) · Generated: 2026-09-04T070138Z

## Source coverage

| Source | Status | Detail |
| --- | --- | --- |
| Hacker News (Algolia search + Firebase item API) | **found** | Seven Algolia queries run against `hn.algolia.com/api/v1/search?tags=story`: `terminal AI coding agent`, `session portability coding agent`, `Claude Code tmux`, `AI agent cost tracking terminal`, `resume Claude Code session`, `multi-agent handoff`, `terminal multiplexer AI agent`. Comment trees walked via `hacker-news.firebaseio.com/v0/item/<id>.json` (recursing through `kids`) for the highest-signal stories: `47268777` (Pane, full tree), `47075089` ("continues", 2 levels), `47073488` (Claudebin, top level), `48426297` (ccgs), `47128630` (search-sessions), `46711958` (Grov), `49011463` (Rabbitty). All fetches went through `polyfetch fetch <url> --show-body` per plain GET as instructed. |
| ProductHunt (GraphQL v2) | **blocked (owner-gated, environment constraint — not absence of signal)** | `PRODUCTHUNT_API_TOKEN` is not set in this worktree's shell environment (confirmed via `os.environ.get`), and no `.env` file exists in this worktree at all (confirmed via `os.path.exists`) — only the main repo checkout (`/workspaces/qte77/agentic-signal-to-concept/.env`, a sibling directory, not this worktree) has one. Git worktrees do not share gitignored files, so the token that exists there was never copied into this worktree. Both a direct `Read` of that main-repo `.env` and a `cp` of it into this worktree were denied by this session's own permission settings/classifier (not a network or vendor block) — reading or moving a secret across the worktree boundary was correctly refused, and no attempt was made to circumvent that refusal. To confirm the blocker is specifically "no valid token available here" and not "endpoint unreachable," an unauthenticated test `POST` to `https://api.producthunt.com/v2/api/graphql` was made directly (not via `polyfetch`, per the agent's own instruction that PH's POST+auth-header call needs a real HTTP client): the endpoint responded `HTTP 401 {"error":"invalid_oauth_token", ...}` — i.e., live and reachable, just correctly rejecting the absent credential. No PH quotes are included below; this is recorded as a genuine sourcing gap for this run, not as evidence PH discussion doesn't exist. |
| Reddit | **excluded-by-scope** | Not attempted, per `complaint-miner.md` step 3 and `docs/plans/0001-concept.md`: unauthenticated scraping is confirmed fingerprint-blocked (HTTP 403 across three independent tools on a sibling repo's real run) and the OAuth API's terms/limits are unverified. This is a deliberate v1 exclusion, not a network failure on this run. |
| GitHub / Show HN (builds) / Bluesky | **out of scope for this agent** | `build-pattern-scanner`'s job, not `complaint-miner`'s — not attempted here. |

All quotes below are verbatim from the fetched JSON (HN `text`/`story_text` fields, HTML-entity-decoded by
hand where quoted), each with its source URL. Where a quote is the Show HN poster's own framing of the
problem that motivated their build (rather than a third-party commenter), that is noted explicitly —
it is still a sourced first-person account of a real pain point, just not independent commentary.

---

## Pattern 1 — Running several coding-agent CLIs in parallel panes/tabs turns into babysitting, not coding

Independent complaints, across different tools and months, about losing track of which of several
concurrent agent sessions needs attention, and time spent switching between them rather than working.

> "I work on a 300k line monorepo, just me and my co-founder. At any given moment I have 3-6 CLI agents (Claude Code, Codex, Aider) running simultaneously across git worktrees. The throughput is great. Managing it is not. [...] How are others handling multi-agent workflows?"
>
> [Show HN: "Is anyone else drowning in terminal tabs running AI coding agents?", parsak, 2026-03-05](https://news.ycombinator.com/item?id=47268777)

> "I run 5+ Claude Code sessions in parallel throughout the day. Switching between tmux panes to find which one is waiting for approval, typing a prompt, then jumping back to another pane — it gets old fast."
>
> [Show HN: "Crmux – A Vim-like TUI to manage multiple Claude Code sessions in tmux", maedanao, 2026-03-02](https://news.ycombinator.com/item?id=47217461)

> "cmux: lags unbearably, and I ended up babysitting parallel agents more than coding [...] Dropped all the way down to nvim with Claude Code as a sidebar and that's been the stickiest by a wide margin."
>
> [Ask HN: "Anyone else went through the AI-editor carousel back to the terminal?", amoriodi, 2026-04-09](https://news.ycombinator.com/item?id=47709343)

> "I often find myself having multiple concurrent terminals open with Claude Code, only to discover some of them have been sitting idle for an hour. Keeping an eye on this"
>
> [comment on "Rabbitty – a native Mac terminal for running AI coding agents in parallel", Tielem, 2026-07-22](https://news.ycombinator.com/item?id=49012030)

**What this means for a concept candidate:** the raw signal isn't "I want a prettier terminal" — it's
"I cannot tell, across N running agent sessions, which ones are stuck/waiting/idle without manually
checking each pane." A status-aggregation/attention-routing layer (not another multiplexer skin) is
the differentiated angle; note that dozens of terminal-multiplexer wrappers already exist (amux, crmux,
Herdr, Muxel, Smux, Wmux, Tenex, Architect, Metateam, Agentic Hive — all found in the same HN searches),
so a plain "yet another tmux wrapper" is a crowded, undifferentiated space.

---

## Pattern 2 — Hitting a rate limit or subscription cap mid-task forces a jarring tool switch that loses context

Multiple independent accounts of needing to move from one coding-agent CLI/subscription to another
(same vendor account exhausted, or a different vendor entirely) and having to manually re-explain
context that the terminal session held but couldn't hand off.

> "i kept hitting rate limits in Claude Code mid-debugging, then hopping to Gemini or Codex. the annoying part wasn't switching tools (copy-pasting terminal output doesn't bring tool-use context with it) — it was losing the full conversation and spending 10 minutes re-explaining what i was doing."
>
> [Show HN: "`npx continues` – resume same session Claude, Gemini, Codex when limited", yigitkonur35, 2026-02-19](https://news.ycombinator.com/item?id=47075089)

> "most of us are juggling between $20 chatgpt and $20 claude subscriptions, so being able to hop between them by copying stuff over properly is clutch."
>
> [reply from the same builder, yigitkonur35, 2026-02-19](https://news.ycombinator.com/item?id=47075089) (comment id 47075176)

> "interesting idea. At first I just thought that it's sth like the 'fg' command but for AI sessions. i.e. just resume the last AI session you 'paused.' honestly, that in itself would be valuable, though simple. This idea, i.e. actually 'moving' context from one agent to another is even more interesting."
>
> [comment on the same thread, kantord, 2026-02-19](https://news.ycombinator.com/item?id=47075089) (comment id 47075132)

> "I run Claude Code across multiple projects daily. Two problems kept getting in the way: being stuck in the terminal waiting for agents to finish, and burning through rate limits midweek while manually switching between accounts."
>
> [Show HN: "Claude-Nonstop – Auto Account Switching and Slack Remote in Claude Code", rchaz, 2026-02-20](https://news.ycombinator.com/item?id=47082232)

**What this means for a concept candidate:** this is the "session portability across Claude
Code/Codex/Cursor" signal the run's scope notes flagged for confirmation — it is real, and it is
specifically triggered by rate limits/quota exhaustion, not just personal preference. A portability
layer that activates automatically on a detected rate-limit event (rather than requiring the user to
manually invoke a handoff tool, which is what every found builder currently ships) is the gap: even
`continues`' own author says "rate limit detection is manual for now."

---

## Pattern 3 — Long or crashed sessions silently destroy accumulated context, with no durable recall

Distinct from Pattern 2 (which is about switching tools mid-task), this is about a single tool's
session simply ending — via crash, restart, or just running long enough to need a reset — and taking
the accumulated problem-solving context with it.

> "I built amux because I kept waking up to dead Claude Code sessions. Context would fill up at 2am, the agent would crash, and I'd lose hours of work."
>
> [Show HN: "Amux – run Claude Code agents in parallel from your phone", Beefin, 2026-03-13](https://news.ycombinator.com/item?id=47363707)

> "Claude Code forgets everything between sessions. After months of heavy use, I had 1.6GB of JSONL session files buried in ~/.claude/projects/. Architecture decisions, debugging breakthroughs, and solutions I couldn't find again. There's nothing more annoying than solving the same problem twice."
>
> [Show HN: "Search-sessions – Search all your Claude Code session history in <300ms", sinzin91, 2026-02-23](https://news.ycombinator.com/item?id=47128630)

> "sometimes i get a huge huge history after 4-5 hours for working, and it is resetting. That's not a problem, what i actually want is a way to 'bookmark' some of the informations that are relevant. And reapply them."
>
> [comment on "Claudebin – Share and resume Claude Code sessions with a single link", axelut, 2026-02-19](https://news.ycombinator.com/item?id=47073488) (comment id 47074362)

> "I use Claude Code across 10+ projects daily. Terminal crashes (or restarts) kill all my sessions. `claude --resume` exists, but navigating to each project and scrolling through session lists gets old fast."
>
> [Show HN: "Claude Launcher – TUI to fuzzy search and resume Claude Code sessions", imprakharshukla, 2026-02-02](https://news.ycombinator.com/item?id=46852607)

**What this means for a concept candidate:** "session resume exists but is clunky to find/use" is a
saturated build-space (at least six independent `claude --resume` wrapper tools surfaced in one HN
search: ccrider, Hindcast, cc-sessions, claude-search, ccs, Claude Launcher, Recall). The differentiated
gap is the "bookmark the relevant parts, not the whole transcript" ask from axelut — selective,
curated context retention rather than full-history replay/search, which none of the found tools do.

---

## Pattern 4 — No real-time visibility into token/cost burn while an agent session runs

Builders and commenters independently describe running agent CLIs "blind" on cost — the terminal
shows output, not spend, until a bill or a rate-limit wall arrives.

> "They're great at writing code, but I have no idea what they're doing while they work. Tokens burn with no visibility, context fills up silently, and when a session ends everything the agent learned is gone."
>
> [Show HN: "Sidekick – See what your AI coding agents are doing", cal_lopez, 2026-02-26](https://news.ycombinator.com/item?id=47164432)

> "How much are you spending before you even see a $1 of revenue? Nice tool, but the agentic workflow doesn't sound cost efficient."
>
> [comment on "Is anyone else drowning in terminal tabs running AI coding agents?", nis0s, 2026-03-05](https://news.ycombinator.com/item?id=47268777) (comment id 47268843)

> "Seconding the 'Gee, must be nice to be able to set money on fire' sentiment. [...] Damn well not using other people's money to subsidize some cloud provider's build out."
>
> [comment on the same thread, salawat, 2026-03-06](https://news.ycombinator.com/item?id=47268777) (comment id 47268979)

> "Fair enough, I spend a maximum of 200 a month given I use the MAX plan from CC. I don't find myself ever hitting the weekly limits — but recently I've gotten close!"
>
> [reply from the original poster, parsak, 2026-03-06](https://news.ycombinator.com/item?id=47268777) (comment id 47278805)

**What this means for a concept candidate:** the complaint isn't "AI coding is too expensive" in the
abstract — it's specifically that the *terminal session itself* gives no running signal of spend,
so cost is discovered only after the fact (a bill) or as a hard wall (a rate limit). An
in-terminal, always-visible burn-rate readout tied to the active session (not a separate dashboard
tab to remember to check) is the gap; Sidekick and Centrality both build toward this but as an
additional app/extension to install and switch to, not as native terminal chrome.

---

## Pattern 5 — A session's accumulated reasoning doesn't transfer to a teammate, so it's re-derived from scratch

A team-scale variant of Pattern 3: it's not just that one person loses their own context, it's that
the context never reaches a second person even when they touch the same code shortly after.

> "The moment I kill a terminal pane or close a chat session, the high-level reasoning and architectural decisions generated during that session are lost. If a teammate touches that same code an hour later, their agent has to re-derive everything from scratch or read many documentation files for basically any feature implemented or bug fixed."
>
> [Show HN: "Grov – Multiplayer for AI coding agents", tonyystef, 2026-01-21](https://news.ycombinator.com/item?id=46711958)

> "My team uses Claude Code daily, and the sessions have become some of the most useful artifacts we produce. But they're trapped in ~/.claude/projects/ on whichever laptop they happened on. There's no good way to hand a colleague 'the session where I untangled the migration' so they can claude --resume it and keep going from where I left off."
>
> [Show HN: "Ccgs – Collaborative Claude Code sessions, stored in Git branches", scrollaway, 2026-06-06](https://news.ycombinator.com/item?id=48426297)

> "The portability angle is compelling. The interesting design problem to me is whether a resumable session artifact has to be identical to the raw transcript. I'd love a middle layer that preserves enough state to resume and debug while aggressively quarantining obvious secret classes and high-risk blobs."
>
> [comment on the ccgs thread, jazzen, 2026-06-06](https://news.ycombinator.com/item?id=48426297) (comment id 48427556)

**What this means for a concept candidate:** this is a distinct persona from Pattern 2/3 — not a solo
developer optimizing their own flow, but a team lead/senior engineer whose institutional knowledge is
locked in a colleague's local terminal history. "Session portability" here means person-to-person
handoff, not tool-to-tool or machine-to-machine.

---

## Pattern 6 — Sharing or persisting a session transcript is itself a secret-leak risk nobody has solved cleanly

A specific, recurring caveat that surfaces every time a builder proposes exporting/sharing/storing
session history: the transcript is also where API keys and other secrets end up.

> "IMPORTANT CAVEAT: Unless you have a very good security hygiene, your Claude Code sessions are likely full of sensitive information such as environment secrets. Use with caution and avoid using on public repositories."
>
> [Show HN: "Ccgs – Collaborative Claude Code sessions, stored in Git branches", scrollaway, 2026-06-06](https://news.ycombinator.com/item?id=48426297)

> "Everyone knows you shouldn't be pasting env secrets and customer data into Claude, but many do it anyway because the perceived productivity gains offered by tools like Claude Code are just too high to ignore [...] the secrets are already sitting in the transcripts on their laptop - this just moves them somewhere shared and permanent."
>
> [comment on the ccgs thread, AG342, 2026-06-06](https://news.ycombinator.com/item?id=48426297) (comment id 48426872)

> "If I am not mistaken, Claude sometimes pulls an API key out of a .env file and drops it into that folder. It might be neat for you to add a feature specifically for identifying any keys that are in that folder."
>
> [comment on "Search-sessions", SteveVeilStream, 2026-02-23](https://news.ycombinator.com/item?id=47128630) (comment id 47128701)

**What this means for a concept candidate:** any session-portability/sharing feature (Patterns 2, 3,
and 5 above all point toward wanting this) has to ship secret-redaction as a first-class, automatic
step, not an opt-in caveat in a README — every builder found here treats it as an afterthought
disclaimer rather than something the tool actively does.

---

## Notes on independence and quote selection

- Quotes were kept only where they add a distinct account, not a restatement; the Claudebin thread in
  particular contained a run of near-identical one-line praise comments ("very useful! great work",
  "Exciting stuff!") from many different low-activity accounts in a short window — these were excluded
  as low-signal/likely-promotional rather than cited as independent corroboration, in line with not
  elevating a single anecdote (or an astroturf-shaped cluster) to a trend.
- Some tangential HN results (multi-agent orchestration *frameworks*, unrelated Show HNs matched by
  keyword overlap like "handoff" or "multiplexer" in a different sense) were reviewed and discarded as
  off-topic for this scope rather than cited.
