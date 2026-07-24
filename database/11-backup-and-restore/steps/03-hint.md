`pg_restore --list <archive>` dumps the TOC. Filter it with
`grep 'TABLE DATA'` to see just the row payloads. The verify runs
`pg_restore --list` once and confirms a `TABLE DATA   public <table>`
entry exists for each of customers, products, orders and order_items — so
if any is missing, re-take the dump in the previous step.
