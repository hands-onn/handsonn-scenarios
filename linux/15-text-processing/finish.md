# The text-processing toolkit.

Each tool has a niche; pipelines combine them:

| Tool | Best at |
|---|---|
| `grep` | Find/count lines matching a pattern (`-c`, `-i`, `-v`, `-E`, `-o`) |
| `cut` | Extract fixed fields/columns (`-d` delimiter, `-f` fields) |
| `sort` | Order lines (`-n` numeric, `-r` reverse, `-k` by key) |
| `uniq` | Collapse *adjacent* dupes (`-c` count) — always `sort` first |
| `awk` | Per-line field math, filtering, reporting |
| `sed` | Stream substitution/edits (`s///g`, `-i` in place) |

The `cut | sort | uniq -c | sort -rn` idiom — "rank things by frequency"
— is one of the most useful one-liners in ops: top IPs, top error codes,
top slow endpoints, all the same shape.

Rule: reach for `grep`/`cut`/`sort`/`uniq` for simple slicing; `awk` when
you need arithmetic or multi-field logic; `sed` for substitutions. For
anything bigger, a real script.
