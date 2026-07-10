`SELECT pg_terminate_backend(<pid>)` kills the blocking connection and releases its locks. The check confirms zero `idle in transaction` sessions remain.
