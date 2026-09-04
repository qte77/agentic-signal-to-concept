# Complaint/Feature-Request Mining — agent/coding-agent memory & persistent-context tools

Run: `agent-memory` · Generated: 2026-09-04T070136Z · Phase: complaint-miner (§Research)

## Source coverage

| Source | Status | What was tried | What actually happened |
|---|---|---|---|
| Hacker News (Algolia search + Firebase comment API) | **found** | 7 Algolia queries, all reviewed (`agent memory`, `coding agent memory`, `Beads Steve Yegge`, `CLAUDE.md`, `cursor memory context` — all `tags=story`; plus 2 `tags=comment` searches for portability language: "start from scratch every session agent", "lose context switch agent memory") via `hn.algolia.com/api/v1/search`; **top-level comments only (no recursion into reply sub-threads)** fetched via `hacker-news.firebaseio.com/v0/item/<id>.json` for 6 stories (Beads — 18 top-level comments; "I am morally opposed to updating my Claude.md" — 9; "Ask HN: Do you still spend time maintaining Claude.md/AGENTS.md" — 12; "Compress Your Claude.md" — 6; "Universal Memory Protocol" — 25; "Agent memory as a file format" — a 15-comment sample of 44 top-level), plus the primary blog post behind the Claude.md thread (`alex-jacobs.com/posts/claudemd/`). All fetched raw via `polyfetch fetch --show-body` (no summarizing tool used). ~90 individual comments read. **Not a full recursive comment-tree walk as the spec calls for** — reply sub-threads under top-level comments were not fetched; treat this as a partial walk, not exhaustive. | Rich, on-topic discussion across all 6 threads. Quotes below are drawn from independent commenters (different HN usernames, different threads) wherever the pattern claims cross-source support. |
| ProductHunt (GraphQL v2 API) | **blocked — owner-gated, not absence of signal** | Per `complaint-miner.md`, checked for `PRODUCTHUNT_API_TOKEN` before use. This worktree's `.env` does not exist (`Read` returned "File does not exist"), and both a direct `Read` of the main repo's `.env` and a `cp` from it were refused by this session's permission settings ("File is in a directory that is denied by your permission settings" / Bash permission denied) — despite the task brief stating the token had been copied in. Confirmed via `python3 -c "import os; ... os.environ"` that `PRODUCTHUNT_API_TOKEN` is genuinely absent from this process's environment, not just unread. As a diagnostic (not a workaround), sent an unauthenticated POST to `https://api.producthunt.com/v2/api/graphql` via a direct `requests` client — the endpoint is reachable and returned HTTP 401 `{"error":"invalid_oauth_token", ...}`, confirming this is a credential-access failure in this run, not a network block or an absence of PH discussion for this category. No PH `post(slug:...)` lookups (e.g. for Organizational Memory 2.0, Sonnenfeld, Engram, MemHub — the products named in `config/scope.md`) were possible. **This gap should be re-run once the token is actually reachable in-session; it is not evidence PH lacks signal on this category.** |
| Reddit | **excluded-by-scope** | Not attempted, per `complaint-miner.md` and `docs/plans/0001-concept.md`: unauthenticated scraping is confirmed fingerprint-blocked (403) across three independent tools on a sibling repo's real run; OAuth terms/limits remain unverified for this use case. |

All quotes below are verbatim from the fetched HN JSON/HTML, with the comment's or story's own permalink. No paraphrase.

---

## Pattern 1 — Memory is locked to one tool/vendor/machine; switching costs everything

Four independent builders of competing memory tools converge on the identical motivating complaint — not a single anecdote. This is the most heavily corroborated pattern in the run and maps directly to the "cross-agent portability" theme called out in `config/scope.md`.

