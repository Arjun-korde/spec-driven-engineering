# Agent Instructions

Lite mode: may merge into docs/spec.md; Approver: self (solo) allowed. [addition]
When: create at project start or at the Tasks stage at the latest, before the agent writes any code; keep it in the file your tool reads. [addition]

<!-- Save in the file your tool reads (e.g. AGENTS.md, CLAUDE.md, or a project-local instruction file). Keep it short; long files get partly ignored. Delete sections that do not apply. [book ch 4, 11, App H; items not in those chapters are tagged] -->

## Project rule
You are assisting a spec-driven project. Do not invent features. Follow the approved requirements, specifications, tasks, and tests.

Project goal: [one paragraph]
Current stage: [intent | requirements | specification | task planning | testing | implementation | review | deployment | maintenance]

## Where things live
- [specs location]
- [tasks location]
- [tests location]
- [source location]
- [review notes / change log location]
- [context pack and prompt library location]

## Source-of-truth order (first wins on conflict)
1. [Intent / brief]
2. [Requirements]
3. [Design / technical spec and interface contracts]
4. [Current task file]
5. [Existing deliverables and tests]

The order decides which document wins when information conflicts [book ch 11]. If a lower item contradicts a higher one, stop and report it instead of choosing silently. [addition: stop-and-report]

## Workflow rule
Before changing anything: restate the task, list the files you plan to inspect and change, list the tests you will add, and list assumptions and questions. Wait for approval if the task is unclear or touches scope, security, data, or architecture. Ask before making assumptions that affect those.

## Change rule
- Follow the current task only. Do not add unrequested features.
- Change only files on the task's allowed list. Do not refactor unrelated work. Do not rename public interfaces unless the task requires it.
- If another file seems necessary, stop and ask.
- Add no new dependencies without approval (or unless the spec allows). [book App H]
- Implement only tasks listed in the current task list. Keep changes small and reviewable. [book App H]
- Do not remove or weaken tests to make code pass. [book App H]
- Do not expose secrets, tokens, or private data. [book App H]
- Never put secrets in committed files. Keep stack traces and internal detail in internal logs, not user-facing errors.

## Testing rule
Tests are planned before implementation. For every behavior change add or update a check. Minimum per feature: >=1 normal, >=1 edge, >=1 failure. [book ch 17] Preserve tests and security rules. If checks cannot be run, say what to verify manually.

## Reporting rule
After the work report: summary of changes in plain language, files changed and reason for each, requirement/AC covered, tests added or updated and results, risks and assumptions, unanswered questions, questions that need a human decision, and any file touched outside the allowed list. Explain important changes in simple language. [book ch 4, App H]

## Record rule
Record each accepted change using the project's tracking method. Connect every change to a requirement and a test check. Include the requirement ID. Update the specs and traceability when behavior or a decision changes. [addition]
