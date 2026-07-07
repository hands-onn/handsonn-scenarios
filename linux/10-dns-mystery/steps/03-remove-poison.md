# Remove the bad line

Delete the line from `/etc/hosts`:

```bash
sed -i '/127.99.99.99/d' /etc/hosts
```

Or edit interactively:

```bash
vi /etc/hosts
```

Verify:

```bash
cat /etc/hosts
getent hosts handsonn.local
```

`getent` should now report either the real answer (from DNS) or nothing
if the name doesn't exist upstream. In production, "nothing" is the
correct response for a name that isn't actually registered.
