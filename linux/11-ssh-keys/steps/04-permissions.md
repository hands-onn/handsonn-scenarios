# Lock down permissions

sshd rejects `authorized_keys` if the perms are too permissive
(a group- or world-writable file could be swapped out). The exact
required modes:

| Path | Mode |
|---|---|
| `~/.ssh` | `700` |
| `~/.ssh/id_ed25519` (private key) | `600` |
| `~/.ssh/id_ed25519.pub` (public) | `644` (readable is fine) |
| `~/.ssh/authorized_keys` | `600` |

Set them all:

```bash
chmod 700 /root/.ssh
chmod 600 /root/.ssh/id_ed25519
chmod 644 /root/.ssh/id_ed25519.pub
chmod 600 /root/.ssh/authorized_keys
```

Confirm:

```bash
ls -la /root/.ssh
```
