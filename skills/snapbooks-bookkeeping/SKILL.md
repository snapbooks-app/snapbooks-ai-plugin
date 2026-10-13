---
name: snapbooks-bookkeeping
description: Use when the user asks anything about their bookkeeping in Snapbooks — looking up journal entries, documents, bank transactions, customers, suppliers, accounts or VAT codes, or preparing a journal entry, invoice, bank posting or other change in their Snapbooks books.
---

# Working in Snapbooks

Snapbooks is a Norwegian accounting system. The `snapbooks` connector gives you the user's own books through tools; everything you see is limited to the client accounts (companies) their Snapbooks user can access.

## 1. Find the company first

Call `get_user_context` before anything else. It lists the client accounts the user can reach, each with its id. Every other tool takes a `client_account_id`.

- One client account: use it.
- Several: pick the one the user named; if it is ambiguous, ask which company they mean. Accountants often have many clients, so never guess between two.

Amounts always carry a currency. Report them with their currency code; the company's accounting currency is the one its books are kept in.

## 2. Look things up before you answer

Answer from the books, not from general knowledge:

- Documents (supplier invoices, receipts, sales invoices, payslips, statements): `search_documents`, then `read_document` for the details and the full text. Read the text before saying what a document says.
- Journal entries: `search_journal_entries` by date, account, document (`file_id`), amount or text.
- Bank: `search_bank_transactions`; `is_reconciled: false` gives what is not yet posted.
- Customers and suppliers: `search_business_partners`.
- Balances: `get_trial_balance_report` for a period.
- Deadlines: `get_calendar_events`. Open questions from the accountant: `get_open_questions`.
- How Snapbooks works: `search_documentation` / `read_documentation`.

Search tools return a limited page. When a result says more match (`has_more`), narrow the search or raise the limit before summing or saying "all".

## 3. Accounts and VAT codes come from the company's own chart

Never invent an account code or a VAT code, and don't rely on a "standard" chart from memory: companies differ.

- Find accounts with `get_gl_accounts` (search on the description or a code range) and use only codes it returns.
- Find VAT codes with `get_tax_codes` and use only codes it returns, with the rate and accounts it states.
- To see how the company booked something similar before, call `search_documents` with `similar_to_file_id`, then `search_journal_entries` with that document's `file_id`. Follow the company's own practice; say so when you do.
- If you are unsure which account or VAT treatment applies, say what you are unsure about and ask, rather than picking one.

## 4. Changes are prepared, then approved

Action tools (`create_journal_entry`, `create_invoice`, `create_business_partner`, `create_posted_payment`, `set_period_lock`, …) validate the request and return exactly what would be created or changed. Nothing is saved or sent when the tool returns; the note in its result says what happens next:

- **Approval card:** the user sees the change on a card and it happens only when they press **Approve**; the card reports the outcome. Don't call the tool again for the same change unless the user asks for something different.
- **Preview only** (a host without approval cards): nothing is saved. Show what it would do and tell the user to complete it in the Snapbooks app.
- Never say an entry was booked, an invoice sent or a change saved before the card or the user confirms it.
- Before preparing an entry, check `get_period_lock`: dates on or before the lock are refused.
- Journal entries must balance; give each line an account from step 3 and, where VAT applies, a VAT code from step 3.

## 5. What the connector does not do

- It does not move money or send payments to the bank. Paying a supplier happens in the user's online bank or the Snapbooks app.
- It does not return national identity numbers (fødselsnummer). If payroll work needs one, tell the user to check or enter it in the Snapbooks app.
- It does not file the VAT return or the annual accounts; those are filed from the Snapbooks app. Approving a payroll run (`post_payroll_run`) or ending an employment contract submits the a-melding for the period, so say so before the user approves.

When a request falls outside this, say so plainly and point to the Snapbooks app at https://app.snapbooks.no.

## 6. Links

Search results include a `link` of the form `snapbooks://<record-type>/<id>`. Mention the record by its number, partner and date so the user can find it; don't present the `snapbooks://` link as a web address.
