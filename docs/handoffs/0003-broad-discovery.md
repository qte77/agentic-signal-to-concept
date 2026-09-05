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
5. ~~Owner gate~~ — done 2026-09-04: user confirmed the recommended default (#1 agent memory, #3
   terminal/session UX, #5 freelancer finance), and separately asked for horizontal/vertical to be
   named pipeline modes (done — see `AGENTS.md`'s "Two modes" section).
6. ~~Parallel worktree runs~~ — done 2026-09-04: three worktrees
   (`../asc-agent-memory`, `../asc-terminal-ux`, `../asc-freelancer-finance`), full 3-agent pipeline
   in each, merged back via one PR per category. Produced 3 candidates: `Contextlint` (agent-memory),
   `Vigil`/`Overflow` (terminal-ux), `Freehold Finance`/`Upfront Terms` (freelancer-finance). Real
   gap hit and fixed: `.env` isn't carried by `git worktree add` (gitignored) — now an explicit
   pre-dispatch step in `AGENTS.md`.
7. ~~Calibration check~~ — done 2026-09-04, per explicit user request: compared discovery's rank to
   actual vertical yield. Did not hold on raw volume (see the 0003 plan's remaining-work table for
   the full comparison) — a genuinely useful negative result for interpreting future discovery ranks,
   not a bug to fix.
8. ~~Source breadth~~ — done 2026-09-04: GitHub added as a third discovery source (no new access/ToS
   cost, reuses `build-pattern-scanner`'s `gh` CLI auth exactly). BetaList and GH Archive researched
   and tracked as next-tier/owner-gated rather than built (see 0003 plan's table).
9. ~~Second real `signal-discoverer` run (first with GitHub)~~ — done 2026-09-04: 9 categories (up
   from 8), 5 rejected. Caught and correctly excluded a new kind of finding — a single project's
   plugin ecosystem (33 repos, all for `deepseek-ai/deepseek-harness`) that looked cluster-shaped but
   wasn't independent convergence; folded into the spec's ethical-boundary section as a worked
   example. Two more corrections folded back: PH's `first`-capped-at-20 behavior, and explicit
   window-overlap reporting (this run's window turned out ~99% overlapping the first run's, ~12h
   apart — most counts were the same corpus re-measured, one category was genuinely new). Archived
   at `examples/2026-09-04T081918Z-discovery/`.
10. ~~Alpha/beta reframing~~ — done 2026-09-05, per explicit user pushback that discovery's top
    categories are "already crowded." Root cause: discovery's three sources are all supply-side/build
    signal, so its rank measures build volume, not demand — which is also why the calibration check
    (item 7) never correlated with vertical yield. A cheap-automation fix (HN Algolia comment-count as
    a demand proxy) was tested and falsified — see the 0003 plan's remaining-work table for the exact
    queries and result. Resolution: alpha detection stays two-stage — horizontal flags low-count/
    single-source-leaning categories as cheap tentative alpha candidates (no new implementation
    needed), vertical mode's sourced quotes remain the only real demand confirmation. Beta (a
    differentiated angle inside an already-crowded category, e.g. terminal-ux's unaddressed "bookmark
    before crash" ask among 6 saturated `claude --resume` wrappers) is vertical-only by nature — no
    aggregate count can reveal it.
11. **Next real step, not yet decided**: two discovery runs now exist, giving real material toward
    issue #20 (trend-aware recurring runs) once there's genuine time separation between runs — this
    run's own note says spacing future runs further apart (or diffing `objectID`s against the prior
    run) is needed before a velocity signal means anything. Otherwise: pick a category using the
    low-count/single-source lens from item 10 — but filter out categories run 2's own text already
    flags as heterogeneous form-factors rather than single problems (#3 MCP tooling, #5 local-first,
    #9 macOS menu-bar all carry that explicit caveat, so their low counts don't mean low supply on a
    real single problem). Run 2's #4 (job-search/interview-prep tooling — 21 HN + 12 PH, cross-source,
    single-problem-shaped, and genuinely new this run, not a re-measurement of run 1) or #8
    (habit-tracking/journaling — 21 HN + 8 PH, same shape) are the cleaner candidates by this lens. Or
    wait for real elapsed time before a third discovery run. Awaiting user direction.

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
