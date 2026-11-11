# 08 - Security and Reliability

## Purpose
Define security rules and failure behavior as written, testable rules BEFORE code or tests are generated. The agent never decides security policy; the spec does. [book ch 21]

## When to use
Before implementing any feature that handles identity, access, input, stored/transmitted data, secrets, external services, deferred (background) work, or LLM output. Re-run the checklist after agent code is produced and again before deployment. [book ch 21]

## Non-interactive projects (CLI, pipeline, library): summary [addition]
If nothing signs in, skip the authentication, session, and role sections below and say so in one line; the worked example for a CLI is further down. Cover instead:
- Exit codes: one documented code per outcome (success, bad input, failed dependency, partial); no silent success.
- stdout vs stderr: results to stdout, diagnostics and errors to stderr; no secrets or internals in either.
- Secrets in environment or a secret store, never in flags, arguments, files in the repo, or logs.
- File permissions: output, state, and log files created with the least access needed; untrusted paths and input sizes validated.
- Schedule and overlap: what happens on a missed run, a late input, or two runs at once (lock or refuse).
- Idempotency and partial failure: a rerun does not duplicate output; per-item or per-recipient results are recorded; interrupts leave a safe state.

## Inputs
- Approved requirements (REQ-###) and the feature under spec.
- Actors/roles and the resources they touch.
- External dependencies (services, stores, queues, third-party calls).

## Output artifact
- Security rules: `templates/security-rules.md` (SEC-### rules linked to AC-### and TEST-###).
- Failure behavior: `templates/reliability-spec.md`.

## Gate (done-when)
Status values: Not started | Ready | Blocked | Done. [addition]
- [ ] Every protected feature has an authentication requirement. [book ch 21]
- [ ] Every sensitive action has an authorization rule; default is deny. [book ch 21, App M]
- [ ] Role/permission matrix is written. [book ch 21]
- [ ] Input rules are specific, testable, enforced at the system boundary (not only in the caller/client). [book ch 21]
- [ ] Sensitive data is not logged or returned unnecessarily. [book ch 21]
- [ ] No secrets in source, examples, logs, screenshots; placeholders only. [book ch 21]
- [ ] User-visible errors are safe; internals go to internal logs only. [book ch 21]
- [ ] Each important feature has named failure states with recovery path, log event, user-visible message, and test. [book ch 22]
- [ ] Timeouts, retry limits, and stop conditions defined for slow/external operations. [book ch 22]
- [ ] Deferred work has status, retry rule, failure state. [book ch 22]
- [ ] Every SEC-### links to at least one AC-### and TEST-###. [book ch 21; ID format addition]
- [ ] Tests cover abuse cases and failure paths, not only the normal path. [book App M]
- [ ] Agent instructions forbid adding unapproved access paths. [book ch 21]

## Core rules
1. Do not write "make it secure". Write a rule that can be reviewed, tested, and implemented. [book ch 21]
2. Pattern: write the protective rule, connect it to acceptance criteria, generate tests, then implement against the tests. [book ch 21]
3. Reliability is behavior: "If X fails, the system must do Y, record Z, and show message M." [book ch 22]
4. The agent must not invent recovery behavior or extra failure states while coding. [book ch 22]
5. Replace vague phrases ("approved storage", "protected channels", "reasonable time") with concrete values chosen by the project. [addition: the book's own examples use such phrases]

## Security rules, by area
Use one SEC-### per rule. Mechanism-neutral: adapt to the project's auth mechanism (sign-in, SSO, API key, service token, none).

### Authentication ("who are you")
Never assume identity: verify it, create a controlled session/credential, end it safely. [book ch 21]

| Area | Question | Example rule |
|---|---|---|
| Access | How does an actor prove identity? | Private data requires verified identity first. |
| Session lifetime | How long does access last? | Access expires after a defined inactivity period (project sets value). |
| Credential handling | How are credentials protected? | Plain-text credentials are never stored or logged. |
| Recovery | How is access recovered? | Recovery links expire and do not reveal whether an account exists. |
| End of access | How does access end? | An actor can end the current session/credential. |

[book ch 21, generalized]

### Authorization ("what may you do")
- Write a matrix: actions x roles (the project's own role set). Example roles in the book: Owner, Admin, Member, Viewer. [book ch 21]
- Rule form: check actor role AND resource ownership/scope; missing actor means deny; default is deny unless explicitly allowed. [book ch 21 snippet generalized; book App M]
- Model "own" vs "any" separately (e.g. edit own item vs edit any item): the book's role table has separate "Edit any task" and "Edit own task" rows. [book ch 21] The rule form must check ownership/scope, which generalizes the book's code snippet (it omits ownership). [addition]
- Users must not reach another owner's data by changing identifiers. [book App M]
- Admin actions are separated from normal actions. [book App M]

| Action | Role A | Role B | Role C |
|---|---|---|---|
| [action] | Yes/No | Yes/No | Yes/No |

### Validation (every input boundary)
Boundaries: form, interface request, file upload, search field, configuration value [book ch 21]; CLI argument, message payload, file contents [addition]. Validate on the receiving side; do not rely only on the caller. [book ch 21]

| Input | Validation rule | Error behavior |
|---|---|---|
| [input] | [required? type, length/range, format, scope/ownership] | [safe message; nothing saved] |

Include: unknown enum values rejected; resource identifiers must belong to something the actor can access (return safe not-found or access-denied). [book ch 21]

### Data protection
| Area | Question | Rule to write (concrete) |
|---|---|---|
| Minimization | Is this data needed? | Do not collect data the feature does not need. |
| Storage | How is it stored? | [name the mechanism/standard the project uses] |
| Transport | How does it move? | [name the protected channel] |
| Logging | What must not be logged? | Passwords, tokens, reset links, full secret values, keys, sensitive personal data. |
| Retention | How long kept? | [reference the retention rule in the product spec] |

[book ch 21; bracketed cells are an addition to force concreteness]

### Secrets
- Secrets: API keys, database/connection credentials, signing keys, private tokens. Never in source, templates, screenshots, logs, or example output. [book ch 21]
- Use placeholders and document where the real value is configured. [book ch 21]

```
# Pattern only - no real values
APP_ENV=development
DB_CONNECTION=<set_in_local_environment>
SIGNING_KEY=<set_in_local_environment>
THIRD_PARTY_API_KEY=<set_in_local_environment>
```

### Safe errors
Log technical detail internally; show a safe message externally. Stack traces, database messages, private paths, tokens, and config details never reach the user or caller. [book ch 21] Stack traces belong in internal logs only. [book ch 24 error-log fields; reconciliation is an addition, see docs/resolved/07-09.md]

| Problem | Unsafe | Safer |
|---|---|---|
| Sign-in failed | Which part was wrong | "The email or password is incorrect." |
| Access denied | Internal rule details | "You do not have permission to perform this action." |
| Server failure | Stack trace / DB error | "Something went wrong. Please try again later." |
| Validation failure | Raw parser/framework error | "The submitted value does not match the required format." |

[book ch 21]

For non-UI systems, "message" means caller-visible error or exit code. [addition]

### Abuse limits
The book lists rate limits as an item agents miss but gives no template. Add a rule per exposed operation: limit, scope (per actor/source), behavior when exceeded, log event. Project sets values. [addition]

## Worked example A: non-interactive tool (CLI, pipeline, or library) [addition]
Raw requirement: "The tool reads a config file and an input file path, then writes a report file." Insufficient: no rule for who may run it, what input is trusted, where secrets come from, or what an error may print.

| Area | Specification |
|---|---|
| Identity / access | The tool runs with the caller's own operating-system permissions and never escalates; no shared service account unless the spec names one. |
| Authorization | It reads only the paths given and writes only to the output location; refuses paths outside the allowed directory (SEC rule: no path traversal via arguments). |
| Validation | Arguments and file contents are untrusted: required args present, path exists and is a regular file, size/format checked before parsing; unknown option values rejected. |
| Data protection | Output file is created with owner-only permissions; sensitive fields are masked in reports and logs. |
| Secrets | Credentials come from environment variables or a secret store, never from arguments (they show in process lists) or committed files; only placeholders in the config template. |
| Safe errors | Message names the problem and a next step ("input file not found"); no stack trace or absolute internal paths; nonzero exit code per failure class. Details go to an internal log. |
| Abuse limits | Maximum input size and run time; a stop condition for bad input in a batch. |
| Testing | Missing file, unreadable file, malformed content, oversize input, path outside the allowed directory, secret absent from environment, secret not echoed in output or logs. |
SEC-101 example: `Secrets are read only from the environment; the tool exits with a nonzero code and a safe message if one is missing, and never prints it.` Link to AC and TEST as usual. A library applies the same rules to its public function inputs and error types; a pipeline applies them to each source file or message. [addition]

## Worked example B (interactive: invitation feature)
Raw requirement: "A user can invite team members and assign tasks." Insufficient: no inviter, role, data exposure, or unauthorized behavior. [book ch 21]

| Area | Specification |
|---|---|
| Authentication | Only authenticated actors can invite. |
| Authorization | Only Owners and Admins can invite. |
| Role assignment | Inviter selects one approved role. |
| Validation | Address required, valid format, not already a member. |
| Data protection | Invitation tokens never appear in logs or user-facing errors. |
| Secure errors | Unauthorized actors get a safe access-denied response. |
| Testing | Allowed inviter, disallowed inviter, invalid input, duplicate, safe error. |

[book ch 21]

Excerpt: `SEC-001 Only authenticated Owners and Admins can invite.` AC-001 signed-out actor cannot; AC-002 Viewer cannot; AC-003 Member cannot; AC-004 Owner/Admin can with valid input; AC-005 tokens not in logs/errors; AC-006 unauthorized gets safe response. [book ch 21; ID format adapted to conventions]

## Reliability rules
Qualities to specify per feature: [book ch 22]

| Quality | Specify | Example |
|---|---|---|
| Availability | Does it keep responding if one part slows? | If a notification service fails, the main item is still saved. |
| Recoverability | Return to a safe state after failure | If confirmation fails, mark pending, not successful. |
| Observability | What is recorded | Request ID, action, role, error type. |
| Predictability | Consistent behavior under known failures | Invalid input always yields the same response format. |
| User clarity | What the user sees | Helpful message, no internals. |

### Failure sources: ask "what can go wrong, and what should happen next?"
| Source | Question | Example recovery rule |
|---|---|---|
| Input | Missing/invalid/unexpected data? | Reject with field-level messages. |
| Persisted data | Write fails or is slow? | Do not report success; return retry-safe error; log. |
| Network | Request times out? | Timeout rule; allow safe retry. |
| External service | Third party unavailable? | Queue or mark pending. |
| Deferred work | Job fails after the user left? | Store status; retry if safe; expose result. |

[book ch 22]

Per failure state, fill: Trigger, Recovery path, User message, Log event, Test case. [book ch 22]

### Logging
Name events consistently (naming style is a project choice). Fields: event name, severity, request/correlation ID, safe context, failure reason (type or safe code, not a sensitive dump), outcome (recovered/retried/queued/stopped). [book ch 22]

### Timeouts and retries
| Decision | Rule |
|---|---|
| Timeout | Maximum wait; never hang forever |
| Retry count | Limited; never endless |
| Retry delay | Wait before retrying (project sets backoff) |
| Idempotency | Retry only operations without harmful duplicate effects |
| Stop condition | When to give up and report a controlled failure |

[book ch 22]

Rule shape: Timeout [value]; retry [n] times for temporary errors; do not retry invalid input or rejected permission; if retries fail -> [status]; log [EVENT] with request ID and safe code; user message [text]. [book ch 22; values are the project's]

### Queues / deferred work
Define: job name, trigger, minimum safe input data, retry rule, failure state, user visibility (pending/failed/completed). [book ch 22] Use one status vocabulary across features. [addition]

### Safe messages
State what to do next; no internals. Weak: "DatabaseError: connection refused". Better: "We could not save your changes right now. Please try again." [book ch 22]

## AI-feature safety (only if the project has LLM features)
Skip this section otherwise. Apply when the system sends content to, or acts on output from, an LLM.
- Approved-context boundary: use only approved sources; unapproved or draft content is never retrieved. [book ch 28]
- Grounding: answers cite approved sources, or the system says it is unsupported / escalates to a human. [book ch 26, 28]
- AI output is a draft until a human approves it before it is saved or acted on. [book ch 28]
- Never ask for or store passwords or secret keys; minimize personal data; do not expose private account details unless authenticated and supported. [book ch 26]
- Log unsupported answers and escalations; no secrets in logs. [book ch 26, 28]
- Safe versions use sample data only: no live customer records, payment systems, or private production data. [book ch 26]
- Thresholds (confidence, number of retrieved items) are project values recorded in the spec. [addition]
- Tests: normal answer; vague input -> one clarification; no match -> admit and escalate; request for internal details -> refuse; sensitive input -> refuse and do not store. [book ch 26]

## Prompts
Authentication:
> Using the product requirements below, identify every place where the system must verify user identity. Convert each identity requirement into a clear authentication rule, acceptance criterion, and test idea. Do not invent extra features. [book ch 21]

Secrets:
> Review this specification and identify every value that should be treated as a secret. For each one, state where it should be configured, where it must not appear, and how the code should refer to it without exposing it. [book ch 21]

Tests-only security prompt:
> Using the security specification above, create only the tests for [feature]. Do not implement the feature yet. Include tests for authentication, authorization, validation, duplicates, and safe error responses. [book ch 21, neutralized]

Logging rules:
> Using the reliability requirements below, propose safe logging rules for this feature. Include event names, severity levels, useful context fields, and fields that must never be logged. Do not write implementation code yet. [book ch 22]

Implement reliability:
> Implement the reliability behavior exactly as specified. Do not invent additional failure states. Add tests for [normal, failure, retry, user-visible status] before changing production code. [book ch 22]

Gap review:
> Review this reliability spec and list missing failure states, missing recovery paths, and unsafe retries. Do not write code. [book ch 22 exercise, made explicit]

## Review after agent code (status: Not started/Ready/Blocked/Done) [format addition; items from book App M]
- Protected actions require authentication; invalid/expired credentials handled safely.
- Each protected action checks role or ownership; no access by changing identifiers; default deny.
- Session/token handling is defined; authentication errors do not reveal sensitive information. [book App M]
- Input validated at the boundary; sensitive fields protected in storage and logs; secrets not hardcoded or printed.
- Security tests cover abuse cases.

## Common mistakes
- "Make it secure" with no rule.
- Validating only in the caller/client.
- Unspecified recovery, so the agent returns raw exception text.
- Retrying non-idempotent operations.
- Treating security as post-hoc review instead of a pre-code spec.

## Role notes [addition]
- Requester/PM: supplies who may do what, data sensitivity, and user-visible message wording; approves policy.
- Builder: writes SEC rules and failure specs from requirements, then tests first, then code.
- Reviewer/QA: checks the gate, runs abuse and failure tests, verifies no internals leak.

Book inconsistencies resolved here are listed in docs/resolved/07-09.md.
