# Date and Time Functions - Looker Studio

## DATETIME_ADD
Adds a time interval to a date.

**Syntax:**
```sql
DATETIME_ADD(datetime_expression, INTERVAL value UNIT)
```

**Available units:** YEAR, QUARTER, MONTH, WEEK, DAY, HOUR, MINUTE, SECOND

**Examples:**
```sql
-- Add 7 days
DATETIME_ADD(Order_Date, INTERVAL 7 DAY)

-- Add 1 month
DATETIME_ADD(Start_Date, INTERVAL 1 MONTH)

-- Add 2 hours
DATETIME_ADD(Event_Time, INTERVAL 2 HOUR)

-- Due date (30 days)
DATETIME_ADD(Created_At, INTERVAL 30 DAY)
```

---

## DATETIME_SUB
Subtracts a time interval from a date.

**Syntax:**
```sql
DATETIME_SUB(datetime_expression, INTERVAL value UNIT)
```

**Examples:**
```sql
-- Convert UTC to US Eastern (UTC-5)
DATETIME_SUB(created_at, INTERVAL 5 HOUR)

-- 30 days ago
DATETIME_SUB(CURRENT_DATE(), INTERVAL 30 DAY)

-- Previous month
DATETIME_SUB(Order_Date, INTERVAL 1 MONTH)

-- 1 year ago
DATETIME_SUB(CURRENT_DATETIME(), INTERVAL 1 YEAR)
```

---

## DATETIME_DIFF
Calculates the difference between two dates.

**Syntax:**
```sql
DATETIME_DIFF(datetime1, datetime2, UNIT)
```

**Examples:**
```sql
-- Days between two dates
DATETIME_DIFF(End_Date, Start_Date, DAY)

-- Hours elapsed
DATETIME_DIFF(CURRENT_DATETIME(), Created_At, HOUR)

-- Months since signup
DATETIME_DIFF(CURRENT_DATE(), Signup_Date, MONTH)

-- Duration in minutes
DATETIME_DIFF(End_Time, Start_Time, MINUTE)
```

---

## DATE_DIFF
Similar to DATETIME_DIFF but for dates only (no time).

**Syntax:**
```sql
DATE_DIFF(date1, date2)
```

**Example:**
```sql
-- Days between dates
DATE_DIFF(Due_Date, Created_Date)
```

---

## DATE_TRUNC / DATETIME_TRUNC
Truncates a date to a specific precision.

**Syntax:**
```sql
DATE_TRUNC(date, PRECISION)
DATETIME_TRUNC(datetime, PRECISION)
```

**Precisions:** YEAR, QUARTER, MONTH, WEEK, DAY, HOUR, MINUTE, SECOND

**Examples:**
```sql
-- First day of month
DATE_TRUNC(Order_Date, MONTH)

-- First day of year
DATE_TRUNC(Created_At, YEAR)

-- Start of week
DATE_TRUNC(Event_Date, WEEK)

-- Truncate to hour
DATETIME_TRUNC(Timestamp, HOUR)
```

---

## EXTRACT
Extracts a specific part from a date.

**Syntax:**
```sql
EXTRACT(PART FROM date)
```

**Available parts:** YEAR, QUARTER, MONTH, WEEK, DAY, DAYOFWEEK, DAYOFYEAR, HOUR, MINUTE, SECOND

**Examples:**
```sql
-- Extract year
EXTRACT(YEAR FROM Order_Date)

-- Extract month
EXTRACT(MONTH FROM Created_At)

-- Extract day of week (1=Sunday, 7=Saturday)
EXTRACT(DAYOFWEEK FROM Event_Date)

-- Extract hour
EXTRACT(HOUR FROM Timestamp)

-- Extract quarter
EXTRACT(QUARTER FROM Sale_Date)
```

---

## FORMAT_DATETIME / FORMAT_DATE
Formats a date as text.

**Syntax:**
```sql
FORMAT_DATETIME(format_string, datetime)
FORMAT_DATE(format_string, date)
```

