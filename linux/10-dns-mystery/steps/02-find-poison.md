# Something odd about handsonn.local

Try resolving `handsonn.local`:

```bash
getent hosts handsonn.local
ping -c1 handsonn.local
```

You'll see it resolves to `127.99.99.99` — a bogus IP. `dig`, though,
shows something different:

```bash
dig +short handsonn.local
```

The reason: `dig` bypasses `/etc/hosts` and goes straight to the DNS
resolver, but `getent`, `ping`, `curl`, and most application code use
the **resolver library** (nsswitch), which consults `/etc/hosts` first.

Find the poisoned entry:

```bash
grep handsonn.local /etc/hosts
```

Save the bogus IP so the check can verify you saw it:

```bash
grep handsonn.local /etc/hosts | awk '{print $1}' > /tmp/bogus
```
