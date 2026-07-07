# Generate an ed25519 keypair

ed25519 is the modern default — smaller than RSA, faster to generate,
strong security margin. Generate one non-interactively:

```bash
ssh-keygen -t ed25519 -N '' -C "handsonn-lab" -f /root/.ssh/id_ed25519
```

- `-t ed25519` — algorithm.
- `-N ''` — empty passphrase (fine for a lab; add one in real life).
- `-C "..."` — a comment that appears at the end of the public key.
  Convention is `user@host` or an identifier for what the key is for.
- `-f PATH` — output file for the private key. Public key gets `.pub`
  appended.

Look at the two files:

```bash
ls -la /root/.ssh
cat /root/.ssh/id_ed25519.pub
```
