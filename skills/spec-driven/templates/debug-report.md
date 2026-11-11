# Debug Report: BUG-###
Delete sections that do not apply. Fill Symptom, Expected, Actual, and Evidence BEFORE changing code.

Related requirement: [REQ-###]   Related task/test: [TASK-### / TEST-###]
Feature: [name]
Priority: P0 | P1 | P2 | P3   (P0 = blocks release / data loss / security; P1 = major; P2 = minor; P3 = cosmetic)
Status: todo | doing | blocked | review | done
Reported by / owner: [role]   Date: [date]

## Symptom
[what failed]

## Expected behavior
[what should have happened, per the requirement]

## Actual behavior
[what happened instead]

## Evidence pack (tick before asking the agent) [book App O]
- [ ] Expected behavior from the spec  - [ ] Actual behavior  - [ ] Failing test name and output
- [ ] Stack trace or log excerpt  - [ ] Recent changes that may have introduced the issue  - [ ] Files or modules likely involved

## Evidence
[logs, trace, failing test, screenshot description, or reproduction steps. For non-code work: the primary failure evidence.]
Reproduction steps:
1.
2.

## Evidence vs requirement
[the gap or contradiction; where actual first differed from expected]

## Root cause
[the real cause, not just the error message. Is the requirement itself silent on this case?]
Alternative causes checked (two): 1. [ ]  2. [ ]

## Broken assumption (if any)
| Assumption | Risk | Spec update | Test update |
|---|---|---|---|

## Smallest safe fix
[what changed; one cause per change]

## Regression test
TEST-###: [name]   Fails before fix: [yes/no]   Passes after fix: [yes/no]
Old tests re-run: [pass/fail]

## Spec update needed
[ ] Requirement  [ ] Technical spec / interface contract  [ ] Agent instruction file  [ ] Test spec
CHG-###: [if the spec changed]

## Root cause review [book App O]
- [ ] Root cause is supported by evidence
- [ ] Fix addresses the cause, not only the symptom
- [ ] Fix does not weaken validation, tests, or security
- [ ] Regression test added or updated
- [ ] Lesson learned recorded below

## Prevention note
[what will not be repeated]

## Repeat-error checklist
- [ ] A test fails before the fix and passes after
- [ ] Requirement states expected behavior clearly
- [ ] Technical spec states the system rule clearly
- [ ] Agent instruction warns against the previous mistake
- [ ] The bug cannot silently return in a future edit
- [ ] The fix does not weaken validation, tests, or security [book App O]

## Triage (QA)
Severity/priority reason: [ ]   Decision: fix now | defer (owner, date) | reject as spec gap (CHG-###)
