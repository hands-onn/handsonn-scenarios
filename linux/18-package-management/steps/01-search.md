# Find a package

This is Alpine Linux, so the package manager is **apk**. (Debian/Ubuntu
use `apt`; RHEL/Fedora use `dnf`/`yum` — same ideas, different verbs.)

First refresh the package index, then search for the `tree` tool:

```bash
apk update
apk search tree
```

`apk search` matches package names and descriptions. To match exactly:

```bash
apk search -x tree
```

You'll see something like `tree-2.3.2-r0` — name, version, release.
Record the package name:

```bash
echo tree > /tmp/pkg_name
```

apt equivalent: `apt-get update && apt-cache search tree`.
