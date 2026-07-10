# Joins + aggregation down.

The toolkit:

- **INNER JOIN** — only matching rows (the intersection).
- **LEFT JOIN** — all left rows + matches; NULLs where none. `WHERE
  right.key IS NULL` = anti-join ("things with no match").
- **GROUP BY** — collapse into groups for `SUM`/`COUNT`/`AVG`/`MAX`.
- **WHERE vs HAVING** — WHERE filters rows *before* grouping; HAVING
  filters *after*, on the aggregate (e.g. `HAVING SUM(amount) > 100`).

A frequent bug: selecting a non-aggregated column that isn't in
`GROUP BY`. Postgres rejects it — every selected column must either be
grouped or wrapped in an aggregate.

Next: **schema-and-constraints** — design tables that reject bad data.
