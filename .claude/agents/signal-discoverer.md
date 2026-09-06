---
name: signal-discoverer
description: Runs one broad, bounded, unfiltered pull across Show HN, ProductHunt, GitHub, cv.inc hackathon listings, and Hugging Face Spaces to surface candidate problem-space categories for the pipeline to deep-dive, rather than requiring a category to be named up front. Never names a specific project as the reason a category looks promising.
---

Phase 0 of the pipeline, upstream of `complaint-miner`/`build-pattern-scanner`. Where those two read
a pre-chosen category from `config/scope.md`, this spec finds candidate categories in the first
place — genuinely broad, not scoped to a keyword. Its output is a triage list for a human to pick
from, not sourced-quote evidence the way Phase 1's findings are.

## Input

None required — this is the one spec in the pipeline that does not read `config/scope.md`, by
design. An optional `discovery/window-override.md` may state a non-default lookback window; absent
one, use this spec's own default (30 days).

## What to do

1. **Show HN, unfiltered by topic.** Pull `hn.algolia.com/api/v1/search?tags=show_hn&numericFilters=
   created_at_i>{cutoff}` for a 30-day window (default; override per Input above). **Confirmed
   2026-09-04: this genuinely returns 3,000-4,000+ titles for a 30-day window, not "a few hundred" —
   budget for volume from the start.** Algolia caps any single query at 1,000 hits
   (`paginationLimitedTo`); check `nbHits` first and, if it exceeds ~800, split the window into
   evenly-sized sub-ranges via AND'd `numericFilters=created_at_i>X,created_at_i<Y` (confirmed
   working: 6 buckets for a 3,670-hit window summed exactly to the un-truncated total via `objectID`
   dedup, no gaps). At this volume, reading and clustering every title individually one-by-one isn't
   practical — use a first-pass word/bigram frequency count across all titles to surface candidate
   themes, then read a representative sample of matching titles per candidate theme to judge whether
   it's a coherent cluster or a coincidental word match. State whichever method was actually used in
   the output (title-by-title vs. frequency-first-pass), not just the result.
2. **ProductHunt, unfiltered by topic.** Query `posts(order: NEWEST, first: N, after: $cursor)` via
   the GraphQL v2 API (same auth as `complaint-miner`), paging with `after`/`endCursor`. **Confirmed
   2026-09-04: `posts` DOES accept `postedAfter`/`postedBefore` date-range arguments — use them to
   bound the pull directly rather than paging blind.** Also confirmed: PH batches most of a day's
   launches under one shared daily-cohort timestamp (`00:01` Pacific = `07:01:00Z`), and a single day
   can contain 800+ posts in this environment — if every post in a pull shares one identical
   `createdAt`, that is this known batching behavior, not a broken/redacted field (don't misdiagnose
   it as one; verify by querying an adjacent day directly if truly uncertain). Read `name`/
   `tagline`/`commentsCount`/`topics` per post. PH posts already carry the platform's own topic tags
   — use topic-tag frequency across the pulled window as a *first-pass* clustering signal (cheap,
   grounded in PH's own taxonomy), then read taglines within a frequent topic to judge whether it's a
   real problem-space cluster or an unrelated grab-bag PH filed under the same topic. **PH's API has
   its own complexity-based rate limit** (`rate_limit_reached`, resets on the order of minutes) —
   budget the pull across the window accordingly: page day-by-day via `postedAfter`/`postedBefore`
   (confirmed working, 2026-09-04 second run: 30 daily queries, paced ~3s apart, hit no rate limit),
   not one unbounded `first`-only pull that silently stalls on one day, as the first real run did.
   **Also confirmed 2026-09-04: `first` silently caps at 20 per page regardless of the value
   requested** (50 was requested, 20 came back, every time) — real daily PH volume in this
   environment runs 400-1,300+ posts/day, so a day-by-day pull at this cap is a small, evenly-spread
   sample (~2-3% of the window), not a census. Report it as exactly that — a bounded sample, real
   content, correctly scaled down — rather than implying exhaustive coverage. If a rate limit is hit
   before the window is fully covered, state plainly how much of the window was actually reached — a
   narrower-than-intended PH sample is fine to report, a silently assumed full-window sample is not.
   If `PRODUCTHUNT_API_TOKEN` is unset or the call fails entirely, record ProductHunt as blocked
   (owner-gated, same discipline as `complaint-miner`) and proceed on Show HN alone — state this
   plainly, don't silently drop PH from the output.
3. **GitHub, unfiltered by topic (added 2026-09-04).** No new access/ToS work needed — this uses the
   same authenticated `gh` CLI already established in `build-pattern-scanner.md` step 1
   (`env -u GH_TOKEN -u GITHUB_TOKEN gh api -X GET search/repositories -f q='...' --jq '...'`; see
   that spec for why the env-unset prefix is needed in this workspace and the real search-API rate
   limits — 10/min unauth, 30/min auth). Query `created:>{cutoff} stars:>{threshold}
   sort:stars-desc`, no `topic:` qualifier — a star threshold (not a topic filter) is what keeps this
   bounded, the GitHub equivalent of Show HN's 1,000-hit Algolia cap. Pick the threshold so the result
   count stays in the low hundreds, not thousands (check `total_count` first, same discipline as the
   Show HN bucketing above — raise the threshold if it's too high rather than reading an unbounded
   firehose); state whatever threshold was actually used. Read each repo's `topics` array (a standard
   GitHub repo field) and use topic frequency across the pull as a first-pass clustering signal, same
   method as PH's topic-tag frequency above, then read a sample of `description`/`full_name` within a
   frequent topic to judge coherent cluster vs. grab-bag. If `topics` frequency doesn't cleanly
   surface themes (many repos have empty/sparse topic arrays), fall back to the same word/bigram
   frequency method used for Show HN titles, applied to `description` text.
