`SET lock_timeout='2s'` then UPDATE A1 — it errors with "lock timeout". Piping the output to /tmp/block_proof captures the word "lock"/"timeout" the check looks for.