> "AI coding breaks when you switch agents. You lose context, lose progress, re-explain everything, and waste 10–30 minutes every time... Right now, your prompt history lives inside tools you don't control. We think it shouldn't. Your prompt history is your moat."
>
> — rueichu_haung, [HN comment](https://news.ycombinator.com/item?id=47934328), 2026-04-28

> "I built this because I was frustrated with how siloed AI memory is in coding agents. Claude Code's memory only works in Claude Code. Cursor's context doesn't follow you elsewhere so you switch tools and lose everything. And none of it is shareable across a team easily."
>
> — memel06, [HN comment](https://news.ycombinator.com/item?id=47625072), 2026-04-03

> "2. Vendor/machine lock-in: I switch between Claude Code, Gemini CLI, and OpenCode depending on the task. And I work on two PCs. CLAUDE.md only works in Claude Code, on one machine. There was no way to carry knowledge across tools or devices."
>
> — Bumblebiber (maker, replying in his own Show HN thread), [HN comment](https://news.ycombinator.com/item?id=47103263), 2026-02-21

> "AI agents can already use tools and coordinate, but their memory is fragmented across project files, agent notes, local stores, databases, and vendor-specific systems. Move to a new tool and the context is gone."
>
> — edihasaj (maker of Universal Memory Protocol), [HN comment](https://news.ycombinator.com/item?id=48428804), 2026-06-06

**Caveat, stated plainly:** the four quotes above are from people who then built a tool to fix the pain they describe — they are self-interested framings, not disinterested venting. The value of citing four *independent* ones together is that they didn't coordinate and arrived at the same complaint from different codebases; that convergence is the signal, not any single quote's rhetoric. One genuinely non-maker source, asking rather than pitching, corroborates the same pain directly:

> "I use Claude for some tasks, Cursor for coding, ChatGPT for research, and Perplexity for quick lookups. The problem is none of them know what I've discussed with the others. I find myself re-explaining the same context repeatedly, or copy-pasting from Notion docs."
>
> — arapkuliev (Ask HN OP, not pitching a product), [HN post text](https://news.ycombinator.com/item?id=46885728), 2026-02-04

**Counter-signal worth carrying forward:** portability is not universally wanted.

> "I don't even want a shared agent memory."
>
> — up2isomorphism, [HN comment](https://news.ycombinator.com/item?id=48429616), 2026-06-06

> "I can see the value in a protocol here, but the issue is these efforts are only as good as the industry adoption that they gain: who is using this? MCP came from Anthropic, A2A from Google so they had big tech backing from day 1. As a developer, I wouldn't touch this without confidence I can get gains down the line from interoperability."
>
> — samdjstephens, [HN comment](https://news.ycombinator.com/item?id=48429255), 2026-06-06

**What this means for a concept candidate:** portability is a real, recurring pain among builders themselves, but any standalone "universal memory format" pitch has to answer the adoption-chicken-and-egg problem samdjstephens names — a format nobody else honors isn't portable, it's just another silo with extra steps.

---

## Pattern 2 — Skepticism that the market needs another memory tool/protocol at all

A recurring, sharply worded reaction: HN readers perceive the agent-memory space as saturated and undifferentiated, and greet new entrants with "why does this exist" rather than curiosity.

> "There are 400+ MCP servers that provide individual agent memory. Engram is not that. Engram is a consistency layer."
>
> — simplyjosh56 (maker, citing the crowding himself while pitching his own entrant), [HN comment](https://news.ycombinator.com/item?id=47602486), 2026-04-01

> "with apologies to Andy Warhol - in the future, everyone will have a universal protocol for agent memory that is on the HN front page for 15 minutes."
>
> — evil-olive, [HN comment](https://news.ycombinator.com/item?id=48429360), 2026-06-06

> "I would love to know how many countless others on HN, like me, find themselves reading about a very [sic] they have built and have been using for months talked about like it's a revolutionary new idea."
>
> — fractorial, [HN comment](https://news.ycombinator.com/item?id=48429380), 2026-06-06

> "Why should something like this make it to the front page?"
>
> — docheinestages, [HN comment](https://news.ycombinator.com/item?id=48429939), 2026-06-06

> "I went through the whole readme first and kept wondering what problem the system aims to address. I understood that it is a distributed issue tracker. But how can that lead to a memory upgrade? ... So is the issue the format or lack of structure which a local database can bring in?"
>
> — wowamit (on Steve Yegge's Beads), [HN comment](https://news.ycombinator.com/item?id=46077589), 2025-11-28

> "Cool stuff. The readme is pretty lengthy so it was a little hard to identify what is the core problem this tool is aiming to solve and how is it tackling it differently than the present solutions."
>
> — iddan (on Beads), [HN comment](https://news.ycombinator.com/item?id=46076877), 2025-11-28

> "I don't understand the point of this project. We already have github/gitlab for tasks, and if you want to query the history of a chat just stuff the spans in otel."
>
> — CuriouslyC (on Beads), [HN comment](https://news.ycombinator.com/item?id=46078569), 2025-11-28

**What this means for a concept candidate:** a new memory product's *first* burden is proving it isn't the 401st MCP memory server. Differentiation has to be legible in the first paragraph of any pitch, not buried in an architecture doc — the three Beads-thread complaints above (wowamit, iddan, CuriouslyC) are specifically about a well-regarded, high-profile tool (111 points, Steve Yegge's name) still failing that test for a chunk of readers.

---

## Pattern 3 — Memory poisoning, drift, and staleness: the audit/trust gap

Matches the "review/audit of stored memory" theme flagged in `config/scope.md`. Multiple independent commenters describe the same failure mode — stale or wrong memories re-surfacing and silently corrupting agent behavior — and note that diagnosing it is hard.

> "Does anyone else not use memory? I find once there is one poisoned line of text it negatively affects everything else downstream. Instead, I use a temp/ folder with documents and use different files for different agents and models. Then I have to constantly prune and delete the files. Any information that can be extrapolated is just noise which negatively affects the agent... it is noise, will drift, and be impossible to debug why the agent keeps producing undesired behavior."
>
> — dataviz1000, [HN comment](https://news.ycombinator.com/item?id=49509345), 2026-08-31

> "'Irrelevant material is simply never surfaced by the semantic search.' thats quite optimistic. there's lots of 'memory' or past chats with agents that should be suppressed and forgotten because they were looking in the wrong place or were eventually proven wrong. yet semantically they'd look very relevant to a future search."
>
> — Avijit_Thawani, [HN comment](https://news.ycombinator.com/item?id=49510885), 2026-08-31

> "My main gripe so far is that I have to push the agent to maintain an organized graph... The kg has some design limits that make poisoning hard to diagnose, so that's still on the list to fix."
>
> — r14c, [HN comment](https://news.ycombinator.com/item?id=49522604), 2026-09-01

> "The most important thing for a memory system is not only remember and recall or search, it is maintain, that including, merge, forget, update etc. That is how human's work."
>
> — linggen (maker of linggen.dev), [HN comment](https://news.ycombinator.com/item?id=49512811), 2026-08-31

> "I think eventually you need some kind of system that ranks pieces of data based on how useful they are. I.e. for the web we did that with link count etc. We need some other mechanism for judging and ranking pieces of 'memory' for 'agents'"
>
> — pianopatrick, [HN comment](https://news.ycombinator.com/item?id=49511573), 2026-08-31

**What this means for a concept candidate:** storing memory is the easy 20%; the requested capability nobody ships yet is *lifecycle management* — merge, forget, rank-by-usefulness, and (critically) a way to tell *why* the agent is behaving badly when a bad memory is the cause. An audit/diff/provenance layer on top of an existing memory store is a more defensible wedge than yet another storage backend.

---

## Pattern 4 — CLAUDE.md/AGENTS.md instruction rot: manual toil that ages badly across model upgrades

A dedicated HN thread (28 comments) formed around a blog post making exactly this argument; independent commenters corroborate the core claim even while disagreeing about the right response.

> "I'm fairly sure a meaningful percentage of my system prompt is now actively making things worse—instructions written for a model that no longer exists, aggressively steering a smarter one away from things it would have gotten right on its own. But I can't tell which lines those are, because to find out I'd have to delete one and see if anything bad happens, and that's how you get force-pushed to main."
>
> — Alex Jacobs, [I Am Morally Opposed to Updating My CLAUDE.md](https://alex-jacobs.com/posts/claudemd/), 2026-08-19

> "The file becomes a grievance archive... A system prompt you maintain over time is a diary. A very specific kind of diary, where every entry is a thing that hurt you."
>
> — Alex Jacobs, [same article](https://alex-jacobs.com/posts/claudemd/), 2026-08-19

> "If we're writing a document that polices the model behavior, we're altering every version of the model+harness we use henceforth. Most things I wrote in a Claude.md because Opus 4.something was crap had roots in Opus, Claude Code, system prompts, and our own bad code we wrote last year. None of these things exist today but the Claude.md file can stick around like it's all still necessary. I too am morally opposed--I abide by a 100 line (short lines, not paragraphs) limit and edit it rarely."
>
> — JasonSage, [HN comment](https://news.ycombinator.com/item?id=49379041), 2026-08-20

> "We are talking about a thing that's consumed all human knowledge whose superpowers are summarization and understanding. Why the fuck are we writing all these .md files!? I resent everything about this. But I write the .md files because others on the team are not as good at Just Talking To It. The md files are there because juniors don't know what to ask for."
>
> — Swizec, [HN comment](https://news.ycombinator.com/item?id=49376419), 2026-08-20

**What this means for a concept candidate:** there's demand for something that treats instruction files like code that can go stale — versioned, model-scoped, with a way to see which rules are still earning their keep — rather than a flat append-only file. Swizec's last line also surfaces a segment split worth noting: solo/expert users increasingly resent maintaining these files at all, while team settings still need them for less-fluent teammates.

---

## Pattern 5 — Doubt that memory/instruction files are actually obeyed, and no way to test that they are

> "It's not a surprise that you don't expect the rules there to be followed even with less than 100 lines of changes. Yet still see folks like Karpathy post rules around with hundreds of stars."
>
> — david_d8912 (Ask HN OP), [HN post text](https://news.ycombinator.com/item?id=48160604), 2026-05-16

> "Behavior rules (rules regarding comments, naming, dos and don'ts)... such rules work if they are concise, specific, and based on an already existing failure pattern. They don't work if created in anticipation of some failure that didn't happen yet. Rules made from scratch are usually not followed."
>
> — luodaint, [HN comment](https://news.ycombinator.com/item?id=48169873), 2026-05-17

> "AGENTS.md is extremely important - it's probably the highest leverage thing you can give your agent... For example, if I write a bad AGENTS.md for a repo with 100 engineers actively working in it, then every agent for every engineer gets worse, without anyone really noticing. I think we should move towards data-based tuning of AGENTS.md, testing out changes, gathering data, and then making a decision on whether or not to ship it."
>
> — bisonbear, [HN comment](https://news.ycombinator.com/item?id=48160918), 2026-05-16

**What this means for a concept candidate:** bisonbear names the gap directly — there's no eval loop for instruction/memory-file changes, so teams are flying blind on whether a given line helps, hurts, or does nothing (echoing Pattern 4's "I can't tell which lines those are"). A lightweight before/after regression harness for memory-file edits is a concrete, narrow, buildable wedge distinct from a full memory-storage product.

---

## Pattern 6 — Token/cost overhead of memory and instruction files, contested

Maps to the "cost of storage/retrieval" theme in `config/scope.md`. Real complaint, but the thread itself shows the fix is contested — compression has real detractors.

> "I found the memory system files compressed more aggressively than the project CLAUDE.md because they had more prose decoration. Anyone tried a middle ground where you keep human-readable formatting in the project file but compress the memory files only?"
>
> — jchilcher (article author, in his own comments), [HN comment](https://news.ycombinator.com/item?id=47146186), 2026-02-25

> "It looks like Claude tokenizer handles markdown pretty efficiently, like a ## header is like 1-2 tokens. So I think the actual token savings are smaller than the byte-level numbers suggest. Where this actually matters is if maybe if you're on Haiku with big codebases where system prompt and context fight for space. On Opus? Probably not worth making your files unreadable for humans?"
>
> — mihneadevries, [HN comment](https://news.ycombinator.com/item?id=47148280), 2026-02-25

> "I've found that maintaining minimal structural cues (typically a single repeating heading) in Claude.md helps it better index information. And anecdotally a lot less GREP'ing, web searching and wasted tokens."
>
> — F7F7F7, [HN comment](https://news.ycombinator.com/item?id=47146844), 2026-02-25

> "The lazy loading is key — the agent never reads the database directly. It makes tool calls and gets back only what it asked for. A typical session start costs ~20 tokens for the L1 overview. Drilling into one specific topic costs ~80 tokens. Compare that to a MEMORY.md that injects 3000-8000 tokens wholesale every time."
>
> — Bumblebiber (maker), [HN comment](https://news.ycombinator.com/item?id=47103263), 2026-02-21

**What this means for a concept candidate:** naive "compress the markdown" is disputed (mihneadevries' pushback on byte-savings-vs-token-savings is a real technical objection, not just noise). What has stronger, undisputed support is *retrieval-scoped* loading (Bumblebiber's ~20-80 token drill-down vs. 3000-8000 token wholesale injection) — the win is in not loading everything every turn, not in shrinking what's loaded.

---

## Pattern 7 — A recurring "just use files/grep, don't overbuild this" pushback against structured memory systems

Every ambitious memory architecture proposal in this run drew at least one comment arguing the plain filesystem (or a good static instruction file) already does the job, and that added structure is complexity without proven payoff.

> "This seems way too complicated and unnecessary. Agents are perfectly capable of discovering memories on the FS, following agent instructions. I guess this adds indexing and querying but most coding agents have good solutions for this already, and it works automagically for everything, not just memories. What we could use instead is a file system layout standard, which could subsume memories and a lot more."
>
> — avaer, [HN comment](https://news.ycombinator.com/item?id=48429028), 2026-06-06

> "How about just a memory dir in your project's git folder? Agents can run grep just fine."
>
> — bryanlarsen, [HN comment](https://news.ycombinator.com/item?id=48429539), 2026-06-06

> "But in my experience having a really good AGENTS.md file almost always produce better results than enabling memory."
>
> — nzach, [HN comment](https://news.ycombinator.com/item?id=49510061), 2026-08-31

> "I don't think I feel a need for more agent memory... I do like how it's exposed and searchable, and not invisible. But I honestly just send my questions/tasks away to my magic agent and eventually it gets it right anyway; do I need more discrete memory my team has to maintain? (That's an earnest question, not disregard for this)"
>
> — ltsSmitty, [HN comment](https://news.ycombinator.com/item?id=49508790), 2026-08-31

**What this means for a concept candidate:** any pitch for a *new storage layer* has to clear a high bar against "why not just grep a folder" — this objection appeared, unprompted, against nearly every memory-system Show HN sampled in this run. A wedge built on top of the existing filesystem/git substrate (indexing, audit, lifecycle — see Pattern 3) is more likely to survive this objection than a new database or protocol.

---

## Cross-cutting note on quote provenance

Several strong quotes in Patterns 1, 2, 3, and 6 come from people who are also the maker of a competing tool, describing the pain that motivated their build rather than venting as an unaffiliated user. This is disclosed inline at each such quote (e.g. Bumblebiber, edihasaj, simplyjosh56, linggen). It does not invalidate the pattern — multiple independent makers converging on the same complaint from different codebases and different tools is itself a form of corroboration — but it is a different evidentiary weight than an aggrieved end-user complaint, and any concept candidate built on these patterns should seek independent (non-maker) validation before treating them as proven market pain. Pattern 1 now also carries one genuine non-maker source (arapkuliev, an Ask HN OP) corroborating the same pain without a product to sell.
