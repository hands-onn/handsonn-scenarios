# Find the runaway

Something is chewing the CPU on this pod. Your job: find its PID.

Two tools worth trying:

```bash
# Snapshot, sorted by CPU%.
ps -eo pid,pcpu,comm --sort=-pcpu | head

# Live view; press q to exit.
top -b -n 1 | head -20
```

Look for the process near the top. When you have its PID, save it:

```bash
echo <PID> > /tmp/pid
```
