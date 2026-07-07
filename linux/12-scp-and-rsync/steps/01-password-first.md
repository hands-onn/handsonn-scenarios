# First contact — password auth

You have two pods:

- **client** (this terminal) — has your ssh client + keypair + sample files.
- **sshserver** — running sshd on port `2222` as user `handsonn`,
  with password `labpass` for first-time bootstrap.

This is exactly how you'd handle a fresh cloud VM: the provider gives
you a starter credential (root password, EC2 key, whatever) — you use
it once to install your real credentials, then close the door behind you.

Try a password login:

```bash
ssh -p 2222 handsonn@sshserver
```

At the prompt, type `labpass`. Take a look around:

```bash
whoami       # handsonn
ls -la ~     # /home/handsonn is $HOME here
exit
```

For scripting, `sshpass` supplies the password non-interactively:

```bash
sshpass -p labpass ssh -p 2222 -o StrictHostKeyChecking=no handsonn@sshserver 'hostname'
```

Never leave sshpass usage in production scripts — it's a red flag.
Here it's fine because we're about to install a real key.
