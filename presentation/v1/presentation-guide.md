# "Aligned by Design": Presenter's Guide

A slide-by-slide script for the V2 deck (25 slides), for an audience of **bank business and
technology leaders together**. Tone: measured, practical, no drama.

---

## Read this first: the whole talk in one page

### The argument

Every feature is now built by four parties: **the business, the engineering team, the developer,
and an AI** that writes much of the code. Each holds a different piece of the picture, and the AI
holds only what is written down and given to it.

**The crux: all four must share one agreed picture of the feature**, meaning the same decisions,
scope, cases and build plan. That picture has to be written down, reviewed and signed, because
that's the only form the AI can work from.

OpenSpec builds that picture step by step, and each document aligns a specific set of parties:

| Document | Aligns | On what | Signed by |
|---|---|---|---|
| **Proposal** | Business ↔ Tech | What we're solving, why, and what we won't do (high level) | The business sponsor |
| **Business rules** | The business teams ↔ Tech | Every rule, decision and customer message, each with an ID, owner and source | Product, risk, compliance, operations |
| **Design** | The engineering teams: development, architecture, security, QA | How it's built, how it fits the architecture, that it's secure and testable, every case | Each team, in review |
| **Tasks** | Developer ↔ AI | What gets built, in what order, verified how, by our house rules | The developer, at each gate |
| **All three** | Everyone ↔ AI | The AI builds from what was agreed, not from a guess | — |

**How every document is made: inputs → questions → organised document.** The AI takes in what
people already have (conversations, notes, docs, guidelines, code), probes with questions (for the
proposal this is *explore*), reflects back what it understood, and organises the answers into a
document with a fixed structure. Its owners sign it, and it becomes an input to the next document.

**The AI's role in every document: probe, reflect back, agree.** The AI doesn't just draft. It
probes us to be more thorough (what about holidays? what if the network times out after the money
has left?), pushes for edge and error cases, and reflects back **what is covered and what is not**,
until the people who own that document agree.

When something changes, it changes in the documents first, so everyone, including the AI's next
session, sees the same update.

### The talk in three lines (memorise these)

> 1. Alignment means one agreed picture, shared by the business, the team, the developer and the AI.
> 2. For each document, the AI probes us, reflects back what is and isn't covered, and we agree.
> 3. It stays aligned because every change goes through the documents first.

### Plain-language glossary

| Term | Say it like this |
|---|---|
| OpenSpec | A simple, open-source way of keeping a feature's agreed decisions as documents next to the code |
| Proposal | The high-level business document: what, why, and what we're *not* doing |
| Business rules | The business's rules (BR-n), decisions (D-n) and exact customer wording (MSG-n), each with an owner and source |
| Design | The engineering document: how it will be built, with diagrams and every case worked through |
| Tasks | The step-by-step build plan the AI follows, with checkpoints |
| Explore | The first step: the AI questions the idea with you, before any document exists, until the problem and scope are clear |
| Spec | The agreed behaviours from the proposal, written as short, testable WHEN/THEN statements |
| Probe | The AI asks the questions we didn't think of: edge cases, error cases, rules, limits |
| Reflect back | The AI states plainly what the document now covers and what it doesn't, so we can agree or correct it |
| Phase gate | A checkpoint: the AI stops, tests must pass, and a person reviews before it continues |
| H / E / X | Happy path (all goes as expected) / Edge case (unusual but legitimate) / Error case (something fails) |
| Worked example | A concrete case written out in advance ("rent due on Thanksgiving → paid Friday") that becomes a test |
| Cross-cutting concerns | What every feature must get right but nobody asks for: transactions, concurrency, error handling, alerts, security |
| End-of-day (EOD) batch | The bank's overnight processing (interest, statements). New overnight jobs must not collide with it |

### Numbers cheat sheet (all from the demo change)

| Fact | Number | Where |
|---|---|---|
| Starting point | One line: "Let customers schedule payments." | — |
| Business rules / decisions / customer messages | 17 / 10 / 12 | business-rules.md |
| Rules proven by a worked case | 17 of 17 | business-rules.md ↔ design.md |
| Things explicitly out of scope | 7 | proposal.md → What This Feature Does NOT Cover |
| Customer messages agreed word for word | every one | proposal.md → Behavior Rules |
| Worked cases | 21 (5 happy path, 10 edge, 6 error) | design.md → Worked Examples |
| Design decisions (with rejected alternatives) | 5 | design.md → Design Decisions |
| Diagrams | 13 | design.md |
| Build tasks / gated phases | 26 / 4 | tasks.md |
| Agreed behaviours covered by a worked example | 22 of 22 | spec.md ↔ design.md |
| Worked examples assigned to exactly one gate test | 21 of 21 | design.md ↔ tasks.md |
| Mid-flight rule change | 22 lines, 4 documents, 1 commit; proposal unchanged | `git diff master retry-8pm` |

---

## Section 1: The problem

### Slide 1: Cover, "Aligned by design"

**Core point:** This talk is about getting four parties, one of them an AI, to share one picture
of a feature.

**Talking points**
- Name the four parties: the business, the engineering team, the developer, and the AI.
- The claim: alignment doesn't come from more meetings. It comes from a small set of documents
  that every party, the AI included, helps shape, challenges and signs.
