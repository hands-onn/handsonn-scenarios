# scp: file over SSH

`scp` = "secure copy" — a thin wrapper over SSH that copies files
instead of opening a shell.

Copy `/work/a.txt` to `handsonn@sshserver:/config/a.txt`:

```bash
scp -P 2222 -i /root/.ssh/id_ed25519 \
  -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null \
  /work/a.txt handsonn@sshserver:/config/a.txt
```

Note the **capital `-P`** for the port. (SSH uses lowercase `-p`. Yes,
it's inconsistent. Yes, everyone gets bitten by it.)

You can copy in either direction:

```bash
# Local → remote
scp -P 2222 -i /root/.ssh/id_ed25519 /work/a.txt handsonn@sshserver:/config/

# Remote → local
scp -P 2222 -i /root/.ssh/id_ed25519 handsonn@sshserver:/etc/hostname /tmp/remote-hostname
```

Recursive (whole directory): `scp -rP 2222 -i KEY SRC DST`.

`scp` is being deprecated in newer OpenSSH — it's essentially a symlink
to `sftp` in modern distros. `rsync` (next step) is the modern choice.
