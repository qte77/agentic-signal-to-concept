# names/

Per-run output of `name-brand-vetter`: `<date-time-iso>-<slug>-names.md`, a taxonomy-scored candidate
name shortlist plus a PR-launch-sweep result for the 2-3 finalists that survive it.

`names/*.md` is gitignored by default, same treatment as `findings/*-findings.md` and
`discovery/*.md` — working evidence for a run, not this repo's tracked product output by default. A
naming run worth keeping permanently gets archived under `examples/<date-time-iso>-<slug>-names/`,
matching the convention used for every other run type (see `examples/README.md`) — **but only when the
concept is this repo's own**. A run vetting names for an external concept (sfclarity, sfsanity, or any
other project not owned by this repo) must never be archived here or anywhere else in this repo's
tracked output — see `config/name.example.md`'s Output ownership section and the standing
sfclarity/sfsanity boundary.

## Expected structure

- **Input** — which concept was vetted (this repo's `candidates/*.md` path, or a standalone concept
  description) and its output-ownership declaration.
- **Tier 1 — generated candidates, cheap filter** — one row per candidate name: Build pattern used
  (compound/suffix/metaphor), Say/Picture/Travel/Market-easy judgment notes, category-clash + `.com`
  fast-check result, pass/cut.
- **Tier 2 — PR-launch sweep, finalists only (2-3 names)** — one subsection per finalist: web search,
  trademark-database results (sources checked named explicitly), domain age (RDAP), TLD spread,
  GitHub, social handles (or "not automatable, owner step" where that applies).
- **The finalist** — the name selected, with the reasoning tying back to "the one you can't stop
  saying, clear to hold, fluent to spread," and every hedge from the sweep still attached (never
  silently dropped — see `claim-verification.md`'s hedge-decay failure mode).
- **Not legal advice** — a screening pass, not legal clearance; repeated in every output file, not
  just this README.

See `.claude/agents/name-brand-vetter.md` for the full spec.
