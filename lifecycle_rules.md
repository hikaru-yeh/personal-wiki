# Lifecycle Rules

This file defines metadata and lifecycle rules for Shane Wiki v2. Use it together with `AGENTS.md`, `privacy_sanitize_rules.md`, and `course_project_ingest_rules.md`.

## Scope

These rules apply to metadata normalization, linting, stale-state decisions, supersession, and archiving for `Wiki_Pages/**/*.md`.

During the first application pass, only add metadata. Do not rewrite body content, delete content, move files, or modify `Raw_Sources/`.

## Standard Frontmatter

Every wiki page should use YAML frontmatter. Recommended fields:

```yaml
---
type: profile | person | event | analysis | summary | course | project | index | log
status: active | stale | superseded | archived
sensitivity: public | internal | private | sensitive
pii_categories: []
created: YYYY-MM-DD
last_updated: YYYY-MM-DD
last_confirmed: YYYY-MM-DD
confidence: 0.7
sources_count: 1
decay_profile: transient | fact | durable
last_privacy_review: YYYY-MM-DD
review_required: false
supersedes: []
superseded_by: []
related_pages: []
---
```

## Field Semantics

- `type`: the page's primary purpose. If uncertain, use the closest generic type, such as `summary` or `analysis`.
- `status`: the page's current lifecycle state.
- `sensitivity`: determined according to `privacy_sanitize_rules.md`.
- `pii_categories`: mark only categories; do not put actual PII in metadata.
- `created`: the date the page was first created; if uncertain, use the earliest log date or `null`.
- `last_updated`: the date body content was last updated; do not change it to today for a metadata-only trial run.
- `last_confirmed`: the date the page's main knowledge was last confirmed.
- `confidence`: confidence score from 0.0 to 1.0.
- `sources_count`: estimated number of sources supporting the page's main content.
- `decay_profile`: decay type used for stale-state decisions.
- `last_privacy_review`: most recent privacy/sensitivity metadata review date.
- `review_required`: set to `true` when human review is needed, especially for `sensitive` pages.
- `supersedes`: older pages or claims replaced by this page.
- `superseded_by`: newer pages or claims that replace this page.
- `related_pages`: main cross-references, using Obsidian wikilinks.

## Status Rules

- `active`: currently valid and usable as a main query source.
- `stale`: may be outdated and needs reconfirmation.
- `superseded`: replaced by a newer page or claim, but retained for historical context.
- `archived`: retained but not used as a primary query source.

## Confidence Rules

- `0.9-1.0`: highly certain; supported by multiple sources or recently confirmed explicitly.
- `0.7-0.8`: reliable, but still context-dependent or based on a small number of sources.
- `0.5-0.6`: preliminary organization, single source, or needs human confirmation.
- `<0.5`: low confidence; must set `review_required: true`.

During first-pass metadata normalization, if source count or freshness cannot be confirmed, use `confidence: 0.6` and set `review_required: true` when appropriate.

## Decay Profiles

- `transient`: short-term information, such as temporary emotions, current state, one-off events, or short-term application status.
- `fact`: ordinary facts, such as resumes, person information, job-search records, or social-relationship summaries.
- `durable`: long-term stable knowledge, such as values, skills, course notes, architecture decisions, or index structure.

## Stale Rules

During lint or lifecycle review, use `last_confirmed` and `decay_profile` to decide whether content should be marked `stale`:

- `transient`: can be marked `stale` after 30-90 days without confirmation.
- `fact`: can be marked `stale` after 180-365 days without confirmation.
- `durable`: should only be marked `stale` after 365-730 days without confirmation.

If a `sensitive` page has not been reviewed for a long time, it may remain `active` while also setting `review_required: true`.

## Supersession Rules

When new information replaces old information, do not delete the old page.

Old page:

```yaml
status: superseded
superseded_by:
  - "[[new_page]]"
```

New page:

```yaml
supersedes:
  - "[[old_page]]"
```

If only a single paragraph or claim is replaced, rather than the whole page, mark the supersession relationship in the body first. Set the whole page to `superseded` only when the main content of the entire page is no longer valid.

## Archive Rules

Do not delete content merely because it has low confidence, is outdated, has been replaced, or is rarely used.

Suggested archive location:

```text
Wiki_Pages/_archive/
```

Archive principles:

- Set `status: archived`.
- Preserve original page content.
- Move it out of the main index or lower its priority in the index.
- Record archive operations in `Wiki_Pages/log.md`.
- Do not archive or modify `Raw_Sources/`.

## Path Defaults

Unless page content clearly requires higher sensitivity:

- `Wiki_Pages/courses/**`: `sensitivity: public`, `decay_profile: durable`
- `Wiki_Pages/people/**`: `sensitivity: private`, usually `decay_profile: fact`
- `Wiki_Pages/self/**`: `sensitivity: private`, usually `decay_profile: durable`
- `Wiki_Pages/interview/**`: `sensitivity: private`, usually `decay_profile: fact`
- `Wiki_Pages/log.md`: `sensitivity: private` or `sensitive`
- content involving health, legal context, conflict, identity, contact information, credentials, or intimate conversations: `sensitivity: sensitive`, `review_required: true`

## First-Pass Metadata Policy

During first-pass metadata normalization:

- only modify frontmatter
- do not modify body content
- do not perform sanitizing replacements
- do not move or delete pages
- do not modify `Raw_Sources/`
- use conservative defaults for uncertain fields
- if uncertain whether content is sensitive, raise sensitivity and set `review_required: true`
