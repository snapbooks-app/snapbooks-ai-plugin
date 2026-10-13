# Snapbooks plugin

Connects Claude (Claude Code, Cowork and claude.ai) to [Snapbooks](https://snapbooks.no), the Norwegian accounting system. With it, Claude can look up your books, read vouchers and documents, check bank transactions, trial balances and reporting deadlines, and prepare journal entries, invoices, bank postings, payroll changes and period locks for you to approve. The same package works as a Grok Build plugin.

## What's included

- **MCP server** (`.mcp.json`): the Snapbooks remote server at `https://api.snapbooks.com/mcp`, over streamable HTTP with OAuth. The full tool list is documented at [developer.snapbooks.no/mcp](https://developer.snapbooks.no/mcp).
- **Skills**:
  - `snapbooks-bookkeeping` — how to work with the books: pick the company, look things up, use the company's own accounts and VAT codes, and how previews and approvals work.
  - `month-end-check` — a checklist for closing a month or VAT period.
  - `supplier-invoice-review` — reviewing, checking and preparing bookings of supplier invoices and receipts.

## Setup

1. Install the plugin:
   - Cowork and claude.ai: install Snapbooks from the plugin directory.
   - Claude Code, from this repository's own marketplace:

     ```
     /plugin marketplace add snapbooks-app/snapbooks-ai-plugin
     /plugin install snapbooks@snapbooks
     ```
2. The first time a Snapbooks tool runs, your browser opens the Snapbooks login. Sign in with your Snapbooks user and authorize access. You can untick write access to connect read-only.
3. Ask, for example: "What is left before the VAT return for my company?"

You need a Snapbooks user. Sign up at [app.snapbooks.no](https://app.snapbooks.no/sign-up).

## Data and privacy

- The plugin reaches only the companies (client accounts) your Snapbooks user can access, with the same access rules as the Snapbooks app.
- Data a tool returns is sent to the AI assistant you use and processed by its provider under your agreement with them. Snapbooks does not receive your conversation.
- National identity numbers (fødselsnummer) are never returned by the connector.
- Nothing is saved or sent until you press **Approve** on the approval card the assistant shows; in an assistant without approval cards, changes are previews you complete in the Snapbooks app. The connector never moves money.
- Network endpoints: `https://api.snapbooks.com` only. No credentials are stored in the plugin; sign-in uses OAuth in your browser.
- Revoke access any time under *API access* in the Snapbooks app, or by disconnecting the connector in your assistant.

Privacy notice: [hjelp.snapbooks.no/avtaler/privacy-ai-assistants](https://hjelp.snapbooks.no/avtaler/privacy-ai-assistants)

## Support

Email [hei@snapbooks.no](mailto:hei@snapbooks.no) or see [developer.snapbooks.no/mcp](https://developer.snapbooks.no/mcp).

## License

MIT — see [LICENSE](LICENSE).
