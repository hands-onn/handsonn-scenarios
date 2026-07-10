The blocker is the backend whose state is `idle in transaction` (it ran BEGIN + UPDATE then sat idle). Save its numeric `pid` from pg_stat_activity to /tmp/blocker_pid.
