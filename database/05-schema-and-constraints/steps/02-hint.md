Do the valid insert first (alice, balance 100), then attempt the two bad inserts — they'll error, which is correct. The check wants exactly 1 row in `accounts` afterward.
