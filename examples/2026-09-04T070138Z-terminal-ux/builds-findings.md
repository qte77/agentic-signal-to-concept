# Terminal/session UX for AI coding agents — Build-pattern findings

## §Research

Scope per `config/scope.md`: independent small-build activity (AI-assisted "vibe coded" or
conventionally built, treated identically) converging on the terminal/session UX layer built
specifically around coding-agent workflows — session portability, cost tracking, multi-agent
handoff — not a single product. Surfaced by `signal-discoverer`'s first real run, category #3
(`scope.md` cites this as `discovery/2026-09-04T060617Z-categories.md`, but that path does not
exist in this worktree; the file was found instead at
`examples/2026-09-04T060617Z-discovery/categories.md` — noted here as a stale-path correction,
not re-verified against `scope.md`'s author). No date window is bounded in
`scope.md`; this run applies `build-pattern-scanner`'s own v1 default — a 12-month window,
**2025-09-04 through 2026-09-04** (the run date). **This is the first-ever run of this spec for
this scope** — no prior findings file to reconcile against.

### Independence heuristic (v1 default, applied as-is)

Per `.claude/agents/build-pattern-scanner.md`: two builds count as independent only if ALL of —
distinct author/org handles; both created within the 12-month window; no direct fork/clone
relationship to each other; no shared canonical upstream repo. **Stated plainly as the spec's
coarse v1 default, not a validated methodology.**

**Basis for the "no fork" clause:** none of this run's GitHub Search API queries used a `fork:`
qualifier, and GitHub's Search API excludes forks from results by default without one — every
GitHub-sourced item below is fork-filtered by GitHub itself, not by per-repo inspection of the
`.fork` field.

**Two heuristic weaknesses observed this run, flagged not silently corrected:**

1. **Same-author repeat entries.** `SimonMallas` posted four near-identical repos in Pattern B
   (`agent-letterbox-cmux`/`-zellij`/`-herdr`/`-tmux` — the same "agent-to-agent mail" concept
   ported to four different multiplexers), and `gitstq` posted four overlapping repos within
   Pattern C (`AgentDeck`, `AgentPulse-CLI`, `AgentSessionForge`, `termwise`). Each is counted
   **once** per author below, per the heuristic's "distinct author/org handles" clause — the
   duplicate repos are not additional independent instances, but they do show the same builder
   iterating on the theme repeatedly, which is itself weak corroboration of how compelling the
   problem looks to at least these two individuals.
2. **Convergent naming, not a shared upstream.** Three unrelated repos by three different owners
   independently landed on the name "agent deck" / "AgentDeck": `asheshgoplani/agent-deck` (833
   stars), `IkumaHayashi/agent-deck`, and `gitstq/AgentDeck`. Checked directly — different owners,
   different repos, no fork relationship, no shared canonical upstream — so all pass the
   heuristic's literal clauses as independent. Flagged because the name convergence itself is a
   data point (multiple builders reaching for the same "deck of agent sessions" metaphor
   unprompted), not because it threatens double-counting.

### Source coverage

- **GitHub Search API (`search/repositories`), via the authenticated `gh` CLI** (`env -u GH_TOKEN
  -u GITHUB_TOKEN gh api -X GET search/repositories -f q='...' -f sort=updated -f per_page=30
  --jq '...'`) — **found**, richly. `gh api rate_limit` before the run confirmed the search
  bucket at 30/30 remaining (authenticated). Five queries run, all `created:>2025-09-04` (the
  12-month window), sorted by `updated` (most recently active in-window matches, not
  highest-relevance or newest-created — stated so the samples below are understood as a slice,
  not an exhaustive enumeration). **These five query populations are not mutually exclusive** —
  only one cross-query overlap was directly observed (`Aho1ic/Aeroric`, matched by both of the
  first two queries' 30-item samples), but the full overlap across all five `total_count`s is
  unmeasured; the counts below are reported per query, never summed into one population figure:
  `terminal AI coding agent session` (147 total hits, 30 sampled), `claude code session manager`
  (485 total hits, 30 sampled), `multi-agent handoff terminal` (14 total hits, all 14 sampled),
  `ai agent cost tracking terminal` (16 total hits, all 16 sampled), `"session portability"
  agent` (4 total hits, all 4 sampled). A sixth and seventh check were run per the spec's step 4
  to test the compliant Bolt.new/v0-Vercel substitute for this category specifically:
  `topic:bolt "bolt.new" created:>2025-09-04` (4 hits, inspected directly via full item fields
  this time, not just `total_count`) and `"v0.dev" terminal agent created:>2025-09-04` (0 hits).
  The 4 `topic:bolt`/`bolt.new` hits are genuinely Bolt.new-related on inspection (a
  Bolt.new-branded desktop tool, a Bolt.new Chinese-language community mirror, and two individual
  "built with Bolt.new" web-app projects — a coffee-shop landing page and a CRUD "wall of love"
  app) but **none are terminal/session-UX category matches** — Bolt.new's own user base skews
  toward web-app prototyping, not terminal-agent tooling, so this substitute channel is
  found-empty *for this category specifically*, not a source failure and not evidence the hits
  are unrelated to Bolt.new. An eighth check, `topic:hackathon "coding agent" terminal
  created:>2025-09-04` (with the spec's <500-star noise-reduction default applied via `select
  (.stargazers_count < 500)`), was run as the hackathon-showcase substitute — **0 total hits**,
  also found-empty for this category. The remaining step-4 substitutes the spec names
  (`topic:lovable`, `topic:replit`, and the Algolia `vibe coded`/`lovable`/`Show HN hackathon`
  queries) were **not run this pass** — not attempted, not a finding either way; flagged for a
  future run rather than silently omitted.
- **Show HN, via HN's Algolia API** (`hn.algolia.com/api/v1/search_by_date`, single free-text
  term, `tags=show_hn`, `hitsPerPage=30`), fetched via `polyfetch fetch <url> --show-body` —
  **found**. Query `terminal AI coding agent` returned 30 hits (sorted most-recent-first); the
  fetch tool's own output-size cap truncated the read partway through, so **only the first ~15 of
  the 30 were actually reviewed this run** (the trailing ~6 weeks of the window) — the remaining
  ~15, further back in the window, are unread this pass, not confirmed empty. Of the ~15
  reviewed, 9 are cited below as specifically about the terminal/session-UX layer itself (the
  rest — TheGitAI, Clixad, Memcode, DSCode, a Termux-native agent, MandoCode's first post, SerTerm
  — are adjacent "a coding agent
  that happens to run in a terminal" launches, not primarily about session/terminal UX, and are
  not cited as pattern evidence).
- **Bluesky public post-search API** — **blocked**, re-confirmed today. `polyfetch fetch
  'https://public.api.bsky.app/xrpc/app.bsky.feed.searchPosts?q=AI%20coding%20agent%20terminal'
  --json` returned `FingerprintBlock` / HTTP 403 after 3 attempts, consistent with the spec's
  documented finding. Not re-checked against the sibling `getProfile` endpoint this run (already
  established in prior runs); treated as blocked per the four-state discipline.
- **Devpost, Lovable/Replit/Bolt.new/v0-Vercel showcases, Indie Hackers, TrustMRR** —
  **excluded-by-scope**, per the spec's ruled-out list. Not attempted directly (the Bolt.new/v0
  GitHub-search substitute above was attempted, per instruction, and found empty for this
  category specifically).

### Ethical boundary applied

Every pattern below names an aggregate count and lists multiple independent instances; no single
project is presented as "the one to build like." Stars/points/org-vs-individual ownership appear
only as neutral corroborating metadata, never as a ranking signal elevating one project above the
others in its pattern.

---

### Pattern A — Terminal/desktop session manager for AI coding agents (general multiplexing, resume, cross-harness monitoring)

**147 raw hits on `terminal AI coding agent session` + 485 raw hits on `claude code session
manager`** (two overlapping free-text queries whose full-population overlap is unmeasured — see
Source coverage; one repo, `Aho1ic/Aeroric`, is confirmed to appear in both 30-item samples and
is counted once below); this is the dominant convergence in the category — a terminal, TUI,
desktop, or editor-embedded surface that manages multiple AI coding-agent sessions (Claude Code,
Codex, Cursor, Gemini CLI, OpenCode, Aider, etc.) at once, with session persistence/resume as the
recurring core feature. The 14 below are a representative, diverse cited subset spanning distinct
platforms (desktop GUI, TUI, VS Code sidebar, Neovim, Emacs, Raycast, Telegram/mobile, macOS
menu bar, browser dashboard) — not an exhaustive count; more were seen in the two 30-item samples
than are cited here for space.

> "Desktop GUI for managing multiple AI coding-agent sessions (Claude, Gemini, Aider, Codex, …) —
> the GUI counterpart of agent-session-manager. Wails + Svelte + xterm.js."
> — izll, [izll/agent-session-manager-desktop](https://github.com/izll/agent-session-manager-desktop),
> created 2026-06-22

> "Multi-device terminal server for AI coding agents. Server-side VTE, session persistence, file
> browser, web preview, plugin system. Self-hosted."
> — xichan96, [xichan96/dinotty](https://github.com/xichan96/dinotty), created 2026-05-12, 677
> stars

> "Terminal session manager for AI coding agents. One TUI for Claude, Gemini, OpenCode, Codex,
> and more."
> — asheshgoplani, [asheshgoplani/agent-deck](https://github.com/asheshgoplani/agent-deck),
> created 2025-12-03, 833 stars

> "See, steer, and remember every coding agent session — Claude Code, Codex, Cursor, Gemini. Team
> memory, live steering from any device, line-level agent attribution."
> — codecast-sh (Organization), [codecast-sh/codecast](https://github.com/codecast-sh/codecast),
> created 2025-12-10, 30 stars

> "Terminal workspace for running parallel AI coding agents (Claude Code, Codex CLI, Antigravity
> CLI) on macOS and Linux. Features multi-session resume, API usage dashboards, and automated git
> worktrees."
> — kkd927, [kkd927/kmux](https://github.com/kkd927/kmux), created 2026-04-12, 25 stars

> "Terminal UI for monitoring AI coding agents — like top, but for Claude Code, Codex, Aider,
> Gemini, Goose. Reads /proc + session transcripts."
> — MBrassey, [MBrassey/agtop](https://github.com/MBrassey/agtop), created 2026-04-30, 14 stars

> "The terminal for running multiple AI coding agents in parallel — Claude Code, Codex, Gemini
> CLI, opencode. A live status sidebar shows which session is working, done, or needs you, so you
> never miss the agent waiting on input. macOS, built on Tabby."
> — jessemaxh, [jessemaxh/GlanceTerm](https://github.com/jessemaxh/GlanceTerm), created 2026-06-22

> "Search, pin, and resume Claude Code sessions from Raycast"
> — imranismail, [imranismail/raycast-claude-session-manager](https://github.com/imranismail/raycast-claude-session-manager),
> created 2026-09-04

> "Claude Code session manager in Neovim"
> — kuangliu, [kuangliu/claude-sessions.nvim](https://github.com/kuangliu/claude-sessions.nvim),
> created 2026-08-04, 2 stars

> "Native macOS session manager for AI-powered development — orchestrates git worktrees, Claude
> Code, and GitHub/GitLab issues in an embedded terminal."
> — corveil (Organization), [corveil/crow](https://github.com/corveil/crow), created 2026-03-27,
> 18 stars

> "Embed AI coding agents in the VS Code sidebar with full terminal and tmux session management."
> — sage-z-cn, [sage-z-cn/ai-sidebar-terminal](https://github.com/sage-z-cn/ai-sidebar-terminal),
> created 2026-05-30

> "A cmux-like manager and control plane for many concurrent Claude Code (claude-code-ide)
> sessions in Emacs: butler/worker orchestration, per-session document panel, and a butler
> document repository."
> — toracle, [toracle/cc-butler](https://github.com/toracle/cc-butler), created 2026-07-03

> "Call sign 'Charlie-Charlie' — Control Claude Code, OpenAI Codex & other terminal AI agents
> from your phone. Telegram topics ↔ tmux: live pane screenshots & key presses, tappable
> interactive prompts, one-tap parallel git-worktree agents, two-way voice, session resume.
> Self-hosted, no cloud middleman."
> — MrCryptoHat, [MrCryptoHat/ccbot](https://github.com/MrCryptoHat/ccbot), created 2026-07-01, 1
> star

> "Know which AI coding session needs you, without hunting through terminals. A macOS menu bar
> app for Claude Code and Codex."
> — ThinkVelta (Organization), [ThinkVelta/agent-tracker](https://github.com/ThinkVelta/agent-tracker),
> created 2026-07-31, 1 star

**What this shows:** across fourteen independently-authored repos, spanning fourteen different
technical approaches to the same problem (a desktop app, a browser-embedded server, a TUI, a VS
Code sidebar, an Emacs package, a Neovim plugin, a Raycast extension, a Telegram/mobile bridge, a
macOS menu-bar app, a macOS-native app, an org-backed product), the recurring core feature is the
same three things: run several coding-agent sessions at once, know which one needs you, and
resume a session that would otherwise die with its terminal window. None of the fourteen forks or
clones another (per the search API's default fork exclusion — see the independence-heuristic
basis above); all were created independently within the window.

---

### Pattern B — Multi-agent coordination and handoff between concurrent coding-agent sessions

**14 raw GitHub hits, 11 distinct-author instances, all cited** (after collapsing `SimonMallas`'s
four same-concept repos to one) for tools whose explicit purpose is coordinating *handoffs* —
passing context, state, or control — between multiple agent sessions, not merely running them
side by side.

> "Agent-to-agent mail for multi-agent AI coding teams — every handoff a durable letter:
> coordination that is also the team's memory." (posted separately for cmux, Zellij, Herdr, and
> tmux — same author, same concept, four multiplexer-specific ports; counted once here)
> — SimonMallas, [SimonMallas/agent-letterbox-cmux](https://github.com/SimonMallas/agent-letterbox-cmux),
> created 2026-07-11

> "A multi-agent attention cockpit for triage, handoff, sessions, terminals, and engineering
> resources."
> — Icesource, [Icesource/straydeck](https://github.com/Icesource/straydeck), created 2026-07-22,
> 2 stars

> "AI powered Multi Agent Terminal...Transform your CLI into an AI-powered mission control.
> Orchestrate concurrent autonomous coding agents like Claude Code and Antigravity - across a
> dockable split-pane terminal grid, protected by real-time safety guardrails and cross-session
> handoffs."
> — pisigmac, [pisigmac/BigCode](https://github.com/pisigmac/BigCode), created 2026-08-18

> "Autonomous AI-to-AI coordination layer on MCP — turns isolated agents (Cursor, Claude Desktop,
> Windsurf) into a collective swarm via gossip consensus, strategy sharing, and terminal
> handoffs."
> — Jason-Vaughan, [Jason-Vaughan/Medusa](https://github.com/Jason-Vaughan/Medusa), created
> 2026-04-05, 1 star

> "Terminal switchboard for running multiple Codex/Claude CLI accounts side by side, with
> isolated credentials, persistent tmux sessions, and recorded handoffs between agents."
> — RallyGLSX, [RallyGLSX/polyagent](https://github.com/RallyGLSX/polyagent), created 2026-08-19

> "A filesystem-based handshake protocol that lets two terminal AI agents collaborate on the same
> task — shared spec, ownership state, append-only message logs, and handoff discipline rules."
> — jayadevrana, [jayadevrana/ai-handshake-protocol](https://github.com/jayadevrana/ai-handshake-protocol),
> created 2026-07-18

> "Local multi-agent coordination for terminal-native coding workflows: shared tmux workspace,
> cross-pane messaging, and thread transport for large handoffs."
> — maxto, [maxto/agent-mux](https://github.com/maxto/agent-mux), created 2026-04-22, 2 stars

> "Every AI agent on one ship. Native multi-agent terminal that auto-detects local AI auth
> (Claude/Codex/Copilot/Gemini/Ollama) and runs them simultaneously with built-in relay handoff."
> — Manavarya09, [Manavarya09/argo](https://github.com/Manavarya09/argo), created 2026-05-01, 8
> stars

> "Multi-agent control plane for coding CLIs (Codex, OpenCode, CommandCode, Hermes) with one tmux
> session, one .logs/ source of truth, terminal + web frontends."
> — trentisiete, [trentisiete/endy](https://github.com/trentisiete/endy), created 2026-05-05, 7
> stars

> "Local-first multi-agent developer workspace with terminal-native workflows, explicit handoffs,
> and inspectable project memory."
> — Jihaoyb, [Jihaoyb/gen](https://github.com/Jihaoyb/gen), created 2026-03-22

> "Personal multi-agent cockpit. Codex + Claude orchestration with VPS handoff, file dashboard,
> and a 6-pane terminal UI heading to Tauri/Windows."
> — Avi977, [Avi977/invisible](https://github.com/Avi977/invisible), created 2026-05-25

**What this shows:** eleven independently-authored projects, none sharing an upstream, converge
on the same specific sub-problem — that running agents in parallel isn't enough; someone has to
formalize how state and attention pass *between* sessions when one agent's work depends on
another's.

---

### Pattern C — Cost/token spend tracking dashboards for agent sessions in the terminal

**16 raw GitHub hits, 13 distinct-author instances** (after collapsing `gitstq`'s four repos to
one) for tools whose primary or headline feature is showing token usage, spend, or budget for
coding-agent sessions from the terminal. **12 of the 13 are cited below**; the 13th,
`shayan-ali-shah/Convigas-Code` ("A terminal coding agent that stops re-reading your codebase —
cost per turn stops tracking conversation length"), is excluded as borderline scope — it's a
cost-*reduction* technique described in passing, not a cost-*tracking* dashboard, and is flagged
here rather than silently dropped.

> "Track token usage, cost and cache hit rate of AI coding agents from the terminal."
> — Sanstoolow0513, [Sanstoolow0513/toksight](https://github.com/Sanstoolow0513/toksight), created
> 2026-08-30

> "A Rust terminal agent harness for multi-provider models, with tools, permissions, sessions,
> cost tracking, and isolated evaluations."
> — ducks, [ducks/claux](https://github.com/ducks/claux), created 2026-04-01, 16 stars

> "Local-first control plane for AI coding agents — launch browser terminals, run and remotely
> control sessions, and track files, tokens & cost across Claude Code, Codex, Cursor, Gemini CLI
> and 20+ more. 100% local, no telemetry."
> — superbasedapp (Organization), [superbasedapp/observer](https://github.com/superbasedapp/observer),
> created 2026-04-30, 42 stars

> "See what your AI coding agent is doing. Multi-provider assistant & session monitor for VS Code
> and the terminal — inline completions, code transforms, and a full TUI dashboard tracking
> tokens, cost, and context across Claude Code, OpenCode, and Codex."
> — cesarandreslopez, [cesarandreslopez/sidekick-agent-hub](https://github.com/cesarandreslopez/sidekick-agent-hub),
> created 2026-01-10, 81 stars

> "Open-source terminal AI coding agent on Mistral (Devstral/Codestral). Claude Code-style CLI
> with MCP, subagents, hooks, sessions, web tools, permissions & cost tracking."
> — Anicodeth, [Anicodeth/nightshade](https://github.com/Anicodeth/nightshade), created 2026-08-08

> "htop for AI coding agents — live terminal dashboard with real-time cost tracking, context
> health, and spin detection"
> — loop-eng (Organization), [loop-eng/loopctl](https://github.com/loop-eng/loopctl), created
> 2026-07-15

> "Monitor what your AI coding agents are spending. Real-time TUI + web dashboard for OpenAI,
> Anthropic, Google AI, DeepSeek. Budget alerts, session tracking, per-model breakdowns."
> — betty02, [betty02/agentcost](https://github.com/betty02/agentcost), created 2026-06-25

> "Terminal-native observatory for AI agent swarms on Ruflo MCP — live topology, signed session
> recording, cost/budget tracking, anomaly alerts"
> — HackerElman16, [HackerElman16/aura-swarm](https://github.com/HackerElman16/aura-swarm),
> created 2026-06-10

> "Lightweight AI Agent Terminal Multiplexer with Cost Tracking & Audit Log" (posted alongside
> three sibling repos by the same author — AgentPulse-CLI, AgentSessionForge, termwise — same
> builder, four overlapping attempts; counted once here)
> — gitstq, [gitstq/AgentDeck](https://github.com/gitstq/AgentDeck), created 2026-06-08

> "Secure, read-only operational dashboard for OpenClaw AI agents. Dark terminal aesthetic, real
> system metrics, cron monitoring, cost tracking."
> — justinpreston, [justinpreston/kano-dashboard](https://github.com/justinpreston/kano-dashboard),
> created 2026-02-26

> "AI-powered terminal coding agent, built with Go. Multi-provider, real-time cost tracking,
> interactive TUI."
> — xincode-ai (Organization), [xincode-ai/xin-code](https://github.com/xincode-ai/xin-code),
> created 2026-04-03, 1 star

> "Full-screen terminal dashboard for monitoring and commanding AI agents running in TinyAGI.
> Real-time activity feeds, task tracking, cost monitoring, agent-to-agent message visibility,
> and broadcast messaging from a single pane of glass."
> — atnine-ai (Organization), [atnine-ai/tinyagi-tui](https://github.com/atnine-ai/tinyagi-tui),
> created 2026-03-21

**What this shows:** cost/token visibility recurs as a headline feature (not an afterthought)
across these 12 cited tools (13 counting the excluded borderline instance above) — several
explicitly framed as "htop"/"top" for agents, suggesting a shared mental model of the missing
observability layer.

---

### Pattern D — Cross-harness session portability ("teleport" a session between Claude Code/Codex/Cursor/Hermes)

**4 raw GitHub hits, 4 distinct-author instances, all cited** — the narrowest and most specific
pattern found this run, and a direct, sourced confirmation of the note carried from discovery
(`config/scope.md`: "session portability across Claude Code/Codex/Cursor came up repeatedly in
titles alone during discovery, worth confirming as a real complaint"). Here it surfaces as a real,
independently-attempted **build** target, not just a complaint: four unrelated builders, in four
different repos, all built a tool whose sole purpose is exporting a session from one coding-agent
harness and resuming it in another.

> "Local-first coding-agent session portability: Codex to ATIF to Hermes"
> — Valdemar-Yu, [Valdemar-Yu/harnesshop](https://github.com/Valdemar-Yu/harnesshop), created
> 2026-08-29

> "Local-first cross-harness agent-session portability: export a coding-agent session from one
> harness (Claude Code, Codex, Hermes) and resume it in another."
> — connectwithprakash, [connectwithprakash/agent-session-bridge](https://github.com/connectwithprakash/agent-session-bridge),
> created 2026-07-19

> "Provenance-preserving, bidirectional session portability for AI coding agents."
> — Agentryx-ai (Organization), [Agentryx-ai/pass-the-thread](https://github.com/Agentryx-ai/pass-the-thread),
> created 2026-07-26

> "Agent agnostic session portability, teleport your sessions between coding agents with ease"
> — tornikegomareli, [tornikegomareli/agent-teleport](https://github.com/tornikegomareli/agent-teleport),
> created 2026-03-21, 4 stars

**What this shows:** small in count but tight in convergence — four builders, independently,
identified the exact same gap (a session is trapped in the harness that started it) and built the
exact same category of fix, spread across five months of the window (March, July x2, August).

---

### Pattern E — Show HN corroboration: grassroots, individually-launched terminal/session-UX tools

**9 distinct-author Show HN launches**, a separate signal channel from the GitHub search above
(none of the 9 repos below overlap the 41 GitHub-search citations in Patterns A–D: 14 + 11 + 12
+ 4) — each is a first-person "I built this because X was missing" post about the terminal/session
layer specifically, not a general coding-agent product launch.

> "I wanted a way to use my existing AI subscriptions as a terminal assistant... I know that
> coding agents can run shell commands already but that puts the AI in the driver's seat. Terminai
> let's me be the driver, with codex or claude (or any other agent) sitting quietly in the
> backseat until I want its help. [...] Vibe coded: Unabashedly written mostly by Claude and
> Codex."
> — emosenkis, [Show HN post](https://news.ycombinator.com/item?id=49055227) linking
> [emosenkis/terminai](https://github.com/emosenkis/terminai), created 2026-07-26T06:06Z, 3 points
> (this author also posted a second Show HN about the same tool roughly 9 hours later the same
> day — item 49058883, created 2026-07-26T15:05Z — counted once here per the independence
> heuristic's same-author dedup)

> "I'm an indie developer... I started using AI coding agents a few months ago, and one problem
> kept bothering me: Windows doesn't have a simple and reliable equivalent to tmux or screen. If I
> accidentally closed a terminal window, the agent session running inside it was gone too. [...]
> That's why I built qscreen."
> — dualface, [Show HN post](https://news.ycombinator.com/item?id=48989416) linking
> [dualface/qscreen](https://github.com/dualface/qscreen), created 2026-07-21, 2 points

> "I ran them in a split terminal for a few weeks, and quickly spotted two main problems. The
> first is that I couldn't easily tell which agent was stuck waiting on me and which was still
> working... So I built my own solution - Shikigami. It's a desktop app that runs agents side by
> side, each one in its own git worktree... The sessions are resumable too, you can quit the app,
> come back tomorrow, and a long conversation is where you left it."
> — igor_nast, [Show HN post](https://news.ycombinator.com/item?id=48966140) linking
> https://shikigami.dev/, created 2026-07-19, 7 points

> "The terminal app built for AI coding agents and other long-running sessions. thel keeps each
> session alive in the background and can anchor it to its own git worktree, so you can run a
> fleet of them at once and tell at a glance which one needs you."
> — kimr, [Show HN post](https://news.ycombinator.com/item?id=49059916) linking https://thel.dev/,
> created 2026-07-26, 2 points

> "Quick backstory... I wanted tabs, though... Having up to 4 agents running in one window is
> pretty cool... Context snapshots — closing an agent archives the conversation instead of
> deleting it, and you can snapshot mid-conversation to get an AI-written recap you can hand to a
> different model or a fresh agent and have it pick up the thread."
> — devmando, [Show HN post](https://news.ycombinator.com/item?id=49099891) linking
> [DevMando/MandoCode.Desktop](https://github.com/DevMando/MandoCode.Desktop), created 2026-07-29,
> 1 point

> "I built Relay around a simple idea: many of us have an unused PC server at home, or a VPS
> dedicated to AI-assisted coding, but the coding agents running there are still tied to that
> machine's terminal... Relay brings Claude Code, Codex, OpenCode, and Hermes into one interface
> that you can access from your phone, browser, or another computer. Sessions stay alive, so you
> can start work on one device and continue from another."
> — elin66alpha, [Show HN post](https://news.ycombinator.com/item?id=49356310) linking
> [elin66alpha/Relay](https://github.com/elin66alpha/Relay), created 2026-08-19, 6 points

> "Since the start of this year I've coded pretty much exclusively by running multiple AI agents
> in parallel. Having tried a bunch of tools for managing them, I kept coming back to tmux... So I
> built tmux-agent-switcher, a tmux plugin that gives me a better overview of all of them without
> replacing tmux as the core tech."
> — ymir_e, [Show HN post](https://news.ycombinator.com/item?id=49278395) linking
> [Ymirke/tmux-agent-switcher](https://github.com/Ymirke/tmux-agent-switcher), created 2026-08-12,
> 2 points

> "From early on, I favor AI CLI using terminal over AI in IDE... However, even though I like it
> very much, terminal can be limiting, and agents can do more for us than standard terminal
> allows. [...] Now, I have a terminal but not just a terminal, which I use daily for all my
> projects."
> — aywu, [Show HN post](https://news.ycombinator.com/item?id=49259180) linking
> [albertwujj/agent-term](https://github.com/albertwujj/agent-term), created 2026-08-11, 1 point

> "Federaide is a general purpose multi-agent harness that runs in your terminal... Wanted to
> 'vibecode' something... When I started this (in March) multi-agent harnesses were not a thing
> (arguably they still aren't mainstream, but there are other projects out there now)."
> — petrokitty, [Show HN post](https://news.ycombinator.com/item?id=49273811) linking
> https://federaide.rocklab.in, created 2026-08-12, 3 points

**What this adds:** the GitHub-search patterns above could, in principle, be read as SEO-driven
repo proliferation rather than genuine grassroots demand. These nine first-person Show HN
narratives — each independently describing hitting the identical wall (a lost session, an
un-attributable agent, a session trapped in one machine or one harness) before building a fix —
corroborate that the GitHub volume tracks a real, individually-felt problem, not just repo spam.

---

### Summary

- **Patterns found:** 5 (A: general session/terminal manager · B: multi-agent handoff/coordination
  · C: cost/token tracking dashboards · D: cross-harness session portability · E: Show HN
  grassroots corroboration of A/B/D-shaped problems)
- **Independent instances cited (deduplicated per the heuristic, each with author handle, URL, and
  creation date):** 14 (Pattern A, representative subset of a much larger population) + 11
  (Pattern B, all distinct-author instances) + 12 (Pattern C, cited; a 13th distinct-author
  instance identified but excluded as borderline scope) + 4 (Pattern D, all instances) + 9
  (Pattern E, Show HN, no overlap with A–D) = **50 sourced, individually-cited instances**. These
  are drawn from five separate, **not-summed** GitHub query populations (147 + 485 + 14 + 16 + 4
  raw hits — reported per query because their overlap is unmeasured, see Source coverage) plus 30
  Show HN hits (of which ~15 were reviewed). Patterns B, C, and D cite the full identified
  distinct-author population for their respective queries; Pattern A cites 14 of a much larger
  ~60-repo sample (two queries' worth) for space.
- **Independence-heuristic caveats surfaced:** two same-author multi-repo clusters (`SimonMallas`
  x4 within Pattern B, `gitstq` x4 within Pattern C, each collapsed to one instance) and one
  convergent-naming coincidence ("agent-deck"/"AgentDeck" by three unrelated owners) — both
  flagged above, neither hidden.
- **Source states:** GitHub Search API — found (rich; authenticated `gh` CLI, 30/30 rate-limit
  budget available throughout) · Bolt.new/v0-Vercel GitHub substitute (step 4) — found-empty for
  this category specifically (4 genuinely Bolt.new-related hits inspected directly, none
  terminal-UX-relevant; 0 `v0.dev` hits) · hackathon-showcase GitHub substitute — found-empty (0
  hits) · remaining step-4 substitutes (`topic:lovable`, `topic:replit`, Algolia
  `vibe coded`/`lovable`/`Show HN hackathon`) — **not run this pass**, flagged for a future run ·
  Show HN (Algolia) — found, partially read (~15 of 30 hits reviewed; remainder unread, not
  confirmed empty) · Bluesky — blocked (re-confirmed today, `FingerprintBlock`/403) ·
  Devpost/Lovable-Replit/Bolt.new-v0/Indie Hackers/TrustMRR direct access — excluded-by-scope,
  unattempted (per the spec's ruled-out list).
- **Net read for the concept-synthesis stage:** this category shows unusually dense, multi-angle
  convergence for a first-ever scan — not one pattern but four distinct, independently-populated
  sub-problems (general session management, cross-agent handoff, cost visibility, cross-harness
  portability) plus first-person corroboration on Show HN that these are felt problems, not just
  SEO-shaped repo volume. Pattern D (session portability) is the most direct, sourced confirmation
  of the specific signal discovery flagged as worth checking.
