# --delete: make destination match source

By default `rsync` only *adds* — files that exist on the destination
but not the source are left alone. `--delete` removes them.

First, delete a file locally to prove the point:

```bash
rm /work/a.txt
```

Then sync with `--delete`. Note the trailing slash on the source —
this time we want contents-into-contents:

```bash
SSH_CMD="ssh -p 2222 -i /root/.ssh/id_ed25519 -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null"
rsync -a --delete -e "$SSH_CMD" /work/ handsonn@sshserver:/home/handsonn/work/
```

Confirm `a.txt` is gone from the destination:

```bash
ssh -p 2222 -i /root/.ssh/id_ed25519 handsonn@sshserver 'ls /home/handsonn/work'
```

`--delete` is powerful and dangerous. **Always dry-run first**:

```bash
rsync -a --delete -n -v -e "$SSH_CMD" /work/ handsonn@sshserver:/home/handsonn/work/
```

The `-n` (or `--dry-run`) shows what *would* happen without touching
anything. Skipping the dry-run is how backup scripts nuke productions.
