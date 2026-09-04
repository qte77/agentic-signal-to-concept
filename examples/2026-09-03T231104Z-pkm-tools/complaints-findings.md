# PKM tools — Complaints findings

## §Research

Scope per `config/scope.md`: recurring complaints about existing note-taking/PKM tools (Notion,
Obsidian, Evernote, Roam Research, Logseq, Joplin, etc.), not a single product. This run follows up
on the pilot smoke-test (`examples/pkm-tools-pilot/findings.md`), which established the pipeline
mechanics with three patterns from four HN queries. This pass deliberately searched different
query terms and different tools (Roam Research, Logseq, Joplin — not just Notion/Obsidian/Evernote)
to surface independent evidence rather than re-mining the same threads.

### Source coverage

- **Hacker News (Algolia comment search)** — reachable, queried via `polyfetch fetch <url>
  --show-body` (`hn.algolia.com/api/v1/search`, `tags=comment`), i.e. the raw JSON API response, no
  summarizing/paraphrase-risk substitute. Nine queries run this pass: eight genuinely **found**
  results used below ("Roam Research frustrating", "Logseq slow" (34 hits), "second brain never
  revisit", "Obsidian plugin broke", "hoard notes links struggle", "Notion search terrible", "Notion
  search useless", "Obsidian search sucks"), and one, "notes search find frustrating", that returned
  results but all noise (unrelated photography-forum comments matching on "find"/"frustrating") and
  contributed nothing. Quotes below are drawn from authors and threads **not** used in the prior
  pilot run, per the run instructions to find new independent evidence where possible; no quote here
  duplicates one already in the pilot file.
  For the one story that looked most promising for a full-thread walk — the Ask HN thread anchoring
  Pattern 3 below (`story_id=46826277`) — the story itself was fetched via the official Firebase API
  (`hacker-news.firebaseio.com/v0/item/46826277.json`, through `polyfetch`), which returned
  `"descendants":217` and a 76-entry top-level `kids` array; the full 217-comment tree was **not**
  walked. Instead, the query used to actually pull quotes was Algolia's story-scoped search
  `tags=comment,story_46826277` combined with `query=hoard`, which returned the 38 comments
  containing the word "hoard" — a subset of the 76 top-level replies, not the full thread — and
  that subset is what Pattern 3's quotes below are drawn from. Worth flagging for transparency: that
  Ask HN story was itself posted by a founder ("item007") soft-validating a product idea in the
  space, not a neutral prompt — the two quotes drawn from it below (nicbou, Barrin92) are other
  users' organic, first-person answers about their own note-hoarding behavior, not manufactured, but
  the thread's origin is disclosed here per the claim-verification rule against under-stating
  context.
- **ProductHunt** — **blocked** (owner-gated). Confirmed via a direct environment check
  (`'PRODUCTHUNT_API_TOKEN' in os.environ` → `False`) that no token is configured in this
  environment; see `.env.example` for what a token would need. Recorded as blocked, not as absence
  of signal — identical status to the pilot run.
- **Reddit** — **excluded-by-scope**, not attempted. Per `docs/plans/0001-concept.md` and
  `complaint-miner.md`: unauthenticated scraping is confirmed fingerprint-blocked across three
  independent tools on a sibling repo's real run, and the OAuth API's terms/limits are unverified.
  This is a deliberate v1 exclusion, not a network failure or a gap to fill improvisationally.

All quotes below are copied verbatim from each comment's `comment_text` field in the raw Algolia
JSON (HTML entities decoded, `<p>`/`<em>` tags stripped for readability; no paraphrasing).

---

### Pattern 1 — PKM tools are perceived as slow regardless of vendor, not just Notion

The pilot's Pattern 1 found this complaint specifically about Notion/Confluence. This pass found
the same complaint, independently, aimed at Roam Research and Logseq — and one commenter states
outright that the whole product category ("Obsidian or Logseq are not different") scales poorly.

> "Last couple years I've been on roam research for note taking. But takes more than 8 seconds to
> load for me. So slow I'm diy mission to make my own solution."
> — keizo, comment on ["Fast Software, the Best Software
> (2019)"](https://news.ycombinator.com/item?id=33587096), 13 Nov 2022

> "Because it's an awesome concept with a well-rounded implementation on the user side. It just
> sucks hard on the technical side. I mean, it's a good tool, but it has an upper ceiling of what
> one should do with it. But this is a general problem with all those young fancy tools. Obsidian
> or Logseq are not different in that regard. They all are scaling poor. They are simply not meant
> for this."
> — PurpleRamen, comment on ["Obsidian 1.5 Desktop
> (Public)"](https://news.ycombinator.com/item?id=38777537), 27 Dec 2023

> "I also use Logseq for quick daily notes, since I like that it has the infinite vertical view & I
> want these notes open in a different program. But it has some quirks and tells that make it feel
> like lower quality software compared to Obsidian, e.g. its startup time is horribly slow."
> — bryanhogan, comment on ["I built a faster Notion in
> Rust"](https://news.ycombinator.com/item?id=46056289), 26 Nov 2025

**What this means for a candidate:** the pilot's finding generalizes — this isn't a Notion problem,
it's a category-level pattern. Three independent users, three different tools (Roam, "the whole
category" generically, Logseq), describe the same felt-performance complaint. A candidate
addressing "PKM tool complaints" broadly should treat startup/load latency as a cross-vendor bar to
clear, not a Notion-specific differentiator.

---

### Pattern 2 — Capturing notes is easy; finding them again is the actual unsolved problem

> "Been using Notion for over 2 years now, and unfortunately I must say I am not going to be using
> them moving forward. You end up with a soup of documents each with their own structure, search is
> terrible, creating is easy sure - but finding the right content sucks."
> — sergiotapia, comment on ["Notion Acquires
> Automate.io"](https://news.ycombinator.com/item?id=28459099), 8 Sep 2021

> "Yes, I can't believe how few good complete solutions there are for this that don't involve me
> curating anything. Currently I have obsidian notes for different libs and technologies and really
> useful stuff or things I plan on reading, they go there but search of the page itself is
> non-existent."
> — alan_n, comment on ["Ask HN: How do you save and browse external interesting
> URLs?"](https://news.ycombinator.com/item?id=34752101), 11 Feb 2023

**What this means for a candidate:** two independent users, describing different tools (Notion;
Obsidian) but the identical asymmetry — writing in is frictionless, getting information back out is
not. This is a retrieval problem, not a capture problem; a
candidate's differentiation should not be "easier note-taking" (a solved problem per these users)
but "actually findable later."

---

### Pattern 3 — Note-hoarding in a PKM tool doesn't reliably convert into thinking or retrieval value

> "...using obsidian for 6 months as second brain among other things, i don't believe it has really
> helped me think other than as being a place where I also create more polished notes where I must
> crystallize ideas. What it has done is enable my idea hoarding/belief there's gold in them there
> old thoughts, so I spend too much time checking if I already had a thought rather than just
> thinking."
> — chermi, comment on ["It's so hard to finish an idea that is not yours and is just suggested by
> AI"](https://news.ycombinator.com/item?id=49456277), 26 Aug 2026

> "At some point, organisation can become a form of procrastination. Building a second brain is not
> Doing The Thing. A note is not an intention. It commits to memory, not to action. I really don't
> care about having a whole searchable, tagged database; I hardly ever look at those notes again."
> — nicbou, comment on ['Ask HN: Do you also "hoard" notes/links but struggle to turn them into
> actions?'](https://news.ycombinator.com/item?id=46831349), 30 Jan 2026

> "Frankly all I have is a TODO.org file and I delete everything that's older than a month because I
> wasn't going to deal with it anyway... Once a year I delete everything if I can't remember why I
> took it, most of what survives seem to be book reviews. I don't use any AI stuff, just grep. All
> the second brain stuff just sounds like digital hoarding."
> — Barrin92, comment on ['Ask HN: Do you also "hoard" notes/links but struggle to turn them into
> actions?'](https://news.ycombinator.com/item?id=46831999), 31 Jan 2026

**What this means for a candidate:** three independent users, across different tools and even
different philosophies (Obsidian as "second brain"; a plain-text TODO.org file; no tool at all as
the implied fix), converge on the same underlying complaint: organizing/storing notes is being
mistaken for progress, and the material rarely gets used again once captured. One explicitly named
the mechanism ("organisation can become a form of procrastination... a note is not an intention"),
one is blunter ("all the second brain stuff just sounds like digital hoarding"). A dedicated Ask HN
thread existing on exactly this question is itself a signal the pain point is recognized broadly
enough to warrant its own discussion, beyond just these three quoted answers — though see Source
coverage above for that thread's own founder-pitch origin.

---

### Pattern 4 — Obsidian's community plugin ecosystem trades core functionality for unvetted risk

The pilot's Pattern 2 covered Obsidian sync/versioning data loss. This is a related but distinct
risk surface: the plugin ecosystem itself, which real incidents and reviewers describe as
structurally under-vetted.

> "The first safety warning assures the user that 'plugin security is important to [Obsidian]'...
> Given that vaults are just Markdown documents, and plugins are so safe (or so Obsidian seems to
> claim), why should a person feel at all concerned clicking yes to these prompts?... The design of
> Obsidian plugins was clearly broken from the beginning, and the official messaging around them has
> always been more encouraging than wary. This sort of event is an absolutely inevitable consequence
> of those decisions."
> — troad, comment on ["Obsidian plugin was abused to deploy a remote access
> trojan"](https://news.ycombinator.com/item?id=48096495), 11 May 2026

> "...the popular Templater plugin gets a 92 rating, with Excellent Health and Satisfactory review. But
> the disclosures are pretty concerning: dynamic code execution, network calls, wasm blobs, malware
> scan not available, etc. I know it's tricky to boil this down to a single numerical score that
> works for everyone, but I think the bar needs to be higher than this."
> — guiambros, comment on ["The Future of Obsidian
> Plugins"](https://news.ycombinator.com/item?id=48117151), 13 May 2026

> "...Obsidian has really a huge amount of plugins for data-handling. At some point, it was so bad that
> there were multiple competing task-plugins which broke each other just because they had different
> formatting for dates."
> — PurpleRamen, comment on ["A structured note-taking app for personal
> use"](https://news.ycombinator.com/item?id=38733210), 22 Dec 2023

**What this means for a candidate:** three independent users describe two distinct failure modes of
the same root cause — an ecosystem of third-party code with weak review standards that users rely
on for core functionality. One is a security incident (malware via a plugin), one is a specific
plugin's concerning permission profile despite a high trust score, one is functional (plugins
silently breaking each other). A candidate that positions itself around extensibility needs a real
answer to "how do you vet what runs against my data" — "there's a large plugin ecosystem" is, per
these users, part of the complaint, not a selling point.

---

### Pattern 5 — PKM power users chronically migrate between tools without settling

> "I also went back to Org-mode some years ago... I loved what Logseq had to offer, and setup
> monthly donations for a while, but after the long period of apparent stagnation I lost faith and
> jumped ship. It's sad because Logseq felt like a more focused tool than either Emacs or Obsidian,
> and really nailed the UI/UX that I wanted."
> — setopt, comment on ["Logseq 2.0 Beta (DB version) is
> here"](https://news.ycombinator.com/item?id=48898981), 13 Jul 2026

> "Switched to Obsidian for faster startup time, which is at the top of my feature list for such
> apps. Joplin got worse over time with more notes. I considered Roam and Notion, but having to pay
> AND slow startup made no sense, although Notion features are quite nice. Now thinking about adding
> Logseq to work with my Obsidian."
> — repple, comment on ["Obsidian 1.5 Desktop
> (Public)"](https://news.ycombinator.com/item?id=38775725), 26 Dec 2023

> "After Logseq moved to an app focus and abandoned the 'edit anywhere' convenience of being
> browser-based, I lost interest. I liked the original Logseq/Roam/Athens model, and I couldn't find
> anything similar elsewhere, so I just wrote my own... I'm sorry to have said goodbye to what used
> to be a great community project, but they've been following the classic enshittification model,
> albeit slowly, for a while now."
> — flkiwi, comment on ["Logseq 2.0 Beta (DB version) is
> here"](https://news.ycombinator.com/item?id=48898192), 13 Jul 2026

**What this means for a candidate:** three independent users (one crossing threads/years from the
other two) describe a pattern of repeated tool migration — Joplin→Obsidian, Logseq→Org-mode,
Logseq→self-built — each citing a different proximate trigger (price+speed, stagnation, a
platform-model change) but the same underlying behavior: none of these users found a tool that
stuck. One user's response was to abandon the market entirely and write a personal replacement. A
candidate should treat "will this still be worth using in two years" as a real, previously-broken
promise for this audience, not a hypothetical objection — and should note that at least one
committed user's bar was low enough that they'd rather self-build than switch to another existing
product.

---

**Caveat on all quotes above:** fetched via `polyfetch fetch --show-body` against the Algolia
search API directly — raw JSON, no summarizing/paraphrase-risk substitute (per
`complaint-miner.md`'s "Fetch tooling" section). Quotes are copied verbatim from each hit's
`comment_text` field with HTML entities decoded and `<p>`/`<em>` tags stripped; nothing was rephrased
or invented.
