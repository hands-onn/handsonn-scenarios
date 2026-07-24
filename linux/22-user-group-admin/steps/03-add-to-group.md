# Make deploy a member of deployers

`useradd` gave `deploy` its own primary group. To let it collaborate in the
shared deploy area, add it as a **secondary** member of `deployers`.

The critical detail is the `-a` (append) flag:

```bash
usermod -aG deployers deploy
```

Without `-a`, `usermod -G` **replaces** the account's entire supplementary
group list — a classic way to accidentally lock a service out of everything
else it belonged to. With `-a`, you only add.

Confirm the membership:

```bash
id deploy
getent group deployers
```

`id deploy` should now list `deployers` among its groups, and the `deployers`
line in `getent group` should name `deploy` as a member.
