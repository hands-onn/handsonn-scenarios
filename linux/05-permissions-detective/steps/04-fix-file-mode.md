# Fix the authorized_keys mode

`authorized_keys` must be readable by the owner only — mode `600`
(rw-------). sshd refuses to trust a file group or world can write.

```bash
chmod 600 /home/alice/.ssh/authorized_keys
```
