# What did the package install?

Package managers track exactly which files belong to each package. List
`tree`'s files:

```bash
apk info -L tree
```

You'll see `/usr/bin/tree` and its man page / docs. This ownership
tracking is why package managers can cleanly *remove* software later —
they know every file to delete.

Record the binary path:

```bash
apk info -L tree | grep 'bin/tree' | sed 's#^#/#' > /tmp/pkg_files
cat /tmp/pkg_files
```

Other useful queries:
- `apk info tree` — description, size, dependencies.
- `apk info --who-owns /usr/bin/tree` — which package owns a file.

apt equivalents: `dpkg -L tree` (files), `dpkg -S /usr/bin/tree` (owner).
