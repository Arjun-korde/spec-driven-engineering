# Review Package: [TASK-### / REQ-###: short title]

<!-- Pull-request equivalent. Paste into a host's review request if one exists, otherwise save with the change log. If it cannot explain what changed and why, it is not ready. Delete sections that do not apply. [book ch 15, App L] -->

## Requirement link
- [REQ-###]: [short statement]
- Acceptance criteria covered: [AC-### list]
- Task: [TASK-###]   Spec change: [CHG-### or None]

## What changed
- [change 1]
- [change 2]

## How it was tested
- [tests run, TEST-### IDs, results]
- [normal / edge / failure cases checked]

## Files changed
- [path]  (allowed by task: yes/no; reason if no)

## Spec and traceability updates
- [documents updated and what changed, or "none needed"]

## Security notes [book App L]
- [security-sensitive paths touched, or "none"]

## Data or interface changes [book App L]
- [persisted data / interface contract changes, or "none"]

## Rollback notes [book App L]
- [how to undo this change safely]

## Open questions, assumptions, risks
- [item]

## Review notes (what the reviewer should confirm)
- [e.g. error message does not reveal sensitive details]
- [e.g. value matches the technical spec]

## Reviewer order [book ch 15]
- [ ] Re-read requirement and acceptance criteria
- [ ] Check the changed-file list before reading code
- [ ] Inspect the diff for unexpected deletions, unrelated edits, or unrelated formatting/dependency changes
- [ ] Secrets and credentials are not committed [book App L]
- [ ] Run or review linked tests
- [ ] Check error handling, validation, security-sensitive paths
- [ ] Specs and traceability match the final behavior
- [ ] Accept only after understanding the change

Review status: Needs review | Approved | Needs update
