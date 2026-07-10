# grep: find and count

`/data/access.log` is a small web access log. `grep` finds lines
matching a pattern; `grep -c` counts them.

Look at the log first:

```bash
cat /data/access.log
```

Count the server errors (HTTP 500):

```bash
grep ' 500 ' /data/access.log        # see them
grep -c ' 500 ' /data/access.log     # count them
```

The spaces around `500` avoid matching a 500 that appears in a byte
count or path. Write the count:

```bash
grep -c ' 500 ' /data/access.log > /tmp/errors
cat /tmp/errors
```

Handy grep flags: `-i` (case-insensitive), `-v` (invert), `-E`
(extended regex), `-o` (only the match), `-r` (recursive).
