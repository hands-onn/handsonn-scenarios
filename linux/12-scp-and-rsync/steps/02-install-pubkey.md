# Install your public key on the server

The cleanest way — one command that reads your local pubkey, ssh-es to
the server using the password, and appends the key with the right
permissions:

```bash
sshpass -p labpass ssh-copy-id \
  -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null \
  -i /root/.ssh/id_ed25519.pub \
  -p 2222 handsonn@sshserver
```

`ssh-copy-id` is the idiomatic bootstrap tool — it's what everyone
should use and half of people don't know exists.

If you'd rather do it the manual way (understanding what ssh-copy-id
does under the hood):

```bash
cat /root/.ssh/id_ed25519.pub | \
  sshpass -p labpass ssh \
    -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null \
    -p 2222 handsonn@sshserver \
    'mkdir -p ~/.ssh && chmod 700 ~/.ssh && cat >> ~/.ssh/authorized_keys && chmod 600 ~/.ssh/authorized_keys'
```

Now verify pubkey auth works — no password prompt:

```bash
ssh -i /root/.ssh/id_ed25519 -p 2222 \
    -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null \
    handsonn@sshserver 'echo ok'
```

Once this works, in a real deployment you'd disable password auth on
the server:

```
# /etc/ssh/sshd_config
PasswordAuthentication no
```

We're leaving password auth on here so you can retry the previous
step if you need to.
