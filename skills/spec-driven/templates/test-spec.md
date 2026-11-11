# Test Spec: [feature or change name]
Delete sections that do not apply. Plan this BEFORE implementation. Fields from [book ch17, App G]; Plan state, Result, and Evidence are [addition].

Requirement(s): [REQ-###, ...]   Spec/contract version: [id or date]
Each TEST-### verifies one or more AC-### (or the REQ if it has no AC). The REQ/AC/TASK/TEST mapping lives only in docs/traceability-matrix.md; link to it, do not copy it. A table-driven test with several cases may use one TEST-###. [addition]
Owner (default for rows below): [role]   Date: [date]   Review status: Needs review | Approved | Needs update
Project level names mapped to taxonomy: [e.g. "API tests" = Integration; "smoke" = End-to-end flow; "regression" = purpose, not a level]

Levels: Unit | Integration | End-to-end flow | Security/abuse | Performance/limits
Case categories: Normal | Edge | Failure
Plan state: Planned | Written | Automated | Manual | Blocked
Result: Not run | Pass | Fail
Minimum per requirement in scope: >=1 Normal, >=1 Edge, >=1 Failure.

| TEST-### | REQ / AC | Scenario | Level | Case category | Preconditions | Input / action (valid, invalid, boundary, or malicious) | Expected result | Failure meaning (what it means if this fails) | Risk covered | Owner | Plan state | Result | Evidence |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| TEST-001 | REQ-### / AC-### | | | Normal | | | | | | | Planned | Not run | |
| TEST-002 | REQ-### / AC-### | | | Edge | | | | | | | Planned | Not run | |
| TEST-003 | REQ-### / AC-### | | | Failure | | | | | | | Planned | Not run | |

## Coverage check
| REQ | AC | Normal | Edge | Failure | Security | Gap? |
|---|---|---|---|---|---|---|
| REQ-### | AC-### | TEST-### | TEST-### | TEST-### | TEST-### or n/a (reason) | |

## Security / abuse checks (SEC-### rules proved)
| TEST-### | SEC-### | Who is allowed / not allowed | Input rejected or info withheld | Expected result |
|---|---|---|---|---|

## Isolation-boundary checks (only if more than one owner/group exists)
| TEST-### | Caller A | Resource owned by B | Operation tried | Expected refusal (no existence leak) |
|---|---|---|---|---|

## Performance / limit targets (project-set; record source)
| Key action | Target | Source of target | TEST-### |
|---|---|---|---|

## Decisions to record
- Validation vs permission check order: [chosen order] (ADR-### or note)
- Normalization allowed? [yes/no, per spec]
- Refusal message policy (generic vs specific): [policy]

## Review of AI-generated tests
Label each: Strong | Weak | Missing | Invented
| TEST-### | Label | Problem | Action (rewrite / add / remove / raise CHG-###) |
|---|---|---|---|

## Manual / exploratory sessions (optional)
| Session | Charter | Date | Findings -> TEST-### / BUG-### |
|---|---|---|---|

## Run summary
Build/version: [id]
Plan state counts: Planned [n]  Written [n]  Automated [n]  Manual [n]  Blocked [n]
Result counts: Not run [n]  Pass [n]  Fail [n]
