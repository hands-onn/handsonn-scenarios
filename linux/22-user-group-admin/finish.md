# Service account provisioned.

You built a least-privilege deploy identity the way it's done on real hosts:

| Layer | What you did | Why |
|---|---|---|
| **Group** | `groupadd deployers` | One group owns the shared area and the sudo grant. |
| **User** | `useradd -r -m -s /sbin/nologin deploy` | A system account with a home dir and **no interactive shell** — stolen creds get no shell. |
| **Membership** | `usermod -aG deployers deploy` | `-a` **appends** — dropping it would wipe the account's other groups. |
| **Shared dir** | `chmod 2775 /srv/app` (setgid) | New files inherit the `deployers` group automatically — no per-file `chgrp`. |
| **Sudo** | scoped `/etc/sudoers.d/deployers`, `chmod 0440`, `visudo -cf` | Grant *specific commands*, never `ALL`; validate before it goes live. |

## The habits that matter

- **`getent` over grepping `/etc/passwd`** — it reads the same NSS sources
  (files, LDAP, SSSD…) that applications do, so it's the source of truth.
- **`usermod -aG`, always with `-a`** — the single most common way to
  accidentally lock a service account out of its groups.
- **setgid on shared dirs** — `drwxrwsr-x`. The lowercase `s` is the whole
  point; without it, files land in the creator's primary group and teammates
  can't write them.
- **Drop-ins + `visudo -cf`** — never hand-edit `/etc/sudoers`; drop a scoped
  file in `/etc/sudoers.d/`, keep it `0440`, and **validate syntax first**. A
  malformed sudoers file can lock even root out of `sudo`.

## A note on capabilities

Creating users/groups and writing under `/etc/sudoers.d` require root. This lab
runs the pod as root and requests the `SETUID`, `SETGID`, and `CHOWN`
capabilities so `useradd -m` can assign the account's IDs and own its home
directory. On a locked-down platform that does **not** grant those caps,
`useradd -m` may fail to create or chown the home dir — in that case create the
home path manually and `chown deploy:deploy` it, then continue. On a normal
Linux host running as real root, none of this applies.
