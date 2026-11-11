# Reliability Spec: [Feature]
Lite mode: may merge into docs/spec.md; Approver: self (solo) allowed. [addition]
Delete sections that do not apply. Rule form: "If X fails, the system must do Y, record Z, and show message M." [book ch 22; structure adapted: the book's separate Background Job Rules and Queue Failure Rules are merged into the deferred-work table]

Feature: [name]   Requirement: REQ-###   Review status: Needs review | Approved | Needs update

## Normal behavior
[what happens when everything works]

## Failure states (repeat the block per state)
### Failure state: [name]
- Trigger:
- Recovery path:
- User/caller message:
- Log event (name, severity, fields):
- Test case: TEST-###

## Timeout rules
Also state the total run budget so timeouts x retries fit inside it. CLI: interrupts/signals and partial output. [addition]
| Operation | Max wait | On timeout |
|---|---|---|

## Retry rules
| Operation | Retries | Delay | Idempotent? | Do not retry when | Stop condition |
|---|---|---|---|---|---|

## Deferred / background work (Applies when: queues, jobs or schedules exist)
Pipeline/scheduled alternative: schedule and timezone, missed run, overlapping runs (lock or skip), rerun idempotency, late or partial input, failure notification, per-item or per-recipient results. [addition]
| Job | Trigger | Minimum input | Retry rule | Failure state (queue failure rule) | User visibility |
|---|---|---|---|---|---|
Use one status vocabulary (e.g. pending / failed / completed).

## Data safety rules
[no false success, no duplicate side effects, consistent state after failure]

## Monitoring or alerting notes (Applies when: the feature runs unattended or in production)
[signals and who reviews; see monitoring-maintenance template]

## Definition of done
- [ ] All expected failure states handled. [book ch 22] None invented by the agent. [addition]
- [ ] Logs safe (no secrets) and useful. [book ch 22] Stack traces internal only. [addition: reconciles ch 21 and ch 24]
- [ ] User-facing errors clear and safe.
- [ ] Tests cover normal and failure behavior, written before implementation. [addition]
