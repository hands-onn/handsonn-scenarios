# find: pinpoint large files

`du` finds big directories; `find` pinpoints big *files* by size,
anywhere in a tree:

```bash
find /data -type f -size +10M
```

- `-type f` — files only (not directories)
- `-size +10M` — larger than 10 megabytes (`+` = greater than)

Write the offending path:

```bash
find /data -type f -size +10M > /tmp/big_file
cat /tmp/big_file
```

`find` is enormously flexible — combine with `-mtime` (age), `-name`
(pattern), and `-exec` to act on matches:

```bash
# files not modified in 30+ days, list details
find /data -type f -mtime +30 -exec ls -lh {} \;
```

Careful with `find ... -delete` or `-exec rm` — always run the bare
`find` first to see exactly what matches.
