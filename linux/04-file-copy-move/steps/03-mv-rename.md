# Rename with mv

`mv` moves a file, but if source and destination are on the same
filesystem it acts as a rename — no data is copied.

Rename `/work/src/two.txt` to `/work/src/renamed.txt`:

```bash
mv /work/src/two.txt /work/src/renamed.txt
```

Confirm the old name is gone and the new one exists:

```bash
ls /work/src
```
