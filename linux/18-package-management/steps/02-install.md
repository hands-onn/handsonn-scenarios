# Install from the repository

Install the package. `--no-cache` skips keeping the index on disk (good
hygiene in containers):

```bash
apk add --no-cache tree
```

Confirm the command now exists and run it:

```bash
which tree
tree -L 1 /
```

The package manager downloaded the package + any dependencies from the
Alpine mirror and placed its files on the filesystem.

apt equivalent: `apt-get install -y tree`.
