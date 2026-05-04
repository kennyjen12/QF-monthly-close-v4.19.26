# QuantFi Monthly Accounting Close — The Actual Process

Reconstructed from 12+ months of `#qf-accounting` Slack threads and the `QF Financials/{YYYY}/{Month YYYY} Close/` Drive artifacts.

## Cadence
- Target close: 5–7 business days after month-end
- Critical path: Toggl hours → bank feed categorization → accruals → bills/invoices → FS → distribution
- Bottleneck: contractor Toggl hours must be in by EOD Saturday after month-end

## Roles
| Role | Owner | Responsibility |
|------|-------|----------------|
| CFO/Approver | Kenny Jen (KJ) | Rate decisions, source-of-truth rate sheet, payouts approval, Mercury direct deposits (Konscious, Hyer), UpWork report pulls |
| Partner/Approver | Christian Sanford (CS) | Client-side recognize/defer/write-off decisions, distribution adjustments |
| Bookkeeper | Michael Kumov (MK, mkumov@tma-finance.com) | QBO entries, accruals, FS, profit allocation, closing package assembly |
| Internal admin | Alyssia | Subscription consolidation table; QuantFi Internal Toggl hours |

## Systems
- **QBO** (QuickBooks Online): general ledger, bank feeds, invoices, bills. `QBO_ENVIRONMENT=production`.
- **Mercury**: Checking (xx2493), Savings (xx6317), Credit cards (••6524 / ••5869 / ••2401 / ••8643)
- **Bill.com**: AP/AR processing for most clients & contractors
- **Toggl**: time tracking (workspace 8829915, ~32 users)
- **Upwork**: separate vendor billing channel (Alyssia, Khadija, Kelli, Amanda, etc.)
- **Google Sheets**: working files in `QF Financials/{YYYY}/{Month YYYY} Close/`
- **QBO receipt forwarding**: `quantfi+expenses@assist.intuit.com` (or QBO mobile app)

---

## Phase 0 — Pre-close prep (last week of month)
- [ ] **KJ**: Refresh source-of-truth rate sheet — confirm contractor cost rates and client billing rates. Green = confirmed, yellow = needs confirmation. Cross-reference against the "QuantFi - Accruals and Billing SOP" sheet.
- [ ] **KJ**: Heads-up to clients on planned overage/retainer changes (common: Red Apple, Treville).
- [ ] **KJ + CS**: Receipts current — for any transaction >$75 in the month, ensure receipt is attached in QBO (forward to `quantfi+expenses@assist.intuit.com` or use the QBO mobile app). Add memo explanations for ambiguous expenses.
- [ ] **Alyssia**: Subscription table refresh — share with KJ for review.

