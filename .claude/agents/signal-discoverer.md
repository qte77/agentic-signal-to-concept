---
name: signal-discoverer
description: Runs one broad, bounded, unfiltered pull across Show HN, ProductHunt, and GitHub to surface candidate problem-space categories for the pipeline to deep-dive, rather than requiring a category to be named up front. Never names a specific project as the reason a category looks promising.
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
   budget the pull across the window accordingly (e.g. day-by-day via `postedAfter`/`postedBefore`)
   rather than one unbounded `first`-only pull that silently stalls on one day, as the first real run
   did. If a rate limit is hit before the window is fully covered, state plainly how much of the
   window was actually reached — a narrower-than-intended PH sample is fine to report, a silently
   assumed full-window sample is not. If `PRODUCTHUNT_API_TOKEN` is unset or the call fails entirely,
   record ProductHunt as blocked (owner-gated, same discipline as `complaint-miner`) and proceed on
   Show HN alone — state this plainly, don't silently drop PH from the output.
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
4. **Merge all sources' clusters** into one ranked candidate-category list. A category surfaced
   independently by more than one source ranks above one seen in only one — state which sources
   confirmed which category, don't collapse the distinction into an undifferentiated "cross-source"
   label.
5. **Bounded scope, stated plainly (v1 default, not a limitation to work around):** this pass does
   not attempt a broad HN *comment* firehose (unprompted complaint signal without a category term) —
   HN's Algolia API doesn't support the kind of query that would make that bounded and cheap in one
   pass; see `docs/plans/0003-broad-discovery.md`'s Scope section. Do not attempt Reddit, app-store,
   or GitHub-issue discovery either — each needs its own access/ToS check first, the same discipline
   `docs/plans/0001-concept.md` §1–§3 already applied to the existing pipeline's sources. GH Archive
   (the full public GitHub event firehose, `gharchive.org`) is a real candidate next source — free,
   public, no usage restrictions found — but needs either BigQuery credentials (an owner-gated step)
   or raw hourly-JSON parsing (a real engineering lift); not attempted in v1, tracked separately, not
   silently dropped.
6. **Ethical boundary, re-stated at this earliest possible point in the pipeline**: a cluster is
   named by its aggregate theme ("N Show HN posts + M PH launches + K GitHub repos cluster around
   local-first expense-tracking tools"), never by one specific project as the reason the category
   looks promising. If a cluster has only one or two contributing items, say so explicitly rather than
   implying a category-level trend from a single data point.

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
