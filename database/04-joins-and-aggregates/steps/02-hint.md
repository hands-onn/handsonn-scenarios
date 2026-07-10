`WHERE status='paid'` filters before grouping (refunded orders excluded). Aggregate is `SUM(o.amount)`, group by customer, order DESC, take 1. Answer: Carol (200 > Alice's 150).