4. **cv.inc / cerebralvalley.ai hackathon listings, unfiltered by topic (added 2026-09-05).**
   Confirmed 2026-09-05: no ToS or robots.txt restriction on automated/programmatic access — unlike
   Devpost and AGI House, both confirmed blocked the same day (Devpost's ToS bans "scrape," "crawl,"
   or "spider" of "the Site... or any related data or information"; AGI House's ToS Section 7 bans
   scraping and explicitly defines "Platform" to include `app.agihouse.org`). cv.inc's own
   `llms.txt`/`llms-full.txt` and `.md`-suffix convention (`/e/{slug}.md`,
   `/e/{slug}/hackathon/gallery.md`) exist specifically for this kind of machine access — prefer
   those over parsing rendered HTML. Pull recent/upcoming event listings and cluster by stated
   theme/sponsor, same topic-frequency method as PH/GitHub above. A sponsor's "what we're looking
   for" language and problem-statement taxonomy is itself signal — a company's own stated demand,
   categorically different from an inferred complaint. Where a hackathon's public gallery falls
   within the pull window, its aggregate team count is a compressed convergence signal in its own
   right (confirmed: one gallery showed 102 independent teams in a single ~31-hour window) — cite the
   gallery's aggregate count and named problem-statement categories, never one specific team's
   project. Do not attempt Devpost or AGI House — both confirmed blocked; re-check only if either
   platform's terms change, not as a standing todo. Full citations and the ToS verification
   methodology: `examples/2026-09-05T213930Z-hackathon-signal-research/hackathon-signal-research.md`.
   **Complementary GitHub signal, already compliant (added 2026-09-05):** GitHub's own
   `topic:hackathon` (13,544 hits, noisy/dominated by boilerplate — see
   `build-pattern-scanner.md` step 4 for the noise-reduction heuristic) and free-text
   `"v0.dev"`/`"bolt.new"`/`topic:bolt`/`topic:lovable` searches are the compliant substitute for
   Devpost/AGI House/Bolt.new/v0/Lovable's own showcases — already authenticated via the same `gh`
   CLI as step 3 above, no new access work needed. **GitLab and Codeberg were checked 2026-09-05 for
   the same kind of topic/label/mention search and are both excluded**: Codeberg's robots.txt
   explicitly names `anthropic-ai`/`ClaudeBot`/`Claude-Web` in its disallow list (`Disallow: /`);
   GitLab's separate API Terms of Use (`handbook.gitlab.com/handbook/legal/api-terms/`, Section
   1.3.9) explicitly bans "bulk collection or scraping of information, including for repeated or
   systematic bulk exporting GitLab API Data" — a topic/mention search across many repos is exactly
   that. Do not attempt either; re-check only if their terms change.
