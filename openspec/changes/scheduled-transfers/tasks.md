# Tasks

> **Phase gate:** each numbered group below is a phase. A phase's last task is always to run its
> Cucumber feature and get it green. Do not start the next phase until that happens — if it fails,
> fix the phase you're on rather than moving forward and coming back later.

> **Implementation detail:** each task names the [design.md](design.md) section it implements.
> Worked-example IDs (H/E/X) come from design.md → Worked Examples.

## 1. Schedule, edit and cancel

- [ ] 1.1 Add migrations `create_scheduled_transfers` and `create_transfer_executions` with every index in design.md → Data Model (amounts as `bigint` cents); migrate and verify both tables and all six indexes appear in `db/schema.rb` · *Data Model*
- [ ] 1.2 Create `ScheduledTransfer` and `TransferExecution` models (statuses, `changeable?` with the 22:00 cut-off, `cancel!`); verify in `rails runner` that `changeable?` is true at 21:59 and false at 22:00 the evening before `due_on` · *State Transitions*
- [ ] 1.3 Create `PaymentDateCalculator` (nominal date for occurrence n with month-end clamp; `due_on` via `BankHolidayCalendar#next_working_day`); verify in `rails runner` that day 31 gives 30 Apr 2027 and that 26 Nov 2026 gives 27 Nov 2026 · *Key Components, E1, E2*
- [ ] 1.4 Create `ScheduleTransferService` (validation rules from the proposal, schedule + first execution in one transaction, `RecordNotUnique` on `request_key` returns the existing schedule); verify that two calls with the same `request_key` return the same record · *Key Components, Transactions*
- [ ] 1.5 Create `ScheduledTransferPolicy` and its `Scope` (holder can view/cancel, only creator can edit, scoped through `current_customer.accounts`); verify in `rails runner` that Dev can `cancel?` but not `update?` Asha's joint-account payment · *Policy*
- [ ] 1.6 Create `ScheduledTransfersController` and routes from design.md → Controllers and Routes; verify `bin/rails routes | grep scheduled` lists all seven · *Controllers and Routes*
- [ ] 1.7 Build the Schedule a payment form, Upcoming payments list and payment detail (Journeys 1 and 2, including the live adjusted-date message and Confirm disabled after first tap); verify both journeys by hand in dev · *UI Changes*
- [ ] 1.8 Update `PayeesController#destroy` to warn and cancel scheduled payments to the payee in one transaction; verify in dev that deleting a payee with two payments shows the warning · *Transactions*
- [ ] 1.9 Add the "Scheduling a payment" section to the customer help pages; verify every message matches the proposal's Behavior Rules verbatim
- [ ] 1.10 Add `features/support/scheduled_transfers.rb` (Asha / Dev / Omar shared setup) and `scheduled_transfer_steps.rb`, then write `features/payments/scheduled_transfers/scheduling.feature` covering worked examples H1, E1, E2, E3, E5, E6, E7, E8, X5, X6. Run `TEST_ENV_NUMBER=$RANDOM bundle exec cucumber features/payments/scheduled_transfers/scheduling.feature` and confirm it passes before starting phase 2

## 2. Overnight run and debit

- [ ] 2.1 Create `ScheduledPaymentRunJob` on `:payments`, subscribed to the EOD completed event, plus the 04:00 safety cron; verify with `EXPLAIN` that the due-executions query uses the `[status, due_on]` index · *Pipeline / Flow, Transactions and Queries*
- [ ] 2.2 Implement `TransferExecution#claim!` as a conditional update from `scheduled` / `awaiting_funds`; verify that two concurrent claims on one execution produce exactly one `true` · *Transactions and Queries*
- [ ] 2.3 Implement `ExecutionRunner#run`: account-row lock, decision flow, ledger posting with the execution's `idempotency_key`, outbox message and audit entry in one transaction, notifications after commit; verify that killing the process after commit loses nothing and a rerun posts nothing · *Decision Flows, Sequence Diagrams*
- [ ] 2.4 Add `include_overdraft:` to `Account#available_balance` (default `true`); verify existing balance features still pass · *Design Decisions 5*
- [ ] 2.5 Create `NextExecutionPlanner` (next sequence from `nominal_on`, ends schedule after the last occurrence); verify that calling it twice creates one next execution · *Key Components*
- [ ] 2.6 Handle network confirm / reject callbacks (reject posts a reversal and fails in one transaction) and create `ExecutionReconcileJob` (every 15 min, query by reference, never resend with a new key); verify with `FakePaymentNetwork` in timeout mode · *Sequence Diagrams*
- [ ] 2.7 Write `features/payments/scheduled_transfers/execution.feature` covering worked examples H2, H3, E9, X2, X4. Run `TEST_ENV_NUMBER=$RANDOM bundle exec cucumber features/payments/scheduled_transfers/execution.feature` and confirm it passes before starting phase 3

## 3. Funds short, retry and notify

- [ ] 3.1 Route insufficient funds on attempt 1 to `awaiting_funds` and create `InsufficientFundsRetryJob` (18:00 cron, attempt 2 fails the payment); verify in `rails runner` with a $300.00 balance · *Decision Flows*
- [ ] 3.2 Add the customer notification templates (paid, waiting for funds, failed, account unavailable) using `CustomerNotifier`, with SMS only on failures and masked account numbers; verify the rendered text matches the proposal verbatim · *Exception Handling and Monitoring*
- [ ] 3.3 Make a failed occurrence still plan the next one for recurring schedules; verify with the H3 rent schedule · *State Transitions*
- [ ] 3.4 Write `features/payments/scheduled_transfers/retry.feature` covering worked examples H4, E4, E10, X1. Run `TEST_ENV_NUMBER=$RANDOM bundle exec cucumber features/payments/scheduled_transfers/retry.feature` and confirm it passes before starting phase 4

## 4. Audit and operations alerting

- [ ] 4.1 Record an `AuditLog` entry on every execution and schedule status change (actor, from, to, reason), shown in the account's audit history; verify the H2 sequence produces exactly two entries · *Exception Handling and Monitoring*
- [ ] 4.2 Alert operations once when the network is unreachable for more than 15 minutes during a run, and when no EOD event has arrived by 04:00; verify with `FakePaymentNetwork` in unreachable mode · *Exception Handling and Monitoring*
- [ ] 4.3 Add the read-only Scheduled payments tab to the back-office customer view; verify a support user cannot see Edit or Cancel · *Policy*
- [ ] 4.4 Write `features/payments/scheduled_transfers/audit.feature` covering worked examples H5, X3. Run `TEST_ENV_NUMBER=$RANDOM bundle exec cucumber features/payments/scheduled_transfers/audit.feature` and confirm it passes
- [ ] 4.5 (QA, not the apply agent) Run the manual QA checklist on a staging build and record the sign-off
