# Handoff — 0003 broad discovery + parallel category runs

Full design + remaining-work table: `docs/plans/0003-broad-discovery.md`. Don't duplicate that table
here — this doc only onboards the next session to it.

## Lead with this

The user asked twice in one session (2026-09-04) for genuinely categoryless discovery, not a
hand-picked list of categories to choose from. This arc adds a new Phase 0 (`signal-discoverer`)
that does one broad, bounded pull (Show HN + ProductHunt, no topic filter) and clusters it into
candidate categories — the existing 3-agent pipeline (`docs/plans/0002-signal-to-concept-v1.md`)
still runs per-category afterward, unchanged. Parallel execution across chosen categories uses git
worktrees, this repo's own standing rule that's never actually been load-bearing until now (every
prior run used the shared working tree with no real collision, since a single category's two Phase-1
agents write to two different filenames — N *categories* running concurrently would collide on the
single shared `config/scope.md`).

## What's next, in order

1. ~~Write `.claude/agents/signal-discoverer.md`~~ — done 2026-09-04.
2. ~~Add `discovery/README.md` + `.gitignore` entry~~ — done 2026-09-04.
3. ~~Update `AGENTS.md`: Phase 0 + worktree-requirement~~ — done 2026-09-04.
4. ~~Run `signal-discoverer` for real once~~ — done 2026-09-04: Show HN fully covered (3,670 titles,
   6-bucket pull), ProductHunt narrower than intended (one calendar day, root-caused and folded back
   into the spec — see the 0003 plan's remaining-work table). 8 categories promoted, 2 rejected as
   grab-bags. Archived at `examples/2026-09-04T060617Z-discovery/`.
5. **Owner gate, now open**: present the resulting candidate-category list to the user. **Don't
   mechanically apply "top 3 by signal count"** — the discovery run itself flagged #2 (MCP servers)
   and #8 (menu-bar utilities) as "shape of build" patterns needing a narrower angle before they're
   scopeable, and #4 (local-first/self-hosted) as an ethos spanning many domains, same caveat.
   Recommended default instead: **#1 (agent memory/persistent context), #3 (terminal/session UX for
   coding agents), #5 (freelancer finance/expense tools)** — cross-source, narrow, immediately
   scopeable without picking a sub-angle first. User can pick differently.
6. For the chosen categories, set up one git worktree per category (`git worktree add`), write each
   its own `config/scope.md`, and dispatch the full 3-agent pipeline into each — first real test of
   parallel worktree execution. Merge results back via one PR per category (or batched), following
   the same branch → commit → PR → squash-merge discipline every prior arc in this repo has used.

## Watch-outs

- **Discovery output is not sourced-quote evidence** — it's aggregate counts/clusters, a triage step.
  Don't run `scripts/verify_sourcing.py` against it and don't expect it to pass the same bar as a
  `findings/*.md` file; state this distinction in the spec so nobody mistakes a missing citation for
  a bug.
- **Ethical boundary applies at discovery too** — even at the "just clustering titles" stage, never
  let a cluster collapse into naming one specific project as the reason a category looks promising.
- **The worktree rule is finally load-bearing, not decorative** — get the per-worktree
  `config/scope.md` write right (each worktree needs its own, since `config/scope.md` is gitignored
  and won't exist in a fresh worktree checkout by default).
- **This session's environment note still applies**: Bash `ls`/`find`/version-probes are denied in
  this workspace; dispatched agents need to be told to use Read/Write tools for file checks instead,
  and go straight to substantive Bash commands (`gh api`, `polyfetch fetch`, `uv run`). See
  `docs/handoffs/0002-signal-to-concept-v1.md` item 6 for the fuller note — don't duplicate it here,
  just don't forget it when briefing new subagents for this arc either.
