# Don't clobber existing files

By default `cp` **silently overwrites** the destination. That's how many
outages start.

Try this:

```bash
echo "goodbye" > /tmp/danger.txt
cp -n /tmp/danger.txt /work/dst/one.txt
cat /work/dst/one.txt
```

`-n` means "no-clobber". Because `/work/dst/one.txt` already exists, `cp`
leaves the original alone. If you'd omitted `-n`, `one.txt` would now say
`goodbye`.

Alternative: `-i` prompts before overwriting. In scripts, prefer `-n` —
prompts hang.
