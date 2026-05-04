# QuantFi Monthly Accounting Close — The Actual Process

This is the operating playbook for the QuantFi monthly close. Reconstructed from `#qf-accounting` Slack threads + `QF Financials/{YYYY}/{Month YYYY} Close/` Drive artifacts, then iterated with KJ.

## Cadence
- **Target close: 1–3 business days after month-end**
- Critical path: Toggl/UpWork hours → bank feed categorization → accruals → bills/invoices → FS → distribution
- Bottleneck: contractor Toggl hours must be in by **EOD 1 business day after month-end**

## Operating model
KJ + CS handle most of the close with **AI doing the execution**. MK is pulled in only for QBO entries that genuinely need a bookkeeper's hands; that scope is shrinking over time as AI gains direct access.

Each step below is tagged `AI` (Claude executes), `KJ` / `CS` (decision/approval), or `MK` (bookkeeper task). Each phase ends with explicit **Outputs**.

## Decision audit trail
Each close commits a `closes/{YYYY-MM}-decisions.md` file to this repo capturing: per-client retainer/overage/defer/write-off choices, contractor rate exceptions, late-arriving bills, failed payments and resolutions, anything ad hoc. This is the durable memory of *why* a given close went the way it did.

## Roles
| Role | Owner | Responsibility |
|------|-------|----------------|
| Primary executor | **AI (Claude)** | Pull data, categorize, build sheets, draft accruals, assemble closing package, draft confirmations |
| CFO/Approver | **Kenny Jen (KJ)** | Rate decisions, source-of-truth rate sheet, owner distribution approval, UpWork pulls |
| Partner/Approver | **Christian Sanford (CS)** | Client-side recognize/defer/write-off decisions, distribution adjustments |
| Bookkeeper (assist) | **Michael Kumov (MK, mkumov@tma-finance.com)** | Final QBO entries; scope shrinking |
| Internal admin | **Alyssia** | Subscription consolidation table; QuantFi Internal Toggl hours |

## Systems
- **QBO** — general ledger, bank feeds, invoices, bills (production)
- **Mercury** — Checking (xx2493), Savings (xx6317), Credit cards (••6524 / ••5869 / ••2401 / ••8643)
- **Bill.com** — client invoicing (AR) for all clients
- **Toggl** — time tracking (workspace 8829915, ~32 users)
- **Upwork** — separate vendor billing channel for a subset of contractors
- **Google Drive** — `QF Financials/{YYYY}/{Month YYYY} Close/` per-month working files; standing prepaid amortization sheet lives in `QF Financials/`
- **QBO receipt forwarding** — `quantfi+expenses@assist.intuit.com` (or QBO mobile app)

---

## Phase 0 — Collect necessary info (last week of month)

| # | Step | Owner |
|---|------|-------|
| 1 | **Rate sheet refresh** — contractor cost rates + client billing rates, all confirmed (no yellow cells) | `AI drafts → KJ approves` |
| 2 | **Client engagement status** — new/off-boarded clients, term changes; confirm retainer + overage logic per client this month | `AI drafts → KJ + CS confirm` |
| 3 | **Contractor roster** — new/off-boarded contractors, rate changes, ICA status | `AI drafts → KJ confirms` |
| 4 | **Affiliate commission roster** — new arrangements (Brian Yoon/Boundless, Jake Smith/Trippy Goat type additions) | `KJ` |
| 5 | **Personal card QF charges logged** in `QuantFi LLC - Distribution/Contribution [Month YYYY]` sheet (KJ → West 34th/Novo; CS → CTS/Novo) | `KJ + CS` |

**Outputs:** confirmed rate sheet, confirmed client/contractor/affiliate rosters, completed Distribution/Contribution sheet entries.

## Phase 1 — Toggl + UpWork hours → reconciled accruals (Day 0–1)

