# qf-close — QuantFi Automated Monthly Close

## Project Location
`/home/kenny/qf-close/`

## Current State (Feb 2026)
Three APIs live + QBO connected + Google Sheets auth done. 166 tests. AR command works end-to-end.
SOP checklist, expense accrual analysis, 3-sheet workflow, and **unified review workflow** added.

### Unified Review Workflow (NEW — Feb 2026)
`review` → `review --read` → `close --from-review` → `push`
- `qf-close review 2026-03` → writes 4 review tabs to one workbook
- `qf-close review 2026-03 --read` → reads decisions, updates ledger
- `qf-close close 2026-03 --from-review` → executes with confirmed data
- 4 tabs: "Review Accruals" (accruals + expenses + contractor rates), "Review Rates" (client billing rates + evidence), "Review Hours" (aggregated hours), "Review Actions" (per-client billing decisions)
- `review.py` rewritten: new write/read functions, legacy functions preserved at bottom as aliases
- `workbook.py` updated: 8 tabs in `create_monthly_workbook()` (added 4 review tabs)
- 35 new tests in `tests/test_review.py`

### 3-Sheet Workflow (still works)
`pull` → `bill` → `close` → `push` with Google Sheets as the collaboration interface:
- `qf-close pull 2026-02` → Sheet 1 "Hours & Rates" (Michael confirms rates)
- `qf-close bill 2026-02` → Sheet 2 "Billing" (Michael enters invoice amounts)
- `qf-close close 2026-02` → Sheet 3 "Reconciliation" (Kenny approves QBO entries)
- `qf-close push 2026-02` → unchanged
- Old monolithic pipeline preserved via `qf-close close --legacy --approved`
- Workbook IDs persisted in `~/.config/qf-close/workbook_YYYY-MM.json`

### Bank Review (NEW — Feb 2026)
- `qf-close bank-review 2026-02` → pulls all Mercury bank + CC transactions, categorizes, writes Google Sheet
- 3 tabs: "Transactions" (all txns sorted by date), "Summary" (category totals), "Needs Review" (uncategorized)
- Mercury credit cards use org-wide `/api/v1/transactions` endpoint (CC accounts not in `/accounts`)
- Feb 2026: 128 txns (95 CC + 33 bank), 4 CC cards (••6524, ••5869, ••2401, ••8643)
- CC transaction `kind` = "creditCardTransaction", has `mercuryCategory` field (not yet used)
- Cursor-based pagination via `page.nextPage`
- Auto-categorization gaps: most CC txns land in "Credit Card Expense", bank contractor payments in "Other Expense"
- **Next steps**: use Mercury's `mercuryCategory` as fallback, build category override mappings

### Connected
- **Toggl** — Admin token, 32 users visible, workspace 8829915. `TOGGL_API_TOKEN` in `.env`
- **Mercury** — Checking + Savings via `/accounts`, CC via org-wide `/transactions`. `MERCURY_API_TOKEN` in `.env`
- **Bill.com** — v2 API, session auth. `BILLCOM_DEV_KEY`, `BILLCOM_USERNAME`, `BILLCOM_PASSWORD`, `BILLCOM_ORG_ID` in `.env`
- **Google Sheets** — OAuth2 connected. Token at `~/.config/qf-close/google_token.json`
- **Read.ai** — OAuth 2.1 + PKCE, auto-refresh. Tokens at `~/.config/qf-close/readai_tokens.json`. Meeting transcripts, summaries, action items.

### QBO (QuickBooks Online) — CONNECTED (production)
- OAuth2 tokens at `~/.config/qf-close/qbo_tokens.json`, auto-refreshes
- `QBO_ENVIRONMENT=production` — this is the real books, not sandbox
- 25 customers, 100 Chart of Accounts
- Bank accounts in QBO: Mercury Checking (2493), Mercury Savings (6317), Mercury Credit
- Revenue: Consulting Revenue
- Key expense accounts: Contract labor, Software & apps, Legal & accounting services, Insurance, Advertising & marketing
- Partner accounts: Management fees CS/KJ, Due CTS Capital, Due West 34 LLC, Partner contribution/distribution CS/KJ
- Uses `python-quickbooks` library (QuickBooks, AuthClient, entity objects)
- `qbo.py` has: push invoices/bills/JEs/expenses, query_invoices, query_payments, query_expense_transactions, get_ar_aging

