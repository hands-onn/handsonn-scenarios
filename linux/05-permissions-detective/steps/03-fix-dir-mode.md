# Fix the directory mode

sshd requires `~/.ssh` to be **only** readable/writable/executable by
the owner — that's mode `700` (rwx------).

```bash
chmod 700 /home/alice/.ssh
```

Verify:

```bash
stat -c '%a %n' /home/alice/.ssh
```
