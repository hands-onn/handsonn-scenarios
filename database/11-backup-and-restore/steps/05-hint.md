Replay the custom-format archive with `pg_restore` (psql won't read it):

```bash
PGPASSWORD=lab pg_restore -h db -U postgres -d store /tmp/backup.dump
```

Then re-count. The verify queries all four tables live and passes only
when every count matches the seeded originals: `customers=50`,
`products=200`, `orders=500`, `order_items=1500`. Comparing
`/tmp/counts_after` to your `/tmp/counts_before` with `diff` is the
manual version of the same check.
