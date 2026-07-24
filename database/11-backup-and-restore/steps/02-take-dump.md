# Take the backup

`pg_dump` reads a *consistent snapshot* of a single database — it runs
inside a transaction, so even a busy database gives you a coherent
point-in-time copy.

The most useful output format is **custom** (`-Fc`): a compressed binary
archive that `pg_restore` can replay selectively (whole DB, one table,
data-only, schema-only, reordered, in parallel). Plain SQL (`-Fp`) is
human-readable but far less flexible.

Take the dump to `/tmp/backup.dump` — note you connect to the `db` host
just like psql:

```bash
PGPASSWORD=lab pg_dump -h db -U postgres -Fc -f /tmp/backup.dump store
```

Confirm the file exists and is non-trivial in size:

```bash
ls -lh /tmp/backup.dump
```

A few things worth internalizing:

- `pg_dump` needs only **read** access; it doesn't lock out writers for
  the whole dump (it uses MVCC snapshots).
- The dump is **logical** — it's SQL/COPY statements, portable across
  minor versions and machine architectures. That's different from a
  *physical* base backup (`pg_basebackup`) which copies the data files.
- You dump one database at a time. `pg_dumpall` handles roles and
  cluster-wide objects.
