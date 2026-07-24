# You can back up and restore a PostgreSQL database.

You just completed a full disaster-recovery loop:

1. **Baselined** the data — recorded row counts so "recovered" has a
   definition.
2. **Dumped** it with `pg_dump -Fc` — a compressed, custom-format logical
   backup taken from an MVCC snapshot.
3. **Inspected** the archive with `pg_restore --list` — confirming the
   TOC actually contained every table's `TABLE DATA` before trusting it.
4. **Destroyed** the live tables (the disaster).
5. **Restored** with `pg_restore` and **verified** every row count
   matched the originals.

Key ideas to carry forward:

- **A backup isn't proven until a restore succeeds.** Rehearse restores;
  a dump you've never replayed is a hope, not a backup.
- **Logical vs physical.** `pg_dump`/`pg_restore` are *logical* (SQL +
  COPY, portable across versions/architectures, per-database).
  `pg_basebackup` + WAL archiving is *physical* (whole-cluster,
  point-in-time recovery, same major version).
- **Format matters.** Custom (`-Fc`) and directory (`-Fd`) archives allow
  selective and parallel restore (`-t`, `-L`, `--data-only`, `-j`).
  Plain (`-Fp`) is just SQL you replay with `psql`.
- **Restore targets.** Use `--clean`/`--if-exists`, or restore into a
  freshly `CREATE DATABASE`d target, to avoid "already exists" collisions.
- **`pg_dumpall`** captures roles and cluster-wide objects that a
  single-database `pg_dump` does not.

Next time a migration goes sideways, you know the recovery drill.
