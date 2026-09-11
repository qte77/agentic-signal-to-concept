# Name input — copy this file to name.md and fill in

`name-brand-vetter` needs exactly one of the two concept-input options below, not both.

## Option A — this repo's own candidate

- **Candidate file** (path under `candidates/`, e.g. `candidates/2026-09-04T070136Z-agent-memory-candidate.md`):

## Option B — standalone concept (not from this repo's own pipeline)

Fill this in instead of Option A when vetting names for a concept that didn't come from this
pipeline's own `candidates/` output — e.g. an external project's own concept description. **If the
concept belongs to an external project of the user's (see the standing sfclarity/sfsanity boundary),
do not paste that project's own files or private docs into this section — a plain-language concept
description is enough for the vetter to work from.**

- **Concept, one paragraph** (what it does, who it's for):
- **Category / competitive space** (for the category-clash check):
- **Slug** (used in `names/<date-time-iso>-<slug>-names.md`, kebab-case):

## Constraints (optional)

- **Markets/languages that matter for the Travel check** (default: English-speaking, no other market
  named):
- **TLDs that matter for the Clear/PR-launch sweep** (default: `.com` for the cheap filter; `.io`,
  `.ai`, `.dev` added at the PR-launch-sweep stage — see `name-brand-vetter.md`):
- **Trademark classes/categories to check, if known** (optional — the vetter infers a reasonable
  default from the concept if left blank):

## Output ownership (required — see the standing sfclarity/sfsanity boundary)

- **This repo's own concept?** yes/no. If no (the concept is sfclarity's, sfsanity's, or another
  external project's), the run's output must **not** be committed to this repo's tracked `names/` or
  `examples/` — the owner relocates it manually. State that explicitly here so the vetter doesn't
  default to this repo's normal archive path.
