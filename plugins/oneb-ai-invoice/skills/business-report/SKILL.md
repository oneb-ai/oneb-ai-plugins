---
name: business-report
description: "Answer \"how is the business doing\" questions: totals by status, top clients/products, per-client breakdowns, contract analysis. Uses the OneB Invoice (ai) MCP tools."
---

# Workflow: revenue & contracts report

Pick the smallest set of tools that answers the question, then do the analysis yourself in the conversation.

## Money questions
- "How much was issued / paid / is pending / overdue?" → `get_summary_metric` for a document `type` (invoice, estimate, act, …): totals and counts by status × currency. For invoices `pending` already subtracts partial payments; `overdue` is its own status.
- "Who are the top clients / what sells best?" → `get_top_clients` / `get_top_products` (top 13 by PAID invoice revenue per currency, the rest in `other`).
- "Full breakdown per client / product" → `get_clients_analytics` / `get_products_analytics`, optionally `status` (`new`, `pending`, `overdue`, `pending_seen`, `closed`, `paid`).
- Row-level drill-down → `list_documents` with the same filters.

All report tools take `from` / `until` (ISO `YYYY-MM-DD`) and scope filters `profile_id`, `client_id`, `project_id`, `group_id`, `currency_id` — resolve them with the `find_*` tools first. Analytics need a paid plan; if the upstream returns a billing error, relay it as is.

## Contract questions
- Across many contracts (signed vs unsigned, empty fields, totals, expiry windows, per-client) → `list_contracts` (filters `status` `new`/`pending`/`closed`/`signed`, `client_id`, `period`, `search_term`).
- Inside one contract → `get_contract`.

## Presenting
- Never add amounts in different currencies together — report per currency.
- State the period and filters you used.
