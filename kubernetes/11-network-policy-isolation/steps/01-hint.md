If a curl hangs, add `--max-time 5`. Both pods should return `200` right
now — that's the point: with no NetworkPolicy in place, every pod can
reach every other pod. Write any note mentioning "open" / "both" / "200"
to `/tmp/baseline` once you've confirmed it.
