---
name: supplier-invoice-review
description: Use when the user asks to review, check or explain supplier invoices or receipts in Snapbooks — what they are, whether they are booked correctly, whether they are paid, duplicates, or how a new one should be booked.
---

# Reviewing supplier invoices and receipts in Snapbooks

Start with `get_user_context` and pick the client account.

## Find the documents

- Open or overdue payables: `search_documents` with `kind: PAYABLE` and `unpaid_only: true` (add `overdue_only: true` for overdue).
- A specific invoice: `search_documents` with a `query` (invoice number, supplier name or text). If keywords find nothing, try `semantic: true`.
- Document types: `APINV` supplier invoice, `RECPT` receipt, `APCRN` supplier credit note.

For each document you discuss, call `read_document` and read its text before saying what it contains. It also returns the journal entry that books it, its payment lines and any open questions.

## Check the booking

For a booked document, compare what the document says with its journal entry:

- the total, the VAT amount and the VAT rate,
- the period (document date vs. the entry date),
- the supplier,
- the expense account — look up its description with `get_gl_accounts` and check it fits what was bought.

To see how the company normally books this supplier or this kind of purchase, call `search_documents` with `similar_to_file_id` set to the document's `file_id`, then read those documents' journal entries with `search_journal_entries` (`file_id`). A similarity above about 0.9 is usually the same supplier and kind of document — and the same invoice number from the same supplier twice is a likely duplicate upload; point it out.

Report differences as questions for the user or their accountant, not as errors, unless the numbers plainly disagree.

## Prepare a booking

For an unbooked document the user wants booked:

1. Take accounts only from `get_gl_accounts` and VAT codes only from `get_tax_codes`. Never invent a code.
2. Follow how similar documents were booked before; say which document you followed.
3. Check `get_period_lock`: the entry date must be after the lock.
4. Call `create_journal_entry` with the document's `file_id`. Nothing is booked until the user approves it on the card (or, without cards, books it in the Snapbooks app).

If you are unsure about the account or the VAT treatment (for example mixed private and business use, or a foreign supplier), say what is unclear and ask instead of guessing.

## Is it paid?

- `read_document` shows payment requests that have not completed.
- `search_bank_transactions` with the amount (and `free_text` for the supplier or KID) finds the payment in the bank.
- If the money has left the bank but the invoice is still open, offer to post the bank transaction against the invoice with `create_posted_payment`; where the host supports it, the user approves it on a card.

The connector does not pay invoices. Paying is done in the user's online bank or the Snapbooks app.
