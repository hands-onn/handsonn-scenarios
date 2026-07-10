# sed: stream editing

`sed` transforms text as it streams by. Its most-used command is
substitution: `s/pattern/replacement/g`.

Redact the date from every line and save to a new file:

```bash
sed 's#10/Jul/2026#REDACTED#g' /data/access.log > /data/access.redacted.log
head -2 /data/access.redacted.log
```

Notes:
- The delimiter is usually `/`, but any char works — here `#` avoids
  escaping the slashes in the date.
- `g` = global (replace all matches on each line, not just the first).
- `sed -i` edits the file *in place* (no redirect). Powerful and
  dangerous — test with a redirect first, as we did here.

Confirm the original date is gone:

```bash
grep -c '10/Jul/2026' /data/access.redacted.log   # 0
```
