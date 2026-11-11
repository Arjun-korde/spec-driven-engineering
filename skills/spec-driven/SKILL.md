---
name: spec-driven
description: Spec-driven AI engineering workflow for any project, role or tech stack. Use when starting a new project or feature, turning an idea into requirements/specs/tasks/tests before coding, directing an AI agent to implement work in controlled steps, reviewing or debugging AI-generated code, preparing a release, or checking for spec drift. Also use for handoffs between product, engineering and QA.
---

# Spec-Driven AI Engineering

Based on "Spec-Driven AI Engineering" by Gem Iroko. Works for any role (PM, builder, QA, lead, solo) and any stack. Labels in the references: `[book ...]` = from the book, `[addition]` = added to fill gaps or generalize.

**The one rule:** Before code, write the spec. Before accepting code, run the tests. Before release, review the risks. After release, update the spec when reality changes.

## Operating principles
1. The human decides the stage; you work only on the current stage and artifact. Never "build the whole app".
2. Do not guess. Missing information becomes an Open Question; ask the user or record it. A blocking open decision is never treated as an assumption. A low-risk gap may be a recorded assumption (reason, who confirms, status) so work continues; anything touching scope, security, data, contracts or cost must be asked. [addition]
3. Every requirement is testable, has an ID, and traces to design, a task, a test and code.
4. Implement one small task at a time, inside stated file and behavior boundaries.
5. Generated code is a draft: it counts as done only after planned tests pass and it is reviewed.
6. Never invent requirements, add unrequested features or dependencies, remove or weaken tests to pass, or expose secrets.
7. When behavior changes, update the spec, tests and traceability in the same change.

## Shared vocabulary (use these everywhere)
- IDs (3 digits): `REQ-###` requirement, `US-###` user story, `AC-###` acceptance criterion, `TASK-###`, `TEST-###`, `ADR-###` decision, `SEC-###` security rule, `BUG-###`, `CHG-###` spec change.
- Task status: todo | doing | blocked | review | done. Review status: Needs review | Approved | Needs update.
- Requirement priority: Must | Should | Could | Won't. Task/bug priority: P0-P3.
- Test: plan state (Planned | Written | Automated | Manual | Blocked) and result (Not run | Pass | Fail).
- Minimum before building a feature: one normal, one edge and one failure check.
Terms are defined in references/14-glossary-and-tools.md.

## Step 0: Orient
Look for existing artifacts (docs/specs, task list, tests). Then:
- **Nothing exists** -> pick the project profile (CLI/library, data pipeline, web/mobile, API service, LLM feature) in references/12-project-types.md and ask its Intent-time questions, then Intent (references/01-intent.md); offer to scaffold the workspace.
- **Artifacts exist** -> find the current stage in the table, check the previous gate, continue.
- **Existing codebase without specs** -> retrofit path in references/12-project-types.md.
- **User wants one thing** (review, debug, test plan, release check) -> jump to that reference; still ask for the governing requirement.
- **Small, low-risk change** (typo, copy tweak, one-line fix with an existing test): do not run the whole lifecycle. Confirm the requirement it touches, make the change, run the existing check, update the spec if behavior changed. Anything touching behavior, data, security or contracts uses the full loop. [addition]

## Scale to the project: Lite mode vs Full mode [addition]
- **Lite mode** (solo or small project, one person holds all roles): stages 1-3 may live in one `docs/spec.md` using the template sections; skip artifacts that do not apply and say so in one line; the one person approves each gate and records "Approver: self (solo)". Keep the rule intact: requirements testable, tests planned before code, one task at a time, spec updated with behavior.
- **Full mode** (several roles, handoffs, or high-risk work such as security, money, health, data loss): separate artifacts, a named approver per gate, handoffs per references/11.
- Unsure? Start in Lite mode and switch to Full when a second role joins or risk grows.

