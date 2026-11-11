# Monitoring and Maintenance Spec: [Workflow / Feature]
Delete sections that do not apply. [book ch 24; structure adapted]

| Section | Define |
|---|---|
| Feature or workflow | |
| Monitoring signals (logs, metrics, errors, performance, feedback sources) | |
| Health expectations (what healthy looks like) | |
| Alert conditions (signal + threshold; project sets values) | |
| Owner or reviewer (role) | |
| Spec update rule (when the spec must change) | |
| Test update rule (when checks must be added/changed) | |
| Release follow-up (what to check after next deployment) | |

## Signals
| Signal | Area (availability/correctness/performance/errors/usage/security) | Reason | Trigger | Response |
|---|---|---|---|---|

## Error tracking
| Condition | Capture | Severity (P0-P3; project sets response times) | Response | Response time |
|---|---|---|---|---|
Stack traces: internal logs only.

## Performance targets (project values)
| Workflow | Metric | Target | Action if exceeded |
|---|---|---|---|

## Feedback intake
| Feedback | Maps to (REQ / flow / decision / missing AC) | Spec action |
|---|---|---|

## Spec drift checklist (status: Not started | Ready | Blocked | Done) [book App Q; per-signal rows from ch24 table merged in: addition]
After every release:
| Check | Status |
|---|---|
| Requirements updated for accepted changes | |
| Interface/data/technical specs updated if contracts changed | |
| Traceability updated with released test evidence | |
| Decisions that changed design recorded (ADR-###) | |
| Monitoring observations and known limits noted | |
| Behavior vs acceptance criteria compared; source of truth decided | |
| Complaints vs check coverage compared; gaps added | |
| Behavior-changing bug fixes propagated to specs and tests | |
Source of truth: intended and accepted -> update spec; unintended or violates approved rule/security -> fix code; unclear -> Approver decides, record it.

## Monthly review
| Check | Status |
|---|---|
| Top feedback compared with requirements | |
| Frequent errors reviewed; spec or code decision made | |
| Performance and capacity assumptions reviewed | |
| Obsolete tasks removed, superseded decisions marked | |
| Context pack(s) refreshed before next agent work | |
