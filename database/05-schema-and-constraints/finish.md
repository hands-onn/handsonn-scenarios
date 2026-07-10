# A schema that defends itself.

Push invariants into the schema wherever you can — the database enforces
them for *every* writer, forever, regardless of application bugs:

| Constraint | Guarantees |
|---|---|
| `PRIMARY KEY` | Unique, non-null identity per row |
| `NOT NULL` | Column always has a value |
| `UNIQUE` | No duplicate values |
| `CHECK (expr)` | Row satisfies an arbitrary rule |
| `FOREIGN KEY` | Value references a real row elsewhere |
| `DEFAULT` | Sensible value when none given |

Referential-action choices on foreign keys: `CASCADE` (delete children),
`RESTRICT`/`NO ACTION` (block), `SET NULL` (orphan the reference).

Rule of thumb: if a rule can be expressed as a constraint, put it in the
schema — not just the app. Constraints can't be forgotten, bypassed by a
rogue script, or lost in a refactor.

Next: **indexes-and-explain** — make queries fast and read the planner.
