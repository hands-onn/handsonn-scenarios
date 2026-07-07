The check looks for a line starting with `SHA256:` in /tmp/fingerprint. Pipe the ssh-keygen output through `awk '{print $2}'` to extract just the fingerprint column.
