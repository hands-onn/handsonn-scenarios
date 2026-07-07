# Copy a directory

Try copying the whole `/work/src` tree into `/work/dst`. `cp` on a
directory needs `-r` (recursive), otherwise it fails.

```bash
cp -r /work/src /work/dst
```

Wait — that creates `/work/dst/src/`. We want `/work/dst/nested/three.txt`
directly, not `/work/dst/src/nested/three.txt`.

Fix it by copying the *contents* instead:

```bash
cp -r /work/src/. /work/dst/
```

The trailing `/.` says "copy what's inside, not the directory itself."
