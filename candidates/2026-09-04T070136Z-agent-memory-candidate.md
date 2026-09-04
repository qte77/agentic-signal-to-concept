# Candidate — Contextlint

## Concept candidate

- **Name:** Contextlint
- **Live URL:** Not yet built — feeds agentic-grounded-persona-eval Phases 1-2 only, not Phase 3
- **Slug:** contextlint

A hygiene/audit layer that sits on top of the memory and instruction files a team already has —
CLAUDE.md, AGENTS.md, and whatever MCP/local-storage memory store they've adopted — rather than a
new storage backend of its own. It (1) flags stale, contradictory, or "poisoned" entries and surfaces
*why* an agent is misbehaving when a bad memory line is the cause; (2) treats instruction files as
versioned, model-scoped artifacts instead of an append-only diary, so a team can see which rules are
still earning their keep; (3) runs a lightweight before/after regression check when a memory or
instruction file changes, instead of shipping edits on faith. It deliberately does not compete with
the wave of new memory-storage products the builds pass documented — it plugs into them.

## Assumed ICPs

(derived from evidence, not invented)

- Solo/expert developers who maintain CLAUDE.md/AGENTS.md by hand across several coding agents and
  projects and increasingly resent the upkeep itself, not the concept of memory.
- Engineering teams/orgs running one shared AGENTS.md or memory store consumed by every engineer's
  agent, where a single bad edit silently degrades everyone's output and nobody notices until later.
- Maintainers of the existing memory-storage tools cataloged in the builds pass — treated here as an
  integration/distribution surface, not a competitor set — several of whom already name the
  lifecycle/audit gap as an open TODO in their own product.

## Research constraints

(carried over/adapted from config/scope.md)

- Re-run ProductHunt once `PRODUCTHUNT_API_TOKEN` is actually reachable in-session. This run's PH
  status was a credential-access failure in the worktree, not absence of PH signal — `scope.md`
  names PH products (Organizational Memory 2.0, Sonnenfeld, Engram, MemHub) that have not yet been
  checked against this candidate's positioning.
