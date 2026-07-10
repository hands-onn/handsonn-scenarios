# awk: compute across columns

`awk` splits each line into fields (`$1`, `$2`, ... `$NF` = last field)
and runs your program per line. Summing a column is the canonical use:

```bash
awk '{ total += $NF } END { print total }' /data/access.log
```

- `$NF` — the last field (the byte count here).
- `total += $NF` — accumulate, per line.
- `END { print total }` — run once, after all lines.

Write the sum:

```bash
awk '{ total += $NF } END { print total }' /data/access.log > /tmp/total_bytes
cat /tmp/total_bytes
```

awk can also filter: `awk '$(NF-1)==200 { n++ } END{print n}'` counts
lines whose second-to-last field (status) is 200. It's a tiny language —
patterns, actions, variables, and math.
