# Aligned by Design — an OpenSpec demo

How spec-driven development with [OpenSpec](https://github.com/Fission-AI/OpenSpec) keeps **the
business, the engineering teams, the developer and AI** working from one agreed picture of a
feature.

This repo has two parts:

1. **A presentation** for business and technology leaders, with a slide-by-slide speaker guide.
2. **A worked example** it's built on: one complete OpenSpec change for a fictional retail bank's
   *scheduled and recurring transfers*, which also serves as the live demo.

---

## The presentation

| | |
|---|---|
| ▶ **View the slides** | [Aligned by Design (v1)](https://raw.githack.com/ausangshukla/openspec_demo/master/presentation/v1/aligned-by-design.html) · [source](presentation/v1/aligned-by-design.html) |
| 🗒 **Speaker guide** | [presentation-guide.md](presentation/v1/presentation-guide.md): core point, talking points, examples and a summary for every slide |

In the slides: **→ / Space / click** for next, **←** for previous, **N** to show the speaker notes.
Download the HTML file and open it in any browser to present offline (the fonts need an internet
connection; without one it falls back to system fonts).

**The argument in three lines**

> 1. Alignment means one agreed picture, shared by the business, the engineering teams, the
>    developer and the AI.
> 2. For each document, the AI takes in the inputs, probes with questions, reflects back what is
>    and isn't covered, and organises the answers; the owners sign.
> 3. It stays aligned because every change goes through the documents first.

---

## The worked example

`openspec/changes/scheduled-transfers/` starts from a one-line ticket, *"Let customers schedule
payments"*, and turns it into a complete, agreed, traceable change:

| Document | What it holds | Signed by |
|---|---|---|
| [proposal.md](openspec/changes/scheduled-transfers/proposal.md) | Why, 5 user stories, screens, 7 things we won't do (high level) | Business sponsor |
| [business-rules.md](openspec/changes/scheduled-transfers/business-rules.md) | 17 rules (BR-n) with owner and source, 10 decisions (D-n) with rationale, 12 customer messages (MSG-n) | Product, risk, compliance, operations |
| [spec.md](openspec/changes/scheduled-transfers/specs/scheduled-transfers/spec.md) | The rules restated as testable WHEN / THEN scenarios | — |
| [design.md](openspec/changes/scheduled-transfers/design.md) | Architecture, 13 diagrams, 21 worked cases (happy / edge / error), cross-cutting concerns, design decisions | Development, architecture, security, QA |
| [tasks.md](openspec/changes/scheduled-transfers/tasks.md) | 26 tasks in 4 phase-gated steps, each citing the design and naming the cases its tests prove | Developer |

`lld.md` and `qa-checklist.md` are deliberately left unwritten, so they can be generated live in
the demo.

**Traceability, checked:** every business rule is proven by a worked case (17/17), every spec
scenario is covered (22/22), and every worked case is assigned to exactly one phase-gate test
(21/21).

**A change mid-flight:** branch `retry-8pm` moves the insufficient-funds retry from 6 pm to 8 pm.

```bash
git diff master retry-8pm   # 4 documents, 22 lines; the proposal doesn't change
```

> The bank, customers and business rules are fictional, invented to make the demo concrete.

---

## The process and templates

The flow is defined by a custom schema in [`openspec/schemas/spec-driven/`](openspec/schemas/spec-driven/):

```
explore → proposal → business-rules → specs → design → lld → qa-checklist → tasks → apply
```

Each step has a template (the sections it must contain) and an instruction (what the AI reads,
what it probes for, and what it must reflect back before finishing). Beyond stock OpenSpec, the
schema adds:

- **business-rules** — rules, decisions and customer wording in one place, with IDs
- **lld** — a low-level design with reviewer checks and a sign-off
- **qa-checklist** — a manual checklist that reuses the same case IDs as the tests

Adding your own (for example `security-review.md` or `architecture.md`) is a template plus an
entry in `schema.yaml`.

---

## Try it

Requires the [OpenSpec CLI](https://github.com/Fission-AI/OpenSpec):

```bash
npm install -g @fission-ai/openspec

openspec status   --change scheduled-transfers   # which documents exist, what's next
openspec validate scheduled-transfers --strict   # structural checks
openspec show     scheduled-transfers            # the change at a glance
```

The OpenSpec skills for [Claude Code](https://claude.com/claude-code) are in `.claude/skills/`
(`openspec-explore`, `openspec-propose`, `openspec-apply-change`, …), so an AI agent can run the
same workflow: explore an idea, draft the next document, or implement the tasks phase by phase.

---

## Repository layout

```
presentation/v1/                     slides (HTML) and speaker guide
openspec/changes/scheduled-transfers the worked example
openspec/schemas/spec-driven/        the custom schema and templates
.claude/skills/                      OpenSpec skills for Claude Code
```
