# Mermaid erDiagram — Validation Rules

13 rules for generating valid `erDiagram` syntax. Rules marked ✓ are required; rules marked ✗ are forbidden.

---

## ✓ Required Rules

**R1. Valid cardinality only**

Use exactly these four cardinality symbols:

| Symbol | Meaning |
|--------|---------|
| `\|\|` | Exactly one |
| `}o` | Zero or more |
| `}\|` | One or more |
| `\|o` | Zero or one |

✓ Correct: `Customer \|\|--o{ Order : "places"`
✗ Incorrect: `Customer ONE--MANY Order : "places"`

---

**R2. Relationship label always in quotes**

Every relationship declaration must end with a label in double quotes.

✓ Correct: `Order }|--\|\| Product : "contains"`
✗ Incorrect: `Order }|--\|\| Product : contains`
✗ Incorrect: `Order }|--\|\| Product`

---

**R3. Entity names with spaces must be quoted**

If an entity name contains spaces, wrap it in double quotes.

✓ Correct: `"Order Item" }o--\|\| Order : "belongs to"`
✗ Incorrect: `Order Item }o--\|\| Order : "belongs to"`

Prefer PascalCase (`OrderItem`) to avoid quoting entirely.

---

**R4. Valid attribute types only**

Allowed types: `string`, `int`, `boolean`, `float`, `date`, `datetime`

✓ Correct: `string name` / `int quantity` / `datetime created_at`
✗ Incorrect: `varchar name` / `integer quantity` / `timestamp created_at` / `text description`

---

**R5. No semicolons or commas in attribute blocks**

Attribute lines end with the field name (and optional key constraint). No trailing punctuation.

✓ Correct:
```
Order {
  int id PK
  string status
}
```
✗ Incorrect:
```
Order {
  int id PK;
  string status,
}
```

---

**R6. Maximum one key constraint per attribute**

Each attribute can have at most one of: `PK`, `FK`, or `UK`.

✓ Correct: `int customer_id FK`
✗ Incorrect: `int customer_id PK FK`

---

**R7. Use `[Description]` syntax for placeholders — never `{{VARIABLE}}`**

✓ Correct: `string [attribute-name]`
✗ Incorrect: `string {{attribute_name}}`

---

## ✗ Forbidden Patterns

**R8. No `mandatory` keyword**

Optionality is expressed through cardinality, not the `mandatory` keyword.

✗ Incorrect: `Customer mandatory--Order : "places"`

---

**R9. No inline comments (`//` or `#`)**

Mermaid erDiagram does not support inline comments.

✗ Incorrect: `string name // customer full name`

---

**R10. Entity names must be PascalCase (or quoted)**

Do not use snake_case or lowercase for entity names.

✓ Correct: `CustomerOrder`, `ProductVariant`
✗ Incorrect: `customer_order`, `productvariant`

---

**R11. No duplicate relationship declarations**

Each pair of entities may have at most one relationship per direction.

✗ Incorrect: Declaring `Customer ||--o{ Order` twice in the same diagram.

---

**R12. Self-referential relationships must have an explicit label**

If an entity refers to itself, the relationship label is mandatory and must be meaningful.

✓ Correct: `Employee }o--o{ Employee : "manages"`
✗ Incorrect: `Employee }o--o{ Employee : ""`

---

**R13. Attribute names must not start with a number**

✓ Correct: `int quantity`, `string address_line1`
✗ Incorrect: `int 1st_quantity`

---

## Self-Check (5 Questions Before Writing)

Run these checks mentally before generating the `erDiagram` block:

1. **Entity names** — Are all entity names PascalCase, or quoted if they contain spaces?
2. **Relationship labels** — Do all relationship lines end with a label in double quotes?
3. **Attribute types** — Are all attribute types from the allowed list (`string`, `int`, `boolean`, `float`, `date`, `datetime`)?
4. **Cardinality syntax** — Does every cardinality use only `||`, `}o`, `}|`, or `|o`?
5. **Placeholders** — Are there any `{{variables}}` that should be `[placeholders]`?

If any answer is "no", fix before writing.
