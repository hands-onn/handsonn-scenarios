# Inspect the broken layout

User `alice` was created with a `~/.ssh/authorized_keys`, but someone
messed up the permissions and sshd will now refuse to use it.

Take a look:

```bash
ls -la /home/alice/.ssh
```

You should see something like:

```
drwxrwxrwx 2 root root ...  .
-rw-rw-rw- 1 root root ...  authorized_keys
```

Two things are wrong:
1. Owner is `root`, not `alice`.
2. Modes are world-writable (`777` on the dir, `666` on the file). sshd
   refuses to use `authorized_keys` if the permissions are that loose.

When you've read this, tell the check you're ready:

```bash
touch /tmp/inspected
```
