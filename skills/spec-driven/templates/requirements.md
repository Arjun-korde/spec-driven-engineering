# Requirements

Lite mode: may merge into docs/spec.md; Approver: self (solo) allowed. [addition]

<!-- Delete sections that do not apply. Every requirement needs at least one AC (incl. a failure AC)
     and states missing/empty/delayed/denied/failed behavior or "n/a: reason". No implementation choices. -->

Project: [name]
Status: Needs review | Approved | Needs update
Source: docs/intent.md

## Problem statement
[from intent]

## Actors and permissions
<!-- Roles may be people, systems, jobs, or consumers. Write "no roles" if none. -->
| Role/actor | Can do | Cannot do (n/a if no restriction) |
|---|---|---|
| [role] | [actions] | [restricted actions] |

## Requirements
Type: functional | nonfunctional | business-rule | constraint. Priority: Must | Should | Could | Won't.
Optional per requirement: Title, User value, Dependencies, Notes.

| ID | Type | Priority | Requirement | Missing/empty/delayed/denied/failed behavior |
|---|---|---|---|---|
| REQ-001 | functional | Must | [Actor] must be able to [action] [object] so that [outcome]. | [behavior or n/a: reason] |
| REQ-002 | nonfunctional | Should | [Quality condition with measurable limit and workload]. | [behavior when limit exceeded] |
| REQ-003 | business-rule | Must | [Policy the system must enforce]. | [behavior on violation] |
| REQ-004 | constraint | Must | [Technology, time, data, environment, or integration limit]. | n/a |
Missing/empty/delayed/denied/failed: write 'n/a: reason' for states that cannot occur (e.g. denied in a local tool, delayed for a pure function). [addition]

## Acceptance criteria
| AC | REQ | Given | When | Then |
|---|---|---|---|---|
| AC-001 | REQ-001 | [starting condition] | [action or event] | [expected result] |
| AC-002 | REQ-001 | [edge condition] | [action] | [expected result] |
| AC-003 | REQ-001 | [failure/denied/empty condition] | [action] | [safe behavior or error response] |

## Assumptions [addition]
Provisional choices: recorded, with a reason and a confirm-by owner; they do not block work unless they affect scope, security, data, contracts or cost. Open questions below must not be guessed.
| Assumption | Why | Confirm by (who/when) | Status (open/confirmed/rejected) |
|---|---|---|---|
| [ ] | [ ] | [ ] | open |

## Open questions
| # | Question | Owner | Must be answered before design? |
|---|---|---|---|
| 1 | [ ] | [ ] | yes/no |

## Quality checklist (per requirement)
- [ ] Clear (no guessing)  - [ ] Actor defined  - [ ] Action defined  - [ ] Bounded
- [ ] Testable  - [ ] Traceable  - [ ] No implementation leak  - [ ] Missing states covered (missing, empty, delayed, denied, failed)
- [ ] Two readers would interpret it the same way
