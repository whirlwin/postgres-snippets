# postgres-snippets
-------------------

Useful queries in PostgreSQL.

----

## 1. Metadata

### Finding currently running queries
```sql
SELECT * FROM pg_stat_activity;
```

### Calculate diff for all tables in a schema
```sql
DO $$
    DECLARE
        table_name text;
        query text;
        result text;
    BEGIN
        FOR table_name IN SELECT tablename FROM pg_tables WHERE schemaname = 'public'
            LOOP
                query := format('SELECT md5(array_agg(md5(row_to_json(t)::text))::text) AS checksum FROM %I t;', table_name);
                EXECUTE query INTO result;
                RAISE NOTICE 'Checksum for table %: %', table_name, result;
            END LOOP;
    END
$$;
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

### Finding all current connections
```sql
SELECT
    pid,
    usename,
    datname,
    application_name,
    client_addr,
    backend_start,
    state,
    state_change
FROM
    pg_stat_activity
ORDER BY
    backend_start;
```
