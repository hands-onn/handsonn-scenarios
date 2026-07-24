# Freeze and thaw: SIGSTOP (19) / SIGCONT (18)

Some signals **cannot be trapped, blocked, or ignored** — the kernel handles
them itself. **SIGKILL (9)** is one. **SIGSTOP (19)** is the other: it freezes
a process mid-flight. It keeps its memory and open files, but the scheduler
stops giving it CPU. **SIGCONT (18)** thaws it and it picks up exactly where
it left off. This is what your shell's job control (`Ctrl-Z`, `fg`, `bg`)
does under the hood.

Freeze the daemon:

```bash
kill -STOP $(cat /tmp/found_pid)
```

Now inspect its **state**. In `ps`, the STAT column shows a one-letter code;
a stopped process is **`T`** (traced/stopped):

```bash
ps -o pid,stat,comm | grep -w "$(cat /tmp/found_pid)"
# or read it straight from /proc (field 3 of stat):
awk '{print $3}' /proc/$(cat /tmp/found_pid)/stat
```

You should see **`T`**. Record the letter you observed:

```bash
echo T > /tmp/stopped_state
```

Because SIGSTOP is untrappable, the daemon wrote **no** marker — it never got
to run any handler. That's the whole point of SIGSTOP.

Now thaw it and confirm it's runnable again (state returns to `S` — sleeping —
or `R` — running):

```bash
kill -CONT $(cat /tmp/found_pid)
ps -o pid,stat,comm | grep -w "$(cat /tmp/found_pid)"
```

Common state letters: `R` running, `S` interruptible sleep, `D` uninterruptible
sleep (usually blocked on I/O), `T` stopped, `Z` zombie.
