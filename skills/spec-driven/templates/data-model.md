# Data Model: [feature or module]

Lite mode: may merge into docs/spec.md; Approver: self (solo) allowed. [addition]

Delete sections that do not apply (skip entirely if nothing is persisted). Storage-neutral: "entity" means table, collection, file, record type, or dataset.

Status: Needs review | Approved | Needs update   Linked requirements: REQ-### ...
Data in motion (pipeline/CLI input that is not persisted): use the entities and fields blocks anyway, and add field meaning, source, freshness, owner and source precedence on conflict. [addition]

## 1. Entities
| Entity | Purpose (what it stores) | Linked REQ | Rule that must always be true |
|---|---|---|---|
| [ ] | [what the system must remember] | REQ-### | [ ] |

## 2. Fields (one block per entity)
Entity: [name]   Primary key: [field]
| Field | Type | Required | Default | Validation / constraint (unique / allowed values / limits) | Sensitive? (personal / confidential / security) | Used by (contract / validation / REQ) |
|---|---|---|---|---|---|---|
| id | [ ] | yes | generated | unique identifier | no | |
| [ ] | [ ] | yes/no | [ ] | [ ] | [ ] | [ ] |

## 3. Allowed-value sets (define once, reference everywhere)
| Set | Values |
|---|---|
| [e.g. item status] | [values] |

## 4. Relationships and keys
| From | To | Cardinality (1:1 / 1:N / N:N) | Key/reference | Deletion behavior (restrict / cascade / archive / retain-for-audit) | Why |
|---|---|---|---|---|---|
| [ ] | [ ] | [ ] | [ ] | [ ] | [ ] |

## 5. Ownership / isolation boundary (Applies when: data of several owners is stored; else 'none')
Owner key on every record and every query: [user | project | organization | none] - how derived: [from authenticated context, never from client input unless stated].

## 6. Indexes / lookups (Applies when: lookups are performance-relevant)
| Common lookup | Index / access path |
|---|---|
| [ ] | [ ] |

## 7. Sensitive data
[Which fields are personal, confidential, or security-sensitive; how each is protected (access, masking, not logged).]
Data in transit: [files, messages, email or network hops that carry these fields; protection and retention at each hop] [addition]

## 8. Retention and archival
[What is kept, for how long, how removed.]

## 9. Migration notes (Applies when: persisted data already exists)
[How a change to this model is applied safely: order of steps, existing data handling, rollback.]

## 10. Cross-check (complete before approval)
- [ ] Every field in every interface contract exists here.
- [ ] Every validation rule refers to an existing field or entity.
- [ ] Concepts used in permission rules (e.g. membership, role) have a model.
- [ ] Optional vs required is the same here and in all contracts.
- [ ] Allowed-value sets match across schema, validation, and examples.
- [ ] Invalid data is made hard to store by constraints, not only by application code.
