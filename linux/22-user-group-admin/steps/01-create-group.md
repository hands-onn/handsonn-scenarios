# Create the deployers group

A new CI pipeline needs its own least-privilege identity on this host. The
standard pattern is: one **group** that owns the shared area, one **service
user** that the pipeline runs as, and a tightly scoped sudo rule. We build it
bottom-up, starting with the group.

Create a group called `deployers`:

```bash
groupadd deployers
```

Confirm it landed in the group database:

```bash
getent group deployers
```

You should see a line like `deployers:x:1001:` — name, password placeholder,
GID, and (for now) an empty member list. `getent` reads the same NSS sources
the whole system uses, so a hit here means every tool will see the group.
