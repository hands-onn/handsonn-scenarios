Add a second entry to `spec.rules` with `host: apps.handsonn.local` and one
path `/` -> `app-a:80`. Because `apply` overwrites the whole rules list, keep
the `/a` and `/b` path rules from step 2 in the same manifest — the check
still requires them. Re-run the full `kubectl apply -f - <<'EOF'` block.
