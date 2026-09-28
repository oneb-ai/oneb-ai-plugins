---
name: copy-or-convert-document
description: "Duplicate a document of the same type, or turn one into another type (estimate→invoice, invoice→act/payout) keeping the parent link. Uses the OneB Invoice (ai) MCP tools."
---

# Workflow: copy or convert a document

## 1. Find the source
`list_documents` (filter by `type`, `status`, `client_id`, period, `search_term`) or `get_document` → take its **alias** (`source_id`), not the numeric id.

## 2. Pick the tool
- **Same type, a new copy** (e.g. next month's invoice) → `duplicate_document`. The copy is never linked to the source. Items are copied unless you pass `items` (then fully replaced). Override only what the user asked: `number`, dates, `client_id`, `account_id`, `project_id`, `comment`, `payment_description`, `items`.
- **Another type from this one** (estimate → invoice, invoice → act / payout) → `create_document_from_document` with `target_type`. The backend links child to parent. If a child of that type already exists the call fails with `existing_document` — show it to the user and pass `allow_duplicate=true` only if they really want another.

## 3. Naming — do not touch
The document `name` is the issuer's own label ("Рахунок", "Рахунок-фактура", "Продаж", "Акт виконаних робіт", "Кошторис", …). Do not pass `name` unless the user explicitly asks for a different one. Never append "(копія)", "Copy of …", periods or project tags to `name` or `items[].name`. Period notes go to `items[].description`, `comment` or `payment_description`.

## 4. After
Show `links.edit_in_service`. For an invoice, offer `share_document`; share only after the user agrees.
