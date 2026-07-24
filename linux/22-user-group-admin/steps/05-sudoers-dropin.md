# Grant a scoped, validated sudoers rule

The pipeline occasionally needs to restart the app service — but nothing else.
Never edit `/etc/sudoers` directly and never hand out `ALL`. The correct
pattern is a small, single-purpose **drop-in** under `/etc/sudoers.d/`, scoped
to the `deployers` group and to specific commands, then **validated before it
goes live**.

Write the drop-in. Scope it to `%deployers` (the `%` means "group") and name
the exact command(s) you want to allow — for example:

```bash
cat > /etc/sudoers.d/deployers <<'EOF'
# Allow the deploy pipeline to restart the app service only.
%deployers ALL=(root) NOPASSWD: /usr/sbin/service app restart
EOF
```

Lock the permissions — `sudo` **refuses to read** a sudoers file that is
group- or world-writable:

```bash
chmod 0440 /etc/sudoers.d/deployers
```

Now the most important habit in this whole lab — **validate the syntax before
you trust it**. A malformed sudoers file can lock everyone (including root) out
of `sudo`:

```bash
visudo -cf /etc/sudoers.d/deployers
```

It should print `... parsed OK`. `visudo -c` checks syntax; `-f <file>`
targets just this drop-in instead of the live `/etc/sudoers`.

> The check rejects a wide-open grant (a `%deployers` rule ending in a bare
> `ALL` command spec, e.g. `%deployers ALL=(ALL) NOPASSWD: ALL`). Keep it
> scoped to real command paths.
