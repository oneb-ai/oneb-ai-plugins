---
name: author-template
description: "Create a new contract / appendix / statement template from a recipe, a library example or scratch: map placeholders to system fields, validate, preview, save. Uses the OneB — contract templates MCP tools."
---

# Workflow: author a new template (contract / appendix / statement)

Family: contract → `create_template` with `document_kind=contract` (default); appendix (додаток) → pass `parent_id` of the contract template; statement (заява) → `document_kind=statement`. Authoring needs the admin/owner role.

## 1. Pick a starting point (do not write from a blank page)
- A real sample: `list_contract_examples` (optionally by `category`) → `get_contract_example` to read it → `clone_example(id, name)` copies it into the workspace as a new template. Then continue with the `edit_template` workflow.
- A universal skeleton: `get_contract_recipe` → a pre-validated Lexical `body` with parties, dates, clause-list and signature block.
- An existing template of the workspace: `list_templates` → `get_template` and model on it.

## 2. Learn what you may insert
- `get_editor_capabilities` — supported node types, format bits, `system_field_aliases`.
- `list_document_vars` (with `parent` for an appendix) — the variable palette; `list_variable_definitions` — the workspace registry (bind with `config.variableKey` = slug; add new ones with `create_variable_definition`).
- `get_dialect_reference`, `get_field_reference` when unsure.

## 3. Map every placeholder
For each placeholder call `suggest_variable` (Ukrainian label → best field + ready `var` node). Rules:
- Party / requisite / date / signature data is a **system field** set by alias in `source` (`company_name`, `client_tax_id`, `date_from`, …) — never `input_text`.
- Signature block: issuer `company_name` + `company_requisites` + `company_signature` + `company_pib`; client `client_name` + `client_requisites` + `client_signature` + `client_pib`. `*_pib` / `*_representative` may be empty for sole proprietors — don't rely on `*_representative`.
- Priced specification → `spec_*` and `subtotal` / `total` (+ `*_in_words`, `*_full`) aliases.
- `input_*` types only for data that is genuinely not a system field (rent amount, payment day, custom clauses).

## 4. Dialect traps (backend renderer, not vanilla Lexical)
- `page-break` (hyphenated), `clause-list` / `clause-item` for auto-numbered clauses; no `image`, no `horizontalrule`.
- Element nodes MUST have `format` (string, e.g. `""`), `indent`, `version`.

## 5. Check before saving
1. `get_legal_checklist` — make sure the contract is legally complete.
2. `validate_template_body` — fix every `error` until `valid: true` (the backend does not reject bad nodes, it renders them as visible errors).
3. `preview_template` — look at the HTML.

## 6. Save
`create_template(name, language, body, document_kind? | parent_id?, signer?)`. Report the new id. Documents are created from it on the Invoice server (`create_contract` / `create_statement`).
