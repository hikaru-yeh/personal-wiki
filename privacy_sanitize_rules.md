# Privacy / Sanitization Rules

This file defines the privacy and sanitization rules the LLM Wiki must follow during ingest, query, lint, page updates, sensitivity tagging, and people-graph creation.

`AGENTS.md` is the primary operating specification. Whenever work involves PII, private content, sensitive content, sanitization, sensitivity metadata, people-graph privacy, or writing sensitive content, this file must also be followed.

## Core Principles

The purpose of sanitization is to reduce exposure risk, not to erase the knowledge structure.

Information that is valuable for self-understanding, the people graph, career events, decision context, or long-term pattern analysis should not be deleted by default. Control risk through sensitivity tagging, summarization, PII redaction, and user confirmation.

## Sanitization Check

Before writing any content derived from source material into `Wiki_Pages/`, run a sanitization check.

The sanitization check must include:

- identifying PII and sensitive-information categories
- determining the sensitivity level
- deciding which private or sensitive content has knowledge value worth writing to the wiki
- redacting unnecessary exact PII and summarizing sensitive details
- preserving non-verbatim information needed for the knowledge graph, such as person aliases, relationship types, event types, time ranges, source types, and confidence levels

## Content That Should Not Be Written Directly

Unless the user explicitly asks, do not write the following content to wiki pages in an untagged, unsummarized, verbatim, or overly detailed form:

- phone numbers
- email addresses
- full addresses
- national IDs, passport numbers, tax IDs, or bank details
- API keys, tokens, passwords, or login credentials
- long verbatim quotes from third-party private messages
- medical, mental-health, sexuality/sexual, conflict, legal, labor, or employment-dispute details

## High-Context Sensitive Content

High-context sensitive content, such as medical, mental-health, identity, conflict, legal, labor, or employment-dispute information, should not be deleted by default.

If this information is important for self-understanding, the people graph, career events, decision context, or long-term pattern analysis, it may be written to the wiki, but it must:

- be marked as `sensitive`
- set `review_required: true`
- prefer summaries over verbatim quotations
- redact unnecessary exact PII
- preserve event type, relationship type, time range, impact, source type, and confidence level
- avoid exposing identifiable third-party details unless that person already has an explicit wiki alias or person page

## Placeholders

When an exact value is not necessary, use these placeholders:

- `[PHONE_REDACTED]`
- `[EMAIL_REDACTED]`
- `[ADDRESS_REDACTED]`
- `[TOKEN_REDACTED]`
- `[PRIVATE_PERSON]`

## Sensitivity Metadata

Every newly created or updated wiki page must include sensitivity metadata.

Sensitivity levels:

- `public`: safe for broad reuse
- `internal`: personal but low risk
- `private`: personal, social, or career details
- `sensitive`: health, legal, conflict, identity, contact information, credentials, or intimate conversations

Recommended metadata fields:

```yaml
sensitivity: private
pii_categories: []
last_privacy_review: YYYY-MM-DD
review_required: false
```

For `sensitive` pages:

- prefer summaries over verbatim quotations
- cite source types rather than raw private content
- avoid copying third-party PII
- set `review_required: true`

## Sensitivity Approval Step

During ingest, if source content contains private or sensitive information that may be written to `Wiki_Pages/`, confirm with the user first.

When asking for confirmation, do not paste full sensitive source text. Summarize:

- pages expected to be written
- sensitivity level: `private` or `sensitive`
- sensitive categories involved, such as health, mental health, career conflict, legal context, intimate conversation, third-party people, or contact information
- knowledge value to preserve, such as relationship context, event context, career impact, or long-term patterns
- planned handling: keep in full, summarize, redact PII, or do not write

For each category of private or sensitive content, ask the user to choose:

- `write summary`: preserve knowledge value without verbatim detail
- `write detailed content`: preserve more complete content while still redacting PII and marking it sensitive
- `do not write`: do not place it in `Wiki_Pages`; only record in the log that it was skipped

Do not write the private or sensitive content to `Wiki_Pages/` before the user confirms.

If the user approves writing it:

- mark the correct `sensitivity`
- set `review_required: true` on `sensitive` pages
- prefer summarization over verbatim quotation
- redact unnecessary exact PII
- preserve structured information needed for the people graph and long-term analysis

## People Graph And Relationship Analysis

For people-graph and relationship analysis, preserve necessary structured information:

- people may use internal wiki aliases or existing person-page names, such as `[[person_x]]`
- preserve relationship types, such as friend, family, colleague, interview_contact, conflict_party, or support_network
- preserve event types and time ranges, such as "2025 workplace conflict" or "2026 interview preparation"
- preserve source types, such as LINE, Facebook, Instagram, Gmail, resumes, or interview materials
- preserve `confidence`, `sources_count`, and `last_confirmed`
- avoid preserving full original text, complete contact details, full addresses, account credentials, or identifiable third-party details

Do not remove important entities and relationships merely because of sanitization. If information is sensitive, raise the sensitivity level and set `review_required: true`.

## Default Sensitivity By Path

Unless the user explicitly says otherwise, apply these default sensitivity levels by page path:

- `Wiki_Pages/courses/**`: always `public`
- `Wiki_Pages/people/**`: default `private`
- `Wiki_Pages/self/**`: default `private`
- `Wiki_Pages/interview/**`: default `private`
- `Wiki_Pages/log.md`: default `private`
- pages involving health, legal context, conflict, identity, contact information, credentials, or intimate conversations: `sensitive`

Pages under `Wiki_Pages/courses/**` are always marked `public`; ordinary course-note content does not raise them to `private` or `sensitive`.

## Query Privacy Rules

When a query needs to use private or sensitive pages, follow these rules.

### General Queries

- Private pages may be read to answer user questions, but answers should avoid unnecessary PII or third-party private details.
- If a sensitive page is needed, first decide whether the user question clearly requires that content.
- If the question can be answered without sensitive pages, prefer not to read or cite them.
- Do not output phone numbers, email addresses, addresses, tokens, credentials, complete private conversations, or third-party PII.
- When citing sources, cite wiki page names. Do not expose sensitive `Raw_Sources/` filenames or raw private-message text.

### High-Context Tasks Explicitly Authorized By The User

If the user explicitly requests any of the following tasks, private or sensitive page details may be used:

- interview preparation
- STAR story organization
- resumes, cover letters, or job-search narratives
- self-understanding, psychological patterns, or life-event analysis
- people graphs or relationship analysis
- career events, conflict events, legal context, or labor/employment context

For these tasks, the agent may use detailed story context, but must still:

- avoid outputting phone numbers, email addresses, addresses, tokens, credentials, or full account information
- avoid long verbatim private conversations unless the user explicitly asks
- prefer rewriting sensitive content into interview-ready narratives, summaries, STAR structures, or anonymized versions
- preserve necessary event details, actions, conflicts, decisions, outcomes, and reflection
- refer to third parties using wiki aliases, role labels, or the minimum necessary identifying information
- proactively point out which details should be anonymized or removed if the material may be shared externally

## Existing Data Tagging Policy

When adding sensitivity metadata to existing `Wiki_Pages/` pages, the first pass should only tag metadata. Do not rewrite body content, delete content, or replace content with sanitized versions.

If a page already has frontmatter, preserve existing fields and only add missing fields.

If a page has no frontmatter, add YAML frontmatter at the top.

Each page must include at least:

- `type`
- `status`
- `sensitivity`
- `pii_categories`
- `last_privacy_review`
- `review_required`

After the first tagging pass, pages marked `sensitive` with `review_required: true` can go through manual review or a second sanitization pass.