- Shape of the talk: the problem, how OpenSpec creates alignment document by document (with the AI
  probing us at each step), how it stays aligned when things change, traceability from agreement to test, costs,
  then a live demo on a banking feature.

**Summary:** "One feature, four parties, one agreed picture."

**Transition:** "Let's start with a ticket we all recognise."

---

### Slide 2: "From one line to one agreed picture"

**Core point:** A feature starts as one vague line. Alignment means turning it into a complete,
agreed picture, and OpenSpec does that systematically.

**Talking points**
- The starting point, the kind of ticket we all see: **"Let customers schedule payments."**
- Everything that had to be decided before it could be built correctly:
  - **Proposal:** the why, 5 user stories, 7 things explicitly out of scope.
  - **Business rules:** 17 rules, 10 decisions and 12 customer messages, each with an owner and a
    source.
  - **Design:** 21 worked cases (5 happy path, 10 edge, 6 error), 5 design decisions with the
    alternatives rejected, and the cross-cutting concerns: transactions, concurrency, error
    handling and alerts, security.
  - **Tasks:** 26 steps in 4 phases, each ending in a gate.
- Every one of those was a question someone had to answer. OpenSpec makes sure each question is
  **asked**, **answered by the right party**, and **written down where the AI will read it**.
- **This slide is the map.** Each box is opened up later: the proposal on slide 8, the business
  rules on slide 9, the design on slides 10–11, the tasks on slide 12.
- The templates help: every section must be filled in or marked "N/A" with a reason, so skipping
  something is a visible decision, not an accident.

**Example to cite:** "The one-liner doesn't say what happens on a bank holiday, on the 31st of a
30-day month, or when the network times out after the money has left. The documents do."

**Summary:** One line in, one agreed picture out, with every question answered by its owner.

**Transition:** "So who has to answer all of these questions?"

---

### Slide 3: "Every feature is now built by four parties"

**Core point:** Each party holds a different piece of the picture, and the AI holds only what is
written down.

**Talking points**
- **The business** (product, risk, operations) knows what customers need and which rules apply.
- **The engineering teams** (development, architecture, security, QA) know how the bank's systems
  fit together, what keeps them secure, and how to prove they work.
- **The developer** owns the change, directs the AI, and answers for the result.
- **The AI** writes much of the code, but wasn't in any meeting. It knows only what it's given to
  read.
- Ask the question: *where does their shared picture live?* Today it's scattered across meetings,
  tickets, chat and people's memory. None of it is versioned, reviewable as a whole, or readable
  by the AI.

**Example to cite:** "The decision that a scheduled payment must never use the overdraft might be
made in a risk meeting. If it isn't written somewhere the AI reads, then as far as the AI is
concerned, it doesn't exist."

**Summary:** Four parties, four partial pictures, and one of them can only read.

**Transition:** "So what exactly needs to be aligned, and between whom?"

---

### Slide 4: "Four alignments every feature needs"

**Core point:** Alignment isn't one thing. It's four specific agreements, each between specific
parties and each with a home. Every one covers not just the happy path but the **edge and error
cases** too (H/E/X).

**Talking points**

Walk the table row by row:

| Between (document) | They must agree on | Happy path / Edge / Error (from the demo) | Signed by |
|---|---|---|---|
| **Business ↔ Tech** (proposal + business rules) | What we're solving, why, and what we won't do | **H:** rent paid on the 1st · **E:** due date is a bank holiday · **X:** not enough money on the day | Business: product, risk, compliance |
| **Engineering teams: dev · architecture · security · QA** (design) | How it's built; that it fits the architecture, is secure, and can be tested | **H:** overnight run debits and sends · **E:** the run starts twice · **X:** network times out after the debit | Each team, in review |
| **Developer ↔ AI** (tasks) | What gets built, in what order, how it's verified, following our house rules | Every H/E/X case becomes a test that must pass at a phase gate: 21 in all | The developer, at each gate |
| **Everyone ↔ AI** (all three, in the repo) | The AI builds from what was agreed, not from its own guess | The AI reads the agreed cases; it doesn't invent its own | — |

- **Explain H/E/X once:**
  - **H**, the happy path: everything goes as expected.
  - **E**, an edge case: unusual but legitimate, like a holiday, the 31st, or a double tap.
  - **X**, an error case: something fails, like no money, a frozen account, or a network timeout.
- Most misunderstandings live in E and X. Everyone agrees on the happy path; the arguments, the
  defects and the incidents come from the edges and the errors.
- Notice who owns which cases:
  - the **business** decides the customer-facing ones (what happens on a holiday? with no money?);
  - the **engineering teams** decide the system ones (the job runs twice? the network times out?);
  - the **developer and the AI** agree that every case is tested before moving on.
- Explain the last row: the AI starts every session by reading these documents. If the cases are
  written and agreed, it builds them; if not, it fills the gaps itself.
- Point out that each row has a **named signatory**. That's what turns a conversation into an
  agreement.

**Example to cite:** "Everyone agrees rent should be paid on the 1st. The alignment work is agreeing
what happens when the payment date is a bank holiday, when there isn't enough money, and when the network times
out after the money has left. In the demo those are cases E1, X1 and X2, each signed and each a test."

