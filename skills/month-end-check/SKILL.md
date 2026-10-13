---
name: month-end-check
description: Use when the user wants to close a month or VAT period in Snapbooks, asks what is left before the VAT return, or wants a health check of their books for a period — unposted bank transactions, open questions, overdue invoices, deadlines and the period lock.
---

# Month-end and VAT-period check in Snapbooks

Goal: tell the user, for one company and one period, what still needs doing before the period can be reported and locked. You report and prepare; the user decides and approves.

Start with `get_user_context` to pick the client account (ask if several could match), and agree on the period: a calendar month, or the VAT term (two months for most Norwegian companies — check `get_calendar_events` to see which VAT deadlines the company has).

## Checklist

Work through these in order and keep a short note of each finding. Run the searches for the period's dates.

1. **Period lock** — `get_period_lock`. If the period is already locked, say so; later steps are then for information only.
2. **Deadlines** — `get_calendar_events` for the coming weeks: the VAT return and any other reporting due for this period.
3. **Bank transactions not posted** — `search_bank_transactions` with `is_reconciled: false` for each bank account in the period. Every bank movement should be posted. For each, look for the document it pays: `search_documents` with `unpaid_only: true` and the amount or partner name.
4. **Open questions** — `get_open_questions`. Unanswered questions from the accountant usually block posting of a voucher or transaction.
5. **Open payables and receivables** — `search_documents` with `kind: PAYABLE` and then `RECEIVABLE`, `overdue_only: true`. Overdue items are not wrong in themselves, but a "paid" invoice that is still open often means a bank transaction was posted to the wrong place.
6. **Balances** — `get_trial_balance_report` for the period. Point out what looks off and explain why, without concluding it is an error:
   - balances on suspense or clearing accounts that should be empty at period end,
   - a bank account whose closing balance in the books differs from the bank's balance,
   - VAT accounts whose movement does not match the period's sales and purchases.
   Use the account descriptions the report returns; don't assume account numbers.
7. **Payroll** (if the company has employees) — `search_payroll_runs` for the period: is there a posted run, or only a draft?

## Report

Give the user a short list grouped as **Must fix before reporting**, **Check**, and **OK**, with the record numbers, dates and amounts (with currency) you found. Offer to prepare the next step for any item — for example a bank posting with `create_posted_payment`, which shows an approval card where the host supports it.

## Locking the period

Only after the user confirms the period is reported (for example the VAT return is filed) offer to move the lock with `set_period_lock` to the last day of the period. The lock date must have passed. The change happens only when the user approves it; never move the lock back without the user asking, and warn that reopening a period may require correcting reports already filed.
