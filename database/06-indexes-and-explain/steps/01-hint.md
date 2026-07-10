`EXPLAIN SELECT ...` prints the plan; the first line will contain "Seq Scan". Piping the first line to /tmp/plan_before captures it. The check greps for "seq".
