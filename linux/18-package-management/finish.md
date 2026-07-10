# Package management across distros.

Same concepts, different commands:

| Task | Alpine (apk) | Debian/Ubuntu (apt) | RHEL/Fedora (dnf) |
|---|---|---|---|
| Refresh index | `apk update` | `apt-get update` | `dnf check-update` |
| Search | `apk search X` | `apt-cache search X` | `dnf search X` |
| Install (repo) | `apk add X` | `apt-get install X` | `dnf install X` |
| List pkg files | `apk info -L X` | `dpkg -L X` | `rpm -ql X` |
| Who owns a file | `apk info --who-owns F` | `dpkg -S F` | `rpm -qf F` |
| Install local file | `apk add --allow-untrusted ./f.apk` | `dpkg -i ./f.deb` | `dnf install ./f.rpm` |
| Remove | `apk del X` | `apt-get remove X` | `dnf remove X` |

Key ideas that transfer everywhere:
- **Refresh the index** before installing, or you get stale/missing pkgs.
- The manager resolves **dependencies** automatically from repos — but a
  bare local file may need a follow-up (`apt-get install -f`) to pull
  deps the file didn't bundle.
- Package managers **track file ownership**, enabling clean removal and
  "what installed this file?" forensics.
