---
name: send-contract
description: "Create a contract (договір) or statement (заява) from a template and send it so the issuer/client fills the fields via the share link. Uses the OneB Invoice MCP tools."
---

# Workflow: create and send a contract (договір) or statement (заява)

The gateway creates the document and sends it; the variable fields inside it are filled by a HUMAN (issuer or client) on the web fill page. Never try to fill contract fields yourself.

## Steps
1. **Workspace.** `get_active_workspace` (switch with `set_active_workspace` if needed).
2. **Parties.** `find_company` → `profile_id` (issuer). `find_client` → `client_id` (optional for a contract; required for a statement signed by the client). Missing client → `create_client` after confirming details.
3. **Template.** `list_contract_templates` for the document `language` → pick `template_id` with the user. No suitable template → it has to be authored on the Contract Template server first (workflow `author_template` there).
4. **Create.**
   - Contract → `create_contract` (`profile_id`, `language`, `template_id`; optional `client_id`, `currency_id` via `find_currency`, `project_id`, specification `items`). Set `allow_client_filling=true` when the client should fill their own fields.
   - Statement → `create_statement` with `signer` = `issuer` or `client` (then `client_id` is required). A statement is NOT a contract appendix (додаток).
5. **Hand over.** Show `links.edit_in_service` — it opens the data-filling page (`…/contracts/{alias}/fill` or `…/statements/{alias}/fill`).
6. **Send.** On the user's approval `share_document` → return `links.public_url` for the client.

## Checking a contract later
- `get_contract` — one contract: parties, specification, signing state, filled `fields`, rendered text.
- `list_contracts` — many contracts with their `fields` for analysis (signed vs unsigned, empty fields, expiry).