**Summary:** Four agreements, four owners, each covering the happy path, the edges and the errors.

**Transition:** "Which leads to the core idea."

---

### Slide 5: Statement slide (teal)

**Core point:** Alignment isn't a meeting. It's a set of documents every party, the AI included,
helps shape, challenges and signs.

**Talking points**
- Meetings create a *feeling* of alignment that fades, and the AI can't read it.
- Documents that are probed, reviewed and signed create alignment that lasts, can be checked, and
  is the AI's actual input.

**Summary:** Written, challenged and signed beats discussed.

**Transition:** "And the AI plays a specific part in getting those documents right."

---

### Slide 6: "For each document: inputs in, questions out, one organised document"

**Core point:** Every document is made the same way. The AI takes in everything relevant, probes us
with questions and reflects back what it understood, then organises the answers into a document
with a fixed structure, which its owners sign.

**Talking points**

Walk the three rows, left to right:

- **Proposal:**
  - *Inputs:* conversations, meeting notes (e.g. Zoom transcripts), existing documents, existing
    code.
  - *Probe and reflect back:* this is **explore**, where the problem gets tightened. "What if it's
    a bank holiday? Can it use the overdraft? Joint accounts? What are we *not* doing?" The AI
    reflects back what's in and what's out until the business agrees.
  - *Organised into:* proposal.md, with a fixed structure: why, user stories, screens, scope,
    impact. It stays high level. **The business sponsor signs.**
- **Business rules:**
  - *Inputs:* the proposal, bank policies and regulations, product rules, meeting notes, and what
    the system does today.
  - *Probe and reflect back:* "Who owns this rule? Which policy says so? What are the exceptions:
    holidays, month ends, joint accounts? What exactly does the customer see?"
  - *Organised into:* business-rules.md: a rule catalogue (BR-n) with owner and source, decisions
    (D-n) with rationale, input validation, customer wording (MSG-n). **Product, risk, compliance
    and operations sign.**
- **Design:**
  - *Inputs:* the signed proposal, the signed business rules, architecture, security and QA guidelines, the tech stack and
    best practices, business rules, existing code.
  - *Probe and reflect back:* "What if the run starts twice? A timeout after the debit? Who is
    alerted? Does it fit around end-of-day processing?"
  - *Organised into:* design.md: structure, happy/edge/error cases, worked examples, patterns,
    standards. **The engineering teams sign.**
- **Tasks:**
  - *Inputs:* the signed design, the LLD and QA checklist, UI design guidelines and CSS standards,
    the patterns to use, house rules (CLAUDE.md), existing code and tests.
  - *Probe and reflect back:* "In what order? How is each step verified? Where do I stop for
    review? Is every case covered by a gate?"
  - *Organised into:* tasks.md: phases, design citations, verification, gates. **The developer
    approves.**
- **Three points to land:**
  1. **Inputs are wide.** The AI reads what people already have: notes, docs, guidelines, code.
     Nobody starts from a blank page.
  2. **Questions come out, answers go in.** The AI never answers its own questions; people do.
     That's where alignment happens.
  3. **The structure is fixed by the template.** Every proposal and every design has the same
     sections, so they're complete, comparable and reviewable. And each signed document becomes
     an input to the next.

**Example to cite:** "For the proposal, the AI read the meeting notes and the existing payments
code, then asked whether a scheduled payment may use the overdraft. Risk said never. That answer
is now rule BR-7 and decision D-3 in the business rules, and an input to the design."

**Summary:** Inputs in, questions out, answers organised into a signed document, which feeds the
next one.

**Transition:** "Let's open up each document and see what that produced."

---

### Slide 7: Section divider, "How each document aligns the parties"

**Core point:** The next slides are real excerpts from a complete OpenSpec change for a retail
bank's scheduled and recurring transfers.

**Talking points**
- The demo change lives in `openspec/changes/scheduled-transfers/`. Everything shown is quoted from it.

---

### Slide 8: proposal.md, Business ↔ Tech

**Core point:** The proposal is the high-level agreement between business and tech: what, why, for
whom, and what not. It deliberately leaves the detailed rules to the next document.

**Talking points**

Walk the five boxes, citing the demo:

1. **Why and what changes: the problem.** Before/after flows. Today customers must remember rent on
   the 1st, or visit a branch; afterwards they schedule once and the bank does the rest.
2. **User stories and screens: who, and what they see.** 5 user stories with acceptance criteria,
   and mockups of "Schedule a payment" and "Upcoming payments".
3. **Scope: what we will *not* do.** 7 exclusions, such as no international transfers, no
   credit-card source accounts and fixed amounts only, each saying where it goes instead.
4. **Impact and benefits.** What it touches (the app, payees, overnight processing,
   notifications, operations) and 4 concrete benefits for customers.
5. **FAQ, and a pointer to the rules.** The questions support and operations will ask. Every
   detailed rule, decision and message is referenced by ID (BR-n, D-n, MSG-n) and lives in the
   business rules document.
- **The sixth box, the AI's part:** it probes the idea (this is *explore*), reflects back what's
  in and out of scope, and the business sponsor signs.

**Example to cite:** "The proposal says: if there isn't enough money, the bank tries again later
that day and never uses the overdraft. *When* it retries, and *why* the overdraft is excluded, are
rules BR-7 and BR-8, owned by risk and product, in the next document."

