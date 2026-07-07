# authorized_keys

`~/.ssh/authorized_keys` on the *server* is the list of public keys
allowed to log in as that user. To let this key log in to some other
server as root, you'd install the **public** key there — never the
private one.

For this lab, we'll authorize ourselves to log in as root on this same
pod:

```bash
cp /root/.ssh/id_ed25519.pub /root/.ssh/authorized_keys
```

One key per line. If you want to allow multiple identities, append
their .pub contents.

Confirm:

```bash
cat /root/.ssh/authorized_keys
```
