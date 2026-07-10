# A query that hangs

Somewhere, a transaction grabbed a lock on inventory row `A1` and never
released it (a classic "idle in transaction" bug — an app opened a
transaction and forgot to commit). Any write to `A1` now blocks.

Prove it. Set a short `lock_timeout` so you fail fast instead of hanging
forever:

```bash
PGPASSWORD=lab psql -h db -U postgres -d shop -c "
  SET lock_timeout = '2s';
  UPDATE inventory SET qty = qty - 1 WHERE sku = 'A1';
" 2>&1 | tee /tmp/block_proof
```

You'll get `ERROR: canceling statement due to lock timeout` — your
UPDATE couldn't acquire the row lock because someone else holds it.

Note row `B2` is fine — the lock is row-level, only on `A1`:

```bash
PGPASSWORD=lab psql -h db -U postgres -d shop -c \
  "UPDATE inventory SET qty = qty - 1 WHERE sku = 'B2'"
```