**Summary:** High level and signed: the problem, the people, the scope and the impact.

**Transition:** "The detail lives in the business rules."

---

### Slide 9: business-rules.md, the business teams

**Core point:** Business rules and decisions get their own document: the single place where every
rule, every decision and every customer message lives, each with an ID, an owner and a source.
This is where product, risk, compliance and operations align with each other and with tech.

**Talking points**

Walk the five boxes, citing the demo's business-rules.md:

1. **Rule catalogue.** 17 rules, each one testable sentence with an ID, an owner and a source.
   For example, BR-7: "A scheduled payment never uses the overdraft," owned by Risk, from the
   lending policy.
2. **Decisions.** 10 decisions, each with its rationale and the alternatives rejected, so they
   aren't re-argued later. For example, D-3: one retry at 6 pm, then fail. Using the arranged
   overdraft was rejected by Risk as unconsented borrowing.
3. **Input validation.** Every field's rule and default, tied back to a rule ID.
4. **Customer wording.** 12 messages, word for word, the only place they're defined. For
   example, MSG-5: "This is more than your daily transfer limit of {limit}." QA checks each one
   verbatim.
5. **Proven by the design.** The design's worked examples cite rule IDs, and all 17 rules are
   proven by at least one case. For example, BR-7 is proven by case E4, "overdraft not used".
- **The sixth box, the AI's part:**
  - it reads the proposal, the policies and the existing behaviour;
  - it probes for each rule's owner, source, exceptions and exact wording;
  - it reflects back a summary of rules and assumptions to confirm;
  - product, risk, compliance and operations each sign.
- **Why a separate document:** rules change more often than the proposal, and they have
  different owners. Keeping them in one place with IDs makes them traceable (slide 14) and easy
  to change (slide 20: the 8 pm change touched this document and never touched the proposal).

**Example to cite:** "An auditor asks who decided that scheduled payments never use the overdraft,
and why. Answer: D-3, decided by Risk and Product, with the rationale and the rejected
alternative written down, and rule BR-7 proven by test E4."

**Summary:** Every rule has an ID, an owner, a source and a test.

**Transition:** "Next, the engineering teams agree on how."

---

### Slide 10: design.md, the engineering teams

**Core point:** The design breaks the approach down until nothing is implied. Five parts, each
probed by the AI and reviewed by the engineering teams, so they agree on *how* before any code exists.

**Talking points**

Walk the five boxes, citing the demo in each:

1. **Components and sequence diagrams: the structure.**
   - Component boundaries, a class diagram, the data model, the payment's state lifecycle, and
     sequence diagrams for the happy path, the funds-short retry and the timeout. 13 diagrams in
     all.
   - This is where the team checks it **fits the architecture and doesn't disturb other moving
     parts**.
2. **Happy, edge and error cases (H/E/X): the coverage.**
   - 5 happy, 10 edge and 6 error cases, e.g. E1 due date is a bank holiday, E9 the run starts
     twice, X2 the network times out after the debit.
   - The AI probes hardest here: "What if it runs twice? What if the account is frozen on the day?"
3. **Worked examples: the precision.**
   - Every case is written as Given / When / Then with real data. For example: "Asha schedules
     $400.00 to Ravi Kumar for Thu 15 Oct."
   - Each row becomes exactly one automated test, so the team agrees on *behaviour*, not just
     boxes and arrows.
4. **Patterns and orthogonal concerns: what nobody asks for but everything needs.**
   - Patterns:
     - start the run when end-of-day processing completes (design decision 3: "EOD sometimes runs
       late, and a fixed time would collide with interest posting");
     - a transactional outbox;
     - one idempotency key per payment, so it's never debited twice.
   - Orthogonal concerns:
     - transactions and query cost;
     - error handling, and who is alerted;
     - authorization and security.
   - Each is filled in or marked "N/A" with a reason.
5. **Best practices and house rules: the bank's standards.**
   - Money in whole cents, never floating point.
   - No network call inside a database transaction.
   - Account numbers masked everywhere.
   - Every status change audited.
   - Reuse the existing ledger, calendar and notifier rather than building new ones.
- **The sixth box, the AI's part:** it probes each of the five, reflects back what's covered and
  what's marked N/A, and development, architecture, security and QA each review and sign
  (next slide). The AI later reads the signed design as its
  brief.

**Example to cite:** "Worked example X2, a timeout after the money has left: check with the network
15 minutes later, complete or refund, and never resend under a new reference, because that's how
double payments happen. Decided in review, on paper, before a line of code."

**Summary:** Structure, coverage, precision, patterns and standards, all agreed by the engineering
teams before code.

**Transition:** "And the design isn't reviewed by one team. It's reviewed by every team that has to live with it."

---

### Slide 11: "One design, reviewed by every engineering team"

**Core point:** The design is where the engineering teams align with each other. Development,
architecture, security and QA each review it from their own angle and sign. For simple changes
that's one design document; for complex ones, each team gets its own specialised document.

**Talking points**

Walk the four teams, with what each checks, from the demo design:

| Team | Checks in the design | From the demo |
|---|---|---|
| **Development** | The approach: components, classes, data model, sequences | Class diagram, data model, 3 sequence diagrams |
| **Architecture** | Fit: boundaries, contracts, patterns, reuse, impact on other systems | Start after end-of-day processing; outbox; reuse the existing ledger and notifier |
| **Security** | Authorization, data protection, secrets | Every lookup scoped to the customer's own accounts; account numbers masked; idempotency keys never derived from customer data |
| **QA** | Testability: every case covered, each one a test | 21 worked cases (H/E/X), each one test; QA checklist with the same IDs |

- **This is team-to-team alignment.** Without it, security finds the problem in a late review,
  architecture finds it in production, and QA finds it by guessing the cases. With it, all four
  sign the same design before code exists.
- **Two ways to run it:**
  - **Simple change → one design document.** It has sections for each concern, and each team
    reviews its sections.
  - **Complex change → specialised documents.** For example `architecture.md`,
    `security-review.md` and `test-plan.md`. Each has its own template, is probed by the AI, and
    is signed by its owning team. They're added the same way as the LLD and QA checklist (slide 18).
- The AI's part is the same for every team: it probes for that team's concerns, reflects back
  what's covered and what's N/A, and the team signs.

**Example to cite:** "Security's question for this feature: can a customer schedule a payment from
someone else's account? The design answers it (every lookup goes through the customer's own
accounts), and worked example X6 turns that answer into a test."

