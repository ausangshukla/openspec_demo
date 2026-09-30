# Spec Delta

## Purpose

Lets retail customers schedule one-off and recurring transfers to saved payees, and has the bank
execute them on the working payment date without ever using the overdraft or debiting twice.

## ADDED Requirements

### Requirement: Schedule a transfer
The system SHALL let a customer schedule a one-off or recurring (weekly or monthly) transfer from a
savings or current account they hold to a saved, verified payee, for a first payment date between
tomorrow and 365 days ahead. The system SHALL create exactly one scheduled payment per confirmed
request.

#### Scenario: One-off transfer scheduled
- **WHEN** a customer confirms a one-off transfer with a valid account, payee, amount and date
- **THEN** a scheduled payment is created with status "scheduled"
- **AND** the customer sees "Payment scheduled" with a reference number
- **AND** the payment appears in Upcoming payments

#### Scenario: Recurring monthly transfer scheduled
- **WHEN** a customer confirms a monthly transfer that runs until cancelled
- **THEN** a recurring schedule is created and its first payment is listed in Upcoming payments

#### Scenario: Duplicate confirmation
- **WHEN** the same confirmation is submitted twice
- **THEN** only one scheduled payment exists

#### Scenario: Amount above daily limit
- **WHEN** a customer enters an amount above their daily transfer limit
- **THEN** nothing is scheduled
- **AND** the customer sees "This is more than your daily transfer limit of {limit}."

### Requirement: Working-day adjustment
The system SHALL make a payment due on a weekend or bank holiday on the next working day, and a
monthly payment due on a day the month does not have on the last day of that month. The adjusted
date SHALL be shown to the customer before they confirm.

#### Scenario: Due date is a bank holiday
- **WHEN** the chosen date is a bank holiday
- **THEN** the payment is made on the next working day
- **AND** the form shows "This payment will be made on {date}, the next working day." before confirming

#### Scenario: Monthly on the 31st in a 30-day month
- **WHEN** a monthly payment set for the 31st falls due in a 30-day month
- **THEN** it is made on the last day of that month

### Requirement: Execute on the payment date
The system SHALL execute each due payment once, after the end-of-day batch completes, by debiting
the from-account and sending the payment to the payment network. The system SHALL never debit the
same payment twice.

#### Scenario: Payment made
- **WHEN** a payment is due and the balance covers it
- **THEN** the account is debited, the payment is sent, and on confirmation its status is "completed"
- **AND** the customer receives "Your payment of {amount} to {payee} has been made."

#### Scenario: Recurring schedule rolls forward
- **WHEN** a payment from a recurring schedule completes or fails
- **THEN** the next payment of that schedule is due on its next date

#### Scenario: Run repeated
- **WHEN** the payment run is started a second time for the same day
- **THEN** no payment is debited a second time

### Requirement: Insufficient funds
The system SHALL NOT use an overdraft for a scheduled payment. When the available balance, excluding
any overdraft, does not cover a due payment, the system SHALL retry once at 8 pm the same day and
SHALL fail the payment if the balance still does not cover it.

#### Scenario: Retry succeeds
- **WHEN** the morning run finds too little money and enough arrives before 8 pm
- **THEN** the customer is told "We'll try again at 8 pm"
- **AND** the 8 pm retry makes the payment

#### Scenario: Still short after retry
- **WHEN** the balance still does not cover the payment at 8 pm
- **THEN** the payment status is "failed"
- **AND** the customer receives a push notification and SMS explaining there wasn't enough money

#### Scenario: Overdraft never used
- **WHEN** the balance covers the payment only by using the overdraft
- **THEN** the payment is treated as insufficient funds

### Requirement: Change or cancel before the cut-off
The system SHALL let a customer edit or cancel a scheduled payment until 10 pm the evening before
it is due, and SHALL refuse changes after the cut-off.

#### Scenario: Cancel before cut-off
- **WHEN** a customer cancels a payment before the cut-off
- **THEN** its status is "cancelled" and it is never executed

#### Scenario: Change after cut-off
- **WHEN** a customer tries to edit or cancel after the cut-off
- **THEN** nothing changes
- **AND** the customer sees "This payment is being processed and can no longer be changed."

### Requirement: Joint accounts
The system SHALL show scheduled payments from a joint account to every holder, SHALL let any holder
cancel them, and SHALL let only the creating holder edit them.

#### Scenario: Co-holder cancels
- **WHEN** a joint holder cancels a payment their co-holder created
- **THEN** the payment is cancelled

#### Scenario: Co-holder cannot edit
- **WHEN** a joint holder tries to edit a payment their co-holder created
- **THEN** the edit is refused

### Requirement: Payee deletion
The system SHALL warn a customer deleting a payee that has scheduled payments, and SHALL cancel
those payments when the customer confirms.

#### Scenario: Deleting a payee with scheduled payments
- **WHEN** a customer deletes a payee with 2 scheduled payments and confirms the warning
- **THEN** the payee is deleted and both payments are cancelled

### Requirement: Unknown network outcome
The system SHALL resolve a payment whose network outcome is unknown by querying the network with
the same payment reference, and SHALL either complete it or reverse the debit. It SHALL never
resend it under a new reference.

#### Scenario: Timeout after debit
- **WHEN** the network times out after the account was debited
- **THEN** the payment stays "processing" until reconciliation confirms or reverses it
- **AND** the account is debited at most once

### Requirement: Account unavailable
The system SHALL fail a due payment whose from-account is frozen or closed and SHALL notify the customer.

#### Scenario: Frozen account on the payment date
- **WHEN** the from-account is frozen on the payment date
- **THEN** the payment is "failed" and the customer receives a push notification and SMS

### Requirement: Access control and audit
The system SHALL allow scheduling, viewing and changing payments only on accounts the customer
holds, and SHALL record every status change of a scheduled payment in the account audit history.

#### Scenario: Another customer's account
- **WHEN** a request names an account the customer does not hold
- **THEN** it is refused with "Account not found" and a security event is logged

#### Scenario: Status change audited
- **WHEN** a payment changes status
- **THEN** an audit entry records who or what changed it, the old status and the new status

### Requirement: Operations alerting
The system SHALL alert operations when the payment network is unavailable during a run for more
than 15 minutes.

#### Scenario: Network unavailable
- **WHEN** the payment network rejects connections for more than 15 minutes during a run
- **THEN** operations are alerted once, and payments wait to be sent when it recovers
