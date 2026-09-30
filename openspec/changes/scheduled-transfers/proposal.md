# Scheduled & Recurring Transfers — Proposal

> **Audience:** Business team, product, risk, operations, QA, customer support — non-technical.
> **Companion documents:** [Business rules](business-rules.md) · [Design](design.md)

> **Planning phase — no code changes.** This document proposes and explains the change. Do not
> edit application code while writing it; implementation happens later, in the tasks phase.

---

## Why

Customers can only send money "now". To pay rent on the 1st or repay a friend on payday they have
to remember, log in on the day and do it by hand, or visit a branch to set up a paper standing
order that they then cannot see or change in the app. Missed rent and late-fee complaints are among
the most common reasons customers call support, and competitors already let customers schedule
payments in the app.

## What Changes

Customers can schedule a transfer to a saved payee for a future date, once or on a repeating
schedule, from the mobile and web app. The bank makes the payment on the day, tells the customer
how it went, and never takes the account into overdraft to do it.

- A customer can schedule a **one-off** or **recurring** (weekly or monthly) transfer to a saved
  payee.
- Payments due on a **weekend or bank holiday**, or on a day a month doesn't have, move to a
  sensible date, and the customer sees it before confirming.
- If there is **not enough money** on the day, the bank tries again later that day, then tells the
  customer. **The overdraft is never used.**
- A customer can **edit or cancel** a scheduled payment until a cut-off before it is due.
- Every scheduled payment appears in a new **Upcoming payments** list.

The exact rules, decisions and customer wording are in [business-rules.md](business-rules.md)
(BR-1 to BR-17, D-1 to D-10, MSG-1 to MSG-12).

### Flow Diagram

**Paying rent on the 1st — before:** the customer has to remember, or go to a branch.

```mermaid
flowchart LR
    A["Rent due on the 1st"] --> B{"Customer remembers?"}
    B -->|yes| C["Logs in on the day<br/>and pays by hand"]
    B -->|no| D["Payment missed<br/>late fee, calls support"]
    A -.-> E["Or: branch visit to set up<br/>a paper standing order"]
    classDef manual fill:#fdecea,stroke:#c62828,color:#000
    class C,D,E manual
```

**Paying rent on the 1st — after:** set up once in the app; the bank does the rest.

```mermaid
flowchart LR
    A["Customer schedules rent<br/>monthly on the 1st"] --> B["Bank runs it on the day<br/>(next working day if a holiday)"]
    B --> C{"Enough money?"}
    C -->|yes| D["Paid, receipt sent"]
    C -->|no| E["Retry later<br/>the same day"]
    E -->|now enough| D
    E -->|still short| F["Failed, customer told<br/>overdraft never used"]
    classDef new fill:#d4edda,stroke:#2e7d32,color:#000
    class A,B,E new
```

---

## User Stories

### US-1: Schedule a one-off payment

**As a** retail customer,
**I want** to schedule a transfer to a saved payee for a future date,
**So that** I don't have to remember to do it on the day.

**Acceptance criteria:**
- From **Transfers → Schedule a payment**, the customer picks a from-account, a saved payee, an
  amount and a date (BR-1, BR-2, BR-3).
- If the date is a weekend or bank holiday, the form shows the adjusted date before the customer
  confirms (BR-5, MSG-1).
- After confirming, the customer sees a confirmation with a reference number (MSG-11), and the
  payment appears in **Upcoming payments**.
- Tapping **Confirm** twice creates only one scheduled payment (BR-17).

### US-2: Schedule a recurring payment

**As a** retail customer,
**I want** to pay the same payee the same amount every week or month,
**So that** regular bills like rent are paid without me.

**Acceptance criteria:**
- The customer chooses **Weekly** or **Monthly**, a first payment date, and an end: "After N
  payments", "On a date", or "Until I cancel".
- A monthly payment on the 29th–31st follows BR-6; the form says so (MSG-2).
- **Upcoming payments** shows the next payment date for each recurring schedule.

### US-3: Change or cancel a scheduled payment

**As a** retail customer,
**I want** to edit or cancel a payment before it goes,
**So that** I stay in control if my plans change.

**Acceptance criteria:**
- **Edit** and **Cancel** are available until the cut-off (BR-10); after it, the buttons are
  replaced by an explanation (MSG-6).
- Cancelling a recurring schedule asks whether to cancel only the next payment or all future
  payments (MSG-12).

### US-4: Be told what happened

**As a** retail customer,
**I want** to know when a scheduled payment is made or fails,
**So that** I am never surprised by my balance or by an unpaid bill.

**Acceptance criteria:**
- The customer is told about every outcome (paid, waiting for funds, failed), and failures also
  arrive by SMS (BR-15; wording MSG-3, MSG-4, MSG-9, MSG-10).

### US-5: Joint accounts

**As a** joint account holder,
**I want** to see and stop payments my co-holder scheduled from our joint account,
**So that** neither of us is surprised by money leaving it.