**Summary:** One design, four reviewing teams, four signatures, and the depth scales with the change.

**Transition:** "Then the developer and the AI have to agree on how it gets built."

---

### Slide 12: tasks.md, Developer ↔ AI

**Core point:** The tasks turn the agreed design into a build plan the developer approves and the
AI follows step by step. Broken open, it has five parts, and together they mean nothing on the list
is skipped and nothing off the list is built.

**Talking points**

Walk the five boxes, citing the demo's real tasks.md:

1. **Order: phases in dependency order.** 4 phases, 26 tasks:
   - schedule/edit/cancel;
   - overnight run and debit;
   - funds short, retry and notify;
   - audit and operations alerts.

   Each phase builds on a tested one.
2. **Citations: every task names the design section it implements.** For example, task 2.1 cites
   "Pipeline / Flow" and task 2.3 cites "Decision Flows". Review becomes a comparison.
3. **Verification: every task says how it's proven done.** For example, task 2.2: "two concurrent
   claims on one execution produce exactly one true." A tick means verified, not just written.
4. **Gates: every phase ends in a test that must pass.** Task 2.7 writes the tests for cases H2,
   H3, E9, X2 and X4, and phase 3 can't start until they pass. The AI stops here for human review.
5. **Standards and docs: built in, not bolted on.**
   - House rules are part of the tasks. For example, task 3.2: masked account numbers in every
     notification.
   - Documentation ships per phase. Task 1.9 writes the help page and checks every message
     matches the business rules' customer wording (MSG-n) word for word.
- **The sixth box, the AI's part:**
  - it reflects back its plan (order, checks, stops) and confirms every worked case is covered
    by a gate;
  - the developer approves;
  - the AI then ticks boxes and stops at each gate;
  - if the session restarts, it resumes at the first unticked box.

**Example to cite:** "On a long feature the AI's memory gets summarised. The task file doesn't.
Next session, it opens tasks.md, finds 2.3 unticked, and carries on exactly where the plan says."

**Summary:** Order, citations, verification, gates and standards: the developer and the AI agree
on how the design becomes code.

**Transition:** "Because every case has an ID, we can trace every test back to an agreement."

---

### Slide 13: Section divider, "Proving it"

**Core point:** Agreement is only useful if you can prove the code matches it. The next three
slides show how: traceability, coverage, and better code reviews.

---

### Slide 14: "Every test traces back to an agreement"

**Core point:** Every case has an ID that runs from the agreement to the test to the QA check. So
you can trace any test back to the agreement it proves, and the AI can check coverage
mechanically: **nothing agreed goes untested, and nothing is tested that wasn't agreed.**

**Talking points**
- **One line on specs, since the word appears here:** a spec restates the agreed rules as short,
  testable WHEN/THEN statements.
- Walk the chain on one real case, "not enough money, still short after the 6 pm retry":
  1. **Rule BR-8** (business rules): one retry at 6 pm, then fail.
  2. **Design, worked example X1:**
     - *Given* a balance of $300.00 all day;
     - *When* the 6 pm retry runs;
     - *Then* the payment fails, the customer gets a push and an SMS, and nothing is debited.
  3. **Task 3.4, the phase-3 gate:** write the retry tests covering H4, E4, E10 and **X1**, and
     they must pass before phase 4.
  4. **Automated test:** a scenario named "X1 Still short after retry".
  5. **QA checklist:** row X1, checked by hand on staging (generated from the same IDs).
- **Coverage checks the AI runs,** which we ran on the demo:
  - every business rule is proven by at least one worked example: **17 of 17**;
  - every agreed behaviour is covered by at least one worked example: **22 of 22**;
  - every worked example is assigned to exactly one phase-gate test: **21 of 21**;
  - each gate's tests pass before the next phase starts.
- **It works in both directions:**
  - *forwards*, every agreement has a test;
  - *backwards*, every test and every task points to an agreement, so unrequested work stands out.
