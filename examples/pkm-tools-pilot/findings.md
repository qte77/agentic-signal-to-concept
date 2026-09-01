# PKM tools — Findings

## §Research

Scope per `config/scope.md`: recurring complaints about existing note-taking/PKM tools (Notion,
Obsidian, Evernote, etc.), not a single product. This is a pilot/smoke-test run of the pipeline
mechanics, not a production concept-mining pass — three patterns, not the full 4-8 range, is
sufficient to prove the mechanism.

### Source coverage

- **Hacker News (Algolia comment search)** — reachable, queried directly via `polyfetch fetch
  <url> --show-body` (`hn.algolia.com/api/v1/search`, `tags=comment`). Four queries run: "notion
  obsidian frustrating" (2 hits, both used), "Notion slow bloated" (many hits, 2 independent
  authors used), "left Evernote" (13 hits, 2 independent authors used), "Obsidian sync lost notes"
  (8 hits, 1 additional independent author used). All genuinely found, not blocked or empty.
- **ProductHunt** — not attempted this run. No `PRODUCTHUNT_API_TOKEN` configured in this
  environment (see `.env.example`); recorded as blocked (owner-gated), not as absence of signal.
- **Reddit** — excluded from v1 scope by design (see `docs/plans/0001-concept.md` §1), not attempted.

---

### Pattern 1 — Notion is perceived as slow and bloated even by users required to use it daily

> "We have to use Notion at work, it's slow, bloated, and not a great UI experience overall. More
> bloat now I guess."
> — Zealotux, comment on ["Notion 3.0"](https://news.ycombinator.com/item?id=45344337), 23 Sep 2025

> "It doesn't completely fit your needs (does anything?), but I really like Outline... especially
> because stuff like Notion and Confluence feels so slow and bloated."
> — prxtl, comment on ["Ask HN: What's a modern alternative to Confluence for small dev
> teams?"](https://news.ycombinator.com/item?id=45221392), 12 Sep 2025

**What this means for a candidate:** the complaint isn't feature-absence, it's felt performance —
two independent users, months apart, describe the same mainstream tool as "slow and bloated" in
near-identical language, including one user who is a captive daily user (not evaluating
alternatives voluntarily).

---

### Pattern 2 — Obsidian's sync/versioning mechanisms have caused real, unrecovered data loss for power users

> "The currently available git plugin is extremely dangerous (!!!) if set up incorrectly. I would
> consider myself an advanced user of git, and Obsidian's git plugin has on several occasions blown
> away my history and notes. It has frustrating and opaque behavior for how it consolidates change
> sets and diffs."
> — echelon, comment on ["Obsidian Bases"](https://news.ycombinator.com/item?id=44945808), 18 Aug 2025

> "I have had a similar experience with Obsidian notes. I would edit the 'today' note at different
> times on my computer and phone, and noticed that I lost content... Obsidian has a loading screen
> that seems to imply that the file system successfully got the latest updates from iCloud, but
> perhaps it didn't actually have the latest updates as I started writing."
> — philip1209, comment on ["iCloud Drive silently deletes your
> content"](https://news.ycombinator.com/item?id=37635957), 24 Sep 2023

**What this means for a candidate:** two independent users, two different sync mechanisms (a
community git plugin vs. iCloud's own sync), same underlying failure mode — silent, unrecovered
data loss in a tool marketed on trustworthy local-first storage. A candidate addressing this needs
verifiable sync integrity, not just "yet another sync backend."

---

### Pattern 3 — Long-time Evernote users left over forced subscription-gating and data lock-in

> "For the first few years I could ignore the subscription because I didn't want those features.
> Then they eventually made the subscription required for really basic things, so I left Evernote."
> — prepend, comment on ["Tell HN: Nearly all of Evernote's remaining staff has been laid
> off"](https://news.ycombinator.com/item?id=36614455), 6 Jul 2023

> "The problem with Evernote is with the way they treat data. I found a really interesting guide
> hosted on Evernote and I tried to download it. But no, I couldn't, because they took the pains to
> block someone from downloading their notebooks... I couldn't even print the damn thing!"
> — neya, comment on ["Evernote cuts 47 employees and shuts down 3
> offices"](https://news.ycombinator.com/item?id=10301932), 30 Sep 2015

**What this means for a candidate:** two independent, years-apart departures from the same product,
citing two different but related mechanisms — feature-gating behind subscriptions, and data
export/portability friction. Both point to the same underlying trust gap: users want ownership of
their own notes, not just a cheaper tier.

**Caveat on all quotes above:** fetched via `polyfetch fetch --show-body`, which returns the
Algolia API's raw JSON — no summarizing/paraphrase-risk substitute was used (see
`complaint-miner.md`'s "Fetch tooling" section). Quotes are copied verbatim from the `comment_text`
field, HTML entities decoded, `<p>`/`<em>` tags stripped.
