# Fix ownership

`chown user:group path` changes ownership. Recursive form is `-R`.

Set both the `.ssh` directory and the `authorized_keys` file to belong
to alice:

```bash
chown -R alice:alice /home/alice/.ssh
```

Verify:

```bash
ls -la /home/alice/.ssh
```
