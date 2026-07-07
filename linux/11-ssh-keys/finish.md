# Ready to SSH.

Mental model for what happens on `ssh user@host`:

1. Client offers each private key in `~/.ssh/`.
2. Server checks each offered public key against `user`'s
   `~/.ssh/authorized_keys` on the server.
3. If a match: server sends a challenge encrypted with that pubkey;
   client proves it holds the matching privkey by decrypting.
4. Connection is upgraded to an encrypted session; shell/scp/tunnels
   ride on it.

Key hygiene:
- One key per **purpose** (personal, work, deploy-to-prod) not per host.
- Passphrase your private keys (`ssh-add` caches the decrypted key so
  you're not typing it repeatedly).
- Rotate. `ssh-keygen -y -f OLD_KEY > OLD_KEY.pub` regenerates a public
  key from a private key if you lose track.

Next: **scp-and-rsync** puts these keys to work moving files between pods.
