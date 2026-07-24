`-tA` gives tuples-only, unaligned output — perfect for a machine-readable
file. The heredoc runs four `SELECT`s and redirects all of them to
`/tmp/counts_before`. The verify greps for the exact lines
`customers=50`, `products=200`, `orders=500`, `order_items=1500`, so make
sure the file contains those (re-run the block if in doubt).