## Phase 1 — Toggl hours collection (Day 0–1 of new month)
- [ ] **All contractors logged Toggl hours by EOD Saturday after month-end.** KJ chases stragglers (historical laggards: Patrick, Lisa).
- [ ] **MK** pulls Toggl Reports → builds Billables Confirmation sheet: `QuantFi - {Month YYYY} Close (Billables Confirmation)`.
- [ ] **KJ** confirms in the sheet:
  - [ ] All contractors present (no missing entries)
  - [ ] Hours look reasonable per client
  - [ ] Non-billable tags correctly applied (flag if Hyer / Everbloom show non-billables that should be billable — Matheus has been a recurring source of mis-tagging)
  - [ ] Toggl tags for split clients verified (Red Apple split CFO/Controller/Bookkeeping; Miko's Hyer-vs-Everbloom split per UpWork memo lines)

## Phase 2 — Bank feeds + categorization (Day 1–3, parallel to Toggl)
- [ ] **MK**: Mercury Checking (xx2493) bank feed synced to QBO
- [ ] **MK**: Mercury Savings (xx6317) bank feed synced to QBO
- [ ] **MK**: Mercury Credit Card feeds synced to QBO (each card: ••6524, ••5869, ••2401, ••8643). New cards must be linked to QBO before they'll appear.
- [ ] **MK**: Categorize all bank transactions in QBO using the bank feed
- [ ] **MK**: Bill.com → QBO sync (paid invoices recognized; received payments matched to invoices)
- [ ] **MK**: Receipt matching — receipts forwarded to `quantfi+expenses` auto-match in QBO. Flag any unmatched.
- [ ] **MK**: Post Transaction Inquiry sheet for ambiguous Mercury txns: `QuantFi - Transaction Inquiry (MM.DD.YYYY).xlsx`
- [ ] **KJ + CS**: Respond with categorizations:
  - `OpEx (Customer Success)` for client-related charges (e.g., Hyer Boots = Hyer reimbursable)
  - `Computer (Reimbursable from Client)` for client-billed hardware
  - `Software & apps` for SaaS
  - `Personal` → route to Distribution/Contribution sheet
- [ ] **MK**: Generate Subscription Expenses Excel — `QUANTFI LLC_Subscription Expenses [Month YYYY]`

## Phase 3 — UpWork accrual (Day 2–3, parallel)
- [ ] **KJ**: Download UpWork Transaction Report (CSV: `Upwork YYYY-MM-DD_transaction_report.csv`)
- [ ] **KJ**: Download UpWork Timesheet (memo lines needed for client allocation, esp. Miko)
- [ ] **KJ**: Download UpWork Accrual Report only when transaction report isn't representative
- [ ] **KJ**: Post all three to MK in `#qf-accounting` thread
- [ ] **MK**: Allocate UpWork transactions to vendors based on UpWork contract descriptions — direct to P&L per category, NOT via AP accruals. Common vendors:
  - Alyssia Pelley (Org VA → Internal)
  - Khadija (Lead Gen)
  - Kelli (Growth Marketing)
  - Amanda Hood (Compliance Admin)
  - Miko Ramljak (split: Hyer + Everbloom per memo lines)
- [ ] **KJ**: Categorize any "missing from UpWork report" charges (typically S&M connection credits)

## Phase 4 — Per-client accruals review (Day 2–4)
- [ ] **MK** posts per-client accrual decisions in `#qf-accounting`. For each client:
  - Beginning Balance (deferred revenue / overage carry from prior month)
  - This month's accrual (Toggl hours × rate)
  - Recommended action: charge retainer? bill overage? recognize deferred? write off?
- [ ] **KJ + CS** answer per client. Decision matrix:
  - **Retainer this month?** Y/N (skip if heavily prepaid, e.g., Berg sometimes)
  - **Bill overage?** Y/N + amount (Everbloom = always N; Treville = write off above $13.5k unless agreed)
  - **Recognize deferred?** Y/N (e.g., Volition expiring hours)
  - **Override?** (Konscious = always recognize $15k regardless of hours)
- [ ] **MK** builds the full Toggl Report: `QuantFi - Toggl Report ({Month YYYY}).xlsx`
  - Per-client: Sum of Bill Coef, Total Contractor Cost, Hours (Billables), Total Billable to Customers
  - Accrual Summary: Beginning Balance, Payments, Balance Invoice, Comment
  - Per-affiliate commission tracking (Isaac, Miko, Jon Ong, Brian Yoon, Jake Smith)
  - Per-large-client detail tabs (Everbloom, Chew, Berg, Boundless, Trippy Goat) — month-by-month rollforward

## Phase 5 — QBO bills + invoices (Day 4–5)
- [ ] **MK**: Create contractor bills (AP) in QBO — one bill per contractor.
  - Code to `Cost of labor - COGS` for client work, or category-specific accounts for Internal contractors (Alyssia, Braedon, Vadym, Michael Kumov, Mitch)
- [ ] **MK**: Send bills to contractors for confirmation via Bill.com
- [ ] **MK**: Create client invoices (AR) in QBO — one invoice per client (retainer + overage):
  - **Bill.com clients**: Berg, BGC, Bottlecapps, Boundless, Chew, Everbloom, Fabrik, Red Apple, Treville, Volition
  - **Mercury direct (NOT Bill.com)**: Hyer ("HyerBoots"), Konscious ("Konscious LLC" / "Enclave LLC")
- [ ] **MK**: Create affiliate commission bills — `QuantFi - Affiliate Commission [Month YYYY]`
- [ ] **MK**: Recurring journal entries:
  - Prepaid expense amortization (e.g., Delve $800/mo over 12 months)
  - Business insurance amortization (~$423/mo)
  - Any other multi-month prepaids

## Phase 6 — Distribution / Contribution (Day 4–5, parallel)
- [ ] **KJ**: Fill `QuantFi LLC - Distribution/Contribution [Month YYYY]` sheet — QF charges paid on personal cards (West 34th, Novo, etc.)
- [ ] **CS**: Fill the same sheet — QF charges paid on personal cards (CTS, Novo, etc.)
- [ ] **MK**: Record intercompany distributions/contributions in QBO
  - Categorize to `Partner contribution/distribution CS` or `Partner contribution/distribution KJ` (Other Current Liability)
  - Update `Owner Distribution - CS / KJ` equity entries

## Phase 7 — Closing package assembly (Day 5–6)
- [ ] **MK** runs final QBO reports for {Month YYYY}:
  - P&L (Income Statement)
  - Balance Sheet
  - Statement of Cash Flows
  - AR Aging Detail
  - AP Aging Detail
  - Income Statement by Customer
- [ ] **MK** builds `QuantFi - Profit-Share Allocation [Month YYYY]`:
  - QuantFi Net Profit ({Month})
  - Net Profit Split (50/50)
  - KJ allocation: balance carry + adjustment + month allocation; split → West 34th Management Fees ($7,500) + Personal
  - CS allocation: balance carry + adjustment + month allocation; split → CTS Management Fees + Personal
- [ ] **MK** uploads to Drive: `QF Financials/{YYYY}/{Month YYYY} Close/`. Required files:
  - `QuantFi - Profit-Share Allocation [Month YYYY]`
  - `QUANTFI LLC_FS [Month YYYY]` (P&L, BS, CF tabs)
  - `QuantFi - Toggl Report ({Month YYYY})`
  - `QuantFi - Affiliate Commission [Month YYYY]`
  - `QUANTFI LLC_Subscription Expenses [Month YYYY]`
  - `QuantFi - Transaction Inquiry (MM.DD.YYYY)`
  - `QuantFi LLC - Distribution/Contribution [Month YYYY]`
  - `Contractor Payments ({Month YYYY})/` subfolder

## Phase 8 — Review + approval (Day 6)
- [ ] **MK** posts in `#qf-accounting`: *"Attached is the {Month YYYY} Closing Package with estimated payouts for review… Please confirm, or let me know if you have any questions, and I'll schedule a distribution."* — with links to the Drive folder + Profit-Share + FS.
- [ ] **KJ + CS** each review:
  - [ ] FS: revenue trend reasonable, expense lines reasonable
  - [ ] Profit allocation: balance carry from prior month is correct
  - [ ] AR aging: no unexpected balances; deferred revenue (negative balances) looks right per client
  - [ ] Customer P&L: per-client revenue matches expectations
- [ ] **Both confirm "looks good"** in thread

## Phase 9 — Payouts (Day 6–7)
- [ ] **MK** schedules AP payments via Bill.com (contractors, vendors)
- [ ] **MK** schedules owner distributions via Mercury:
  - West 34th (KJ Management Fees) — $7,500
  - CTS Capital (CS Management Fees) — $X,XXX
  - Personal CS distribution
  - Personal KJ distribution
- [ ] **CS + KJ** approve scheduled distributions in Mercury
- [ ] **MK** confirms scheduled in `#qf-accounting`

## Phase 10 — Post-close (the following week)
- [ ] **MK** pulls updated AR Aging Report once payments clear (Tue–Wed) and flags collection follow-ups
- [ ] **KJ + CS**: chase any missing receipts
- [ ] **KJ**: update rate sheet for next month if any rates changed
- [ ] **December close only**: MK begins 1099 prep + tax docs after Dec close lands

---

## Per-client cheat sheet
| Client | Retainer | Pay channel | Notes |
|--------|----------|-------------|-------|
| Berg | $4,000 | Bill.com | Often prepaid; watch deferred. Jon Ong 10% commission |
| BGC | $5,000 | Bill.com | Bumped Feb/Mar to $5k |
| Bottlecapps | varies | Bill.com | Deferred revenue tracking |
| Boundless | $20,000 | Bill.com | Brian Yoon 10% commission |
| Chew | $7,500 | Bill.com | Watch deferred |
| Everbloom | $7,500 base + accruals | Bill.com | Miko 20% profit share + $750 min. **DO NOT bill overage** per CS |
| Fabrik | $2,500 / 10h | Bill.com / UpWork | Jaclyn |
| Hyer | $11,250 + overage | **Mercury direct ("HyerBoots")** | NOT Bill.com — most common double-count risk |
| Konscious | $15,000 flat | **Mercury ("Konscious LLC" / "Enclave LLC")** | Always recognize $15k regardless of hours |
| Red Apple Fireworks | $14,875 + overage | Bill.com | Toggl tags split CFO/Controller/Bookkeeping |
| Treville | $13,500 | Bill.com | Write off above $13.5k unless agreed |
| Trippy Goat | n/a | — | Jake Smith 10% commission |
| Volition | $4,000 | Bill.com | Watch deferred rollover |

## Per-affiliate commission cheat sheet
| Affiliate | Client(s) | Rate | Notes |
|-----------|-----------|------|-------|
| Isaac Strulowitz | Multiple (Everbloom, BGC, Bottlecapps, Berg) | varies | See Toggl Report Affiliate Commission tab |
| Miko Ramljak | Everbloom | $750/mo + 20% profit share | Based on accrued recognized income |
| Jon Ong | Berg | 10% (~$50–$1.6k/mo) | |
| Brian Yoon | Boundless | 10% | |
| Jake Smith | Trippy Goat | 10% | |

## QBO chart of accounts (key categories)
- **Income**: Consulting Revenue
- **COGS**: Cost of labor - COGS
- **Expenses**: Administrative Work · Advertising & marketing (Social media) · Commissions & fees · Contract labor (Upwork fees) · General business expenses (Bank fees, Memberships & subscriptions) · Insurance · Legal & accounting services · Office expenses (Merchant account fees, Office supplies, Software & apps) · Rent · Travel
- **Other Income**: Credit card rewards · Other income
- **Other Expenses**: Management fees CS · Management fees KJ
- **Equity**: Opening balance equity · Owner Distribution CS/KJ · Retained Earnings
- **Other Current Liabilities**: Partner contribution/distribution CS/KJ · Customer deposits

## Watch-outs (silent failure scenarios)
- **Mercury deposit doesn't match a client** → silently dropped from AR
- **Bill.com customer name can't resolve** → recorded as "Unknown"
- **Hyer = Mercury direct** ("HyerBoots"), NOT Bill.com — double-count risk
- **Konscious = always $15k flat** regardless of Toggl hours
- **Everbloom = NO overage billing** per CS standing instruction
- **UpWork charges not in transaction report** (S&M credits) → KJ must categorize manually
- **Receipts >$75 missing** → KJ/CS pull from card and forward to `quantfi+expenses@assist.intuit.com`
- **Toggl billable/non-billable tags wrong** → contractor (e.g., Matheus) needs to fix before accruals are finalized
- **Personal card QF charges not entered in Distribution/Contribution sheet** → revenue/distribution math will be off
- **New Mercury CC not linked to QBO** → transactions won't appear in bank feed
