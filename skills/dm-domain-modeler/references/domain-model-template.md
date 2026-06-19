# Domain Model Template

Output structure for `docs/dome/domain-model/domain-model.md`. Use `[placeholder]` syntax for all variable content — never `{{VARIABLE}}`.

---

```markdown
---
date: [YYYY-MM-DD]
project: [project-name]
phase: design
status: draft
generated_by: dm:domain-modeler
---

# Domain Model — [project-name]

## Entity Catalog

| Entity | Description | Key Attributes |
|--------|-------------|----------------|
| [EntityName] | [what this entity represents] | [id, name, …] |

## Relationships

| From | Relationship | To | Cardinality | Notes |
|------|-------------|-----|-------------|-------|
| [EntityA] | [verb phrase] | [EntityB] | [one-to-many] | [optional note] |

## Business Glossary

> Include this section only when domain terms need disambiguation.

| Term | Definition | Synonyms | Contrasts with |
|------|-----------|----------|---------------|
| [term] | [definition] | [synonym1, synonym2] | [contrasting concept] |

## ER Diagram

\`\`\`mermaid
erDiagram
    [EntityA] {
        int id PK
        string [attribute-name]
    }
    [EntityB] {
        int id PK
        int [entity-a-id] FK
    }
    [EntityA] ||--o{ [EntityB] : "[relationship-label]"
\`\`\`
```

---

## Section Rules

- **Entity Catalog** — one row per entity; description explains the business concept, not the technical implementation.
- **Relationships** — written in natural language (e.g., "Customer places Order"), not code notation.
- **Business Glossary** — optional; include only if domain terms risk ambiguity.
- **ER Diagram** — always the last section; embedded directly in the document (no separate file needed for v1).
- All placeholders use `[square brackets]` — never `{{double braces}}`.
