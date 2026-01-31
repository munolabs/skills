# Looker Studio Skill

A comprehensive skill for creating dashboards and calculated fields in Google Looker Studio (formerly Data Studio).

## What's Included

| Reference | Topics |
|-----------|--------|
| [date-functions.md](./references/date-functions.md) | DATETIME_ADD, DATETIME_SUB, DATETIME_DIFF, EXTRACT, FORMAT_DATETIME, timezone conversion |
| [text-functions.md](./references/text-functions.md) | CONCAT, SUBSTR, REPLACE, REGEXP_EXTRACT, REGEXP_REPLACE, pattern matching |
| [aggregation-functions.md](./references/aggregation-functions.md) | SUM, AVG, COUNT, COUNT_DISTINCT, MAX, MIN, PERCENTILE, STDDEV |
| [logic-functions.md](./references/logic-functions.md) | CASE WHEN, IF, IFNULL, COALESCE, comparison and logical operators |
| [conversion-functions.md](./references/conversion-functions.md) | CAST, SAFE_CAST, data types, format conversions |
| [postgresql-connection.md](./references/postgresql-connection.md) | Supabase setup, read-only users, query optimization, troubleshooting |
| [resources.md](./references/resources.md) | Official docs, courses, YouTube channels, best practices |

## Quick Examples

```sql
-- Convert UTC to local timezone
DATETIME_SUB(created_at, INTERVAL 5 HOUR)

-- Conditional logic
CASE
    WHEN Revenue > 10000 THEN 'High'
    WHEN Revenue > 5000 THEN 'Medium'
    ELSE 'Low'
END

-- Handle nulls
IFNULL(Campaign, 'Direct')

-- Avoid division by zero
Revenue / NULLIF(Sessions, 0)
```

## Credits

This skill was created with the expertise of [Daniela](https://www.youtube.com/watch?v=_VxYaOn98-s), our resident Looker Studio wizard. She's built hundreds of dashboards and knows every formula trick in the book.

## License

MIT License - See [LICENSE.txt](./LICENSE.txt)