- **Why it matters for a bank:**
  - "Show me where this rule is tested" is answered in one hop, for an auditor, a regulator or a
    new team member.
  - When a test or QA check fails, you know exactly which agreement broke and who signed it.
  - When the business changes a rule (slide 20), the ID tells you which tests must change.
- **This is where the AI and the tasks do the heavy lifting:** the AI keeps the IDs consistent
  across documents and runs the coverage checks; the tasks bind each ID to a gate that must pass.

**Example to cite:** "An auditor asks how we know a scheduled payment never uses the overdraft. We
point to rule BR-7 (and decision D-3), design case E4, task 3.4 and the test named E4. One chain, no
searching."

**Summary:** One ID from agreement to test to QA; the AI checks nothing falls through.

**Transition:** "And here is the whole feature's coverage on one page."

---

### Slide 15: "Coverage you can see: every case, every gate"

**Core point:** The whole feature on one page. Every agreed case appears under the phase-gate test
that proves it, so gaps and duplicates are visible at a glance.

**Talking points**
- Read the top line: **17 rules → 21 worked cases → 4 phase gates → 21 tests. Uncovered: 0.
  Duplicated: 0.**
- Walk the four columns, one per phase gate in tasks.md:
  - **Phase 1, schedule/edit/cancel** (`scheduling.feature`): H1, E1, E2, E3, E5, E6, E7, E8, X5, X6.
  - **Phase 2, overnight run and debit** (`execution.feature`): H2, H3, E9, X2, X4.
  - **Phase 3, funds short, retry, notify** (`retry.feature`): H4, E4, E10, X1.
  - **Phase 4, audit and operations alerts** (`audit.feature`): H5, X3.
- Point out the mix: most of the 21 are **edge (E) and error (X)** cases. That's where the
  coverage work is, and where misunderstandings usually hide.
