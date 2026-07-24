# Create the deploy system user

Now the account the pipeline actually runs as. Three properties matter for a
service account:

- **System account** (`-r`): no password aging, and a UID from the low
  system range — it's infrastructure, not a person.
- **Has a home directory** (`-m`): a place for keys, caches, and dotfiles.
- **Cannot log in interactively** (`-s /sbin/nologin`): if someone steals the
  credentials there's no shell to drop into. `nologin` prints a polite refusal
  and exits.

Create it:

```bash
useradd -r -m -s /sbin/nologin deploy
```

Inspect the result:

```bash
getent passwd deploy
id deploy
```

The last field of the `passwd` line is the login shell — it must be a
`nologin` (or `false`) shell. Field 6 is the home directory, which should now
exist on disk.
