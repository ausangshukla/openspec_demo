# Scheduled & Recurring Transfers — Business Rules and Decisions

> **Audience:** Product, risk, compliance, operations, QA and support — non-technical.
> **Companion documents:** [Proposal](proposal.md) · [Design](design.md)

> **Planning phase — no code changes.** This is the single place where the business's rules,
> decisions and customer wording for this change live. The proposal stays high level and refers
> here by ID. The design's worked examples cite these rule IDs, and every rule is proven by at
> least one of them.

---

## Contents

- [Rule Catalogue](#rule-catalogue)
- [Decisions](#decisions)
- [Input Validation](#input-validation)
- [Customer Wording](#customer-wording)
- [Open Questions](#open-questions)
- [Sign-off](#sign-off)

---

## Rule Catalogue

| ID | Rule | Category | Owner | Source | Wording |
|---|---|---|---|---|---|
| BR-1 | A payment can only be scheduled from a savings or current account the customer holds (sole or joint) that is open. | Eligibility | Product | Retail accounts policy | — |
| BR-2 | A payment can only be scheduled to a saved, verified payee of the customer. | Eligibility | Risk | Payee verification policy | — |
| BR-3 | The first payment date is between tomorrow and 365 days ahead. | Timing | Product | Product decision | — |
| BR-4 | A schedule is once, weekly or monthly, and ends after 2–120 payments, on a date, or when cancelled. | Timing | Product | Product decision | — |
| BR-5 | A payment due on a weekend or bank holiday is made on the next working day, and the customer sees the adjusted date before confirming. | Timing | Operations | D-1 | MSG-1 |
| BR-6 | A monthly payment set for the 29th–31st is made on the last day of shorter months, and returns to its original day in longer months. | Timing | Product | D-2 | MSG-2 |
| BR-7 | A scheduled payment never uses the overdraft: only the balance excluding any overdraft counts. | Funds | Risk | Lending policy; D-3 | — |
| BR-8 | If the balance does not cover a payment at the morning run, it is retried once at 6 pm the same day, then fails. | Funds | Product | D-3 | MSG-3, MSG-4 |
| BR-9 | The amount is above $0.00, has at most 2 decimal places, and is within the daily transfer limit, checked when scheduling and again on the payment date. | Limits | Risk | Transfer limits policy; D-7 | MSG-5 |
| BR-10 | A customer can edit or cancel a payment until 10 pm the evening before it is due; after that it cannot be changed. | Changes | Operations | D-4 | MSG-6 |
| BR-11 | Every holder of a joint account sees its scheduled payments; any holder can cancel one; only the holder who created it can edit it. | Changes | Product | D-6 | MSG-7 |
| BR-12 | Deleting a payee that has scheduled payments warns the customer first, then cancels those payments. | Changes | Product | D-5 | MSG-8 |
| BR-13 | One failed payment in a recurring schedule does not stop later payments. | Outcomes | Product | D-8 | — |
| BR-14 | A payment whose account is frozen or closed on the payment date fails, and the customer is told. | Outcomes | Risk | Account restrictions policy | MSG-9 |
| BR-15 | The customer is notified of every outcome by push (paid, waiting for funds, failed); failures also go by SMS. | Outcomes | Product | D-9 | MSG-3, MSG-4, MSG-9, MSG-10 |
| BR-16 | Every status change of a scheduled payment is recorded in the account's audit history. | Audit | Compliance | Record-keeping policy | — |
| BR-17 | Confirming the same payment twice creates only one scheduled payment. | Eligibility | Product | Product decision | MSG-11 |

---

## Decisions

| ID | Question | Decision | Rationale | Alternatives rejected | Decided by |
|---|---|---|---|---|---|
| D-1 | Due date is a weekend or bank holiday? | Next working day, shown before confirming | Payments don't settle on non-working days; customers must know the real date | Previous working day: takes money earlier than asked | Product, Operations |
| D-2 | Monthly on the 29th–31st? | Last day of shorter months, back to the original day after | Customers expect "month-end rent" to land every month | Skip the month: a missed rent payment | Product |
| D-3 | Not enough money on the day? | One retry at 6 pm the same day, then fail. The overdraft is never used | Many salaries arrive during the day; using the overdraft would create borrowing and fees the customer didn't ask for | Use the arranged overdraft: rejected by Risk as unconsented borrowing | Risk, Product |
| D-4 | Cut-off for changes? | 10 pm the evening before the payment date | End-of-day processing starts at 10:30 pm; later changes can't be guaranteed | Until the run starts: cancellation could silently fail | Operations |
| D-5 | Payee deleted with payments pending? | Warn first; confirming cancels those payments | Customers shouldn't lose payments without knowing | Block payee deletion: poor experience | Product |
| D-6 | Joint accounts: who can change a payment? | Any holder can cancel; only the creator can edit | Either holder must be able to stop money leaving; only the creator should redirect it | Any holder edits: one holder could redirect the other's payment | Product, Risk |
| D-7 | When is the daily limit checked? | When scheduling and again on the payment date | Limits can change between scheduling and payment | Only when scheduling: a lowered limit would be bypassed | Risk |
| D-8 | One failure in a recurring schedule? | Later payments continue | Stopping silently would miss the next rent | Suspend after a failure: customer may not notice | Product |
| D-9 | How are customers notified? | Push for every outcome; SMS added for failures | Failures need guaranteed reach; successes don't warrant SMS cost | Email only: slower, often unread | Product, Operations |
| D-10 | Existing branch standing orders? | Unchanged; not shown in the app in v1 | Migration needs data clean-up first | Migrate now: risk of duplicate payments | Operations |

---

## Input Validation

| Field | Rule | Default | Message |
|---|---|---|---|
| From account | BR-1 | The customer's primary account | — |
| Payee | BR-2 | — | — |
| Amount | BR-9 | — | MSG-5 |
| First payment date | BR-3; adjusted per BR-5 | Tomorrow | MSG-1 |
| Repeat | Once, Weekly or Monthly (BR-4) | Once | MSG-2 when a monthly day is 29–31 |
| Ends (recurring) | After 2–120 payments, on a date after the first payment, or until cancelled (BR-4) | Until cancelled | — |
| Reference | Optional, up to 18 characters, letters, numbers and spaces | Empty | — |

---

## Customer Wording

| ID | Shown when | Exact text | Channel |
|---|---|---|---|
| MSG-1 | The chosen date is a weekend or bank holiday | "This payment will be made on {date}, the next working day." | Form |
| MSG-2 | A monthly day of 29–31 is chosen | "Paid on the last day of shorter months" | Form |
| MSG-3 | Not enough money at the morning run | "We couldn't make your {amount} payment to {payee} yet because there isn't enough money in your account. We'll try again at 6 pm." | Push |
| MSG-4 | Still not enough at the retry | "Your {amount} payment to {payee} couldn't be made because there wasn't enough money in your account." | Push + SMS |
| MSG-5 | Amount above the daily limit | "This is more than your daily transfer limit of {limit}." | Form |
| MSG-6 | Edit or cancel after the cut-off | "This payment is being processed and can no longer be changed." | Screen |
| MSG-7 | A joint holder tries to edit a payment someone else created | "Only the person who set up this payment can change it." | Screen |
| MSG-8 | Deleting a payee with scheduled payments | "You have {n} scheduled payments to {payee}. Deleting this payee will cancel them." | Dialog |
| MSG-9 | Account frozen or closed on the payment date | "Your {amount} payment to {payee} couldn't be made. Please contact us." | Push + SMS |
| MSG-10 | Payment made | "Your payment of {amount} to {payee} has been made." | Push |
| MSG-11 | Payment confirmed | "Payment scheduled" with the reference number | Screen |
| MSG-12 | Cancelling a recurring schedule | "Cancel only the next payment, or all future payments?" | Dialog |

---

## Open Questions

| # | Question | Owner | Status |
|---|---|---|---|
| 1 | Remind customers the day before a large payment? | Product | Deferred to a notification-preferences change |

---

## Sign-off

| Role | Name | Date | Decision |
|---|---|---|---|
| Product | | | |
| Risk | | | |
| Compliance | | | |
| Operations | | | |
