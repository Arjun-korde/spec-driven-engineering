# Technical Specification: [System or feature name]

Lite mode: may merge into docs/spec.md; Approver: self (solo) allowed. [addition]
Draft the deployment plan (templates/deployment-plan.md) while doing this stage. [addition]

Delete sections that do not apply. Do not guess: put undecided items in section 10.

Status: Needs review | Approved | Needs update
Linked requirements: REQ-### ...
Test/deployment approach: see test-spec.md and deployment-plan.md (or add sections here)
Version / date: [ ]

## 1. System overview
- System name: [ ]
- Purpose (1-2 sentences): [ ]
- Primary users/consumers: [role or system]
- Core capabilities: [ ]
- System boundary: includes [ ]; does NOT include [ ]
- External dependencies: [services, APIs, file systems, models]
- Assumptions: see the Assumptions table below

## 2. Architecture overview
- Style: [chosen style] - see ADR-###
- Components (name | owns | must not do):

| Component | Owns | Must not do |
|---|---|---|
| [ ] | [ ] | [ ] |

- Data flow: [how data moves from trigger to result]
- State ownership: [where important state is stored and updated]
- Trade-offs accepted: [ ]
- Diagram (text/ASCII is fine): [ ]

## 3. Interface surface (UI, CLI output, public API, report format)
Applies when: there is a UI. CLI/library/job alternative: commands, flags, stdout/stderr, exit codes, report or message format. [addition]
- Entry points (screens/commands/functions): [ ]
- Inputs and fields (required/optional, type, validation): [ ]
- States: loading | empty | success | error | disabled | permission-denied [keep those that apply; job: outcome states such as success / partial / failed / skipped] [addition]
- Accessibility / usability basics: [ ]

## 4. Core logic / services
| Rule (plain language) | Linked REQ | TASK / TEST (filled in later) |
|---|---|---|
| [ ] | REQ-### | |

- Authorization rules (only if actors differ in what they may do): [ ]
- Background jobs / schedule (timezone, overlapping runs, idempotency): [ ]
- Integrations: see section 6

## 5. Data model
See `data-model.md`. Summary: [entities, key relationships, owner/isolation boundary, deletion rules]

## 6. Interface contracts
See `interface-contract.md` (one per interface).
| Interface | Kind (HTTP/CLI/library/event/file/model) | Contract location | Version |
|---|---|---|---|
| [ ] | [ ] | [ ] | [ ] |

Project error shape (single): `code`, `message`, optional `field`. Internal detail goes to logs only.

Integrations (per external service): provider | purpose | data sent | data received | failure behavior | timeout/retry | security rule

## 7. Security
Summary only; rules live in security-rules.md. Authentication/Authorization rows: only if users sign in. [addition]
| Area | Requirement | ID |
|---|---|---|
| Authentication | [ ] | SEC-### |
| Authorization | [ ] | SEC-### |
| Input validation | [ ] | SEC-### |
| Sensitive data | [ ] | SEC-### |
| Secrets (none in source or examples) | [ ] | SEC-### |
| Safe errors (no stack traces/private data to users) | [ ] | SEC-### |

## 8. Performance
| Operation | Target (project sets) | Data size / load | How measured |
|---|---|---|---|
| [ ] | [ ] | [ ] | [ ] |

## 9. Error handling
| Situation | User/consumer sees | Logged internally | Retry/fallback |
|---|---|---|---|
| Invalid input | | | |
| Not authenticated / no permission (only if users sign in) | | | |
| Not found | | | |
| External service failure | | | |
| Unexpected failure | general safe message | full detail | |

## 10. Open questions
| Question | Decision owner | Blocks implementation? (yes/no) | Resolution |
|---|---|---|---|
| [ ] | [ ] | [ ] | |

## 11. Decisions and traceability
ADRs: docs/decisions/ADR-### [title] ...
Each important decision links to a REQ; TASK/TEST links are filled in later in docs/traceability-matrix.md (do not duplicate the mapping here).

## Assumptions [addition]
Provisional choices: recorded, with a reason and a confirm-by owner; they do not block work unless they affect scope, security, data, contracts or cost. Open decisions (section 10) must not be guessed.
| Assumption | Why | Confirm by (who/when) | Status (open/confirmed/rejected) |
|---|---|---|---|
| [ ] | [ ] | [ ] | open |
