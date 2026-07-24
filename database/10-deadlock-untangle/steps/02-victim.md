# Which transaction lost?

When Postgres detects a deadlock it doesn't hang — it picks a **victim**
and aborts *that* transaction's statement with `ERROR: deadlock
detected`. The other transaction is unblocked and continues normally.

Which of our two sessions was the victim? The victim is the one whose log
contains the deadlock error. Read both:

```bash
echo '--- sess_a ---'; cat /tmp/sess_a.log
echo '--- sess_b ---'; cat /tmp/sess_b.log
```

The victim's log ends in `ERROR: deadlock detected` plus a `DETAIL`
line naming the processes and the `ShareLock on transaction` it was
waiting for. The survivor's log just shows its `COMMIT`.

Record the victim's name (`sess_a` or `sess_b`):

```bash
# figure it out from the logs above, then write it, e.g.:
echo "sess_a" > /tmp/victim.txt
```

The check confirms the name you wrote matches the session that actually
errored.

> Note: which session becomes the victim can vary run to run — Postgres
> aborts whichever transaction is cheapest to roll back. Read the logs;
> don't guess.
