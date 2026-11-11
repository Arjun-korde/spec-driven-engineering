# Engineering quality review (monthly or per release) [book ch 30]

Delete sections that do not apply. Optional audit mode.

```
Period reviewed:
Projects or features shipped:
Requirements that changed:
Tasks completed with agent support:
Defects found before release:
Defects found after release:
Most common review issue:
Most common agent mistake:
Template that needs improvement:
Agent rule that needs improvement:
Spec drift found:
Action items for next month: (each with owner)
```

Optional metric snapshot [book ch 30 metrics; no numeric targets, set your own]:

| Metric | How measured | This period | Direction (healthy) |
|---|---|---|---|
| Requirement clarity | Questions raised at task handoff | | Fewer basic questions |
| Rework rate | Tasks reopened after review | | Lower |
| Test usefulness | Bugs found by tests before release | | More meaningful pre-release failures |
| Review findings | Comments by category | | Repeated categories decrease |
| Spec drift | Released behavior reflected in docs | | Fewer undocumented changes |
| Production stability | Errors, incidents, rollbacks, response time | | Fewer incidents, faster recovery |

Close-out: [ ] each action has an owner [ ] changed templates/rules have a new version and a CHG-### record (`templates/spec-change-record.md`) [ ] drift items assigned. [addition]
