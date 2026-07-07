# You did the real cross-host flow

The bootstrap path you just walked is the real one:

1. Password login to prove the server accepts you.
2. `ssh-copy-id` (or manual `cat >> authorized_keys`) to install your
   pubkey with correct permissions.
3. From that point on: pubkey auth only. Disable password login on the
   server (`PasswordAuthentication no` in `sshd_config`).

## scp vs rsync — when to pick which

| Situation | Tool |
|---|---|
| One small file, one-shot | `scp` |
| Anything recurring or > 1 file | `rsync` |
| Network keeps dropping | `rsync -P` (partial + resume) |
| Deploy artifact bundle | `rsync -av --delete` after a `--dry-run` |
| Migrate a home directory | `rsync -aH --numeric-ids` |
| Sync between servers | `rsync -e ssh SRC USER@HOST:DST` (or push/pull) |

Flags to remember:
- `-a` archive (everything you probably want).
- `-v` verbose, `-P` progress + resume, `-n` dry run.
- `-e "ssh -p N -i KEY"` custom transport.
- Trailing slash on source: contents-only, not the directory.

`scp` is being deprecated in newer OpenSSH (it's silently a symlink to
`sftp` in modern distros). `rsync` isn't going anywhere.
