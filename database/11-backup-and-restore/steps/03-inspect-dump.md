# Trust, but verify the archive

A backup you've never inspected is a backup you don't actually have. The
custom format carries a **table of contents (TOC)** listing every object
in the archive. Read it *without touching the live database*:

```bash
pg_restore --list /tmp/backup.dump
```

You'll see entries like:

```
;
;  ...
215; 1259 16390 TABLE   public customers postgres
...
3012; 0 16390 TABLE DATA public customers   postgres
3013; 0 16400 TABLE DATA public products    postgres
3014; 0 16410 TABLE DATA public orders       postgres
3015; 0 16420 TABLE DATA public order_items  postgres
```

Two kinds of entries matter here:

- **`TABLE`** — the table *definition* (columns, constraints).
- **`TABLE DATA`** — the table's *rows*. If a `TABLE DATA` line is
  missing, that table's rows aren't in the archive and won't come back on
  restore.

Confirm all four tables have a `TABLE DATA` entry:

```bash
pg_restore --list /tmp/backup.dump | grep 'TABLE DATA'
```

You should see one line each for `customers`, `products`, `orders`, and
`order_items`. That TOC is also what lets you do selective restores later
(`pg_restore -t orders`, `--data-only`, `-L` with an edited list, etc.).
