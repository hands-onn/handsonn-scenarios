# Cross-check against /proc

`ss` told you a PID and a short name (`python3`). Before you kill anything,
confirm you understand *exactly* what that process is — the short name in
`ss` can be misleading. The authoritative source is `/proc/<pid>/`.

The full command line lives in `/proc/<pid>/cmdline`, but its arguments are
separated by **NUL bytes**, so `cat` mashes them together. Translate the
NULs into spaces:

```bash
tr '\0' ' ' < /proc/$(cat /tmp/port_pid)/cmdline; echo
```

You should see the real invocation, something like:

```
python3 -m http.server 8080 --bind 0.0.0.0
```

Other useful views of the same process:

```bash
ls -l /proc/$(cat /tmp/port_pid)/exe    # the actual binary on disk
cat    /proc/$(cat /tmp/port_pid)/status | head    # state, uid, threads
```

Save the command line so we can confirm you identified the culprit:

```bash
tr '\0' ' ' < /proc/$(cat /tmp/port_pid)/cmdline > /tmp/port_cmd
```
