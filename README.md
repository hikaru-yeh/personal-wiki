# Personal-Wiki-Rules

A sanitized rule framework for an LLM-maintained personal wiki built with the concept of ['llm-wiki by Karpathy'](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f) with upgraded framework of ['llm-wiki-v2'](https://gist.github.com/rohitg00/2067ab416f7bbe447c1977edaaa681e2)

This repository demonstrates how I designed an agent-operated knowledge system that can turn messy personal source material into structured wiki pages while preserving privacy, auditability, and long-term maintainability. The original private vault contains personal notes and source material; this public version keeps only the reusable structure, operating rules, and safe example pages.

## Why This Project Exists

Personal knowledge bases are difficult to maintain with LLM agents because they mix useful long-term knowledge with sensitive information: career history, relationships, interviews, private messages, job-search records, and identity details.

This project solves that problem by defining clear boundaries for what agents may read, write, summarize, cite, and preserve. It treats the wiki as a governed system rather than a loose folder of notes.

## What This Repository Shows

- Agent instructions for maintaining a personal wiki safely and consistently.
- A read-only source area (`Raw_Sources/`) separated from maintained knowledge pages (`Wiki_Pages/`).
- Privacy and sanitization rules for PII, sensitive topics, third-party details, and private source material.
- Lifecycle metadata for confidence, review state, stale content, supersession, and archiving.
- Example public wiki pages that demonstrate frontmatter, indexes, logs, and cross-references.
- Guidance for using Gemini CLI when a task requires large-context repository analysis.
- One-way bridge rules for referencing an external methodology wiki without merging privacy models.

## Repository Structure

```text
.
|-- AGENTS.md
|-- CLAUDE.md
|-- privacy_sanitize_rules.md
|-- lifecycle_rules.md
|-- gemini-usage.md
|-- Raw_Sources/
|-- Wiki_Pages/
|   |-- index.md
|   |-- log.md
|   `-- topic_example_wiki_workflow.md
`-- README.md
```

## Core Design

`Raw_Sources/` is the user-managed input area. Agents treat it as read-only.

`Wiki_Pages/` is the maintained knowledge layer. Agents may create and update pages there only after following the relevant planning, privacy, and lifecycle rules.

`CLAUDE.md` defines the operating modes for ingest, update, correction, reorganization, query, and lint. These modes make the agent's behavior predictable and auditable.

`privacy_sanitize_rules.md` defines how private and sensitive information should be summarized, redacted, tagged, or held back for user approval.

`lifecycle_rules.md` defines how wiki pages age over time through metadata such as `status`, `confidence`, `last_confirmed`, `review_required`, `supersedes`, and `superseded_by`.

## Privacy-First Workflow

This project is built around the assumption that a personal wiki may contain information that should never be copied verbatim into a public or semi-public answer.

The rules require agents to:

- identify PII and sensitive content before writing wiki pages
- use summaries instead of raw private messages where possible
- tag pages with sensitivity metadata
- request user approval before writing private or sensitive material
- avoid exposing third-party personal details unnecessarily
- keep source files read-only

## Relationship To knowledge-wiki

This repository is intentionally separate from [`hikaru-yeh/knowledge-wiki`](https://github.com/hikaru-yeh/knowledge-wiki/tree/main).

The two repositories serve different purposes:

| Area | knowledge-wiki | personal-wiki |
|---|---|---|
| Content type | External knowledge and methodology | Personal facts, history, and execution records |
| Sensitivity | Lower, mostly public sources | Higher, including PII, career context, and relationships |
| Maintenance rhythm | Continuous collection and synthesis | Event-driven updates around jobs, interviews, courses, and life events |
| Privacy model | Lightweight | Explicit privacy and sanitization rules |
| Main audience | Owner and potentially shareable readers | Owner only in the private version |
| Agent rules | Medium complexity | Higher complexity with lifecycle, correction, and people-disambiguation rules |

Merging the two would blur their safety boundaries. The public knowledge wiki should not inherit heavy personal-wiki privacy rules, and the personal wiki should not inherit the looser rules of a public knowledge base.

Instead, this project uses one-way bridging: personal wiki pages may cite methodology pages from `knowledge-wiki`, but the content stays in its own repository. For example, an interview page can reference `[[KnowledgeBase::Resume Optimization]]` while keeping private application records inside the personal wiki.

## Example Pages

The repository includes three safe example pages under `Wiki_Pages/`:

- `index.md`: a minimal navigation page
- `log.md`: an example maintenance log
- `topic_example_wiki_workflow.md`: a sample maintained wiki page with metadata and cross-references

These examples show the expected page shape without exposing any private source material.

## Portfolio Highlights

This project demonstrates:

- agent workflow design
- privacy-by-design documentation
- structured knowledge management
- metadata and lifecycle governance
- Obsidian-style wiki organization
- human-in-the-loop approval rules
- separation of public methodology knowledge from private execution records

## What Is Not Included

This public repository intentionally excludes:

- personal raw source files
- private wiki pages
- social media exports
- email archives
- resumes and cover letters
- private messages
- machine-local permission settings
- `.git/` history from the private working vault

Only the reusable rules, structure, and safe examples are included.
