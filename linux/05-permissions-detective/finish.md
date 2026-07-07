# Fixed.

sshd's "StrictModes" checks require:

| Path | Mode | Owner |
|---|---|---|
| `~/.ssh` | `700` | you |
| `~/.ssh/authorized_keys` | `600` | you |
| `~/.ssh/id_*` (private keys) | `600` | you |

If any is wrong, sshd silently ignores the file and falls back to
password auth (or refuses login). Look in `/var/log/auth.log` on the
server side when you're diagnosing.
