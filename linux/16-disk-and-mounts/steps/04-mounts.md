# What's mounted, and how?

Every filesystem visible to the container is listed in `/proc/mounts`
(or via the `mount` command). Look at the root filesystem:

```bash
mount | grep ' / '
cat /proc/mounts | grep ' / '
```

Each line: `device  mountpoint  fstype  options`. The **third field** is
the filesystem type (e.g. `overlay` in containers, `ext4`/`xfs` on real
disks). Options like `ro` (read-only), `noexec`, `nosuid` are security-
relevant — a `ro` mount is why "permission denied" can happen even as
root.

Extract the root filesystem type:

```bash
awk '$2=="/" {print $3}' /proc/mounts > /tmp/rootfs_type
cat /tmp/rootfs_type
```

In containers you'll typically see `overlay` — the layered union
filesystem that makes image layers work.