- Reddit (excluded-by-scope, confirmed fingerprint-blocked in a sibling repo's run) and Bluesky
  (confirmed blocked this run, fresh 403 `FingerprintBlock`) stay excluded/blocked; do not re-attempt
  without new authorization or verified access.
- Weight the "why does this exist" skepticism documented in the complaints pass explicitly when
  building Phase 2 personas — include a skeptical-evaluator persona alongside adopters, since that
  reaction appeared against nearly every memory-system Show HN sampled in this run, not as an outlier.
- When Phase 1 of `agentic-grounded-persona-eval` researches this candidate, explicitly compare its
  audit/lifecycle-first positioning language against the storage-first positioning used by the
  aggregate builds population — the differentiation claim below only holds if that language gap is
  real and legible in the wild, not assumed from this synthesis pass alone.
- Treat "why not just grep a folder" as a required objection the eventual pitch must answer in its
  first paragraph — per the complaints pass, differentiation that isn't legible immediately loses
  readers before they reach an architecture section.

## Evidence

### Source coverage summary

Both Phase 1 passes exist for this run — the first-ever execution of both specs for scope
`agent-memory` — so this candidate is **cross-source-evidenced at the category level**, though
individual features below vary in whether they carry direct cross-source corroboration (noted per
item). Note on precondition-checking: this synthesis pass was handed both findings file paths
directly by the invoking task rather than independently globbing `findings/*-agent-memory-*-findings.md`
and taking the lexicographically-last match of each (the environment blocked shell-based
file-listing this run). Since this is confirmed the first-ever run for this scope, there is only one
match per glob either way, so this does not change which files were used — but it is a deviation from
the spec's stated precondition-check procedure, noted rather than silently skipped.

- **Complaints** — [`findings/2026-09-04T070136Z-agent-memory-complaints-findings.md`](../findings/2026-09-04T070136Z-agent-memory-complaints-findings.md).
  HN only, real signal (7 patterns, 34 sourced quotes, ~90 comments read across 6 threads).
  ProductHunt is **blocked, not absent** this run — a confirmed credential-access failure in this
  worktree, not a finding that PH lacks discussion. Reddit excluded-by-scope.
- **Builds** — [`findings/2026-09-04T070136Z-agent-memory-builds-findings.md`](../findings/2026-09-04T070136Z-agent-memory-builds-findings.md).
  GitHub + Show HN, real signal (3 patterns, 37 cited independent instances, 649 raw unfiltered
  GitHub matches for the tightest query alone). Bluesky **blocked, not absent** (fresh 403
  `FingerprintBlock`). Devpost/Lovable/Replit/Bolt.new/v0/Indie Hackers/TrustMRR excluded-by-scope;
  GitHub-topic and Show HN substitutes used instead, per spec.

### Per-feature grounding

**1. Audit/lifecycle/drift-handling as the core value prop** — grounded in complaints Pattern 3
("Memory poisoning, drift, and staleness: the audit/trust gap"), in
[the complaints findings file](../findings/2026-09-04T070136Z-agent-memory-complaints-findings.md).

> "Does anyone else not use memory? I find once there is one poisoned line of text it negatively
> affects everything else downstream... Any information that can be extrapolated is just noise which
> negatively affects the agent... it is noise, will drift, and be impossible to debug why the agent
> keeps producing undesired behavior."
>
> — dataviz1000, [HN comment](https://news.ycombinator.com/item?id=49509345), 2026-08-31

> "My main gripe so far is that I have to push the agent to maintain an organized graph... The kg has
> some design limits that make poisoning hard to diagnose, so that's still on the list to fix."
>
> — r14c, [HN comment](https://news.ycombinator.com/item?id=49522604), 2026-09-01

*Partial cross-source corroboration:* builds Pattern 1 ("MCP or local-storage persistent memory built
specifically for coding agents"), in
[the builds findings file](../findings/2026-09-04T070136Z-agent-memory-builds-findings.md), shows 3
of its 15 individually cited instances already bolting on partial lifecycle features as secondary
bullets within a storage-first pitch, not as any observed build's primary feature — cited here as an
aggregate observation across independently-owned repos, not as a template to replicate (per this
spec's ethical-boundary rule):

> "...Structured scaffold + drift detection CLI."
> — mex-memory (org), [mex-memory/mex](https://github.com/mex-memory/mex), created 2026-03-21

> "...Records issues, attempts, fixes and decisions, then warns your agent before it repeats an
> approach that already failed..."
> — riponcm, [riponcm/projectmem](https://github.com/riponcm/projectmem), created 2026-05-09

> "...it decays what you stop touching, promotes what recurs, filters recall to the git branch you're
> on..."
> — m-agahi, [m-agahi/yadgar](https://github.com/m-agahi/yadgar), created 2026-07-25

**2. Versioned, model-scoped instruction-file hygiene** — grounded in complaints Pattern 4
("CLAUDE.md/AGENTS.md instruction rot"), in
[the complaints findings file](../findings/2026-09-04T070136Z-agent-memory-complaints-findings.md).

> "I'm fairly sure a meaningful percentage of my system prompt is now actively making things
> worse—instructions written for a model that no longer exists, aggressively steering a smarter one
> away from things it would have gotten right on its own. But I can't tell which lines those are,
> because to find out I'd have to delete one and see if anything bad happens, and that's how you get
> force-pushed to main."
>
> — Alex Jacobs, [I Am Morally Opposed to Updating My CLAUDE.md](https://alex-jacobs.com/posts/claudemd/), 2026-08-19

**No build-side corroboration found in this run** — flagged single-source (complaint-only). See
thin-grounding flags below.

**3. Before/after regression check for memory/instruction-file edits** — grounded in complaints
Pattern 5 ("Doubt that memory/instruction files are actually obeyed, and no way to test that they
are"), in [the complaints findings file](../findings/2026-09-04T070136Z-agent-memory-complaints-findings.md).

> "I think we should move towards data-based tuning of AGENTS.md, testing out changes, gathering
> data, and then making a decision on whether or not to ship it."
>
> — bisonbear, [HN comment](https://news.ycombinator.com/item?id=48160918), 2026-05-16

**No build-side corroboration found in this run** — flagged single-source (complaint-only), and the
thinnest-grounded piece of this candidate. See thin-grounding flags below.

**4. Sits on top of the substrate people already trust, rather than replacing it** — directly answers
complaints Pattern 7 ("A recurring 'just use files/grep, don't overbuild this' pushback against
structured memory systems"), in
[the complaints findings file](../findings/2026-09-04T070136Z-agent-memory-complaints-findings.md):

> "This seems way too complicated and unnecessary. Agents are perfectly capable of discovering
> memories on the FS, following agent instructions... What we could use instead is a file system
> layout standard, which could subsume memories and a lot more."
>
> — avaer, [HN comment](https://news.ycombinator.com/item?id=48429028), 2026-06-06

— and indirectly answers complaints Pattern 2 ("Skepticism that the market needs another memory
tool/protocol at all") by deliberately not being another storage backend:

> "There are 400+ MCP servers that provide individual agent memory..."
>
> — simplyjosh56, [HN comment](https://news.ycombinator.com/item?id=47602486), 2026-04-01

*Cross-source note:* Pattern 2's skepticism is itself independently corroborated by the scale
documented in builds Pattern 1 — 15 cited + ~634 further raw GitHub matches on the tightest query, 12
more Show HN launches in builds Pattern 2, and four independently-owned "engram"-named projects alone
(`Gentleman-Programming/engram`, `cdzzy/engram`, `aiengram/engram`, `mwihoti/engram`). Neither pass
references the other; the convergence is between two independently-collected datasets pointing at the
identical saturation, which is the strongest form of signal this method produces.

**5. Retrieval-scoped cost accounting, as a secondary feature, not the differentiator** — complaints
Pattern 6 ("Token/cost overhead of memory and instruction files, contested"), in
[the complaints findings file](../findings/2026-09-04T070136Z-agent-memory-complaints-findings.md):

> "The lazy loading is key — the agent never reads the database directly... A typical session start
> costs ~20 tokens for the L1 overview. Drilling into one specific topic costs ~80 tokens. Compare
> that to a MEMORY.md that injects 3000-8000 tokens wholesale every time."
>
> — Bumblebiber (maker), [HN comment](https://news.ycombinator.com/item?id=47103263), 2026-02-21

Bumblebiber is disclosed as a maker in the source findings (pitching his own tool); weighted
accordingly. No build-side corroboration in this run — single-source, secondary to the core pitch.

**6. Explicitly not pursued: a new cross-agent-portable memory-storage format/protocol.** Complaints
Pattern 1 ("Memory is locked to one tool/vendor/machine; switching costs everything"), in
[the complaints findings file](../findings/2026-09-04T070136Z-agent-memory-complaints-findings.md),
is the single most heavily corroborated complaint pattern in the entire run — four independent makers
plus one non-maker Ask HN OP converging on the identical pain — and it maps directly onto builds
Pattern 1, in [the builds findings file](../findings/2026-09-04T070136Z-agent-memory-builds-findings.md),
the dominant, most heavily populated build pattern observed (15 cited instances, 649 raw matches).
That is the cleanest full cross-source convergence in this evidence set: demand (complaints Pattern 1)
and dominant existing supply (builds Pattern 1) point at the identical gap. The conclusion drawn from
that convergence is deliberately the opposite of "build here" — this space is already being solved by
dozens of independently-owned projects (per the naming-convergence and `recallops` collision caveats
already logged in the builds findings), and complaints Pattern 1 itself carries a stated counter-signal
against a new protocol specifically:

> "I can see the value in a protocol here, but the issue is these efforts are only as good as the
> industry adoption that they gain... As a developer, I wouldn't touch this without confidence I can
> get gains down the line from interoperability."
>
> — samdjstephens, [HN comment](https://news.ycombinator.com/item?id=48429255), 2026-06-06

Building a new storage/portability layer here would be, in complaints Pattern 2's own words, the
"401st MCP memory server" — this candidate routes around that saturation by not competing on storage
at all.

### Thin-grounding flags

- **Audit/lifecycle/drift feature (item 1), the core wedge — grounded, but not as thickly as its
  "core value prop" billing implies.** The comment IDs cited from complaints Pattern 3 (dataviz1000,
  Avijit_Thawani, pianopatrick, linggen — all 2026-08-31 — plus r14c the next day) cluster tightly
  enough that they plausibly come from one or two threads, not the several independent threads that,
  e.g., Pattern 1's four-maker convergence draws from; the complaints findings file itself only claims
  "multiple independent commenters," not multiple threads. On the build side, the corroboration is 3
  secondary bullets inside 15 storage-first pitches, not a build pattern of its own. Net: this is the
  least thin of this candidate's grounded features, but it is multi-commenter-in-few-threads plus
  secondary-feature-in-a-storage-pitch, not a thick, independently-multi-threaded convergence — stated
  plainly rather than implied by the "core value prop" framing above.
- **Regression/eval-harness feature (item 3)** rests on three HN quotes, all from one Ask HN thread,
  none from a builder, and has zero corroboration from any observed build in this run. Real and
  directly on-point language, but the narrowest evidentiary base in this candidate — the
  thinnest-grounded piece of it.
- **CLAUDE.md/AGENTS.md-rot framing (item 2)** rests on one dedicated HN thread built around a single
  blog post (Alex Jacobs, corroborated by JasonSage and Swizec within that same thread). Strong
  quotes, but one thread is a narrower evidentiary base than item 1's, and it has no build-side
  corroboration either.
- **Maker-sourced quotes carried forward, not dropped:** r14c (item 1) and Bumblebiber (item 5) are
  disclosed makers of competing/adjacent tools in the source findings, describing gaps in their own
  products rather than venting as disinterested users. Their inclusion is intentional — independent
  makers naming the same unsolved gap is itself corroboration — but it is a different evidentiary
  weight than an aggrieved end-user complaint, consistent with the complaints findings' own
  cross-cutting caveat.
- **PH gap not yet closed:** this candidate has not been checked against the PH-only products named in
  `config/scope.md` (Organizational Memory 2.0, Sonnenfeld, Engram, MemHub) for direct positioning
  overlap, because PH access failed on credentials this run rather than returning a real empty result.
  This is a gap for the sibling pipeline's Phase 1 to close, not one this synthesis pass can close by
  itself.
