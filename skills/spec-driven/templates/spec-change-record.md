# Spec Change Record: CHG-###
This is the single change-record format for the whole skill (references 09, 10, 13 point here). Delete fields that do not apply.
Book ch 24 has 6 fields: change date, reason, affected requirement, affected tests, affected code area, review status. Every other field below is an [addition]: Change ID, source-of-truth decision, affected acceptance criteria, affected tasks/decisions, updated requirement text, change note, release state, approver, and the extra Reason values "drift resolution" and the review-status vocabulary (book: Draft, reviewed, approved, implemented, released).

| Field | Value |
|---|---|
| Change ID [addition] | CHG-### |
| Change date | |
| Reason | Bug fix / user feedback / performance issue / security finding / product decision / drift resolution [addition] |
| Source of truth decision [addition] | Spec was wrong (update spec) / Code was wrong (fix code, BUG-###) / Approver decided |
| Affected requirement(s) | REQ-### / section |
| Affected acceptance criteria [addition] | AC-### |
| Affected tests | TEST-### to add or change |
| Affected code area | module / interface / job / service |
| Affected tasks / decisions [addition] | TASK-### / ADR-### |
| Updated requirement text [book ch 24 prompt output, as field: addition] | |
| Change note [book ch 24 prompt output, as field: addition] | |
| Review status [book ch 24, vocabulary adapted] | Needs review / Approved / Needs update |
| Release state [addition] | Not implemented / Implemented / Released / Deferred / Rejected |
| Approver [addition] | |

Agent prompt:
> Use the current requirement, the observed production behavior, and the proposed change. Return: updated requirement text, affected acceptance criteria, affected tests, affected code areas, and a short change note. Do not add unrelated features.
