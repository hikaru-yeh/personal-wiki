# CLAUDE.md

This repository is an LLM-maintained wiki.

Your job is to read source material placed by the user in `Raw_Sources/` and organize it into structured wiki pages under `Wiki_Pages/`.

## Repository Structure

```text
PROJECT_Shane Wiki/
|-- CLAUDE.md
|-- lifecycle_rules.md
|-- privacy_sanitize_rules.md
|-- Raw_Sources/
`-- Wiki_Pages/
    |-- index.md
    |-- log.md
    |-- courses/
    |   `-- course_index.md
    |-- cc_projects/
    |   |-- _project_template.md
    |   `-- project_index.md
    `-- *.md
```

## Project-Specific Overrides

- Architectural complexity in this project, including lifecycle metadata, the knowledge graph, and crystallization, is intentional. Do not suggest simplifying documented design decisions.
- Skills in this project may intentionally perform bulk transformations. The usual "surgical changes" rule does not apply when running a skill.
- For well-specified tasks with clear success criteria, proceed without asking for clarification.

## Priority Order

If multiple instruction files are present:

1. Follow `AGENTS.md` first.
2. Any operation involving PII, private content, sensitive content, sanitization, sensitivity metadata, privacy in the people graph, or writing sensitive content must also follow `privacy_sanitize_rules.md`.
3. Any operation involving wiki metadata, frontmatter, status, confidence, stale state, supersession, archiving, or lifecycle linting must also follow `lifecycle_rules.md`.

If rules conflict, follow the more specific rule for the content type.

## Sources Of Truth

- `Raw_Sources/` is the user-managed source pool.
- `Wiki_Pages/` is the LLM-managed knowledge base.
- Never modify files in `Raw_Sources/`.
- Only create, update, or maintain files inside `Wiki_Pages/`.

## Raw Source Rules

- Treat `Raw_Sources/` as read-only.
- Do not assume every file in `Raw_Sources/` is completely raw.
- `Raw_Sources/` may contain:
  - original source files
  - user-prepared source drafts
  - user-formatted course notes
  - user-written project wrap-up drafts

## Privacy / Sanitization Rules

All ingest, query, lint, page update, and sensitivity-tagging work must follow `privacy_sanitize_rules.md`.

When source content may contain PII, private information, or sensitive information, read and apply `privacy_sanitize_rules.md` before writing to `Wiki_Pages/`.

## Global Ignore Rules

By default, unless the user explicitly asks otherwise, do not ingest these files as knowledge pages:

- tool state files
- cache files
- temporary files
- hidden files
- pure template files
- pure index files

Examples:

- `.formatter_log.json`
- `_project_template.md`
- source-side helper index files

## Modes

### Ingest

Use ingest mode when the user wants to transform source material from `Raw_Sources/` into wiki pages.

Required workflow:

1. Read the specified source files.
2. Run a sanitization check according to `privacy_sanitize_rules.md`.
3. If private or sensitive content is expected to be written, run the Sensitivity Approval Step from `privacy_sanitize_rules.md` and wait for user confirmation.
4. Before editing `Wiki_Pages/`, propose an update plan listing:
   - pages to create
   - pages to modify
   - links and indexes to update
   - how private or sensitive content will be handled
5. Wait for user approval.
6. Create or update the relevant wiki pages.
7. Update cross-references when needed.
8. Append a log entry to `Wiki_Pages/log.md`:
   - `## [YYYY-MM-DD] ingest | <document name>`
9. When page creation or deletion changes indexes, update `Wiki_Pages/index.md`.

If ingest work involves identity resolution, cross-platform identity matching, nickname mapping, or name ambiguity, also follow this workflow:

1. Check whether `Wiki_Pages/people/disambiguation_queue.md` already contains similar candidates.
2. Do not automatically merge people based only on similar names, mutual friends, nearby locations, or overlapping platforms.
3. If identity is not confirmed, keep entities separate and record `possible_duplicate_of` in the queue or page metadata.
4. Only update `same_as`, merge identities, or rewrite canonical identity when evidence is strong or the user confirms it.
5. If disambiguation involves third-party identity recognition or cross-platform matching, still follow `privacy_sanitize_rules.md`.

### Update

Use update mode when the user gives new facts, supplements, clarifications, or long-term information directly in conversation and asks for it to be written to the wiki, but the source is not a file in `Raw_Sources/`.

Required workflow:

