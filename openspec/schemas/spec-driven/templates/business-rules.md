# [Feature Name] — Business Rules and Decisions

> **Audience:** Product, risk, compliance, operations, QA and support — non-technical.
> **Companion documents:** [Proposal](proposal.md) · [Design](design.md)

> **Planning phase — no code changes.** This is the single place where the business's rules,
> decisions and customer wording for this change live. The proposal stays high level and refers
> here by ID. The design's worked examples cite these rule IDs, and every rule must be proven by
> at least one of them.

> **How this document is made:** the AI reads the proposal, the policies and regulations you
> point it to, meeting notes and existing behaviour, then asks until each rule has an owner, a
> source, its exceptions and its exact wording. It reflects back a summary for you to confirm.

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

<!-- One row per rule. One plain, testable sentence per rule. Owner is a role, not a person.
     Source is a policy, a regulation, or a decision below (D-n). Group rows by category. -->

| ID | Rule | Category | Owner | Source | Wording |
|---|---|---|---|---|---|
| BR-1 | <!-- The system SHALL ... --> | <!-- eligibility / timing / funds / limits / changes / outcomes / audit --> | <!-- Product / Risk / Compliance / Operations --> | <!-- policy, regulation or D-n --> | <!-- MSG-n or — --> |

---

## Decisions

<!-- One row per decision the business made. Record the alternatives and why they were rejected,
     so the decision isn't re-argued later. -->

| ID | Question | Decision | Rationale | Alternatives rejected | Decided by |
|---|---|---|---|---|---|
| D-1 | <!-- question --> | <!-- decision --> | <!-- why --> | <!-- option: why not --> | <!-- role(s) --> |

---

## Input Validation

| Field | Rule | Default | Message |
|---|---|---|---|
| <!-- field --> | <!-- rule --> | <!-- default --> | <!-- MSG-n or — --> |

---

## Customer Wording

<!-- The only place customer-facing text is defined. QA checks every message verbatim.
     Use {placeholders} for values. -->

| ID | Shown when | Exact text | Channel |
|---|---|---|---|
| MSG-1 | <!-- situation --> | <!-- exact text --> | <!-- screen / push / SMS / email --> |

---

## Open Questions

<!-- Genuinely deferrable only. Anything that would change what gets built is answered now. -->

| # | Question | Owner | Status |
|---|---|---|---|
| | | | |

---

## Sign-off

| Role | Name | Date | Decision |
|---|---|---|---|
| Product | | | <!-- Approved / Changes requested --> |
| Risk | | | |
| Compliance | | | |
| Operations | | | |
