# rsync: smarter, faster, resumable

`scp` transfers every byte every time. `rsync` computes deltas — only
changed data crosses the wire — and can resume interrupted transfers.
It's the right tool for anything larger than a single file.

Sync `/work` to `/config/work` on the remote:

```bash
rsync -a \
  -e "ssh -p 2222 -i /root/.ssh/id_ed25519 -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null" \
  /work handsonn@sshserver:/config/
```

Flag walk-through:
- `-a` (archive) — recursive + preserve perms, timestamps, symlinks,
  ownership. This is what you almost always want.
- `-e "ssh ..."` — tell rsync which transport command to use. Needed
  because rsync doesn't have `-p`/`-P` like scp; you push everything
  ssh-related into `-e`.
- `/work` (no trailing slash) — copies the directory itself.
  `/work/` (with trailing slash) — copies the *contents*. This is the
  single most common rsync bug. Here we want the directory itself, so
  no trailing slash — the result is `/config/work/{a,b,c}.txt`.

Extras worth knowing:
- `-v` (verbose), `-P` (progress + resume), `-n` (dry run — SAFE).
- `--exclude='*.log'` to skip patterns.
- `--bwlimit=1000` to cap bandwidth (KB/s).
