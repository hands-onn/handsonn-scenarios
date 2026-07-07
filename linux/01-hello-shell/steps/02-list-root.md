# Step 2 — Inventory the filesystem

List the entries at `/` and save the output so we can verify it later.

```bash
ls / | tee /tmp/listed
```

`tee` writes to the file *and* shows the output on screen.
