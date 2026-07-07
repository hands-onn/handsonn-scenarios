# Fingerprint the key

A fingerprint is a short cryptographic hash of the public key. When you
first SSH to a server, the "Are you sure you want to continue
connecting? RSA key fingerprint is ..." prompt is asking you to verify
the fingerprint out-of-band.

Look at yours:

```bash
ssh-keygen -lf /root/.ssh/id_ed25519.pub
```

You'll see something like:

```
256 SHA256:abc123... handsonn-lab (ED25519)
```

The `SHA256:...` is what you'd compare against the value your provider
publishes.

Save the output for the checker:

```bash
ssh-keygen -lf /root/.ssh/id_ed25519.pub | awk '{print $2}' > /tmp/fingerprint
```
