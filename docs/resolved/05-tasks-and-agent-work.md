# Resolved inconsistencies: 05-tasks-and-agent-work

Book inconsistencies resolved in references/05-tasks-and-agent-work.md. All resolutions are [addition].

- Three task templates (15 fields in ch 14, 9 in ch 16, 7 sections in ch 4, plus a ch 1 task card): merged into one `templates/task.md`.
- Tests "before or alongside" (ch 14) vs "before implementation" (ch 16): tests are planned BEFORE implementation, written first when practical (per ch 16).
- "Likely files" with explain-first (ch 15) vs hard "Allowed / Not allowed files" (ch 16): use hard lists; the agent must stop and ask before touching anything else. The self-review prompt's last item was adapted from "not listed in the task plan" to "not on the allowed list" to match.
- Commit format with and without requirement ID (ch 15): always include the REQ ID where one applies; see `10-version-control-and-change.md`.
- Review layers "three" vs four in the table (ch 16): use four.
- Plan approval "always" (ch 11 loop) vs "if unclear" (ch 11 file): approve if scope, security, data, or architecture is touched or the spec is unclear; otherwise the plan may be pre-approved in the task file (section 4).
- Mixed ID styles (REQ-AUTH-001, REQ-LOGIN-01, AUTH-04, T-LOGIN-01): normalized to REQ-###, TASK-###, TEST-###.
- Ask-first (ch 4) vs explain-before-applying assumptions (ch 3): ask first when scope, security, data, or architecture is affected; otherwise state the assumption in the plan.
