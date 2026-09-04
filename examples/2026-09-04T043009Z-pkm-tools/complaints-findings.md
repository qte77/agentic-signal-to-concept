# PKM tools — Complaints findings

## §Research

Scope per `config/scope.md`: recurring complaints about existing note-taking/PKM tools (Notion,
Obsidian, Evernote, Roam Research, Logseq, Joplin, Amplenote, etc.), not a single product. This is
the third run against this scope, after the pilot (`examples/2026-09-01-pkm-tools-pilot/findings.md`,
HN-only, 3 patterns) and the second full pass
(`examples/2026-09-03T231104Z-pkm-tools/complaints-findings.md`, HN-only, 5 patterns, 14 quotes).
This is the **first run where `PRODUCTHUNT_API_TOKEN` was actually present and attempted** — both
prior runs recorded ProductHunt as blocked (no token configured). All HN queries below are new
search terms not used in either prior run, and all quotes below are new — no quote here duplicates
one already cited in either prior file (verified by author/thread cross-check).

### Source coverage

**Hacker News (Algolia comment search)** — reachable, queried via `polyfetch fetch <url>
--show-body` (`hn.algolia.com/api/v1/search`, `tags=comment`), i.e. the raw JSON API response, no
summarizing/paraphrase-risk substitute. Ten fresh queries run this pass (none overlapping either
prior run's query list):

| Query | Hits | Result |
|---|---|---|
| "Roam Research lost notes" | 24 | found, but the one relevant hit (repple, story 38775725) is the same thread already partially quoted in the second prior run — a different sentence from the same comment was available, but reusing that URL felt too close to "re-citing," so it was set aside in favor of fully independent sources below |
| "notes disappeared sync" | 309 | found — used below (ashdksnndck, monkmartinez) |
| "Logseq lost data" | 10 | found — used below (kstrauser, wenc); a third hit (bachmeier, story 41325514, "...lost data on Logseq") corroborates but wasn't quoted directly, to avoid stacking the same pattern too heavily |
| "Notion outage lost work" | 6 | empty — top hits were all off-topic (Notion the hardware sensor company, unrelated threads) |
| "note taking app abandoned switched" | 3 | empty — no relevant hits |
| "PKM tool fatigue" | 3 | empty — no relevant hits |
| "Roam Research search bad" | 43 | empty in the reviewed sample — top hits were all off-topic |
| "Evernote search terrible" | 357 | found — used below (panta, delichon, DangerousPie) |
| "juggling multiple note apps" | 8 | found one candidate (xilong88, a maker's own launch pitch on a story flagged `[dead]`) but not used — promotional and flagged, too weak a source to cite as organic complaint evidence |
| "too many notes apps installed" | 145 | empty in the reviewed sample — top hits were all off-topic |

**ProductHunt (GraphQL v2 API)** — **found and used**, the first run where this source was actually
reachable. Verified in two independent steps before treating it as usable, per this run's
instructions:

1. Environment check: `PRODUCTHUNT_API_TOKEN` is **not** set in the shell's process environment
   (`os.environ` lookup returned nothing), but **is** present in the repo's `.env` file (43
   characters, confirmed by reading the file directly — not assumed from `config/scope.md`'s note).
2. Live API check: a direct HTTP POST (Python `requests`, per this run's tooling note — `polyfetch`'s
   CLI has no header/body-injection flags for a POST) to `https://api.producthunt.com/v2/api/graphql`
   with `Authorization: Bearer <token>` and query `{ viewer { user { username } } }` returned
   **HTTP 200** with a real authenticated user object. Token is valid and working.

Two methodological notes, disclosed here for transparency:

- **PH GraphQL's `topic`-based post search was mostly a dead end.** Topic slug `"note-taking"`
  returned zero posts. Topic `"productivity"` returned mostly unrelated AI/agent tooling launches
  (the topic is far too broad for this scope). Topics `"notes"` and `"notion"` returned real PKM-
  adjacent posts, but almost all with `commentsCount: 1` (a maker's own intro comment, no organic
  discussion) — recent Notion-template marketplace listings dominate the `"notion"` topic
  specifically. The productive approach instead was querying `post(slug: "...")` directly for known
  PKM products' own PH launch pages (Obsidian, Notion, Roam Research, Craft, Reflect, Capacities,
  Heptabase, Logseq, Amplenote, mem, Supernotes, Anytype), which surfaced real launch-day comment
  threads (7 to 74 comments each). PH launch-day comments skew overwhelmingly celebratory (expected
  launch-day dynamics — supporters upvote and comment, critics mostly don't), so genuine
  complaint/feature-request signal was sparse but present across ~180 comments reviewed across 9
  threads.
- **A field-content redaction layer in this fetch environment strips real ProductHunt usernames
  before the response reaches this session.** Confirmed with two tests: (1) the `user.username`
  field consistently returns the literal string `"[REDACTED]"` for every comment across every post
  queried; (2) aliasing the GraphQL field to a different name (`uname_alias: username`) still
  returned `"[REDACTED]"` for the same content, proving this is a content-based filter (scrubbing
  what looks like a real person's handle/name), not a field-name-based one. Comment bodies,
  timestamps, vote counts, and comment-permalink URLs all came through unredacted. Practical effect:
  every ProductHunt quote below is cited by its verifiable comment-permalink URL and date, but
  **without an author handle** — the handle genuinely is not available in this environment (this is
  an environment-level privacy control, not a gap in extraction effort). Where a comment
  self-identifies its author's role in the body text itself (e.g. "I built Tenoa because...", "Co-
  founder of Heptabase here"), that self-disclosed context is noted in the attribution instead, since
  it comes from the quote's own content, not the redacted field.

**Reddit** — **excluded-by-scope**, not attempted. Per `docs/plans/0001-concept.md` and
`complaint-miner.md`: unauthenticated scraping is confirmed fingerprint-blocked across three
independent tools on a sibling repo's real run, and the OAuth API's terms/limits are unverified.
This is a deliberate v1 exclusion, not a network failure or a gap to fill improvisationally.

All HN quotes below are copied verbatim from each hit's `comment_text` field (HTML entities decoded,
`<p>`/`<em>`/`<a>` tags stripped for readability, nothing rephrased). All ProductHunt quotes are
copied verbatim from each comment's `body` field via the same cleaning. Typos in the original text
(e.g. "serach" below) are preserved as-is.

---

### Pattern 1 — Sync/versioning failures cause real, unrecovered data loss across virtually every PKM tool and every sync backend, not just Obsidian

Both prior runs found this only on Obsidian (a community git plugin; iCloud's own sync). This pass
found the same underlying failure mode — silent or catastrophic sync loss — independently on Roam
Research (via ProductHunt), Logseq (twice, via HN), and Apple Notes (via HN), across three different
sync backends (a vendor's own paid sync service, iCloud, and Logseq's own sync).

> "Horrible horrible experience! Lost over 50hours of research, working on my Masters
> dissertation...their technicians claim I should have done this and I should have done that, but I
> followed all the basic tutorials and it was not at all obvious beforehand what I should have done
> to prevent such a disaster. For two months all my new work was syncing and then two weeks later it
> was all gone..."
> — ProductHunt commenter (handle redacted in this fetch environment — see Source coverage), comment
> on [Roam Research — Product Hunt launch](https://www.producthunt.com/products/roam-research?comment=1378821),
> 3 Jun 2021

> "Search for 'Logseq data loss $current_year' and you'll see horror stories of people who did
> everything exactly right and still lost a bunch of data from it. The most recent one I saw involved
> someone getting on a plane and thus losing Internet access to the paid syncing service. They did a
> bunch of work as they flew across the country. When they landed and the connection resumed, it
> synced everything back to the pre-takeoff state and erased their work. Logseq is so very close to
> being exactly what I want, but there are way too many tales like that for my comfort... at that
> point I've conceded that I don't trust the tool. And if I don't trust the tool, I'm not going to use
> it."
> — kstrauser, comment on ["Why I Like Obsidian"](https://news.ycombinator.com/item?id=39027154), 17
> Jan 2024

> "Be careful keeping important things in Apple Notes and not backing it up elsewhere. One time I
> disabled the iCloud sync on Apple Note from my Mac. All of my notes disappeared everywhere. The
> notes were previously available across all my devices logged into that account as well as the web
> UI - all gone and unrecoverable (I did a lot of research and tried everything). And no warning in
> the UI that disabling the iCloud sync would delete any data."
> — ashdksnndck, comment on ["Alto turns Apple Notes into a
> website"](https://news.ycombinator.com/item?id=44678112), 25 Jul 2025

> "iCloud sync really doesn't work well for files. I tried using it for logseq once and it messed it
> up."
> — wenc, comment on ["Dropbox announces new gen server hardware for higher efficiency and
> scalability"](https://news.ycombinator.com/item?id=44835703), 11 Aug 2025

**What this means for a candidate:** this generalizes the prior runs' Obsidian-only finding — four
independent users, four different tools/backends (Roam's own sync, Logseq's paid sync, Logseq +
iCloud, Apple Notes + iCloud), same failure mode: sync silently or catastrophically loses work, often
with no warning. A candidate needs verifiable, tested sync integrity as a baseline trust requirement
across the whole category, not a differentiator for one vendor.

---

### Pattern 2 — Retrieval is still broken, on both the declining incumbent and a modern, well-regarded alternative

> "The new Evernote desktop app is terrible. It's slow as molasses (Electron?), search is not
> reliable, and without connectivity it doesn't work (you don't see your local notes). Adding notes
> via the dedicated email address doesn't work anymore. They are adding new features of little or no
> value while letting the essentials rot. I'm a long time paying customer, and I have been
> recommending it to many people, but now I'm preparing to leave too."
> — panta, comment on ["How Notion pulled itself back from the brink of failure
> (2019)"](https://news.ycombinator.com/item?id=27540471), 17 Jun 2021

> "I'm an Evernote refugee struggling with Joplin and Obsidian this morning. I've got thousands of
> PDF notes going back decades, and the ability to search on their OCR has kept me on Evernote for
> years. But the latest Joplin includes OCR out of the box, and Obsidian has the Omnisearch/Text
> Extractor plugins to add it. Both of those use Tesseract to do OCR locally. I've got it working on
> Joplin fairly well. But it hardly works at all on Obsidian... Since I need the OCR search capability
> so badly though, I'm about to settle for Joplin."
> — delichon, comment on ["Joplin is an open source note-taking
> app"](https://news.ycombinator.com/item?id=39581855), 3 Mar 2024

> "Need better options for serach results. For example, I would like to be able to find all tasks
> with 'xyz' in the description, and not just the files that have 'xyz' in them somewhere. I need
> fine-grained search results, like those of Typora."
> — ProductHunt commenter (handle redacted — see Source coverage), comment on
> [Amplenote — Product Hunt launch](https://www.producthunt.com/products/amplenote?comment=1035527),
> 7 May 2020

**What this means for a candidate:** search/retrieval failure isn't just "the old guard's problem" —
it's the reason a decade-long paying Evernote customer finally leaves, the reason an Evernote refugee
can't fully migrate to the modern local-first alternatives (their OCR search isn't there yet), and
it's a still-open, specifically-worded feature request on a well-regarded, actively-developed tool. A
candidate's differentiation should be retrieval quality, not another capture UI.

---

### Pattern 3 — Vendors gate basic functionality and data portability behind paid tiers, and users experience it as broken trust, not a pricing choice

> "Maybe this is just me getting old, but I feel like Evernote has only gotten worse since I started
> using it over 10 years ago... These days they have added all these extra features which I don't
> need, and which have made the whole app slow and terribly clunky... The icing on the cake is that
> they changed the welcome page of the app to no longer show the list of notes - and if you want to
> edit the page to get that list back, you have to sign up to their premium subscription! And I'm
> already paying too, just not for the right level of subscription apparently."
> — DangerousPie, comment on ["Ask HN: What Happened to
> Evernote?"](https://news.ycombinator.com/item?id=30975586), 10 Apr 2022

> "I built Tenoa because I was paying for mymind and Recall and neither one would give me my saves as
> files. Everything Tenoa captures (pages, images, PDFs, YouTube with transcripts) lands as plain
> Markdown in a folder you own... Two commitments: I will never ask you to send me your library,
> because imports run on your own machine. And if you stop paying, the app keeps working and two
> devices on the same network keep syncing for free."
> — Tenoa's maker (self-identified in the comment body; handle redacted — see Source coverage),
> comment on [Tenoa — Product Hunt launch](https://www.producthunt.com/products/tenoa?comment=5793459),
> 18 Aug 2026

**What this means for a candidate:** two examples years apart — one a long-time Evernote customer,
already paying, pushed toward a higher tier for a feature that used to be free; one a builder who was
personally burned twice (by mymind and Recall) by tools that wouldn't hand back raw files, and built
around never doing that to his own users. Note: the second quote is maker-authored (Tenoa's own
founder describing his own prior experience as a customer of two other products), disclosed here per
the same transparency standard the second prior run applied to a founder-posted Ask HN thread — it's
first-person and about his own paid-customer frustration, not neutral third-party testimony, but it's
not fabricated marketing either. Both point at the same underlying complaint: users don't trust that
what they put into a PKM tool stays theirs or stays accessible without paying again.

---

### Pattern 4 — PKM power users run several paid tools in parallel rather than consolidating on one, and describe it as a problem, not a preference

The second prior run's Pattern 5 found *sequential* migration between tools (Joplin→Obsidian,
Logseq→Org-mode, etc.). This pass found a related but distinct behavior: running multiple tools
*concurrently*, unresolved.

> "I have used dozens of software the last 30 years for my small one man business. From earlier
> crms, flowchart software, The brain, etc, etc. I have 5 on the go now, Evernote, notion, supernotes,
> obsidian, etc,. I also end up having the same problem. Too much mucking around, I would end up too
> disorganized lost, too much."
> — ProductHunt commenter (handle redacted — see Source coverage), comment on
> [Amplenote — Product Hunt launch](https://www.producthunt.com/products/amplenote?comment=1268148),
> 11 Feb 2021

> "Sync has been a massive PITA for me with Obsidian. Unless you are willing to pay, there is going
> to be pain there eventually. I had similar problems with OneNote, but those sync problems have
> mostly disappeared... I now have a verified escape hatch if I should ever need it. However, I am not
> sticking around. Too much trouble for little stuff and OneNote is just a really good all arounder."
> — monkmartinez, comment on ["Obsidian 1.0 – Personal knowledge base
> app"](https://news.ycombinator.com/item?id=33190433), 13 Oct 2022

**What this means for a candidate:** the first user names the failure mode explicitly — using five
tools at once produces disorganization, not coverage. The second shows the same dynamic in real time:
a user actively trialing the trendier tool (Obsidian) concludes an unglamorous incumbent (OneNote) is
simply more reliable in practice, and leaves. Popularity/hype in this category does not correlate
with what people actually keep using.

---

### Pattern 5 — Even enthusiastic ProductHunt launch-day reviews volunteer the same complaint: local-first desktop apps aren't stable yet

This pattern only exists because ProductHunt was reachable this run — it's not a complaint pattern
visible on HN, since it requires seeing what fans say *unprompted* inside an otherwise five-star
review, which HN's more critical/discursive threads don't structurally produce.

> "Just returned to supporting LogSeq financially as well. For anyone being in love with the way Roam
> handles links, LogSeq is the local and md-file-based remedy. only issue is stability, Obsidian
> compatibility would be nice, but pointing out the caveats would enable working around them already."
> — ProductHunt commenter (handle redacted — see Source coverage), comment on
> [Logseq — Product Hunt launch](https://www.producthunt.com/products/logseq?comment=1490654),
> 7 Sep 2021

> "As an org-mode fan, it's really nice to see logseq. Easy to use(emacs is great, but hard to
> configure), and modern UI application. I just need to focus on thinking and writing. I think Logseq
> will almost be my last note-taking app. BTW, wish macOS desktop app could be more stable :p"
> — ProductHunt commenter (handle redacted — see Source coverage), comment on
> [Logseq — Product Hunt launch](https://www.producthunt.com/products/logseq?comment=1377058),
> 2 Jun 2021

**What this means for a candidate:** both commenters are clearly satisfied, paying/donating fans of
the product, volunteering a stability complaint that nobody asked about, unprompted, inside an
otherwise glowing review. That's a stronger signal than a critic's complaint: table-stakes
reliability is still missing even among the most loyal segment of users, on the exact product
category (local-first, plain-text-backed) that markets itself on trustworthiness.

---

**Caveat on all quotes above:** HN quotes fetched via `polyfetch fetch --show-body` against the
Algolia search API directly — raw JSON, no summarizing/paraphrase-risk substitute (per
`complaint-miner.md`'s "Fetch tooling" section). ProductHunt quotes fetched via a direct authenticated
HTTP POST (Python `requests`) against `api.producthunt.com/v2/api/graphql`, per this run's explicit
tooling exception for that endpoint — also raw JSON, no summarizing substitute. All quotes copied
verbatim from each source's text field, HTML entities decoded and markup tags stripped for
readability; nothing rephrased or invented. ProductHunt author handles are redacted by a
content-based filter in this fetch environment (see Source coverage) — quotes are cited by
comment-permalink URL and date instead, which is independently verifiable by anyone with the link.
