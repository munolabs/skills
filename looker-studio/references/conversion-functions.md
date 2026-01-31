# Conversion Functions - Looker Studio

## CAST
Converts a value to a specific data type.

**Syntax:**
```sql
CAST(expression AS datatype)
```

**Available data types:**
| Type | Description |
|------|-------------|
| `TEXT` / `STRING` | Text string |
| `NUMBER` / `FLOAT64` | Decimal number |
| `INT64` | Integer number |
| `DATE` | Date (YYYY-MM-DD) |
| `DATETIME` | Date and time |
| `BOOLEAN` | TRUE/FALSE |

---

## Common Conversions

### Number to Text
```sql
CAST(Revenue AS TEXT)
CAST(123 AS STRING)

-- With format
CONCAT('$', CAST(Revenue AS TEXT))
CONCAT(CAST(Quantity AS TEXT), ' units')
```

### Text to Number
```sql
CAST('123' AS NUMBER)
CAST(Price_Text AS FLOAT64)
CAST(Quantity_String AS INT64)
```

### Text to Date
```sql
CAST('2024-03-15' AS DATE)
CAST(Date_String AS DATETIME)
```

### Date to Text
```sql
CAST(Order_Date AS TEXT)
CAST(Created_At AS STRING)
```

### Number to Integer (truncates decimals)
```sql
CAST(5.7 AS INT64)  -- Result: 5
CAST(Revenue AS INT64)
```

### Decimal to Percentage (visual)
```sql
CONCAT(CAST(ROUND(Rate * 100, 2) AS TEXT), '%')
```

---

## SAFE_CAST
Similar to CAST but returns NULL if conversion fails (no error).

**Syntax:**
```sql
SAFE_CAST(expression AS datatype)
```

**Examples:**
```sql
-- Doesn't error if fails
SAFE_CAST('abc' AS INT64)  -- Returns NULL

-- Useful for mixed data
SAFE_CAST(Mixed_Field AS NUMBER)

-- Dates that may be invalid
SAFE_CAST(Date_Text AS DATE)
```

**Recommended use:** Always use SAFE_CAST when data may have inconsistent format.

---

## Conversions with Format

### Number with specific format
```sql
-- Two decimals
ROUND(Revenue, 2)

-- No decimals
ROUND(Revenue, 0)

-- As currency (text)
CONCAT('$', CAST(ROUND(Revenue, 2) AS TEXT))
```

### Date with specific format
```sql
-- Text to date with format
PARSE_DATE('%Y%m%d', Date_String)
PARSE_DATE('%m/%d/%Y', Date_Text)

-- Date to text with format
FORMAT_DATE('%m/%d/%Y', Order_Date)
FORMAT_DATETIME('%Y-%m-%d %H:%M', Timestamp)
```

---

## Data Types in Looker Studio

| Type | Description | Example |
|------|-------------|---------|
| **Number** | Numbers | 123, 45.67 |
| **Percent** | Percentages | 0.25 (displays 25%) |
| **Duration** | Duration in seconds | 3600 (displays 1:00:00) |
| **Currency** | Currency | 99.99 (displays $99.99) |
| **Text** | Text | "Hello world" |
| **Date** | Date | 2024-03-15 |
| **Date & Time** | Date and time | 2024-03-15 14:30:00 |
| **Boolean** | True/False | TRUE, FALSE |
| **Geo** | Geographic | Country, City, Lat/Long |
| **URL** | Web addresses | https://... |
| **Hyperlink** | Clickable links | Link with text |
| **Image** | Images | Image URL |

---

## Practical Examples

### Create boolean field
```sql
CASE WHEN Revenue > 0 THEN TRUE ELSE FALSE END
```

### Convert duration to readable text
```sql
CONCAT(
    CAST(FLOOR(Duration_Seconds / 3600) AS TEXT), 'h ',
    CAST(FLOOR(MOD(Duration_Seconds, 3600) / 60) AS TEXT), 'm'
)
```

### Format number as currency
```sql
CONCAT('$', FORMAT('%,.2f', Revenue))
-- Or simpler:
CONCAT('$', CAST(ROUND(Revenue, 2) AS TEXT))
```

### Extract number from text
```sql
SAFE_CAST(REGEXP_EXTRACT(Text, r'(\d+)') AS INT64)
```

### Normalize mixed data
```sql
IFNULL(SAFE_CAST(Value AS NUMBER), 0)
```

### Create dynamic URL
```sql
CONCAT('https://example.com/product/', CAST(Product_ID AS TEXT))
```

---

## Important Notes

1. **CAST can fail** if format is incompatible
2. **SAFE_CAST is safer** for external source data
3. **Arithmetic functions don't work with TEXT** - convert first
4. **BOOLEAN is created with CASE**, not with CAST directly from text
5. **Currency is visual format only**, doesn't convert currencies
6. **Percent expects decimal values** (0.25 = 25%)
