---
name: propagate-template-changes
description: "After a template changed: find the documents made from it, diff each one, confirm with the user and sync them (also the pre-check before deleting a template). Uses the OneB — contract templates (ai) MCP tools."
---

# Workflow: push template changes to existing documents

Template edits do NOT update documents already created from it. Syncing is irreversible and has no dry-run — always diff and confirm first.

## Steps
1. `list_template_documents(template_id)` — every document made from the template with `is_signed` and `has_changes`. Only documents with `has_changes: true` need syncing; signed ones cannot be synced.
2. For each candidate: `get_template_document_diff(template_id, document_id)` — `added`, `removed` (fields whose values would be LOST, with value previews), `changed`, text/style diffs, `summary`.
3. Show the user a short per-document summary, highlighting every `removed` value. Get an explicit yes and the exact list of documents.
4. `sync_template_documents(template_id, document_ids)` — rewrites structure, regenerates uids, carries entered values over by semantic key.
5. Report `updated`, `values_restored`, `skipped_signed`, `skipped_shared` (documents already shared for filling are skipped on purpose so a client's open link keeps working).

## Before deleting a template
`delete_template` does not delete documents — they keep a dangling template reference — and deleting a contract template also deletes its appendix templates. Run step 1 first, show the user what depends on it, and delete only on explicit confirmation.