## Lifecycle, gates and where to read
| # | Stage | Question | Artifact (templates/) | Gate: move on when | Read |
|---|---|---|---|---|---|
| 1 | Intent | Why should this exist? | intent.md | Problem, users, goal, scope, constraints clear; open questions listed | 01-intent |
| 2 | Requirements + product spec | What must it do, for whom? | requirements.md, prd.md | Each requirement specific and testable; scope and out-of-scope explicit | 02-requirements-and-prd |
| 3 | Technical design | How will it be built? | technical-spec.md, adr.md, interface-contract.md, data-model.md | Requirements mapped to modules; contracts, errors, open decisions defined | 03-technical-design |
| 4 | Security + reliability rules | What must be protected; what can fail? | security-rules.md, reliability-spec.md | Rules written before code; failure behavior defined | 08-security-reliability |
| 5 | Test plan | How is it proven? | test-spec.md | Each Must requirement has normal, edge and failure tests (abuse where relevant) | 06-testing |
| 6 | Tasks | What first, second, next? | task.md | Tasks small, ordered, each with REQ, acceptance check, boundaries | 05-tasks-and-agent-work |
| 7 | Implementation | Build one task | code + tests | Task passes its checks within boundaries | 05, 10-version-control-and-change |
| 8 | Review / debug / refactor | Does it match the spec? | code-review-checklist.md, debug-report.md | Matches requirement, design, tests, security; no unrelated changes | 07-debug-review-refactor |
| 9 | Deploy | Safe to run for real? | deployment-plan.md (draft during stage 3-4, finalize here) | Readiness checklist done; rollback owner named | 09-deploy-monitor-maintain |
| 10 | Maintain | Still correct? | monitoring-maintenance.md, spec-change-record.md | Specs updated after every behavior change | 09 |

Cross-cutting: traceability (04-traceability), roles and handoffs (11), project-type adaptations (12), governance and metrics (13), glossary and tools (14). Stages 3-5 may overlap; the rule is that a test plan and security/failure rules exist before the code they cover. [addition]

Read only the reference for the current stage (all in `references/`). Templates are in `templates/`; `templates/prompt-library.md` indexes reusable prompts.

## Stage gate check (use before advancing)
Review the artifact: is it ready for the next stage? List missing information, vague statements (anything not testable), risky assumptions, and exact corrections. Do not start the next stage until the user approves. [book ch3]

## Scaffold a project workspace (offer, do not force)
Create only what the user wants; adapt names to the project. [book, generalized]
```
docs/        intent.md requirements.md prd.md technical-spec.md interface-contract.md data-model.md
             test-spec.md security-rules.md reliability-spec.md deployment-plan.md
             monitoring-maintenance.md traceability-matrix.md
docs/decisions/ADR-###.md   docs/changes/CHG-###.md   docs/feedback/   docs/context/   docs/handoffs/
tasks/       task-list.md (index)  TASK-###.md (one per task)  review-notes.md
(project's own code and test locations)   AGENTS.md or CLAUDE.md -> agent rules (templates/agent-instructions.md)
```
Copy templates and delete sections that do not apply. Keep specs in files, not only in chat.

## Working on a task (the core loop)
1. Confirm the task: ID, linked REQ, acceptance check, allowed files, do-not-change, stop condition.
2. Build a small context slice (templates/context-pack.md): only the relevant requirement, design section and tests.
3. Plan first: state plan, assumptions and questions; wait for approval when the task is unclear or touches scope, security, data or architecture.
4. Tests first where practical; implement only the task.
5. Run the checks. Report: summary, files changed, tests added/updated, risks/assumptions, questions for the human.
6. Update task status, traceability and any changed spec. Review before the next task.

## When things go wrong
- Failing test or bug: debug from evidence (expected vs actual, failing output, logs); no guess-fixes; add a regression test (07).
- Requirement unclear or contradictory: stop, raise it, update the spec; do not pick silently.
- A later stage exposes a gap in an approved artifact (for example test planning finds a missing acceptance criterion): record a spec change (templates/spec-change-record.md) and re-check that gate for that item only. [addition]
- Code and spec disagree: decide which is correct, then change the other (decision rule in 09).
- Agent output wandering: shrink the task, tighten boundaries, re-supply the context slice.

## Roles
One person may hold several roles; the method never requires a role to exist. Requester/PM owns the "what", Builder the "how", Reviewer/QA the "proof", Approver signs off gates. See 11-roles-and-handoffs.

## Source and known gaps
Book: Spec-Driven AI Engineering (Gem Iroko). The book is web/SaaS-centric and has internal inconsistencies; the fixes are tagged `[addition]` and listed in `docs/resolved/` in the plugin. Book figures are images and are not covered.
