---
name: edit-template
description: "Fix or change an existing template surgically with the atomic tools (outline → get_block → one precise edit), without re-authoring the body. Uses the OneB — contract templates (ai) MCP tools."
---

# Workflow: edit an existing template surgically

Never re-author the whole body with `update_template(body)` for a correction: it re-mints every variable uid and detaches documents. Use one atomic tool per change.

## 1. Locate
1. `list_templates` (`document_kind` contract|statement) → template id.
2. `get_template_outline(template_id)` — every block `path`, type, 160-char preview (`truncated`), `has_var`, every variable `uid` / `var_kind`, table `columns: [{id, header}]`. This is the only source of paths, uids and column ids.
3. `get_block(template_id, path)` — full text + raw node of one block. Required when the preview is truncated and before rewriting a block.

`get_template` (whole tree) is a last resort.

## 2. Pick the tool
Text:
- Typo / term / rewording inside a run → `replace_text(find, replace, occurrence?, path?)` — keeps formatting and variable uids.
- Bold / italic / … on a substring → `format_text(find, marks[], mode?)`.
- Rewrite a whole block's words → `set_block_text(path, text|runs)`; refused for blocks holding a variable (use `replace_text`); long blocks need `get_block` first or `confirm: true`.
- paragraph ↔ heading ↔ quote → `set_block_type(path, type, level?)`.

Structure (by `path`):
- `insert_block(path, position, node)`, `replace_block(path, node)`, `delete_block(path)`, `move_block(from_path, to_path, position)`. Use `get_block` output as the node shape.

Variables (by `uid`):
- Point at a system field → `set_field_source(uid, source_alias)`.
- Per-position price token → `set_spec_item(uid, position, row_label, full?)`; totals → `set_total(uid, kind)`; ad-hoc amount with currency → `set_price_currency(uid, currency?)`.
- Several repeated vars → one table: `convert_vars_to_table(uids[], name, columns[])`.

Tables (`input_table`, by `uid`):
- Columns → `edit_table_columns(uid, operations[])` (add / rename / retype / set_width / set_style / reorder). Removing a column is destructive → use the `restructure_table_column` workflow.
- Table options (`min_rows`, `max_rows`, `show_row_numbers`, `display_mode`, `table_style`) → `set_table_config`.

## 3. Verify
Every write validates and returns `preview_html` of the changed block — check it (never a PNG). After a structural edit, re-read the outline (prose/block tools return the refreshed `outline`; table tools don't).

## 4. Existing documents
Edits change the template only. To push them into documents already created from it, run the `propagate_template_changes` workflow.

Note: a contract's `spec_table` renders from the document's items, not the body.
