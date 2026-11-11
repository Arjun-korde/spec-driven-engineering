# 10 - Version Control and Change Tracking

## Purpose
Keep agent-produced work isolated, reviewable, traceable, and reversible, and keep specs in step with what was actually built. [book ch 15]

## When to use
- Before the agent starts a task (create a safe point and an isolated change set).
- When a change is finished and needs review.
- Whenever a requirement, design, or scope changes.

## Inputs
- The task (TASK-###), its requirement (REQ-###), the diff or change list, test results.

## Output artifact
- An isolated change set, a traceable change record (commit or log entry), a review package (`templates/review-package.md`), updated specs and traceability, and a CHG-### record when the spec changes.

## Gate (done-when)
- [ ] Work was done in an isolated change set (branch or equivalent) for one task. [book ch 15]
- [ ] Change record names type, scope, action, and the requirement ID. [book ch 15]
- [ ] Review package explains what changed, why, how it was tested, files changed, and notes. [book ch 15]
- [ ] Changed-file list matches the task's allowed files; any extra file is explained. [book ch 15, 16]
- [ ] The document(s) named in section 5 were updated, and traceability matches the final behavior. [book ch 15]
- [ ] Nothing was accepted that the reviewer does not understand. [book ch 15]

---

## 1. Principle: isolation, review, traceability
- A task plan says what the agent should do; change tracking shows what it actually did. [book ch 15]
- The hosting platform is not important. The important habit is isolation, review, and traceability. [book ch 15]
- Do not let the agent make a large uncontrolled change and ask you to trust it. Work in small steps, review each, record only work you understand. [book ch 15]
- Change tracking does not replace specs, tests, or review; it gives them a place to live. A good record answers: what changed, why, which requirement caused it. [book ch 15]

| AI risk | Response |
|---|---|
| Agent changes more files than expected | Review the diff before accepting |
| Agent mixes features | One change set per task |
| Agent removes working behavior | Compare to the previous record and restore safely |
| Fixes a bug but breaks a requirement | Run tests and check the requirement ID before accepting |
| You forget why a file changed | Clear records linked to specs and tasks |
[book ch 15]

## 2. Choose a tracking method (tool-agnostic)
Use the strongest one available; record the choice in the instruction file. [addition: tool-agnostic framing; book lists the three methods in ch 4]

| Method | Use when | How |
|---|---|---|
| Version control system (Git is the common default; any system works) | One exists or can be created locally | Branch/change set per task; commit per meaningful change |
| Change log | No version control | Dated entries in a change-log file (suggested fields: date, TASK/REQ, what, why, files, result [addition]) |
| Manual snapshots | No version control, big change coming | Copy the project folder, label clearly, before major changes |
[book ch 4]

- A remote host is optional. Local tracking is enough. [book ch 4, 15]
- Track documents (specs, tasks, tests) in the same place as the deliverables. [book ch 15]
- First record: a clean baseline (folders, starter docs, a clear place for future work), taken before the agent starts. [book ch 15]
- Record events: a requirement changed, the agent completed a task, a change was accepted or rejected (with why). [book ch 4]

## 3. Change sets and records
### Isolated change set per task
- If Git: a branch for one task, named with the requirement, e.g. `feature/REQ-006-short-name`. Review the diff, run tests, then commit. [book ch 15]
- If no version control: snapshot before the task; list changed files in the change log after. [addition]

Git sequence (illustrative; adapt to your VCS): [book ch 15]
```
git checkout main
git checkout -b feature/REQ-006-short-name
# Let the agent work on one task. Review the files. Run the tests.
git status
git diff
git add [allowed paths only]
git commit -m "feat(scope): action for REQ-006"
```

### Record format (commit message or change-log line)
```
type(scope): action for REQ-###
```
- Types in common use: feat, fix, test, docs, chore, refactor. [book ch 15; refactor: addition]
- Always include the requirement ID when the change implements, fixes, or documents a requirement. For pure housekeeping with no requirement, use the TASK-### or CHG-### ID. [addition: resolves book example "fix(api): return 401..." which lacked an ID]

| Weak | Better |
|---|---|
| update login | feat(auth): add login validation for REQ-002 |
| fix bug | fix(api): reject missing project name for REQ-003 |
| tests | test(tasks): add due-date validation tests for REQ-004 |
| changes | docs(spec): update task status rules after review (CHG-007) |
[book ch 15; IDs normalized]

Local record suggestions (also usable as change-log lines): new intent doc; refined requirements and acceptance criteria; new task file; test plan added; implementation completed (TASK-###); review notes recorded. [book ch 4]

## 4. Issues and work requests
A work request must not be vague. Book issue fields: issue title, requirement ID, spec source, goal, acceptance criteria, files likely affected. [book ch 15] The task file (`templates/task.md`) serves as the issue when no tracker exists. [book ch 15; mapping to task file: addition]

## 5. Which document to update per change type
| When this happens | Update this document |
|---|---|
| A rule becomes clearer during implementation | Requirements |
| A design decision changes | Technical spec or ADR |
| A new test case is discovered | Test plan |
| A task produces extra work | Task list and traceability matrix |
| A behavior is removed or postponed | Scope and out-of-scope notes |
[book ch 15]

Additions: [addition]
| When this happens | Update this document |
|---|---|
| Scope, requirement, or design changes after approval | Create CHG-### (section 6), then the affected docs |
| A bug is found | BUG-### record, linked test (TEST-###), and requirement if it was ambiguous |
| A file layout, convention, or rule changes | Context pack and agent instruction file |
| Release prepared | Release/deployment checklist and maintenance notes |

- Do not leave code and spec separated. Update the spec when implementation reveals unclear rules. [book ch 15]
- Spec drift: code changes while the spec stays behind; the longer it lasts, the less the spec can be trusted. [book ch 15]
- Update the spec in the same review package as the code (not after merge), so the package shows the final spec. [addition: resolves the book's step order, where spec updates came after opening the review]

## 6. Spec change records (CHG-###)
Record every approved spec change in one format: `templates/spec-change-record.md` (fields, statuses, and prompt are defined only there). [book ch 24, ch 30]
- No implementation of a changed behavior before the CHG is approved. [addition, consistent with book ch 14 "no new code without a matching approved reason"]

## 7. Review package (pull-request equivalent)
A review package is a review packet: requirement links, what changed, how tested, files changed, review notes. Use `templates/review-package.md` (includes security notes, data/interface changes, rollback notes). Where a host provides pull requests, paste it there. [book ch 15, App L]

- If the package cannot explain what changed and why, it is not ready. [book ch 15]
- Fixed review order prevents missing hidden scope changes: (1) re-read requirement and ACs; (2) check changed-file list before code; (3) inspect diff for unexpected deletions or unrelated edits; (4) run or review linked tests; (5) check error handling, validation, security-sensitive paths; (6) update traceability and specs if a documented decision changed; (7) accept only after understanding. [book ch 15]
- Also confirm: no unrelated formatting or dependency changes are mixed in, and the agent had the current context pack. [book App L]

### Agent prompt: scoped implementation with before/after reporting
```
You are working on requirement [REQ-###].
Goal:
[goal]
Rules:
- [rule 1]
- [rule 2]
- Do not change unrelated behavior.
- Do not modify files outside the listed scope unless you explain why first.
Files in scope:
- [path]
Before coding:
1. Summarize the requirement.
2. List the files you plan to change.
3. List the tests you will add.
After coding:
1. Summarize the changes.
2. Explain how each change maps to [REQ-###].
3. Explain how the tests prove the behavior.
```
[book ch 15, adapted: the book's "Likely files" is treated as a hard scope list per ch 16; a stricter stop-and-ask variant of the scope rule is an [addition]]

### Agent prompt: self-review before acceptance
```
Review your own changes before I accept them.
Explain:
1. Every file you changed.
2. Which requirement each change supports.
3. Which tests prove the change works.
4. Any assumptions you made.
5. Any files you changed that were not listed in the task plan.
```
[book ch 15]

## 8. End-to-end change flow
| Step | Action | Output |
|---|---|---|
| 1 | Write or confirm the requirement | REQ-### with ACs |
| 2 | Create the task / work request | TASK-### |
| 3 | Create the isolated change set | Branch or snapshot |
| 4 | Prompt the agent (plan first) | Approved plan |
| 5 | Review the diff | Only allowed files changed |
| 6 | Run tests | Linked TEST-### pass |
| 7 | Record the change | Commit or change-log entry with REQ ID |
| 8 | Prepare the review package | Requirement, tests, files, notes, spec updates |
| 9 | Accept | Specs and traceability match final behavior |
[book ch 15; step 9 reordered: addition]

## Layout
Use the canonical layout in SKILL.md (docs/, docs/decisions/, docs/changes/, docs/feedback/, docs/context/, tasks/, plus the project's code and tests). The change log and review log live in `tasks/review-notes.md` unless the project already has a place. Adapt names to the existing project; do not force a new tree. [book ch 4, 15; layout: addition]

## Common mistakes
| Mistake | Fix |
|---|---|
| Large change accepted on trust | Small steps; review each [book ch 15] |
| Vague record ("fix bug") | Type, scope, action, REQ ID [book ch 15] |
| Several tasks in one change set | One change set per task [book ch 15] |
| Spec updated "later" | Update in the same package [book ch 15; timing: addition] |
| Secrets committed | Keep secrets and credentials out of the repository [book App L] |
| Stack traces in user-facing output | Internal logs only [addition] |
| No baseline before the agent starts | Take a clean baseline/snapshot first [book ch 15] |

Book inconsistencies resolved here are listed in docs/resolved/10-14.md.

Role notes: Requester/PM approves CHG-### records and accepted scope changes; Builder keeps change sets small, writes records, and updates specs; Reviewer/QA follows the review order and refuses packages that cannot explain what changed and why. [addition]
