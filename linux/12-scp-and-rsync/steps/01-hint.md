The checker uses `sshpass -p labpass` to test password auth non-interactively. If it fails, verify the sshserver pod is up: `ping sshserver` and `nc -zv sshserver 2222`.
