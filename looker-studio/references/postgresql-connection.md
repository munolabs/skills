# PostgreSQL / Supabase Connection - Looker Studio

## Connection Configuration

### For Supabase (Recommended)

| Field | Value |
|-------|-------|
| **Host** | `aws-X-REGION.pooler.supabase.com` |
| **Port** | `5432` (session) or `6543` (transaction) |
| **Database** | `postgres` |
| **Username** | `user.PROJECT_REF` |
| **Password** | Your password |
| **SSL** | Disabled* |

**Real example:**
- Host: `aws-1-us-east-2.pooler.supabase.com`
- Username: `looker_readonly.abcdefghijklmnop`

*SSL may cause certificate issues in Looker Studio. Try without SSL first.

---

## Finding Connection Details in Supabase

1. Go to **Supabase Dashboard**
2. **Project Settings** → **Database**
3. Find **Connection string** → **Session mode** or **Pooler**
4. Format is:
   ```
   postgresql://user.PROJECT_REF:PASSWORD@HOST:PORT/postgres
   ```

---

## Create Read-Only User

In Supabase SQL Editor:

```sql
-- Create user
CREATE USER looker_readonly WITH PASSWORD 'YourSecurePassword';

-- Grant read permissions
GRANT USAGE ON SCHEMA public TO looker_readonly;
GRANT SELECT ON ALL TABLES IN SCHEMA public TO looker_readonly;

-- For future tables
ALTER DEFAULT PRIVILEGES IN SCHEMA public
GRANT SELECT ON TABLES TO looker_readonly;
```

---

## Supported PostgreSQL Versions

- PostgreSQL 9.6 to 14

---

## Compatible Data Types

| PostgreSQL | Looker Studio |
|------------|---------------|
| INTEGER, BIGINT | Number |
| NUMERIC, DECIMAL | Number |
| REAL, DOUBLE | Number |
| VARCHAR, TEXT | Text |
| BOOLEAN | Boolean |
| DATE | Date |
| TIMESTAMP | Date & Time |
| CHAR | Text |

**Not supported:**
- INTERVAL
- Arrays
- JSON/JSONB (convert to TEXT)
- Geometric types

---

## Limitations

| Limitation | Value |
|------------|-------|
| Maximum rows per query | 150,000 |
| Accessible schemas | Only `public` |
| Header characters | ASCII only |
| Private IP connections | Not supported |

---

## Query Optimization

### Create Views for Looker
```sql
CREATE VIEW looker_sales_summary AS
SELECT
    DATE_TRUNC('day', created_at) as date,
    COUNT(*) as total_orders,
    SUM(revenue) as total_revenue,
    AVG(revenue) as average
FROM orders
WHERE created_at >= CURRENT_DATE - INTERVAL '90 days'
GROUP BY DATE_TRUNC('day', created_at);
```

### Recommended Indexes
```sql
-- For date filters
CREATE INDEX idx_orders_created_at ON orders(created_at);

-- For common groupings
CREATE INDEX idx_orders_status ON orders(status);
```

---

## Custom Query in Looker Studio

You can use custom SQL when creating the connection:

```sql
SELECT
    id,
    name,
    created_at AT TIME ZONE 'UTC' AT TIME ZONE 'America/New_York' as created_at_local,
    revenue,
    CASE
        WHEN revenue > 1000 THEN 'High'
        WHEN revenue > 500 THEN 'Medium'
        ELSE 'Low'
    END as segment
FROM deals
WHERE created_at >= CURRENT_DATE - INTERVAL '365 days'
```

---

## Troubleshooting

### Connection error
1. Verify correct host (use pooler, not direct connection)
2. Username must include `.PROJECT_REF`
3. Try without SSL first

### SSL asks for certificates
- Disable SSL in configuration
- Supabase pooler works without client certificates

### Query timeout
- Reduce date range
- Create pre-aggregated views
- Limit selected columns

### "Too many rows"
- Query returns more than 150,000 rows
- Add filters or use aggregated views

### Special characters in data
- Use `REPLACE()` to clean in the view
- Headers must be ASCII

---

## Example: Optimized View for Looker

```sql
CREATE OR REPLACE VIEW looker_deals_report AS
SELECT
    d.id,
    d.name,
    -- Convert timezone
    d.created_at AT TIME ZONE 'UTC' AT TIME ZONE 'America/New_York' as created_at,
    -- Clean text
    REGEXP_REPLACE(d.stage, '[^a-zA-Z\s]', '', 'g') as stage,
    -- Metrics
    COALESCE(d.revenue, 0) as revenue,
    -- Calculated dimensions
    EXTRACT(MONTH FROM d.created_at) as month,
    EXTRACT(YEAR FROM d.created_at) as year,
    TO_CHAR(d.created_at, 'YYYY-MM') as period
FROM deals d
WHERE d.created_at >= CURRENT_DATE - INTERVAL '2 years';
```

---

## Refresh Data

Looker Studio caches data. To refresh:

1. In the report, click **More options** (⋮)
2. Select **Refresh data**

Or configure automatic refresh in the data source settings.
