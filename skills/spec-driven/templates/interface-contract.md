# Interface Contract: [name]

Lite mode: may merge into docs/spec.md; Approver: self (solo) allowed. [addition]

Delete sections that do not apply. Define before implementation; producer, consumer, and tests all use this.

Kind: HTTP/RPC | CLI | Library API | Event/message | File/format | Model I/O | Job/pipeline (one file may hold several small boundaries: CLI, input file, DB read, output message) [addition]
Version: [current version]   Linked requirements: REQ-### ...   Status: Needs review | Approved | Needs update

## 1. Identity and purpose
- Name / entry point: [method+path | command | function signature | topic+event | file pattern]
- Purpose: [ ]
- Permission / who may call: [ ]   (Applies when: callers are authenticated; CLI/job: who may run it, as which identity) [addition]

## 2. Input
CLI/library alternative: flags and positional args (flag ordering rules, --help, --version), stdin; job/pipeline: input files, schedule/trigger, late, partial or empty input behavior. [addition]
| Field/arg | Type | Required | Allowed values / limits | Notes |
|---|---|---|---|---|
| [ ] | [ ] | yes/no | [ ] | |

Example input:
```
[concrete example]
```

## 3. Success output
CLI/job alternative: what goes to stdout vs stderr, output-format stability (is it a public contract?), exit code 0, per-item or per-recipient results. [addition]
Shape (predictable, same every time): [ ]
Example:
```
[concrete example; include status/exit code/return type]
```

## 4. Errors (single project error shape)
CLI alternative: exit-code matrix (code | meaning | stdout/stderr) and a finding format (findings are results, not errors, e.g. `file:line: target: reason`). Rows below marked (web) apply only when users sign in. [addition]
Shape: `code` (stable), `message` (safe, human-readable), optional `field`. No stack traces or internal detail.

| Condition | Code | Status / exit code / exception | Field |
|---|---|---|---|
| Missing/invalid input | VALIDATION_ERROR | [ ] | [ ] |
| Not authenticated (web) | [ ] | [ ] | |
| No permission (web) | [ ] | [ ] | |
| Not found | [ ] | [ ] | |
| Other | [ ] | [ ] | |

Example error:
```
{ "error": { "code": "VALIDATION_ERROR", "message": "[safe message]", "field": "[name]" } }
```
Same fields in other forms:
- stderr line: `error[VALIDATION_ERROR]: [safe message] (--[name])`, exit code [n]
- exception/result type: `ValidationError(code, message, field)`
- error event payload: `{code, message, field}`

Response principles (Applies when: HTTP/RPC boundary; for CLI use the exit-code matrix above) [addition]:
- Success (200/201): return only the fields the caller is allowed to see.
- Invalid input (400): explain the invalid field without exposing internals.
- Not authenticated (401): ask the caller to authenticate again.
- No permission (403): do not reveal protected resource details.
- Not found (404): do not confirm whether another owner's resource exists (a denied and a missing resource look the same to the caller).
- Unexpected failure (500): safe generic message; log the internal reason.

## 5. Validation (runs before data is saved)
| Rule type (required / length / allowed value / relationship / permission / date-range) | Rule | Linked REQ/AC |
|---|---|---|
| [ ] | [ ] | |

## 6. Behavior notes
- Business rules enforced beyond basic validation: [ ]
- Side effects / events recorded (audit; Applies when: actions need an audit trail): [ ]
- Idempotency, ordering, limits (rate, size, pagination) if applicable: [ ]   (job: rerun and overlapping-run behavior; signals/interrupts and partial output) [addition]
- Integrations touched and failure behavior: [ ]

## 7. Versioning and compatibility
- Current version: [ ]
- Breaking-change policy: adding optional fields/operations is usually safe; renaming/removing/retyping is breaking and needs a new version.
- Known consumers to update on change (skip for a v0 tool with none): [ ]

## 8. Tests required (written before implementation: unit, integration, edge case)
| TEST-### | Case (normal / edge / failure) | Covers |
|---|---|---|
| TEST-### (filled in later; mapping lives in traceability-matrix.md) | [ ] | [ ] |
