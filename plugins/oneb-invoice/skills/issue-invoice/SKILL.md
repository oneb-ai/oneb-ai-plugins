---
name: issue-invoice
description: "Issue an invoice (or act / payout / purchase) FROM one of the user's businesses TO a client: resolve every id, create the document, offer to share it. Uses the OneB Invoice MCP tools."
---

# Workflow: issue an invoice (or act / payout / purchase)

Direction is always FROM → TO: the ISSUER (one of the user's own businesses) sends the document to the CLIENT, and the client pays into the issuer's RECEIVABLE ACCOUNT.

## Steps
1. **Workspace.** `get_active_workspace`. If it is not the business the user means, `get_workspaces` → `set_active_workspace`.
2. **FROM side.** `find_company` → `profile_id`. If there are several businesses and the user did not name one, ask.
3. **TO side.** `find_client` → `client_id`. Nothing found → confirm the details with the user, then `create_client`.
4. **Money.**
   - `find_currency` → `currency_id`.
   - `find_payment_detail` (the issuer's account the client pays into) → `account_id`. None suitable → `list_payment_provider` to pick a provider, then `create_payment_detail`.
   - Optional: `find_project` → `project_id`.
5. **Items.** For each line that is a catalog product: `find_product` → `items[].product_id` (a new catalog item can be added with `create_product`). Every line still needs its own `name` and `price`; `quantity`, `unit`, `description`, `discount_percent` are optional.
6. **Create.**
   - Invoice → `create_invoice` with the ids above + `language` + `payment_description` + at least one item. Tax: `tax_type` = `with_vat` / `without_vat` / `custom_tax` (+ `tax_percent`).
   - Act of completion → `create_act`; disbursement note → `create_payout`; purchase → `create_purchase` (same FROM→TO/items shape).
   - An act/payout FOR an existing invoice → do NOT create it standalone; use the `copy_or_convert_document` workflow (`create_document_from_document`) so the parent link is set.
7. **After creating.** Show `links.edit_in_service`. Offer to send it with `share_document` (generates the PDF, `new → pending`, returns a public URL). Share only after the user agrees.

## Rules
- Never guess ids — every id comes from a `find_*` / `list_*` call in this conversation.
- Do not invent the document `name`; per-period notes ("за травень") go to `items[].description`, `comment` or `payment_description`.
- All ids are strings; all money is in the document currency.
