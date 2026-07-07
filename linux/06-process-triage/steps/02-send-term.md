# Send SIGTERM

The default `kill` signal is `SIGTERM` (15) — a polite "please shut down"
that gives the process a chance to clean up. Try it:

```bash
kill $(cat /tmp/pid)
```

Then mark that you've tried:

```bash
touch /tmp/termed
```

Well-behaved apps catch SIGTERM and exit cleanly. Ill-behaved ones ignore
it — that's what the next step is for.