**Format codes:**
| Code | Description | Example |
|------|-------------|---------|
| `%Y` | 4-digit year | 2024 |
| `%y` | 2-digit year | 24 |
| `%m` | Month (01-12) | 03 |
| `%d` | Day (01-31) | 15 |
| `%H` | Hour 24h (00-23) | 14 |
| `%I` | Hour 12h (01-12) | 02 |
| `%M` | Minutes (00-59) | 30 |
| `%S` | Seconds (00-59) | 45 |
| `%p` | AM/PM | PM |
| `%A` | Full day name | Monday |
| `%a` | Abbreviated day | Mon |
| `%B` | Full month name | March |
| `%b` | Abbreviated month | Mar |

**Examples:**
```sql
-- Format MM/DD/YYYY
FORMAT_DATETIME('%m/%d/%Y', Order_Date)

-- Full format with time
FORMAT_DATETIME('%m/%d/%Y %H:%M:%S', Timestamp)

-- Time only
FORMAT_DATETIME('%H:%M', Event_Time)

-- Month and year
FORMAT_DATETIME('%B %Y', Created_At)

-- ISO format
FORMAT_DATETIME('%Y-%m-%d', Date_Field)
```

---

## PARSE_DATETIME / PARSE_DATE
Converts text to date.

**Syntax:**
```sql
PARSE_DATETIME(format_string, text)
PARSE_DATE(format_string, text)
```

**Examples:**
```sql
-- Text format YYYYMMDD
PARSE_DATE('%Y%m%d', Date_String)

-- Text format MM/DD/YYYY
PARSE_DATE('%m/%d/%Y', Date_Text)

-- Text with time
PARSE_DATETIME('%Y-%m-%d %H:%M:%S', Datetime_String)
```

---

## CURRENT_DATE / CURRENT_DATETIME
Returns the current date/time.

**Examples:**
```sql
-- Current date
CURRENT_DATE()

-- Current date and time
CURRENT_DATETIME()

-- Days since creation
DATE_DIFF(CURRENT_DATE(), Created_Date)

-- Is today?
CASE WHEN DATE_TRUNC(Event_Date, DAY) = CURRENT_DATE() THEN 'Today' ELSE 'Other' END
```

---

## Direct Extraction Functions

```sql
-- Extract components directly
YEAR(date)      -- Year
MONTH(date)     -- Month (1-12)
DAY(date)       -- Day (1-31)
HOUR(datetime)  -- Hour (0-23)
MINUTE(datetime) -- Minutes (0-59)
SECOND(datetime) -- Seconds (0-59)
WEEK(date)      -- Week of year
QUARTER(date)   -- Quarter (1-4)
```

---

## Practical Examples

### Convert UTC to local time (US Eastern)
```sql
DATETIME_SUB(created_at, INTERVAL 5 HOUR)
```

### Group by month/year
```sql
FORMAT_DATETIME('%Y-%m', Order_Date)
```

### Calculate age in days
```sql
DATETIME_DIFF(CURRENT_DATE(), Birth_Date, DAY)
```

### First and last day of month
```sql
-- First day
DATE_TRUNC(Order_Date, MONTH)

-- Last day
DATETIME_SUB(DATETIME_ADD(DATE_TRUNC(Order_Date, MONTH), INTERVAL 1 MONTH), INTERVAL 1 DAY)
```

### Classify by day of week
```sql
CASE EXTRACT(DAYOFWEEK FROM Order_Date)
    WHEN 1 THEN 'Sunday'
    WHEN 2 THEN 'Monday'
    WHEN 3 THEN 'Tuesday'
    WHEN 4 THEN 'Wednesday'
    WHEN 5 THEN 'Thursday'
    WHEN 6 THEN 'Friday'
    WHEN 7 THEN 'Saturday'
END
```

### Quarter of year
```sql
CONCAT('Q', CAST(EXTRACT(QUARTER FROM Order_Date) AS TEXT), ' ', CAST(YEAR(Order_Date) AS TEXT))
```
