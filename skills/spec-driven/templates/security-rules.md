# Security Rules: [Feature / Project]
Delete sections that do not apply. Fill BEFORE tests and code. [book ch 21; structure adapted]

Lite mode: may merge into docs/spec.md; Approver: self (solo) allowed. [addition]
Applies-when notes: tag each section; delete the ones that do not apply and say so in one line (e.g. 'local read-only tool: no sign-in, no roles'). [addition]

Owner: [name/role]   Review status: Needs review | Approved | Needs update

## Roles x actions (Applies when: more than one actor type; for jobs/CLIs list actors such as operator, scheduler, job identity) [addition]
| Action | [Role A] | [Role B] | [Role C] |
|---|---|---|---|
| [action] | Yes/No | Yes/No | Yes/No |
Default is deny. Distinguish "own" vs "any" where relevant (the book's role table has separate edit-own and edit-any rows). [book ch 21]

## Rules
| ID | Area | Rule (testable) | Linked REQ | Linked AC | Linked TEST | Status |
|---|---|---|---|---|---|---|
| SEC-001 | Authentication (only if actors sign in or are identified) | [e.g. only authenticated actors can ...] | REQ-### | AC-### | TEST-### | Not started |
| SEC-002 | Authorization | | | | | |
| SEC-003 | Validation | | | | | |
| SEC-004 | Data protection | | | | | |
| SEC-005 | Secrets | | | | | |
| SEC-006 | Safe errors | | | | | |
| SEC-007 | Abuse limits [addition] | [limit, scope, behavior when exceeded] | | | | |
Status: Not started | Ready | Blocked | Done

## Input validation
CLI/pipeline: untrusted input it consumes (files, args, paths, size/loop limits). [addition]
| Input | Rule | Error behavior |
|---|---|---|

## Data protection (include data in transit: files moved, email, network calls) [addition]
Data collected: [list]. Never logged: [list]. Storage: [concrete]. Transport: [concrete]. Retention: [rule].

## Secrets (Applies when: credentials, tokens or keys exist; else write 'none')
| Secret | Configured in | Must not appear in |
|---|---|---|
Placeholders only in documentation.

## Safe errors
| Situation | User/caller sees | Internal log only |
|---|---|---|
Stack traces and internal detail: internal logs only.

## AI-feature rules (only if LLM features)
Approved sources only; grounding or "unsupported" + escalation; human approval before save/action; no secrets requested; unsupported answers logged.

## Review checklist (status per row)
| Check | Status |
|---|---|
| Every protected feature has an authentication requirement (n/a: reason if nothing is protected) | |
| Every sensitive action has an authorization rule | |
| Role matrix documented | |
| Input rules specific, testable, enforced at the boundary | |
| Sensitive data not logged or returned unnecessarily | |
| Secrets not in source or examples | |
| Errors safe for users, useful internally | |
| Session/token handling defined; authentication errors reveal no sensitive information [book App M] (only if users sign in) | |
| Every SEC linked to tests (incl. abuse cases) | |
| Agent told not to add unapproved access paths | |
Use: before implementation, after agent code, before deployment.
Cross-rule check: do security rules conflict with reliability rules (e.g. a rerun needs data that SEC forbids storing)? [addition]
