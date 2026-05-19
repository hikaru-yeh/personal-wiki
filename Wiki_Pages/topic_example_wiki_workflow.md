---
type: summary
status: active
sensitivity: public
pii_categories: []
created: 2026-05-19
last_updated: 2026-05-19
last_confirmed: 2026-05-19
confidence: 0.9
sources_count: 1
decay_profile: durable
last_privacy_review: 2026-05-19
review_required: false
supersedes: []
superseded_by: []
related_pages:
  - "[[index]]"
  - "[[log]]"
---

# Example Wiki Workflow

## Main Content

This page is a safe, public example of the standard wiki-page format described in `CLAUDE.md`. It demonstrates how a maintained page should use YAML frontmatter, a clear title, structured body sections, and Obsidian-style cross-references.

A real wiki page should be created only after the agent has checked the relevant rules, respected `Raw_Sources/` as read-only, handled privacy and sensitivity metadata, and identified whether indexes or logs need updates.

## Example Maintenance Notes

- Use lowercase English filenames with underscores.
- Keep source material in `Raw_Sources/` read-only.
- Write maintained knowledge only inside `Wiki_Pages/`.
- Add or update indexes when new pages affect navigation.
- Add a log entry for each ingest, update, correction, reorganization, query, or lint action.

## Cross References

- [[index]]: Main navigation entry for the example skeleton.
- [[log]]: Example operation log entry for this update.