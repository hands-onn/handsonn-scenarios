# Disk triage skills.

The "disk is full" runbook:

1. **`df -h`** — which filesystem is full? (and `df -i` for inodes)
2. **`du -sh /path/* | sort -rh | head`** — drill into the full mount,
   biggest-first, repeat downward until you find the offender.
3. **`find /path -type f -size +100M`** — pinpoint specific huge files;
   add `-mtime +N` to find old cruft.
4. **`/proc/mounts` / `mount`** — confirm fstype and options (a `ro`
   mount or a full `overlay` explains a lot).

Traps worth knowing:
- **Deleted-but-open files**: a process holding a deleted file keeps its
  space until it closes. `lsof +L1` finds them; `df` and `du` disagree.
- **Inode exhaustion**: millions of tiny files fill the inode table;
  `df` shows free bytes but writes still fail. `df -i` reveals it.
