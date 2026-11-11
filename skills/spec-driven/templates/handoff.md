# Handoff templates

Lite mode: may merge into docs/spec.md; Approver: self (solo) allowed. [addition]

Delete sections that do not apply. Pick the part by "who am I handing to".

## Part A - Requester -> Builder [book ch 29]
```
Feature name:
Problem statement:
Target users:
User goals:
Must-have requirements:
Acceptance criteria:
Non-goals for this release:
Known constraints:
Risks and sensitive areas:
Open questions:
Decision owner:
Date of handoff:
```
Check: problem is not just a feature request; each REQ-### must-have has a pass/fail acceptance criterion; non-goals listed; open questions have an owner. [addition]

## Part B - Builder -> Agent [book ch 29]
```
Task ID: TASK-[###]
Linked requirement(s): REQ-[###]
Goal:
Relevant context to use:
Files or modules in scope:
Files or modules out of scope:
Constraints:
Expected output:
Tests to create or update: TEST-[###]
Questions to ask before changing code:
Review checklist:
Do not proceed if:
```
Check: one bounded task; reviewable against requirements, tests, or architecture (Agent Control Rule). "Do not proceed if" = stop conditions, e.g. an answer is missing or a forbidden area would change. [addition]

## Part C - Reviewer -> Decision [addition; layers from book ch 29]
```
Task / change: TASK-[###]
Linked requirement(s):
Layer findings (evidence for each):
- Requirement fit:
- Architecture fit:
- Security and privacy:
- Test evidence:
- Maintainability:
Overbuilt or out-of-scope changes:
Questions for the Requester:
Decision: Accept | Revise | Block
Spec / test update required: Yes / No
Reviewer / date:
```
