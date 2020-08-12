# postgres-snippets
-------------------

Useful queries in PostgreSQL.

----

## 1. Metadata

### Finding currently running queries
```sql
SELECT * FROM pg_stat_activity;
```

### Finding row count for all user tables
```sql
CREATE OR REPLACE FUNCTION count_rows(schema TEXT, tablename TEXT) RETURNS INTEGER AS
$body$
DECLARE
    result INTEGER;
    query VARCHAR;
BEGIN
    query := 'SELECT count(1) FROM ' || schema || '.' || tablename;
    EXECUTE query INTO result;
    RETURN result;
END;
$body$
LANGUAGE plpgsql;

SELECT table_schema, table_name, count_rows(table_schema, table_name) as rowcount_total
FROM information_schema.tables
WHERE table_schema NOT IN ('pg_catalog', 'information_schema') AND table_type = 'BASE TABLE'

```
