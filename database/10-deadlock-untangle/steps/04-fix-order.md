# Fix: one canonical lock order

You cannot stop two transfers from touching the same two rows at once —
that's normal traffic. What you *can* do is make **every** transaction
acquire the rows in the **same global order**, so a cycle can never form.

The rule: sort the rows you're about to lock and always take the
**lowest id first**. Direction of the transfer no longer matters —
whether you're moving 1 -> 2 or 2 -> 1, you lock row 1 before row 2.

The fixed transfer script does exactly that:

```sh
LO=$SRC; HI=$DST
if [ "$SRC" -gt "$DST" ]; then LO=$DST; HI=$SRC; fi
BEGIN;
SELECT balance FROM accounts WHERE id = $LO FOR UPDATE;  -- lowest id first
SELECT balance FROM accounts WHERE id = $HI FOR UPDATE;  -- then the higher
UPDATE accounts SET balance = balance - $AMT WHERE id = $SRC;
UPDATE accounts SET balance = balance + $AMT WHERE id = $DST;
COMMIT;
```

Now sess_a locks 1 then 2. sess_b *also* locks 1 then 2. The second one
to arrive simply **waits** for the first to commit — a brief block, not a
cycle. Run the same opposing pair through the fixed script:

```bash
sh /tmp/run_pair.sh /tmp/fixed_transfer.sh "1 2 100" "2 1 100"
```

You should see `sess_a rc=0  sess_b rc=0` and no deadlock in either log:

```bash
grep -i deadlock /tmp/sess_a.log /tmp/sess_b.log || echo "no deadlock — fixed"
```

Money is conserved: the two transfers cancel, so balances still sum to
2000:

```bash
PGPASSWORD=lab psql -h db -U postgres -d bank -c \
  "SELECT id, owner, balance FROM accounts ORDER BY id;
   SELECT sum(balance) AS total FROM accounts;"
```

The check runs the pair, confirms **no deadlock**, both sessions exit
**0**, and the total balance is **2000**.
