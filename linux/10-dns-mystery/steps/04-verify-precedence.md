# The resolution order

The reason `dig` and `getent` disagreed is `/etc/nsswitch.conf`. Look
at its `hosts:` line:

```bash
grep '^hosts:' /etc/nsswitch.conf
```

Typical order:

```
hosts: files dns
```

- `files` — read `/etc/hosts`.
- `dns` — ask the resolver (using `/etc/resolv.conf`).

Whichever wins first, wins. So `/etc/hosts` overrides DNS by default —
which is exactly why one poisoned line broke resolution for
`handsonn.local` while `dig`, which skips nsswitch, kept working.

Rule of thumb when a hostname resolves "wrong":

1. `getent hosts NAME` — see what the resolver library returns.
2. `dig +short NAME` — see what DNS itself returns.
3. If they differ: check `/etc/hosts` and `/etc/nsswitch.conf`.
4. If DNS itself returns wrong data: check `/etc/resolv.conf` and try
   another resolver (`dig @1.1.1.1 NAME`).
