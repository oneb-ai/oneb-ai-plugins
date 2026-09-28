---
name: restructure-table-column
description: "Destructive table maintenance: remove an input_table column or rename/remove select options and remap the values already stored in documents. Uses the OneB — contract templates (ai) MCP tools."
---

# Workflow: remove a table column or remap select values

Both operations rewrite data already stored in documents. Always preflight, show the user the numbers, and act only on explicit confirmation.

## A. Remove an `input_table` column
1. `get_template_outline(template_id)` → the table `uid` and its `columns: [{id, header}]`.
2. `get_table_column_orphans(template_id, uid)` → how many stored rows hold data in each column.
3. If the column holds data, tell the user how many rows will lose it; optionally inspect with `get_table_storage_rows`.
4. `edit_table_columns(template_id, uid, operations: [{op: "remove", id}], confirm: true)`.
Renaming / retyping / reordering keeps column ids and loses nothing — prefer them when the user just wants a different header or order.

## B. Rename / remove options of a select column
1. The options themselves are changed in the web template editor (there is no atomic tool for select options). To plan before/without saving, pass the intended option list as `current_options` in the next step.
2. `get_select_column_orphans(template_id | document_id, table_uid, column_id, current_options?)` → `orphans: [{value, unsigned_count, signed_count}]`.
3. Agree a mapping with the user: each orphan `from` → new option `to` (empty `to` leaves the value as is).
4. `apply_select_value_mapping(template_id | document_id, table_uid, column_id, mapping)` — DESTRUCTIVE. Signed dependent documents are skipped (`skipped_signed`); targeting a signed contract directly by `document_id` is rejected.
5. Report `contract_updated`, `applications_updated`, `skipped_signed`, `cells_changed`.

`decline_value` helps when row titles / values need Ukrainian grammatical cases.
