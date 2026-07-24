# Meet the daemon

A small service called **signal-daemon** is already running on this pod. It
behaves like a real long-lived process: it installs *signal handlers* (traps)
so it can be reconfigured and shut down cleanly without losing state.

Before you can signal it, you need its **PID**. There are two honest ways to
find a daemon's PID:

```bash
# 1. By its command line (the reliable, name-based way):
pgrep -af signal-daemon

# 2. A well-behaved daemon writes its own pidfile:
cat /tmp/daemon.pid
```

`pgrep -af` prints the PID *and* the full argv, so you can confirm you found
the right thing before you fire signals at it. Cross-check with `/proc`:

```bash
tr '\0' ' ' < /proc/<PID>/cmdline; echo
# busybox ps has no -p filter, so grep the PID out of the full list:
ps -o pid,ppid,stat,comm | grep -w <PID>
```

When you're sure you have the daemon's PID, record it:

```bash
echo <PID> > /tmp/found_pid
```

You'll reuse this PID in every step. Signals are delivered *by PID* — send one
to the wrong number and you page the wrong process.
