# Install from a local package file

Sometimes you have a package **file** rather than repo access — an
air-gapped host, a vendor-supplied build, or a package you fetched
elsewhere. You install it directly from disk.

First fetch a package file (without installing it) into a directory:

```bash
mkdir -p /tmp/pkgs && cd /tmp/pkgs
apk fetch figlet
ls -l /tmp/pkgs
```

`apk fetch` downloads the `.apk` file but doesn't install it. You now
have something like `figlet-2.2.5-r3.apk`.

Install it **from the file**, not the repo:

```bash
apk add --allow-untrusted /tmp/pkgs/figlet-*.apk
```

`--allow-untrusted` is needed because a bare file isn't signed by a
configured repo key. Confirm it works:

```bash
figlet hello
```

The Debian equivalent is `dpkg -i ./package.deb` (then `apt-get install
-f` to pull any missing dependencies). The RHEL equivalent is
`dnf install ./package.rpm`.