1. Decide whether the user-provided content is suitable for long-term storage.
2. Identify the `Wiki_Pages/` pages to create or update.
3. If PII, private content, or sensitive content is involved, follow `privacy_sanitize_rules.md`.
4. If metadata, confidence, `last_confirmed`, or lifecycle state is involved, follow `lifecycle_rules.md`.
5. Before editing, propose an update plan listing:
   - pages to modify or create
   - summary of new or corrected content
   - how private or sensitive content will be handled
   - whether indexes or cross-references need updates
6. Wait for user approval.
7. Update the relevant wiki pages.
8. If indexes are affected, update `Wiki_Pages/index.md` or section indexes.
9. Append to `Wiki_Pages/log.md`:
   - `## [YYYY-MM-DD] update | <summary>`

#### Shortcut For Creating Bridge Pages

Trigger phrases: "add project_X bridge page" or "create X bridge page".

This is a specialized update-mode workflow. The agent reads sources automatically; the user does not need to provide content.

- Source path read automatically:
  `D:\shane_yeh\Documents\_Claude_Code\knowledge-wiki\wiki-pages\project-management\projects\<name>.md`
- Template: `Wiki_Pages/cc_projects/_bridge_template.md`

### Correction

Use correction mode when the user says an existing wiki page contains wrong, outdated, inaccurate, retracted, or rewritten information.

Required workflow:

1. Identify the `Wiki_Pages/` pages and related cross-references that need correction.
2. Determine the basis for the correction:
   - direct user correction
   - source already present in `Raw_Sources/`
   - newly added source material
   - internal contradiction in the wiki
3. If PII, private content, or sensitive content is involved, follow `privacy_sanitize_rules.md`.
4. If metadata, status, confidence, `last_confirmed`, stale state, supersession, or archiving is involved, follow `lifecycle_rules.md`.
5. Before editing, propose a correction plan listing:
   - pages to modify
   - summary of incorrect content
   - summary of corrected content
   - whether metadata needs updates
   - whether indexes or cross-references need updates
6. Wait for user approval.
7. Modify the relevant wiki pages.
8. If the correction affects indexes, project indexes, or course indexes, update the corresponding indexes.
9. Append a log entry to `Wiki_Pages/log.md`:
   - `## [YYYY-MM-DD] correction | <page or issue summary>`

### Reorganization

Use reorganization mode when the user says existing wiki pages should be regrouped, moved, renamed, merged structurally, or re-indexed.

Required workflow:

1. Identify pages to move, rename, merge, or reorganize.
2. Propose a reorganization plan listing:
   - pages to move
   - old path -> new path
   - wikilinks to update
   - indexes or sub-indexes to update
   - whether metadata, sensitivity, or lifecycle state is affected
3. Wait for user approval.
4. Move or rename pages inside `Wiki_Pages/`.
5. Update all affected internal links and cross-references.
6. Update `Wiki_Pages/index.md` and relevant section indexes.
7. If the work is only structural, do not rewrite factual body content unless the user explicitly asks.
8. Append a log entry to `Wiki_Pages/log.md`:
   - `## [YYYY-MM-DD] reorganization | <summary>`

### Query

Use query mode when the user asks about existing wiki knowledge.

Required workflow:

1. Search relevant pages in `Wiki_Pages/`.
2. If the query requires private or sensitive pages, follow the query privacy rules in `privacy_sanitize_rules.md`.
3. Answer based on wiki content.
4. Cite relevant page names in the answer.
5. If the query produces a new durable insight, organize it into a new wiki-page draft.
6. If the query reveals that existing content is wrong, switch to correction mode rather than silently editing.
7. Append a log entry to `Wiki_Pages/log.md`:
   - `## [YYYY-MM-DD] query | <question summary>`

### Lint

Use lint mode when the user requests a wiki health check.

Check for:

- contradictory content
- orphan pages
- missing cross-references
- broken wikilinks
- outdated claims
- broken internal structure
- missing or inconsistent sensitivity metadata
- missing or inconsistent lifecycle metadata
- stale, superseded, or archived state decisions
- reasonable `confidence`, `sources_count`, `last_confirmed`, and `decay_profile`
- missing `review_required` flags on private or sensitive pages
- append to `Wiki_Pages/log.md`: `## [YYYY-MM-DD] lint | <issue count>`

## Extra Rule Triggers

When an operation involves PII, private content, sensitive content, sanitization, sensitivity metadata, people-graph privacy, or writing sensitive content, also read and follow `privacy_sanitize_rules.md`.

