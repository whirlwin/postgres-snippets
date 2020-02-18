# postgres-snippets
-------------------

Useful queries in Postgres used for monitoring.

----

## 1. Metadata

### Finding currently running queries
```sql
SELECT *
FROM pg_stat_activity
```

