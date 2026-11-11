# Resolved inconsistencies: 04-traceability

Book inconsistencies resolved in references/04-traceability.md. All resolutions are [addition].

- 2-digit vs 3-digit task/test IDs and area-coded IDs: standard `PREFIX-###`.
- Worked example had 4 tasks and 3 tests: every task gets a test.
- Matrix template had a Requirement column that the worked row omitted, and no AC column: both included.
- Status vocabularies (Draft/Ready/Done, Passing, Ready for review): split into task status, review status, and test state (Plan state + Result).
- Design link "or implementation approach" vs mandatory design decision: required unless the requirement has a single obvious implementation, which is then stated.
- Bare-string login error vs structured error contract: structured shape used.
- Session expiry both mentioned in the weak/better task and treated as a missing requirement: modeled as a new REQ.
- Capstone task numbers in the book's matrix did not match its task table: not copied; IDs are assigned from the live task list.
