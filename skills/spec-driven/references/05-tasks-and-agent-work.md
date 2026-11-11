# 05 - Tasks and Agent Work

## Purpose
Turn approved specs into small, ordered, testable tasks, and direct the agent through them with narrow, reviewable instructions. [book ch 11, 12, 13, 14]

## When to use
- Before the agent writes or changes any deliverable (code, document, configuration, data, prompt).
- When a feature spec is approved and must be broken into work units.
- When an agent output drifted, grew too large, or cannot be reviewed.

## Inputs
- Approved requirements (REQ-###) with acceptance criteria (AC-###), design decisions (ADR-###), interface contracts, test plan (TEST-###).
- The project's instruction file for the agent (see `templates/agent-instructions.md`).

## Output artifact
- Task list / task files (`templates/task.md`), one context pack per task (`templates/context-pack.md`), the agent's plan, and the agent's completion report.

## Gate (done-when)
- [ ] Each task has one outcome, links to a REQ/ADR, and has checkable done criteria. [book ch 14]
- [ ] Dependencies are listed; P0/P1 tasks are ordered before P2/P3. [book ch 14]
- [ ] Each task states allowed files/areas, not-allowed files/areas, out-of-scope, and a stop condition. [book ch 14, 16, 25]
- [ ] Tests for the task (>=1 normal, >=1 edge, >=1 failure) are planned before implementation starts. [book ch 16, 17]
- [ ] The agent's plan was approved before it edited anything. [book ch 11]
- [ ] The agent's completion report exists and a human reviewed the result before the next task starts. [book ch 11]
- [ ] The context pack was updated after review if any decision, file layout, or rule changed. [book ch 12]

---

## 1. Working model: the agent is a fast junior collaborator
- Treat the agent as a fast junior collaborator, not an independent engineer. It does not know business rules, hidden constraints, architecture, or your quality standard unless told. [book ch 11]
- Do not ask it to "build the whole app". Ask for one task linked to a requirement, a design decision, and a test expectation. [book ch 11, 16]

| Agent is good at | Agent is weak at |
|---|---|
| Boilerplate from clear instructions | Vague requirements |
| Small, well-scoped tasks | Business trade-offs |
| Tests from acceptance criteria | Spotting all security/performance risks unaided |
| Explaining code, proposing refactors | Undocumented legacy behavior |
| Repetitive edits | Protecting architecture without stated boundaries |
[book ch 11]

### Chat assistant vs agent with workspace access
| Situation | Use chat when | Use the agent with workspace access when |
|---|---|---|
| Planning | You need options, risks, structure | A task exists and changes must be applied |
| Spec work | Drafting requirements or specs | Updating local spec files from approved text |
| Implementation | You want pseudocode or an explanation first | Changes are to be made in approved files |
| Review | You want independent critique | You want checks run and mismatches reported |
[book ch 11]

Rule: think and plan in chat; then give the agent a narrow, approved task. [book ch 11] Choose the simplest assistant that fits the current stage. [book ch 4]

## 2. The six-field instruction pattern
Every task assignment contains: [book ch 11]

```
Task: [exact task]
Source of truth: [REQ/AC/ADR ID, spec section, or task file]
Allowed files: [files, folders, modules, or artifacts the agent may inspect or edit]
Do not change: [files, behavior, or design choices that are off-limits]
Expected output: [deliverable, tests, documentation, or review notes]
Completion check: [how the work will be verified]
```

| Weak | Stronger |
|---|---|
| Add sign-in. | Implement REQ-004 only: credential-based sign-in. Use the existing user model. Do not add other sign-in methods. Add validation and checks. |
| Fix the summary view. | Fix TASK-012: the project count excludes archived projects. Update the counting function and its check only. |
| Improve the interface. | Update the create-item contract to require name and owner. Do not change response fields. Add validation errors for missing fields. |
[book ch 11; IDs and domain neutralized]

Mapping of the book's five prose elements to the six fields: boundary = Allowed files + Do not change; review standard = Completion check. [addition]

Example, one safe task (domain is illustrative; adapt to web API, CLI, library, data pipeline, or LLM feature): [book ch 11]
```
Task: Implement validation for REQ-004 (sign-in input).
Source of truth: REQ-004 and the interface contract's error rules.
Allowed files: sign-in handler, input validator, validator check.
Do not change: registration, password reset, persisted-data schema, user roles.
Expected output: validation logic plus checks for missing identifier and missing secret.
Completion check: checks pass and error responses match the interface contract.
```

## 3. Control rules
| Rule | Apply it by |
|---|---|
| One task at a time | Do not combine unrelated features in one request |
| Approved files only | Name the files/folders the agent may edit |
| Plan before edit | Require a short plan before implementation |
| No silent assumptions | Agent reports unclear requirements before building |
| Tests required | Every behavior change adds or updates a check |
| Review before next task | Check the current result before starting another |
| Keep tests | Never remove or weaken tests to make code pass [book App H] |
| No blanket fixes | "Never ask an AI agent to “just fix everything.” Ask it to work from one requirement, one task, one failing test, or one review checklist at a time." [book App J, Prompt Safety Rule] |
[book ch 11]

Spec artifacts as guardrails: requirement = behavior that must exist; acceptance criteria = how correctness is judged; technical spec = design to follow; interface contract = fields, outputs, error rules; traceability matrix = shows whether the task, test, and code are linked to the requirement. [book ch 11]

## 4. Plan-before-edit workflow (prepare / implement / report)
| Stage | Agent must | Human checks |
|---|---|---|
| Prepare | Restate the task, list files it will inspect/change and tests it will add, list assumptions and questions | Scope understood, not expanded |
| Implement | Change only approved files; smallest safe change | Matches the spec; no surprise behavior |
| Report | Summarize changes, tests, risks, open questions | Reviewable without hunting through every file |
[book ch 11, 15]

Approval: always approve the plan for tasks that touch scope, security, data, or architecture or whose spec is unclear; for trivial, fully specified tasks the human may pre-approve the plan in the task file. [addition: the book is ambiguous on whether plan approval is needed for every task]

Practical test: if the agent cannot explain what it changed, why, and how to verify it, the task is not complete. [book ch 11]

### Standard order of tasks within one unit of work
1. Task selected, context pack built. 2. Agent states plan; human approves. 3. Tests written or confirmed first when practical. 4. Agent implements. 5. Run the task's tests. 6. Agent self-review and completion report. 7. Human review. 8. Record any approved spec change (see `10-version-control-and-change.md`) and update context pack. [book ch 25, 12, 16; step ordering merged: addition]

## 5. Context slices and context packs
- Context = background, structure, requirements, decisions, and task details given before the agent acts. Too little causes guessing; too much causes confusion. Give the right information, in the right order, for the current task. [book ch 12]
- A prompt says what to do now; context says what world the agent works in. [book ch 12]
- Test before asking for work: "What does the agent need to know to avoid guessing?" If it is not in the prompt, the pack, or visible files, the agent may invent it. [book ch 12]
- The context must answer: What is this project? What is the task now? Which files matter? What must not change? How will we know it is correct? [book ch 11]

### Context slice pattern (focused task)
1. Current goal. 2. Relevant requirement. 3. Technical rule (architecture, interface, data, or style constraint). 4. Acceptance criteria. 5. Restrictions (what must not change). [book ch 12]

### What to include / exclude
Rule: include what the task type depends on; exclude unrelated layers and features. Smallest context that still allows safe completion. [book ch 12]

| Task type (examples) | Include | Usually exclude |
|---|---|---|
| User-facing screen or output | User story, behavior, component rules, error states | Persisted-data migration details |
| Interface / contract change | Input/output contract, validation, access rule, tests | Full product roadmap |
| Persisted-data change | Entity fields, relationships, constraints, migration notes | UI copy and layout |
| Test writing | Acceptance criteria, expected behavior, edge cases | Unrelated features |
| CLI / library / pipeline / LLM feature | Contract (arguments, API, schema, or prompt contract), failure behavior, tests | Unrelated modules and stages [addition] |
[book ch 12, generalized]

### Context pack contents
Background (name, purpose, users/consumers, stage) - current task (task, expected output, do not change) - relevant requirements with ACs - technical/domain rules (architecture, data, interface, security, plus any domain rules) - file map (only relevant parts) - project conventions - review rules. Use `templates/context-pack.md`. Sections that do not apply may be deleted. [book ch 12]

Add to every pack: allowed-edit file list and stop condition. The file map is not automatically the allowed list; state the allowed list explicitly. [addition: the ch 12 pack lacks Source of truth, Allowed files, and Completion check; the ch 25 pack has a stop condition and tests to satisfy]

### Keeping context clean
- Triggers of context confusion: stale requirements never removed; two names for one feature; a prompt that conflicts with the spec; a file map that no longer matches; acceptance criteria not linked to the current task. [book ch 12]
- When a decision changes, update the context before asking for more work. Do not rely on the agent to guess which instruction is newer. [book ch 12]
- Update the pack after review (not during uncontrolled generation). Checklist: requirement changed? decision changed? file structure changed? new rule needed? old rule now false? does the next task need a smaller slice? [book ch 12]
- Keep the pack compact: one page per task. [book ch 12, Exercise 3]

## 6. Prompting from specs
- A prompt is not a replacement for a spec; it is the short instruction telling the agent what to do with the spec. [book ch 13]
- Do not ask the agent to invent the product. Ask it to implement a specific part of an already defined product. [book ch 13]
- A strong prompt starts from the approved spec (requirement, scope, constraints, expected result), then asks for one specific deliverable. [book ch 13]

### Shared prompt skeleton
All prompts in `templates/prompt-library.md` share this shape: [book ch 11-13 synthesis; framing: addition]
1. Source of truth (pasted or referenced by ID).
2. One small task.
3. Boundaries (do not change / do not add).
4. Acceptance or review criteria.
5. Required output format, including a traceability note (which REQ/AC each part satisfies).
6. Ask the agent to list unclear points as questions before building.

### Prompt quality checklist
- [ ] Approved requirement or spec provided.
- [ ] Limited to one clear unit of work.
- [ ] States what must not change.
- [ ] Includes acceptance or review criteria.
- [ ] Requests a clear output format.
- [ ] Asks the agent to identify unclear details before building.
- [ ] Output must connect back to requirement IDs or spec sections.
[book ch 13]

Prompt types by source (full text in the prompt library): requirement-to-implementation; product-spec-to-feature-plan; technical-spec-to-deliverable; interface-contract implementation; acceptance-criteria-to-tests; safe refactoring; clarify-before-building; implementation plan; implement one task; review against spec. [book ch 13]

Interface-contract prompts must preserve: entry point/operation name, inputs (required and optional), validation rules, output shape, error responses. [book ch 13, generalized]

Refactor prompts must state current behavior that must stay true, the reason, boundaries, and checks that must still pass. Refactoring changes structure only; if behavior changes it is a spec change (CHG-###). [book ch 13, ch 25]

## 7. Turning specs into small tasks
Start from ONE approved feature, not the whole product. Split by: data/state, rules, interface/contract, user-facing surface, error handling, tests, review. [book ch 14]

Guiding question: "What is the smallest useful piece of work that can be completed, tested, and reviewed without building the entire feature?" [book ch 14]

| Feature area | Possible task | Output | Test signal |
|---|---|---|---|
| Data / state | Define record fields | Schema or model | A record can be stored |
| Rules | Define input validation | Validation function | Invalid input is rejected |
| Interface | Define operation contract | Contract document | Correct response is returned |
| User-facing surface | Build the input surface | Screen, command, or component | User/consumer can submit input |
| Error handling | Map failures | Error rules | Failure returns a safe message |
[book ch 14, generalized]

### Task qualities
- Small = one clear outcome. Testable = you can prove it works. Agent-ready = names source requirement, expected files, constraints, done criteria. [book ch 14]
- A task answers: what is changed, why, which spec it comes from, how done is known, what must not change. [book ch 14]
- One-outcome rule: if a task has more than one major outcome, split it. A task that creates a data model, an interface, a user surface, and tests is a mini-project. [book ch 14]
- Avoid "handle everything", "make it robust", "finish the feature"; replace with specific expected behavior. [book ch 14]
- Size: small enough to review in one sitting. [book ch 26, task sizing rule]
- Every task points to an approved requirement or decision. Work with no matching spec entry: pause, then reject, defer, or update the spec first (CHG-###). No new work without a matching approved reason. [book ch 14]

### Merged task template
Use `templates/task.md`: it merges the book's 15-field task template (ch 14) and 9-field template (ch 16), the 7-section task file (ch 4), the ch 1 task card, and the stop condition from the ch 25 context pack. [addition: merge]

### Priority
| Priority | Meaning (task) | Example |
|---|---|---|
| P0 | Must exist before related work can begin; blocks release, data loss, or security | Data model, contract |
| P1 | Required for the feature to be usable (major) | Main operation, validation, error behavior |
| P2 | Useful improvement after core behavior works (minor) | Better loading state |
| P3 | Future or polish (cosmetic) | Animation, optional shortcut |
[book ch 14; extra P0/P1 wording for bugs: addition]

Give the agent P0 and P1 first; do not hand over P2/P3 until the foundation is implemented, tested, and reviewed. Foundation first: data models, contracts, validation rules, security rules. [book ch 14] If a P0 depends on a lower-priority item, raise that item's priority. [addition]

### Dependencies
- A dependency is a task that must finish before another can be completed correctly. Test: "Can this task be tested correctly if the earlier task does not exist?" If no, it is a dependency. [book ch 14]
- Tell the agent explicitly: "Complete TASK-003 only after TASK-001 and TASK-002 are complete." [book ch 14]

### Task list checklist
- [ ] One clear outcome per task.
- [ ] Each task points to a requirement, spec, or design decision.
- [ ] Done criteria are checkable.
- [ ] Dependencies listed before implementation begins.
- [ ] P0 and P1 completed before optional improvements.
- [ ] Each task lists out-of-scope.
- [ ] No task permits rewriting unrelated work.
- [ ] Tests are planned before implementation (book: "before or alongside"; changed, see docs/resolved/05-tasks-and-agent-work.md).
[book ch 14]

### Prompt: spec to tasks
```
Using the approved specification below, create a controlled engineering task list.
For each task, include: task ID, source requirement, goal, expected files, constraints,
dependencies, done criteria, and review notes.
Do not add features that are not in the specification. If something is unclear, mark it as a question
instead of inventing behavior.
```
[book ch 14]

### Example task plan (illustrative domain: sign-in feature)
| Task | Title | Depends on | Done criteria | Out of scope |
|---|---|---|---|---|
| TASK-001 | Define credential fields | None | Fields defined with constraints | No user surface |
| TASK-002 | Write input validation rules | TASK-001 | Weak input rejected by rule | No reset flow |
| TASK-003 | Write operation contract | TASK-001 | Inputs/outputs documented | No token refresh |
| TASK-004 | Implement sign-in operation | TASK-002, TASK-003 | Valid input succeeds; invalid returns safe error | No registration |
| TASK-005 | Build input surface | TASK-003 | Submits to the approved operation | No redesign |
| TASK-006 | Write checks | TASK-004, TASK-005 | Normal and failure cases covered | No unrelated checks |
[book ch 14, IDs normalized to TASK-###]

## 8. Handing a task to the agent
1. Pick one task. Confirm dependencies are done. 2. Build the context pack. 3. Send the six-field instruction (or the "implement one task" prompt). 4. Require the plan first. 5. Approve or correct the plan. 6. Agent implements. [book ch 11, 14, 25]

Generate only the next safe task (prompt): [book ch 14]
```
You are working from the approved [feature] specification.
Generate the implementation instructions for [TASK-###] only.
Use these constraints:
- Do not create [out-of-scope items].
- Do not change [protected persisted data / files] unless the task requires it.
- Follow the approved interface contract.
- Include tests for [normal, failure, edge cases].
Return: files to edit, steps, done criteria, and review checklist.
```

## 9. Agent self-review and completion report
Self-review prompt: [book ch 15]
```
Review your own changes before I accept them.
Explain:
1. Every file you changed.
2. Which requirement each change supports.
3. Which tests prove the change works.
4. Any assumptions you made.
5. Any files you changed that were not on the allowed list.
```
(Last item adapted from "not listed in the task plan" to match the hard file scope, see docs/resolved/05-tasks-and-agent-work.md.)

Completion report fields (merged): files changed and reason for each; requirement/AC covered; tests added or updated and their result; assumptions; risks; unresolved questions; any file touched outside the allowed list and why. [book ch 11, 12, 15; merged: addition]

Human review order: re-read requirement and ACs; check changed-file list before reading code; inspect diff for unexpected deletions or unrelated edits; run or review the linked tests; check error handling, validation, and security-sensitive paths; update traceability and specs if a documented decision changed; accept only after understanding the change. [book ch 15]

Review layers: requirement (satisfies source requirement?), technical (follows design/constraints?), test (prove success and failure paths?), change (no unrelated changes?). [book ch 16]

Quality gate: before implementing, confirm the test plan answers "How will I know this task works without trusting the AI agent blindly?" [book ch 16]

## Common mistakes
| Mistake | Fix |
|---|---|
| "Build the whole system" request | One task from one approved artifact [book ch 11, 16] |
| Agent edits files outside scope | Hard allowed/not-allowed lists; stop and ask to expand [book ch 14-16; resolution: addition] |
| Tasks that mix auth, data, UI, review | Apply the one-outcome rule [book ch 14, 26] |
| Only happy-path tests | Require normal, edge, failure checks [book ch 17] |
| Stale or conflicting context | Update the pack before the next request [book ch 12] |
| Unreviewed result, next task started | Review each task before moving on [book ch 11] |
| Behavior change disguised as refactor | Record as a spec change [book ch 25] |
| Agent invents interface names/formats | Provide the contract; ask for questions first [book ch 13] |
| Approved behavior living only in code | Update the spec after each change [book ch 25] |

Book inconsistencies resolved here are listed in docs/resolved/05-tasks-and-agent-work.md.

Role notes: Requester/PM supplies the approved spec and priority and answers the agent's questions; Builder writes tasks and context packs, approves plans, and directs the agent; Reviewer/QA reviews each result against the requirement, tests, and file scope before the next task. One person may hold all three. [addition]
