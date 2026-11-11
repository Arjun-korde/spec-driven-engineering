# Resolved inconsistencies: 06-testing

Book inconsistencies resolved in references/06-testing.md. All resolutions are [addition].

- Test types: the book lists "edge" and "failure" as test types (ch17 spec template) but treats them as case categories elsewhere. Resolved: two fields, Level and Case category.
- Level names differ across chapters ("unit/integration/E2E/security/performance" ch17 vs "unit/API/integration/UI" ch18 vs six layers in ch27/28 vs App G adding "regression"). Resolved: one five-level taxonomy; project names map onto it; regression is a purpose (re-run of existing checks), not a level.
- "Empty input" is unit/validation in one place and an integration point in another. Resolved: pick the level by what the check exercises (a rule alone = unit; rule plus response handling = integration).
- Requirement IDs for the same example differ across chapters. Resolved: REQ-### everywhere.
- "Three checks" minimum vs five-row examples. Resolved: three is the minimum per requirement, more as risk demands.
- Remove vs update-spec for invented behavior: decision rule added in the reference.
- Prompt output format lacked a requirement column and test ID. Resolved: both added.
- Test status: the book gives Automation status (App G) only; here test state is two fields, Plan state and Result.