**Acceptance criteria:**
- Both holders see all scheduled payments from the joint account.
- Either holder can cancel. Only the holder who created a payment can edit it (BR-11).

---

## UI Behavior

### Transfers → Schedule a payment

```
┌─────────────────────────────────────────────────────┐
│  Schedule a payment                              ✕  │
│                                                     │
│  From      [ Everyday Account ••4821  $2,500.00 ▾]  │
│  To        [ Ravi Kumar ••7730                  ▾]  │
│  Amount    [ $400.00                             ]  │
│  Repeat    ( ) Once  (•) Monthly  ( ) Weekly        │
│  First on  [ 31 Oct 2026                         ]  │
│  ⓘ Paid on the last day of shorter months           │
│  Ends      (•) Until I cancel ( ) After [ ] ( ) On  │
│                                                     │
│  ⓘ 31 Oct is a Saturday. This payment will be made  │
│    on Mon 2 Nov 2026, the next working day.         │
│                                                     │
│                      [ Cancel ]  [ Review ]         │
└─────────────────────────────────────────────────────┘
```

**Behavior notes:**
- Only savings and current accounts the customer holds (sole or joint) appear under **From**.
- Only saved, verified payees appear under **To**.
- **Review** shows a summary; **Confirm** on the summary schedules the payment. Confirm is disabled
  after the first tap until the response arrives.

### Transfers → Upcoming payments

```
┌─────────────────────────────────────────────────────┐
│  Upcoming payments                                  │
│                                                     │
│  Mon 2 Nov   Ravi Kumar        $400.00   Monthly  › │
│  Mon 16 Nov  City Water        $62.40    Once     › │
│  Tue 1 Dec   Ravi Kumar        $400.00   Monthly  › │
│                                                     │
│  Past                                               │
│  Thu 1 Oct   Ravi Kumar        $400.00   Paid     › │
│  Tue 15 Sep  Gym Co            $45.00    Failed   › │
└─────────────────────────────────────────────────────┘
```

**Behavior notes:**
- Opening a payment shows its details, status history, and **Edit** / **Cancel** before the cut-off.
- A failed payment shows the reason in plain words.

---

## What This Feature Does NOT Cover

| Excluded | Covered In |
|---|---|
| International and cross-currency transfers | Later phase. v1 is domestic only |
| Scheduling from a credit card account | Not planned. Savings and current accounts only |
| Variable amounts ("pay my full card bill") | Later phase. Fixed amounts only |
| Business and corporate accounts | Separate corporate-banking change |
| Editing a payment that is already processing | Not possible. Cancel only before the cut-off |
| Migrating existing branch standing orders into the app | Later phase. They keep running as today |
| Payments to a payee who is not yet saved | Not planned. The customer saves the payee first |

---

## Business Rules and Decisions

Validation rules, behaviour rules, the 10 resolved decisions and all customer wording live in
[business-rules.md](business-rules.md).

---

## Capabilities

### New Capabilities

- `scheduled-transfers`: customers schedule one-off and recurring transfers to saved payees; the
  bank executes them on the (working) payment date, retries once when funds are short, and
  notifies the customer of each outcome.

### Modified Capabilities

None.

---

## Impact

- **Mobile and web app:** new Schedule a payment form and Upcoming payments list under Transfers.
- **Payees:** deleting a payee now warns about and cancels scheduled payments to them.
- **Overnight processing:** a new payment run after the end-of-day batch, and a same-day retry run.
- **Notifications:** new push and SMS messages using the existing notification service.
- **Operations:** a new alert when the payment network is unavailable during a run.
- **Audit:** every scheduled payment status change appears in the account audit history.

---

## Benefits

1. Customers stop missing rent and bill payments because they forgot.
2. Customers never slip into overdraft fees because of a scheduled payment.
3. Customers can see and control every future payment in one place.
4. Fewer branch visits for standing orders and fewer "missed payment" support calls.

---

## FAQ

| Question | Answer |
|---|---|
| Who can use this? | Retail customers with a savings or current account, in the mobile and web app |
| Will a scheduled payment ever put me into overdraft? | No. If the balance isn't enough, the payment waits, retries later that day, then fails (BR-7, BR-8) |
| When exactly does the money leave? | Early on the payment date, after the bank's overnight processing |
| Can support staff see scheduled payments? | Yes, read-only, in the existing customer view in the back office |
| What happens to my existing branch standing orders? | Nothing. They keep running as today |
| Is there a fee? | No |
| What if the payment network is down? | Payments wait and are sent when it recovers. Operations are alerted; customers are told if a payment can't be made that day |

---

## Design Principle for the Next Phase

Keep it DRY and simple: reuse the existing ledger posting, payment network client, bank holiday
calendar, notification service and audit log. The design must deal explicitly with the risks this
proposal creates:

- **Double debits.** A run, a retry and a reconciliation can all touch the same payment.
- **Collision with end-of-day processing.** The payment run must not overlap interest posting.
- **Timeouts after a debit.** The customer's money must never be lost or taken twice.