| # | Step | Owner |
|---|------|-------|
| 1 | **All contractors log Toggl hours by EOD 1 BD after month-end** | Contractors |
| 2 | **Check Toggl, identify laggards, message them** (Slack DM / nudge) | `AI checks → AI drafts message → KJ approves send` |
| 3 | **Pull Toggl report: by project × by contractor**, including: accumulated hours, **charged rate** (AP), **billed rate** (AR), subtag validity (Red Apple CFO/Controller/Bookkeeping; Miko Hyer/Everbloom split) | `AI` |
| 4 | **Cross-check Toggl rates against the source-of-truth rate sheet**. Surfaces: rate mismatches, partial-month rates (e.g., contractor mid-month start), ad hoc comms / one-off exceptions | `AI flags → KJ confirms` |
| 5 | **Build reconciled accrual totals**: AR per-client (hours × billed rate); AP per-contractor (hours × charged rate) | `AI` |
| 6 | **Pull UpWork transaction report** — hours × rates per UpWork contractor + client allocation per contract description / memo line | `AI` |
| 7 | **Cross-reference UpWork hours vs Toggl** for dual-loggers (e.g., Miko's Hyer/Everbloom split). UpWork-only contractors (Alyssia, Khadija, Kelli, Amanda) → UpWork is source of truth | `AI flags → KJ resolves` |
| 8 | **Add UpWork totals to reconciled AR + AP** | `AI` |

### Rate reconciliation rules
- **Rate mismatch**: rate sheet wins by default, but AI must surface the mismatch to KJ for confirmation before applying.
- **Partial-month rate** (mid-month start/end, mid-month rate change): AI proposes proration, KJ confirms.
- **Internal hours** (Toggl "QuantFi - Internal" project: Alyssia, Braedon, Vadym, Mitch, Michael Kumov): AP cost only, booked as overhead. **No AR side.**
- **Owner hours** (KJ, CS): logged at client's billable rate. **AR side only — no AP expense.**

**Outputs:** reconciled AR totals per client + AP totals per contractor (Toggl + UpWork merged), rate mismatches/exceptions resolved.

## Phase 2 — Outstanding bills + bank feeds + categorization (Day 1–3, parallel)

| # | Step | Owner |
|---|------|-------|
| 1 | **Sweep email + Slack for outstanding bills/invoices** received during the month not yet logged in Bill.com / QBO. Common: Foley legal, Fintalent (Matheus, Alexis, Boundless), Philip Soriano, Jake Smith referral | `AI sweeps → drafts list → KJ + CS confirm + categorize` |
| 2 | **Pull Mercury data** for each account: <br>• **Mercury Checking (xx2493)** <br>• **Mercury Savings (xx6317)** <br>• **Mercury CC ••6524** <br>• **Mercury CC ••5869** <br>• **Mercury CC ••2401** <br>• **Mercury CC ••8643** <br>(any new card must be linked to QBO + Mercury API access first) | `AI` |
| 3 | **Reconcile Mercury balance vs QBO Mercury account balance** for each account — catch missing/mismatched transactions | `AI` |
| 3a | **Reconcile UpWork CC charges in Mercury vs UpWork transaction report total** — catches missed allocations or unfamiliar UpWork charges (e.g., S&M connection credits) | `AI` |
| 4 | **Auto-categorize Mercury transactions** using prior-month rules + merchant + memo (`Software & apps`, `Office expenses`, `OpEx (Customer Success)`, `Computer (Reimbursable from Client)`, `Personal` → routes to Distribution/Contribution sheet) | `AI drafts` |
| 5 | **Flag ambiguous transactions** (Transaction Inquiry list) | `AI` |
| 6 | **Resolve ambiguous transactions** | `KJ + CS` |
| 7 | **Receipt audit** — list >$75 transactions missing receipts; chase | `AI lists → KJ + CS forward to `quantfi+expenses`` |
| 8 | **Bill.com → QBO sync** (paid invoices recognized; received payments matched) | `MK` |
| 9 | **Post categorized transactions to QBO** | `MK` *(AI candidate once QBO write access granted)* |
| 10 | **Formal QBO bank reconciliation** for each Mercury account — mark cleared txns, match deposits, ending balance reconciled. Repeat for Checking, Savings, and each CC. | `MK` |

**Outputs:** all Mercury txns categorized + posted to QBO, formal bank rec complete for every account, outstanding bills (email/Slack) logged, Transaction Inquiry resolved.

## Phase 3 — AR/AP review + per-client accruals + commissions (Day 2–4)

| # | Step | Owner |
|---|------|-------|
| 1 | **Pull AR aging from QBO** — every client, every open balance | `AI` |
| 2 | **AR balance actions per client** (drives the retainer/overage decision next): <br>• **Write-offs** — stale balances >60 days, deemed uncollectible (e.g., Fras, PetFriendly historically) <br>• **Refunds** — overpayments to be returned <br>• **Credit memos** — billing errors, agreed reductions <br>• **Deferred revenue adjustments** — recognize/release per client (e.g., Volition expiring hours, Trait deposits) | `AI proposes → KJ + CS decide` |
| 3 | **AI builds per-client accrual table** using Phase 1 reconciled AR/AP totals: <br>• **Beginning AR balance** from QBO (negative = deferred revenue, positive = unpaid overage) <br>• Apply Step 2 adjustments (write-offs, refunds, credits, deferred shifts) <br>• This month's accrual (Phase 1 output) <br>• **Ending AR balance pre-retainer** = beginning (post-adjustments) + accrual — drives retainer/overage decision <br>• Per-client AP totals | `AI` |
| 4 | **AI proposes per-client action** using cheat-sheet rules + prior-month patterns: <br>• **Retainer this month?** Y/N — based on ending AR pre-retainer (skip if heavily prepaid) <br>• **Overage?** Y/N + amount — apply standing rules (Everbloom always N; Treville write off >$13.5k; Konscious always $15k) <br>• **Defer / recognize / write off?** | `AI drafts` |
| 5 | **AI calculates affiliate commissions** (formulaic): <br>• Miko: 20% of recognized Everbloom + $750 min <br>• Jon Ong: 10% of Berg <br>• Brian Yoon: 10% of Boundless <br>• Jake Smith: 10% of Trippy Goat <br>• Isaac: per-client mix per ledger | `AI` |
| 6 | **Pull AP aging from QBO** — review for stale open vendor balances; decide clean-up actions (write-off, follow-up, etc.) | `AI proposes → KJ + CS decide` |
| 7 | **KJ + CS review and approve all AR/AP actions + per-client accrual decisions** in `#qf-accounting` thread or working sheet. **All decisions logged to `closes/{YYYY-MM}-decisions.md`.** | `KJ + CS` |
| 8 | **Heads-up to clients on overage / retainer changes** (now we know the number) — common: Red Apple, Treville | `AI drafts → KJ sends` |
| 9 | **AI builds the full Toggl Report**: `QuantFi - Toggl Report ({Month YYYY}).xlsx` <br>• Per-client: Sum of Bill Coef, Total Contractor Cost, Hours (Billables), Total Billable to Customers <br>• Accrual Summary: Beginning Balance, AR Actions Applied, Payments, Balance Invoice, Comment <br>• Per-affiliate commission tracking <br>• Per-large-client detail tabs (Everbloom, Chew, Berg, Boundless, Trippy Goat) — month-by-month rollforward | `AI` |

**Outputs:** AR/AP actions decided + logged, retainer/overage decisions per client, affiliate commissions calculated, full Toggl Report. Decisions log committed to repo.

## Phase 4 — Post bills, invoices, JEs, payouts (Day 3–5; Tracks A–D run in parallel)

### Track A: Client invoicing (AR via Bill.com)
| # | Step | Owner |
|---|------|-------|
| A1 | **AI drafts Bill.com invoices** for retainer + overage per client (using Phase 3 decisions) — all clients via Bill.com | `AI drafts → MK posts` |
| A2 | **AI drafts credit memos / refunds** for clients per Phase 3 #2 decisions | `AI drafts → MK posts` |

### Track B: Contractor + affiliate payouts (AP via Mercury, except UpWork) — runs parallel with Tracks A/C/D and with Phase 5/6
| # | Step | Owner |
|---|------|-------|
| B1 | **Tag payment path per recipient**: <br>• **UpWork contractors** (Alyssia, Khadija, Kelli, Amanda, etc.) → paid automatically via UpWork. No QF action. <br>• **Non-UpWork contractors** (Patrick, John, Jacob, Byoon, Brian Stanley, Leanna, Austin, Matheus, etc.) → Mercury direct <br>• **Affiliate commissions** (Isaac, Miko, Jon Ong, Brian Yoon, Jake Smith) → Mercury direct | `AI tags` |
| B2 | **AI drafts confirmation summary** per recipient: hours × billable rate (contractors) or commission calc (affiliates) | `AI drafts` |
| B3 | **Send confirmation to contractor / affiliate** (Slack/email) | `AI sends → KJ approves outbound` |
| B4 | **2 BD response window**, then default-pay (unless contractor disputes or KJ overrides) | n/a |
| B5 | **Schedule Mercury payments** to confirmed contractors + affiliates | `MK schedules → KJ + CS approve in Mercury` |
| B6 | **Failed payment escalation** — if a Mercury payment bounces (bad routing, closed account, etc.), AI surfaces in `#qf-accounting`, KJ + CS resolve with the recipient (request updated bank info, retry). Log in decisions file. | `AI flags → KJ + CS resolve` |

### Track C: Recurring journal entries
| # | Step | Owner |
|---|------|-------|
| C1 | **Maintain prepaid amortization standing sheet** (lives in `QF Financials/` Drive) — every prepaid being amortized: original amount, start date, term, monthly amount, balance remaining. New prepaids added when categorized in Phase 2 | `AI maintains` |
| C2 | **Generate this month's recurring JE list** from the standing sheet (e.g., Delve $800/mo, business insurance ~$423/mo) | `AI drafts` |
| C3 | **Cross-check against QBO** — confirm no JE duplicated, none missed last month | `AI verifies` |
| C4 | **Post recurring JEs to QBO** | `MK posts` *(AI candidate later)* |

### Track D: Distribution / Contribution
| # | Step | Owner |
|---|------|-------|
| D1 | **AI drafts Distribution/Contribution JEs** from the sheet (entered in Phase 0 #5): <br>• `Management fees CS` / `Management fees KJ` (Other Expense) for West 34th / CTS portions <br>• `Partner contribution/distribution CS/KJ` (Other Current Liability) for direct biz expenses paid personally <br>• `Owner Distribution CS/KJ` (Equity) for distributions | `AI drafts → MK posts` |

**Outputs:** all client invoices + credit memos in Bill.com, contractor + affiliate confirmations sent (and payments scheduled when 2 BD elapses), recurring JEs posted, Distribution/Contribution JEs posted. Failed payments logged + escalated.

## Phase 5 — Closing package assembly (Day 5–6)

| # | Step | Owner |
|---|------|-------|
| 1 | **Pull final QBO reports** for `{Month YYYY}`: P&L, Balance Sheet, Cash Flow, AR Aging Detail, AP Aging Detail, Income Statement by Customer | `AI` |
| 2 | **Draft summary memo** — top-of-package narrative: revenue trend MoM, expense changes, AR/AP aging notable items, distribution math summary, AR actions taken (write-offs/credits/refunds), anomalies surfaced during close | `AI drafts` |
| 3 | **Build `QuantFi - Profit-Share Allocation [Month YYYY]`**: <br>• Net Profit (from P&L) <br>• Net Profit Split (50/50) <br>• KJ allocation: balance carry + adjustment + month allocation; split → West 34th Management Fees ($7,500) + Personal <br>• CS allocation: balance carry + adjustment + month allocation; split → CTS Management Fees + Personal | `AI drafts` |
| 4 | **Build `QUANTFI LLC_Subscription Expenses [Month YYYY]`** — categorize SaaS spend from Mercury CC | `AI drafts` |
| 5 | **Build `QuantFi - Affiliate Commission [Month YYYY]`** | `AI` |
| 6 | **Finalize `QuantFi - Transaction Inquiry (MM.DD.YYYY)`** with any unresolved items | `AI` |
| 7 | **Upload everything to `QF Financials/{YYYY}/{Month YYYY} Close/`** | `AI` |

**Required files in the closing package folder:**
- Summary Memo
- `QuantFi - Profit-Share Allocation [Month YYYY]`
- `QUANTFI LLC_FS [Month YYYY]` (P&L, BS, CF tabs — pulled from QBO)
- `QuantFi - Toggl Report ({Month YYYY})`
- `QuantFi - Affiliate Commission [Month YYYY]`
- `QUANTFI LLC_Subscription Expenses [Month YYYY]`
- `QuantFi - Transaction Inquiry (MM.DD.YYYY)`
- `QuantFi LLC - Distribution/Contribution [Month YYYY]`
- `Contractor Payments ({Month YYYY})/` subfolder

**Outputs:** complete closing package in Drive folder, summary memo, all FS pulled.

## Phase 6 — Review + owner distributions (Day 6)

| # | Step | Owner |
|---|------|-------|
| 1 | **AI posts closing package + summary memo to `#qf-accounting`** with links to Drive + Profit-Share + FS, with the standing message: *"Attached is the {Month YYYY} Closing Package with estimated payouts for review… Please confirm, or let me know if you have any questions."* | `AI` |
| 2 | **KJ review checklist**: FS trend, profit allocation balance carry, AR aging (deferred/overage per client), Customer P&L | `KJ` |
| 3 | **CS review checklist**: same | `CS` |
| 4 | **Both confirm "looks good"** in thread → unlocks distribution scheduling | `KJ + CS` |
| 5 | **Schedule owner distributions in Mercury**: <br>• West 34th (KJ Management Fees) — typically $7,500 <br>• CTS Capital (CS Management Fees) <br>• KJ Personal (remainder of KJ allocation) <br>• CS Personal (remainder of CS allocation) | `MK schedules` *(AI candidate)* |
| 6 | **KJ approves his distributions in Mercury** (West 34th + Personal) | `KJ` |
| 7 | **CS approves his distributions in Mercury** (CTS + Personal) | `CS` |

**Outputs:** package approved by both partners, owner distributions scheduled and approved.

## Phase 7 — Post-close (the following week)

| # | Step | Owner |
|---|------|-------|
| 1 | **Pull updated AR Aging Report** once Bill.com payments clear (typically Tue–Wed) — flag collection follow-ups | `AI` |
| 2 | **Subscription expense rate refresh** — categorize the actual SaaS charges that hit; update Alyssia's standing subscription table | `AI drafts → Alyssia confirms` |
| 3 | **Receipts cleanup** — chase any missing receipts flagged in Phase 2 | `KJ + CS` |
| 4 | **Update rate sheet for next month** if any rates changed during close | `KJ` |
| 5 | **Playbook updates** — any process learnings from this close get committed back to `monthly-close-process.md` | `AI drafts → KJ approves` |
| 6 | **December only**: 1099 prep + tax docs | `MK` |

**Outputs:** AR follow-ups in motion, subscription table refreshed, receipts cleaned, playbook updated for next close.

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
| Hyer | $11,250 + overage | Bill.com | Mercury deposit shows as "HyerBoots" — that's Hyer's payment processor, not a separate channel |
| Konscious | $15,000 flat | Bill.com | Mercury deposit shows as "Konscious LLC" / "Enclave LLC". Always recognize $15k regardless of hours |
| Red Apple Fireworks | $14,875 + overage | Bill.com | Toggl tags split CFO/Controller/Bookkeeping |
| Treville | $13,500 | Bill.com | Write off above $13.5k unless agreed |
| Trippy Goat | n/a | — | Jake Smith 10% commission |
| Volition | $4,000 | Bill.com | Watch deferred rollover |

## Per-affiliate commission cheat sheet
| Affiliate | Client(s) | Rate | Notes |
|-----------|-----------|------|-------|
| Isaac Strulowitz | Multiple (Everbloom, BGC, Bottlecapps, Berg) | varies | See Toggl Report Affiliate Commission tab |
| Miko Ramljak | Everbloom | $750/mo + 20% profit share | Based on accrued recognized income. Also UpWork contractor (separate path). |
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
- **Konscious = always $15k flat** regardless of Toggl hours
- **Everbloom = NO overage billing** per CS standing instruction
- **UpWork charges not in transaction report** (S&M credits) → KJ must categorize manually
- **Receipts >$75 missing** → KJ/CS pull from card and forward to `quantfi+expenses@assist.intuit.com`
- **Toggl billable/non-billable tags wrong** → contractor (e.g., Matheus) needs to fix before accruals are finalized
- **Personal card QF charges not entered in Distribution/Contribution sheet** → revenue/distribution math will be off
- **New Mercury CC not linked to QBO** → transactions won't appear in bank feed
- **Rate sheet ↔ Toggl rate mismatch** → rate sheet wins by default but must be confirmed by KJ before applied
- **Mid-month contractor start/end** → partial-month proration must be confirmed by KJ in Phase 1
- **Failed Mercury payment** → escalate immediately, don't silently retry
