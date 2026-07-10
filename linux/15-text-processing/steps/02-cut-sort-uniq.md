# The classic pipeline: cut | sort | uniq -c

To rank clients by request count, extract the IP (field 1), then count
duplicates. `uniq -c` only collapses *adjacent* duplicates, so you must
`sort` first.

```bash
cut -d' ' -f1 /data/access.log | sort | uniq -c | sort -rn
```

Read it as a pipeline:
- `cut -d' ' -f1` — take the first space-delimited field (the IP).
- `sort` — group identical IPs together.
- `uniq -c` — collapse runs, prefix each with its count.
- `sort -rn` — sort by count, numeric, descending.

The top line is your busiest client. Extract just its IP:

```bash
cut -d' ' -f1 /data/access.log | sort | uniq -c | sort -rn | head -1 | awk '{print $2}' > /tmp/top_ip
cat /tmp/top_ip
```
