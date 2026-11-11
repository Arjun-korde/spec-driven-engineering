# Code Review Checklist
Items from book ch20 and App P (Code Review Checklist); items marked [addition] are not from the book.
Delete sections that do not apply. Review in this order; do not judge style before requirement fit.

Feature or module: [name]
Requirement IDs: [REQ-###]   Task IDs: [TASK-###]   Change: [CHG-### if any]
Reviewer: [role/name]   Date: [date]   Review status: Needs review | Approved | Needs update
Reviewed by: [ ] human  [ ] separate agent session  (the authoring agent's self-check is a first pass only)

## Checklist (pass condition)
1. Requirement fit
- [ ] Every requirement is fully implemented and traceable [book]; use a REQ/TASK ID in tests or the change description [addition]
- [ ] Code implements only approved requirements; no hidden product decisions [book App P]
- [ ] Each acceptance criterion has a matching test or manual check
- [ ] No extra behavior that was not requested
- [ ] Permitted roles respected; failure path handled
2. Design
- [ ] Responsibilities sit in the correct module or layer (per project architecture notes)
- [ ] Duplicated logic removed when safe [book App P]
- [ ] Error handling is explicit and tested [book App P]
- [ ] Validation is not hidden in random helpers; data access is not scattered
3. Security and validation
- [ ] Identity and permission are checked; refusals do not leak whether protected data exists
- [ ] Missing, malformed, or dangerous input is rejected early
- [ ] Secrets are out of source and logs
- [ ] Errors are safe and useful; internal detail only in internal logs
- [ ] Logs are useful without exposing sensitive data [book App P]
- [ ] Security-sensitive paths have negative tests [book App P]
- [ ] Tests verify business behavior, not only implementation details [book App P]
- [ ] Validation vs permission order matches the recorded decision: [ADR-### or note]
4. Performance
- [ ] No repeated work in loops, overfetching, unbounded requests, or needless blocking work
- [ ] Any target used is recorded in the spec
5. Readability
- [ ] Names are clear and the code can be explained simply
6. Refactoring safety (if a refactor was done)
- [ ] One goal, limited scope; behavior unchanged unless the spec approves the change [book App P]; earlier tests still pass
- [ ] Spec updated if the structure became clearer

## Requirement review result
Status per requirement: Fully satisfied | Partially satisfied | Missing | Extra behavior
| REQ-### | Status | Reason | Evidence (TEST-### / location) |
|---|---|---|---|

## Review comments
Severity: P0 = security/data loss/blocks release; P1 = major; P2 = minor; P3 = cosmetic.
| # | Affected REQ/artifact | Risk | Severity | Recommended fix | Changes the spec? |
|---|---|---|---|---|---|

## Decision
[ ] Approve   [ ] Needs update (P0/P1 open: block)   Approver: [role]
Open "Partial" items: fixed | deferred (owner, date) | spec question raised
