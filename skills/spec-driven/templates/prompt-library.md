# Prompt Library

<!-- Reusable prompts, written to the agent. Replace [placeholders]. Keep the shared shape: source of truth, one task, boundaries, criteria, output format, questions first. Sources: book ch 4, 11, 13, 16, 25 and App J, tagged per prompt; [addition] where marked. Prompts whose full text lives in a stage reference are listed as pointers (name | purpose | file and section) so they are maintained in one place. Delete prompts you do not need. -->

## A. Setup and early stages

### A1. Compare assistant fit [book ch 4]
```
I am setting up a spec-driven workflow.
Project type: [describe project]
My experience level: [level]
Current stage: [intent, requirements, specs, tasks, tests, build, review]
Compare what type of AI assistant is appropriate for this stage and explain the safest way to use it.
```

### A2. Generate a workspace plan [book ch 4]
```
I am starting a new spec-driven project.
Project idea: [describe idea]
Create a local workspace plan using separate places for docs, tasks, tests, source, review notes, agent files, and operations notes (adapt to the existing layout if there is one). For each, list the first files I should create and what each file should contain.
```

### A3. Improve an instruction file [book ch 4]
```
Review this agent instruction file.
Instruction file: [paste]
Find vague rules, missing boundaries, missing test expectations, and anything that could allow uncontrolled changes. Return a cleaner version.
```

### A4. Engineering intent [book ch 25]
```
You are helping me prepare a spec-driven project.
Project idea: [idea]
Create a concise engineering intent document with: problem statement, target users, intended outcome, success criteria, non-goals, engineering constraints.
```

### A5. Requirement to product spec [book ch 16]
```
You are helping me turn requirements into a product specification. Use only the requirements below. Do not invent new features. For each requirement, explain the user goal, user flow, success metric, and out-of-scope notes. Return a concise product spec section.
Requirements: [paste]
```

### A6. Technical spec from requirements [book ch 25]
```
Use the following requirements to create a technical specification.
Requirements: [REQ-### to REQ-###]
Include: architecture overview, module responsibilities, persisted data entities (if any), interface boundary, validation rules, access rules, test strategy, implementation constraints.
Do not generate the deliverable yet. Produce a design that can guide it.
```

## B. Clarify and plan (before building)

### B1. Clarify before building [book ch 13]
```
Before writing anything, review the specification below.
List any missing details, contradictions, risky assumptions, or unclear requirements.
Do not implement anything yet.
Specification:
[paste spec]
Return:
- Missing details
- Contradictions
- Questions I should answer
- Safe assumptions, if any
```

### B2. Spec to task list (pointer)
Split an approved spec into controlled tasks | `references/05-tasks-and-agent-work.md`, section 7 "Prompt: spec to tasks" [book ch 14]

### B3. Implementation plan [book ch 13]
```
Using the approved specification below, create a step-by-step implementation plan.
Do not produce the deliverable yet.
Specification:
[paste spec]
Return:
- Files or modules likely involved
- Tasks in the correct order
- Tests to plan before implementation
- Risks to review before starting
```
(Tests are planned before implementation and written first when practical. [addition])