## CLI Commands
- `qf-close ar 2026-02` — AR tracking (Toggl + Bill.com + Mercury)
- `qf-close ar 2026-02 --approve` — Interactive AR confirmation
- `qf-close audit --init` — Seed rate ledger from RATE_TRUTH.md
- `qf-close audit 2026-02` — Rate audit with 8 checks
- `qf-close pull 2026-02` — Pull hours → Sheet 1 "Hours & Rates"
- `qf-close bill 2026-02` — Calculate billing → Sheet 2 "Billing"
- `qf-close close 2026-02` — Reconcile → Sheet 3 "Reconciliation"
- `qf-close close 2026-02 --legacy --approved` — Old monolithic pipeline
- `qf-close checklist 2026-02` — SOP progress (16 steps, auto-detects artifacts)
- `qf-close expense-review 2026-02` — Expense accrual analysis (QBO or Mercury fallback)
- `qf-close bank-review 2026-02` — Mercury bank + CC transaction export to Google Sheet (3 tabs)

## Key Modules
| File | Purpose |
|------|---------|
| models.py | All dataclasses + enums (PaymentRecord, ARBalance, ARSnapshot, etc.) |
| config.py | Google Sheets or YAML config. Client alias resolution. |
| toggl.py | Toggl Reports API v3 (admin token, 32 users) |
| billing.py | Billing calc: hourly, retainer (overage/rollover), fixed, flexible |
| mercury.py | Mercury Banking REST API |
| billcom.py | Bill.com v2 API (session auth, customer resolution) |
| ar.py | AR module: billing_from_ledger, Mercury dedup, AR calc, Rich report, Excel export |
| rate_ledger.py | Persistent YAML ledger with client_aliases, mercury_client_map, payment methods |
| audit.py | 8 audit checks + Excel export |
| qbo.py | QBO OAuth2 + push + expense query for accrual analysis |
| checklist.py | Monthly close SOP (16 steps), state tracking, auto-detection |
| cli.py | Click CLI with dotenv loading |

## Architecture Notes
- `.env` at project root loaded via `python-dotenv` in cli.py
- Rate ledger at `~/.config/qf-close/rate_ledger.yaml` — persistent across runs
- Client name resolution: Toggl short names → ledger aliases → Bill.com customer names
- Mercury dedup: when Bill.com data present, skip Mercury deposits for billcom_* clients (avoids double-counting)
- Hyer pays direct ACH to Mercury ("HyerBoots"), NOT via Bill.com
- Konscious pays via Mercury ("Konscious LLC" / "Enclave LLC")
- All other clients pay via Bill.com ACH

## Known Data Items (Feb 2026 AR)
- $155,766 expected | $127,856 received | $27,910 outstanding
- **Jaclyn Pascocello**: $2,500 Bill.com payment, unknown client — needs alias mapping
- **Konscious**: $15k expected, $0 received in Feb (no Mercury deposit yet)
- **Red Apple**: 125h logged vs 25h retainer cap → $27.6k overage. Verify if expected.
- **Berg**: $36,600 received vs $18,300 expected — likely 2-month prepayment
- **Chew**: $9,056 received vs $7,500 expected — credit from prior month

## Billing Logic (billing_from_ledger)
- Groups all effective rates per client
- Bills each retainer_amount separately (supports multi-retainer like Everbloom CFO+BK)
- Overage calculated on primary retainer (the one with retainer_hours)
- Hourly fallback for categories without retainer
- Everbloom: overage_rate=None (DO NOT BILL OVERAGE per Christian)

## Bill.com API Notes
- v2 API at `https://api.bill.com/api/v2`
- Login: POST form data with devKey, userName, orgId, password (sync token as password)
- Session ID in form data (NOT headers) for subsequent calls
- paymentType comes back as string, needs int() conversion
- Customer IDs resolved via List/Customer.json endpoint