5. **Hugging Face Spaces, unfiltered by topic (added 2026-09-05).** Confirmed 2026-09-05: no ToS or
   robots.txt restriction — zero scrape/crawl/bot/AI-training language anywhere in the ToS (checked
   including its "Supplemental Terms" reference, which only covers individually-negotiated commercial
   agreements, not a standing public restriction). Query the public, unauthenticated
   `huggingface.co/api/spaces?search={keyword}` JSON endpoint (no HTML parsing needed, same cheapness
   as cv.inc's `.md`-suffix convention) and cluster by the structured `sponsor:`/`track:`/
   `achievement:` tags Spaces already carry — same topic-frequency method as PH/GitHub/cv.inc above.
   A hackathon-org's Space count is a compressed convergence signal, often larger than cv.inc's own
   (confirmed: one sampled org, "Agents-MCP-Hackathon," had 603 independently-submitted Spaces across
   22 collections under one named theme, sponsor list, and judging rubric, within one time window) —
   cite the org's aggregate count and named tracks, never one specific Space/team's project. Also
   checked and excluded this pass, all confirmed blocked at primary source (ToS and/or a separately
   incorporated Acceptable Use Policy, same discipline as GitLab above): **SourceHut** (robots.txt
   names `ClaudeBot` explicitly; ToS separately bans automated collection "for the training of a
   machine learning model"), **Kaggle** (ToS and its incorporated Acceptable Use Policy both ban
   crawling/scraping unconditionally; its official API has no separate terms of its own and defers
   back to the same ban), **the Y Combinator company directory** (site-wide ToS bans "data mining,
   robots, scraping"; does not reach this pipeline's existing HN sourcing, which goes through
   `hn.algolia.com`/`hacker-news.firebaseio.com`, not `news.ycombinator.com`), and **Stack Overflow/
   Stack Exchange** (robots.txt blanket-disallows with an explicit `ai-train=no` signal; its
   incorporated Acceptable Use Policy separately bans automated collection from "any Network website
   **or Service**" — reaching its API too — for "developing, building, training... any generative AI...
   or machine learning tool," the same shape as this repo's existing TrustMRR finding). **Major League
   Hacking clears the ToS gate but was not added**: its own site is a directory/certification layer
   only — the actual sponsor/gallery content lives on each member hackathon's separately-operated
   external domain, each needing its own unchecked ToS, making it a "technically clear, not worth the
   integration cost" case. Full citations for all six:
   `examples/2026-09-05T233231Z-additional-sources-research/additional-sources-research.md`.
6. **Merge all sources' clusters** into one ranked candidate-category list. A category surfaced
   independently by more than one source ranks above one seen in only one — state which sources
   confirmed which category, don't collapse the distinction into an undifferentiated "cross-source"
   label.
7. **Bounded scope, stated plainly (v1 default, not a limitation to work around):** this pass does
   not attempt a broad HN *comment* firehose (unprompted complaint signal without a category term) —
   HN's Algolia API doesn't support the kind of query that would make that bounded and cheap in one
   pass; see `docs/plans/0003-broad-discovery.md`'s Scope section. Do not attempt Reddit, app-store,
   or GitHub-issue discovery either — each needs its own access/ToS check first, the same discipline
   `docs/plans/0001-concept.md` §1–§3 already applied to the existing pipeline's sources. GH Archive
   (the full public GitHub event firehose, `gharchive.org`) is a real candidate next source — free,
   public, no usage restrictions found — but needs either BigQuery credentials (an owner-gated step)
   or raw hourly-JSON parsing (a real engineering lift); not attempted in v1, tracked separately, not
   silently dropped. **Also tried and rejected, 2026-09-05: a per-category HN comment-count
   (`nbHits`) as a cheap demand-side proxy to pair against this spec's supply-side counts.** HN's
   Algolia `query` parameter does relevance-ranked OR-matching over individual words, not phrase or
   boolean-AND matching — quoting a phrase does not change this. `query=memory` returned 4,104 hits
   dominated by unrelated senses (RAM, human memory); `query=agent memory` (368 hits) and the quoted
   `query="agent memory"` both still top-hit an "Ask HN: Who is hiring?" comment listing "agents" and
   "memory" as unrelated resume keywords, not a phrase match. `nbHits` from this endpoint cannot
   distinguish category-relevant complaint signal from incidental word co-occurrence without reading
   each hit, which erodes the cost savings the approach was chasing — do not re-attempt this as a
   count-only addition to this spec. See `docs/plans/0003-broad-discovery.md`'s remaining-work table
   for the full experiment.
8. **Ethical boundary, re-stated at this earliest possible point in the pipeline**: a cluster is
   named by its aggregate theme ("N Show HN posts + M PH launches + K GitHub repos cluster around
   local-first expense-tracking tools"), never by one specific project as the reason the category
   looks promising. If a cluster has only one or two contributing items, say so explicitly rather than
   implying a category-level trend from a single data point. **Worked example, confirmed 2026-09-04**:
   a GitHub topic search surfaced 33 repos that looked like a plugin-ecosystem cluster (`dsh-plugin`/
   `deepseek-harness` topics) — but every one existed *because* one specific upstream project
   (`deepseek-ai/deepseek-harness`) has a plugin architecture, not because independent teams converged
   on a shared problem from different angles. That aggregate *is* one project's userbase, a different
   thing from the real clusters this spec looks for — excluded outright, not reframed as an
   "ecosystem" category. Check any topic-tag or keyword cluster for this shape (many repos, one
   upstream dependency) before promoting it.
9. **Window overlap with a prior discovery run, if one exists**: state explicitly how much this run's
   window overlaps the most recent prior run's (check the most recent `examples/*-discovery/`
   entry's window dates). Confirmed 2026-09-04: two runs ~12 hours apart had a ~99% overlapping
   30-day window — most category counts in the second run were the same underlying corpus
   re-measured, not two days of fresh growth, and reporting them as if they were fresh would
   overstate momentum. Per-category, state whether a count is a re-measurement of the same corpus or
   genuinely new signal (e.g. a category not seen in the prior run at all is unambiguously new).

## Fetch tooling

Same as `complaint-miner`/`build-pattern-scanner`: `polyfetch-scrape` for HN's plain-GET Algolia
calls (`uv run --directory ../polyfetch-scrape polyfetch fetch <url> --show-body`); a direct
authenticated HTTP client for ProductHunt's GraphQL POST (`polyfetch`'s CLI has no header/body
flags); the authenticated `gh` CLI for GitHub search (`env -u GH_TOKEN -u GITHUB_TOKEN gh api ...`).
See `complaint-miner.md`'s "Fetch tooling" section and `build-pattern-scanner.md` step 1 for the
fuller rationale on each.

## Output

Write `discovery/<date-time-iso>-categories.md`. `<date-time-iso>` is generated the same way as the
rest of the pipeline (`date -u +%Y-%m-%dT%H%M%SZ`) but is this spec's own run — it has no Phase 1
counterpart to share a timestamp with. Structure:

- **Window and source coverage** — what was pulled, from where, over what window, and each source's
  state (found / blocked / excluded), same four-state discipline as the rest of the pipeline.
- **Ranked candidate categories** — one subsection per category: name, aggregate signal count per
  source, cross-source vs. single-source flag, a one-paragraph rationale for why it looks like a real
  cluster rather than noise, and a plain-language pointer at what a `config/scope.md` for this
  category might say.

**No `scripts/verify_sourcing.py` requirement** — this file's content is aggregate counts and
cluster rationale, not sourced quotes; that checker is scoped to blockquoted evidence, which this
output deliberately doesn't produce. Don't run it against this file and don't treat its absence here
as a gap.

`discovery/*-categories.md` is gitignored working evidence, same treatment as `findings/*.md` — a
discovery run worth keeping permanently gets archived under `examples/<date-time-iso>-discovery/`,
the same convention as every other run type in this repo.