### B4. Product spec to feature plan [book ch 13]
```
Using the product requirements below, create a feature plan for [FEATURE NAME].
Product goal: [paste]
Target user or consumer: [paste]
In scope: [paste]
Out of scope: [paste]
Success metrics: [paste]
Return:
1. The feature behavior in simple steps
2. The surfaces or interfaces needed
3. The main flow
4. Risks or unclear points to clarify before building
```
(Success metrics slot added; the book's tip mentions metrics but its template omitted them. [addition])

### B5. Next safe task only (pointer)
Instructions for one named task only | `references/05-tasks-and-agent-work.md`, section 8 [book ch 14]

## C. Build

### C1. Six-field task assignment (pointer)
Six-field instruction pattern: Task, Source of truth, Allowed files, Do not change, Expected output, Completion check | `references/05-tasks-and-agent-work.md`, section 2 [book ch 11]. To require a plan first, append: "First give me a short plan (files to inspect and change, tests to add, assumptions, questions). Wait for approval if scope, security, data, or architecture is touched or the spec is unclear." [addition; rule in 05 section 4]

### C2. Requirement to implementation [book ch 13]
```
You are working from this approved requirement:
[PASTE REQUIREMENT]
Your task:
[ONE SMALL TASK]
Boundaries:
- Do not change: [files or features]
- Do not add: [out-of-scope items]
Acceptance criteria:
[PASTE ACCEPTANCE CRITERIA]
Output required:
- Implementation steps
- The deliverable or a structured draft
- A short traceability note showing how the output satisfies the requirement
If anything is unclear, list the questions before building.
```
(Last line added to match the prompt quality checklist. [addition])

### C3. Technical spec to deliverable [book ch 13]
```
Use the technical specification below as the source of truth.
Area to work on: [AREA OF THE SYSTEM]
Approved technical decisions:
[PASTE EXCERPT]
Task: [ONE SMALL TASK]
Constraints:
- Follow the existing structure and naming style.
- Do not change unrelated modules.
- Do not introduce a new dependency unless the spec says so.
Return:
- The proposed files to create or edit
- The implementation
- A short explanation of how it follows the technical spec
If anything is unclear, list the questions before building.
```

### C4. Interface contract implementation [book ch 13]
```
Implement the operation using this contract only:
Operation / entry point: [paste]
Inputs (input contract): [paste]
Outputs (output contract): [paste]
Validation rules: [paste]
Error behavior: [paste]
Important boundaries:
- Do not change the contract.
- Do not rename fields.
- Do not add extra output fields.
- If something is unclear, list the question before writing anything.
Return:
- Operation logic
- Validation logic
- Example success output
- Example error output
```

### C5. Implement one task [book ch 13, adapted: "Allowed files" line and the "stop and ask" clause are additions; the book says only "Do not change unrelated files"]
```
Implement this one task only:
[TASK-### AND DESCRIPTION]
Source of truth:
[PASTE REQUIREMENT / TECHNICAL SPEC / INTERFACE CONTRACT]
Allowed files: [list]
Boundaries:
- Do not work on other tasks.
- Do not change files outside the allowed list; if you think you must, stop and ask.
- Do not add features outside the specification.
Return:
- Changes
- Short explanation
- Requirement IDs covered
- Suggested tests
```

### C6. Scoped implementation with before/after report (pointer)
Requirement-scoped change with before-coding and after-coding reports | `references/10-version-control-and-change.md`, "Agent prompt: scoped implementation with before/after reporting" [book ch 15, adapted: hard "Allowed files" list in place of "Likely files"]

### C7. Full implementation prompt [book ch 16]
```
You are implementing one task from the approved spec-driven pipeline.
Task ID: [TASK-###]
Source requirement: [REQ-###]
Technical design reference: [ADR / section]
Allowed files to change: [list]
Files not allowed to change: [list]
Expected behavior: [text]
Required tests: [TEST-### list]
Acceptance criteria: [AC list]
Instructions:
1. Make the smallest safe change.
2. Do not add unrelated features.
3. Explain changed files after implementation.
4. Show how the tests prove the requirement.
```

### C8. Safe refactoring [book ch 13]
```
Refactor the work below without changing its approved behavior.
Current behavior that must remain true:
[PASTE REQUIREMENTS OR ACCEPTANCE CRITERIA]
Reason for refactoring: [SIMPLIFY / REMOVE DUPLICATION / IMPROVE NAMING / IMPROVE ERROR HANDLING]
Boundaries:
- Do not change public names unless requested.
- Do not change input or output formats.
- Do not remove validation rules.
- Do not introduce unrelated features.
Checks that must still pass:
[PASTE TEST LIST]
Return:
1. Refactored result
2. Explanation of what changed
3. Confirmation of what behavior stayed the same
4. Any risk that still needs manual review
```

## D. Tests

### D1. Acceptance criteria to tests [book ch 13, ch 25]
```
Create tests from the acceptance criteria below. Do not test behavior that is not listed.
Feature: [FEATURE NAME]
Acceptance criteria: [PASTE]
Edge cases: [PASTE]
Error cases: [PASTE]
Return a table with: Test ID (TEST-###), Scenario, Inputs, Expected result, Requirement ID covered, Priority [book ch 25].
Include at least one normal, one edge, and one failure case per requirement; add authorization/access cases where relevant. [book ch 17 minimum, merged in]
Then provide the test code or test pseudocode.
```

### D2. Test checklist from requirements [book ch 4]
```
Use the requirements below to create a test checklist.
Requirements: [paste requirements]
Return test ideas per level (unit, integration, end-to-end flow, security/abuse, performance/limits), each tagged normal, edge, or failure. Each test must say what action is performed and what result is expected.
```

## E. Review and change

### E1. Review against the spec [book ch 13]
```
Review the output below against the approved specification.
Do not rewrite the whole solution unless necessary.
Approved specification: [PASTE SPEC]
Output to review: [PASTE OUTPUT OR PLAN]
Return:
- What matches the spec
- What is missing
- What is extra or out of scope
- What should be corrected first
```

### E2. Agent self-review (pointer)
Agent explains every changed file, requirement, test, assumption, and out-of-list file before acceptance | `references/05-tasks-and-agent-work.md`, section 9 (also `references/10-version-control-and-change.md`) [book ch 15]

### E3. Structured review by category [book ch 25]
```
Review the implementation against the specs.
Use these review categories: requirements coverage; architecture match; data integrity; access and ownership (if relevant); validation and error handling; test coverage; refactoring opportunities.
For every issue, provide: the affected requirement or artifact; the risk; the recommended fix; whether the fix changes the spec.
```

### E4. Prompt quality check [book ch 13]
Before sending any prompt confirm: approved spec provided; one unit of work; what must not change; acceptance or review criteria; output format; unclear details to be raised first; output tied to requirement IDs.

## F. Other review prompts [book App J]
Pointers where a stage reference holds the full text; otherwise the App J wording.

### F1. Review generated tests (pointer)
Find shallow tests, missing cases, weak assertions, invented behavior | `references/06-testing.md`, "Prompt: improve shallow tests" [book ch 18; App J "Review generated tests"]

### F2. Debug (pointer)
Root cause from logs, failing tests, expected behavior; evidence before fix | `references/07-debug-review-refactor.md`, "Prompt: root cause" [book ch 19; App J "Debug"]

### F3. Code review [book App J]
```
Review this code against requirements, architecture, security, validation, performance, and maintainability. Do not rewrite yet; provide findings first.
```

### F4. Security review [book App J]
```
Review this feature for authentication, authorization, input validation, data protection, secrets exposure, and secure error handling.
```

### F5. Deployment review [book App J]
```
Review this deployment plan for environment setup, configuration, migrations, rollback, monitoring, and production readiness.
```

### F6. Spec drift review [book App J]
```
Compare current behavior with the original specs. Identify drift, undocumented changes, and specs that need updating.
```

## Prompt Safety Rule [book App J]
Never ask an AI agent to "just fix everything." Ask it to work from one requirement, one task, one failing test, or one review checklist at a time.