- **Who keeps this true:** the AI builds and re-checks this map every time a document changes (a
  case with no gate, or a gate naming a case that doesn't exist, shows up immediately), and the
  developer reviews it before approving the tasks.
- The same IDs appear in the QA checklist, so a failed manual check maps straight back to its case
  and its test.

**Example to cite:** "If the business adds a rule tomorrow, say a new edge case for leap years, it
gets an ID, lands in one of these columns, and the gate won't pass until its test does. If it lands
in no column, the AI flags it."

**Summary:** Coverage isn't a claim; it's a map anyone can check.

**Transition:** "The same IDs and references are what make code review sharper."

---

### Slide 16: "Better code reviews: every change checked against the agreement"

**Core point:** OpenSpec improves code review. The generated code carries references back to the documents: commit messages
cite the task, code cites the design section or decision, tests carry the case ID. So AI review
agents and human reviewers can check every change against exactly what was agreed. Review becomes
comparison, not archaeology.

**Talking points**
- **What the code carries** (illustrative, using the demo's real task numbers and IDs):
  - *Commit:* "2.3 Debit + outbox + audit in one transaction (design: Decision Flows; cases H2, X2)"
  - *Code comment:* "Balance excludes the overdraft (BR-7)"
  - *Test name:* "X2 Timeout after debit is reconciled, never resent"
- **What a review agent checks,** automatically, on every change:
  - **Against the cited section:** does the code do what the referenced task and design section say?
  - **Coverage:** every case ID in this phase's gate has a test with that ID.
  - **House rules:** the LLD's reviewer checks and project rules, e.g. no network call inside a
    database transaction, account numbers masked, every status change audited.
  - **Orphans in both directions:** code with no reference is unrequested work; a reference to a
    section or case that doesn't exist is stale.
- **What the human reviewer does:**
  - opens the cited section beside the diff and asks one question: *does this match what we agreed?*;
  - looks at what the agent flagged, then decides and signs off at the phase gate.
- **Why reviews get better (before vs after):**
  - *Before:* the reviewer reconstructs intent from the ticket and memory, and mostly checks style.
  - *After:* the reviewer compares the diff with the cited agreement; the agent has already checked
    coverage, house rules and orphans, so human attention goes to behaviour and judgement.
- This closes the loop on alignment. The documents aren't only read before coding; they're the
  yardstick the code is reviewed against, by people and agents alike.

**Example to cite:** "The review agent sees a change touching the balance check. The code cites
rule BR-7. It checks that the overdraft is excluded, finds test E4, and passes it to the
human with the decision beside the diff. The reviewer confirms in minutes, not an afternoon."

**Summary:** Every line points back to an agreement, so agents and humans review against the
agreement, not against memory.

**Transition:** "And the set of documents is yours to extend."

---

### Slide 17: Section divider, "Keeping it aligned"

**Core point:** Alignment has to be kept, not just reached: extend the documents to what your team
values, keep roles clear, and push every change through the documents first.

---

### Slide 18: "Add the documents your team values"

**Core point:** OpenSpec isn't a fixed set of documents. Each team adds the documents it values,
such as a low-level design, a manual QA checklist or a business rules catalogue, and each becomes
one more agreed, versioned document in the change folder.

**Talking points**
- **How it works:** the schema file (`openspec/schemas/spec-driven/schema.yaml`) lists the
  documents. Each has:
  - a **template**: the sections it must have;
  - an **instruction**: how the AI should write it and what to probe for;
  - **dependencies**: what must exist first. For example, the tasks can't be written until the
    LLD and QA checklist exist.
- **Three we already use (real, in our schema):**
  - **Business rules (business-rules.md):** the step we just added between proposal and design,
    which shows how easy extending is: a template, an instruction, and its place in the order.
  - **Low-level design (lld.md):** the implementation decisions a reviewing developer signs off
    before coding. It covers types, limits, indexes, locks, retries and error mapping. Every
    section ends in concrete **reviewer checks** (e.g. "No external HTTP call or email inside a
    transaction"). It closes with a **project rules checklist** of our house rules and a sign-off
    table.
  - **Manual QA checklist (qa-checklist.md):** plain-language rows a tester runs by hand on
    staging. It uses **the same H/E/X IDs** as the design and the tests, plus a role sweep
    (who can and can't do what), cross-cutting checks (dates, mobile width, tenant isolation) and
    a sign-off.
- **Specialised design documents per team** (slide 11): `architecture.md`, `security-review.md`,
  `test-plan.md`, for changes big enough to need them.
- **Other examples a bank might add:**
  - a **business rules catalogue**: every rule, its owner and its source policy;
  - a **compliance/regulatory checklist**;
  - a **rollout and runbook** plan: switches, monitoring, how to turn it off;
  - a **threat model**.
- **Every added document gets the same treatment:** the AI drafts it from the template, probes for
  gaps, reflects back what's covered, the owner signs, and it lives next to the others in the
  change folder. Alignment extends to whoever that document serves: testers, operations, risk,
  compliance.
- **Tie to the demo:** `openspec status` on the demo shows the LLD and QA checklist still to do.
  That's the live AI step at the end.

**Example to cite:** "Our QA checklist reuses the case IDs, so when a tester marks row X1 as
failed, it maps straight back to design case X1, task 3.4 and the automated test named X1."

**Summary:** Start with proposal, design and tasks; add whatever your team values, and each one
becomes part of the same agreed picture.

**Transition:** "Put together, here's who does what."

---

### Slide 19: "Who does what, and how it flows"

**Core point:** Every party has a defined part in every document, and the work flows in one loop:
the AI probes, people decide, the documents record. The AI probes and drafts everything, and
decides nothing.

**Talking points**
- **The roles table:**
  - **Proposal:** the business decides and signs; the engineering teams check feasibility; the developer
    co-authors; the AI probes and drafts.
  - **Business rules:** product, risk, compliance and operations decide and sign; the engineering
    teams check feasibility; the AI probes and organises.
  - **Design:** development, architecture, security and QA each review and sign; the developer owns it; the AI probes for cases,
    drafts it, and later reads it as its brief; the business is informed.
  - **Tasks:** the developer owns them and approves each gate; the engineering teams review; the AI reflects
    back the plan, executes, ticks off and stops at gates.
  - **QA checklist:** QA runs it and the business accepts; the AI drafts it from the worked cases;
    the developer fixes defects.
- **The flow strip underneath:**
  1. **Explore:** the AI probes the idea.
  2. **People decide.**
  3. **The AI reflects back, and we agree.**
  4. **The AI builds from the documents.**
  5. **People check at every gate.**
- The rule: **when something changes, it changes in the documents first, then in the code.**
- The governance point for leaders: **no decision reaches code without a named signatory.**

**Summary:** Clear ownership and one loop; the AI never makes an unsigned decision.

**Transition:** "Which brings us to change."

---

### Slide 20: "The business moves the retry to 8 pm: one change, one review"

**Core point:** Alignment isn't a one-off. When a decision changes, every party sees the same
change in one place.

**Talking points**
- The scenario: the business moves the insufficient-funds retry from 6 pm to 8 pm because late
  salary credits arrive up to 19:30.
- **We made this change for real** in the demo repository. It touched **22 lines in 4 documents, in
  one commit**, and **the proposal didn't change at all**, because it's high level:
  - the business rules: rule BR-8, decision D-3 (with the reason) and customer message MSG-3;
  - the spec;
  - the design's lifecycle, sequences and worked cases;
  - one task.
- Each party reviews its part: product and risk review the rule and decision, engineers review the design and
  tasks, and **the AI's next session reads the new rule**.
- Every place the rule appears changes together, so no document is left saying 6 pm.

**Example to cite:** Run `git diff master retry-8pm` in the demo.

**Summary:** One decision changed, one reviewed update, everyone aligned again.

**Transition:** "So what does it cost to work this way?"

---

### Slide 21: Section divider, "Costs and wider benefits"

**Core point:** An honest look at what it costs, then what else gets better.

---

### Slide 22: Costs and limits

**Core point:** Name the costs plainly. They're real but mostly moved, not added.

**Talking points**
- **Costs:**
  - Review time moves earlier: proposal and design are reviewed before code. The questions get
    answered either way; the choice is *before code* or *during rework*.
  - Discipline: every change goes through the documents first, or they drift and stop being the
    shared picture.
  - A one-off effort to tailor the templates to the bank's standards.
- **Limits:**
  - A wrong decision that everyone agreed to is still wrong. OpenSpec makes decisions explicit,
    not better.
  - AI-written code still needs review; the gates make review focused, not optional.
  - Start small: one team and one feature, and see what it surfaces.

**Summary:** Modest, visible costs; clear limits; no silver bullet.

**Transition:** "One last thing: what else gets better."

---

### Slide 23: "Also better across the rest of the SDLC"

**Core point:** The same mechanism (agreed, written, traced) improves much more than building the
feature. Seven more places it pays off.

**Talking points**

Walk the seven boxes briefly, one line each:

1. **Incidents: bug or spec gap.**
   - Every production issue is one of two things.
   - The code broke an agreed case: that's a **bug**, so fix the code.
   - No case covered it: that's a **spec gap**, so add a case, a test and a signature.
   - Root cause becomes a category, not a debate.
2. **Living documentation.** When a change is archived, its agreed behaviour merges into the
   system's living specs. New joiners, and every future AI session, read how the system actually
   behaves instead of relying on tribal memory.
3. **Change impact.** The proposal lists which existing capabilities a change touches, and spec
   changes show exactly which agreed behaviours change, so you know what to retest.
4. **Audit evidence.** Signed decisions, full version history, and traced tests. "Who approved
   this rule, and where is it tested?" is answered from the repository.
5. **Release and support readiness.** Customer wording, the FAQ, operations alerts and help pages
   are written *before* release, so support and operations are ready on day one.
6. **Estimation.** Estimate from settled decisions and a real task list (26 tasks, 4 phases in
   the demo), not from a one-liner.
7. **Handovers.** A new developer, or a new AI session, picks up mid-feature at the first unticked
   task with the full context.

**Example to cite:** "A customer complains that a payment failed on a holiday. Is it a bug or a gap?
Look up the case: E1 says pay on the next working day. If the code didn't, it's a bug. If the case
didn't exist, it's a gap, and it gets added, tested and signed."

**Summary:** Agreed, written and traced pays off from estimation to incidents to audit.

**Transition:** "To sum up."

---

### Slide 24: Summary, "One agreed picture, built and kept"

**Core point:** Recap the argument.

**Talking points**
1. **Alignment means one agreed picture**, shared by the business, the team, the developer and the
   AI: the same decisions, scope, cases and plan.
2. **For each document, the AI probes, reflects back, and we agree.** The proposal aligns business
   and tech (7 exclusions); the business rules align the business teams (17 rules, 10 decisions); the design aligns the team (21 cases, 5 decisions); the
   tasks align the developer and the AI (26 tasks, 4 gates). Every rule and case is traced to a test:
   17 of 17 rules, 21 of 21 worked examples.
3. **It stays aligned.** Every change goes through the documents first, and every party, including
   the AI's next session, sees the same update.

**Transition:** "Let me show you the real documents."

---

### Slide 25: Live demo run-sheet

**Demo steps** (in `/home/thimmaiah/work/openspec_demo`)
1. **Proposal:** open `openspec/changes/scheduled-transfers/proposal.md`. Show *Why*, the user
   stories and *What This Feature Does NOT Cover*; point out it stays high level and cites rule IDs.
2. **Business rules:** open `business-rules.md`. Show the rule catalogue (BR-7, overdraft, owned
   by Risk), the decisions with rationale (D-3), and customer wording (MSG-n).
3. **Design:** open `design.md`. Show the execution lifecycle diagram, the *Worked Examples*
   tables (point to X2), *Design Decisions* (point to decision 3), and *Cross-cutting Concerns*.
4. **Tasks:** open `tasks.md`. Show phase 2, the design citations on each task, and gate task 2.7.
5. **The change:** run `git diff master retry-8pm` and scroll through the four documents changing
   together.
6. **Live AI step, showing the probing:** start the AI's explore step on a small new idea (for
   example "let customers pause a recurring payment") and let the audience watch it probe with
   questions and reflect back what's in and out of scope. Or run
   `openspec status --change scheduled-transfers` and have it draft the low-level design live.

**Backup if something fails live:** slides 8–12, 14–16 and 20 show the same content.

---

## Likely questions and suggested answers

| Question | Answer |
|---|---|
| "Isn't this just more documentation?" | It's the documentation the AI builds from, so it can't quietly go stale. And the AI drafts most of it; people review and decide. |
| "Can't we align in meetings like we do now?" | Meetings align the people in the room, for a while. The AI wasn't in the room and can't read the minutes. Written, signed documents align everyone, including the AI, and stay checkable. |
| "Doesn't the AI just make things up when it drafts?" | That's why it probes and reflects back rather than decides: every decision is answered and signed by a person, and anything not covered is listed as not covered. |
| "Won't this slow us down?" | Review moves earlier rather than being added; the questions get answered either way, before code or during rework. |
| "Who maintains the documents?" | The developer owns them; the business owns the rules and decisions in business-rules.md. Changes go through the documents first (slide 17). |
| "What if the business can't answer a question yet?" | It's recorded as an open question with a status. If it would change what gets built, it's resolved before tasks are written. |
| "Does the AI follow the tasks reliably?" | The file keeps it on plan across sessions, and the gates check it: tests must pass and a person reviews before the next phase. |

## Where things are

- Demo change: `openspec/changes/scheduled-transfers/` (branch `master`).
- The 8 pm rule change: branch `retry-8pm` (`git diff master retry-8pm`).