When an operation involves metadata normalization, lifecycle state, stale decisions, supersession, or archiving, also read and follow `lifecycle_rules.md`.


## Gemini Usage

Use `gemini-usage.md` when a task needs large-context analysis that may exceed the current model context window.

Typical cases include:

- analyzing many files or entire directories at once
- comparing project-wide patterns across `Raw_Sources/`, `Wiki_Pages/`, or scripts
- checking whether a feature, workflow, or rule is implemented across the repository
- summarizing architecture or structure when local file reads would produce too much context
- verifying broad consistency before a lint, reorganization, or documentation update

`gemini-usage.md` is usage guidance for read-only large-context analysis. It does not override `AGENTS.md`, `privacy_sanitize_rules.md`, or `lifecycle_rules.md`, and it does not grant permission to modify `Raw_Sources/`.

## Default Wiki Page Format

When creating or updating wiki pages, prefer the YAML frontmatter defined in `lifecycle_rules.md`.

During the first normalization pass for existing pages, only add frontmatter. Do not rewrite body content, sanitize content, or move files.

```markdown
---
type: summary
status: active
sensitivity: private
pii_categories: []
created: YYYY-MM-DD
last_updated: YYYY-MM-DD
last_confirmed: YYYY-MM-DD
confidence: 0.7
sources_count: 1
decay_profile: fact
last_privacy_review: YYYY-MM-DD
review_required: false
supersedes: []
superseded_by: []
related_pages: []
---

# Page Title

## Main Content

...

## Cross References

- [[Related Page 1]]: relationship note
- [[Related Page 2]]: relationship note
```

## Index Maintenance

- Keep `Wiki_Pages/index.md` up to date.
- When applicable, keep section-specific indexes up to date.
- Do not treat source-side index files as canonical knowledge sources.
- Maintain `Wiki_Pages/cc_projects/project_index.md` only through wiki ingest/update workflows.

## Naming Rules

- Standard wiki filenames use lowercase English words with underscores.
- Person pages: `person_<name>.md`
- Company pages: `company_<name>.md`
- Topic pages: `topic_<topic>.md`

## Responsibility Split

User responsibilities:

- decide what to ingest
- add new material to `Raw_Sources/`
- ask questions
- request lint checks

Agent responsibilities:

- read and understand source material
- create and maintain wiki pages in `Wiki_Pages/`
- maintain cross-references
- maintain indexes
- append operation logs
- never modify `Raw_Sources/`

## External Knowledge Base Bridge

The `interview/` folder in this wiki may cite job-search methodology pages from the external `knowledge-wiki` vault.

One-way bridge: the personal wiki may cite the knowledge base; the knowledge base must not cite the personal wiki back.

### Path Conventions

```text
knowledge-wiki (Windows): D:\shane_yeh\Documents\_Claude_Code\knowledge-wiki\wiki-pages\
knowledge-wiki (WSL):     /mnt/d/shane_yeh/Documents/_Claude_Code/knowledge-wiki/wiki-pages/
```

Before running commands, confirm the environment with `pwd` and choose the appropriate path.

### Citation Format

```markdown
> Knowledge-base reference: [[KnowledgeBase::Page Name]]
> Path: `knowledge-wiki/wiki-pages/<category>/<page>.md`
```

### Bridge Page Mapping

| Knowledge-base page | Core content |
| `Resume Optimization` | AI-assisted Claude prompts, ATS, STAR, and cover letters |
| `Interview Preparation` | Interview workflow, "Why Should We Hire You" formula, and AI mock interviews |
| `Job Search Automation` | Automated job-application workflow and job-filtering prompts |

Bridge-enabled page: only `Wiki_Pages/interview/README.md`.

### Bridge Rules

1. Agents must not directly modify knowledge-base files; they may only read them.
2. Knowledge-base pages may be outdated. If they conflict with the personal wiki, the personal wiki takes priority and the agent should add `(please verify knowledge-base version)`.
3. Bridge references do not count toward `sources_count`.
4. Query mode searches only `Wiki_Pages/` by default. The user must explicitly say "also search the knowledge base" to include it.

## Guardrails

- Do not forget index updates when creating new wiki pages.
- Do not create duplicate overview pages because of hardcoded filenames.
- Unless the user explicitly asks, do not treat helper files as knowledge content.
- When uncertain, prefer preserving source content inside the wiki page with structure around it instead of over-compressing.
- Use a conservative people-disambiguation strategy. When evidence is insufficient, keep people separate temporarily rather than merging identities too early.
