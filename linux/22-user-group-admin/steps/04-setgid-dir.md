# Build the shared setgid directory and prove inheritance

The pipeline drops artifacts into `/srv/app`. Everyone in `deployers` must be
able to read and write there, and — crucially — **every file created inside
must automatically belong to the `deployers` group**, no matter who created
it. That is exactly what the **setgid bit** on a directory does.

Create the directory, give it to the group, and set the mode:

```bash
mkdir -p /srv/app
chgrp deployers /srv/app
chmod 2775 /srv/app
```

Read the mode back:

```bash
stat -c '%A  %G  %n' /srv/app
```

You want to see `drwxrwsr-x` and group `deployers`. The lowercase **`s`** in
the group triad is the setgid bit; the leading `2` in `2775` is what sets it.
(`2775` = setgid + `rwxrwxr-x`: owner and group get full access, others read
and traverse.)

Now **prove** inheritance works. Create something inside and check its group:

```bash
touch /srv/app/deploy.lock
stat -c '%G  %n' /srv/app/deploy.lock
```

Even though `root` created that file, it should be group **`deployers`** —
inherited from the setgid parent, not root's own primary group. That is the
whole point of setgid: shared ownership without anyone remembering to `chgrp`.
