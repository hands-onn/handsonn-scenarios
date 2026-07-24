The exact command is:

```bash
PGPASSWORD=lab pg_dump -h db -U postgres -Fc -f /tmp/backup.dump store
```

`-Fc` selects the custom archive format, `-f` names the output file, and
the last argument (`store`) is the database name. The verify checks the
file is non-empty and that `pg_restore --list /tmp/backup.dump` can parse
it — which only works on a valid custom-format archive.
