# Logic and Conditional Functions - Looker Studio

## CASE WHEN
Multiple conditional logic. The most versatile and commonly used.

**Syntax:**
```sql
CASE
    WHEN condition1 THEN result1
    WHEN condition2 THEN result2
    ...
    ELSE default_result
END
```

**Examples:**

### Simple classification
```sql
CASE
    WHEN Revenue > 10000 THEN 'High'
    WHEN Revenue > 5000 THEN 'Medium'
    WHEN Revenue > 0 THEN 'Low'
    ELSE 'No sales'
END
```

### Based on exact value
```sql
CASE Country
    WHEN 'USA' THEN 'United States'
    WHEN 'UK' THEN 'United Kingdom'
    WHEN 'DE' THEN 'Germany'
    ELSE 'Other'
END
```

### With multiple conditions
```sql
CASE
    WHEN Country = 'USA' AND Revenue > 1000 THEN 'USA High'
    WHEN Country = 'USA' THEN 'USA Low'
    WHEN Revenue > 1000 THEN 'International High'
    ELSE 'International Low'
END
```

### Group values
```sql
CASE
    WHEN Source IN ('google', 'bing', 'yahoo') THEN 'Search'
    WHEN Source IN ('facebook', 'instagram', 'twitter') THEN 'Social'
    WHEN Source = 'email' THEN 'Email'
    WHEN Source = '(direct)' THEN 'Direct'
    ELSE 'Other'
END
```

### Date ranges
```sql
CASE
    WHEN Order_Date >= DATE_SUB(CURRENT_DATE(), INTERVAL 7 DAY) THEN 'Last week'
    WHEN Order_Date >= DATE_SUB(CURRENT_DATE(), INTERVAL 30 DAY) THEN 'Last month'
    WHEN Order_Date >= DATE_SUB(CURRENT_DATE(), INTERVAL 90 DAY) THEN 'Last quarter'
    ELSE 'Older'
END
```

---

## IF
Simple two-option conditional.

**Syntax:**
```sql
IF(condition, value_if_true, value_if_false)
```

**Examples:**
```sql
-- Active/Inactive
IF(Status = 'Active', 'Active', 'Inactive')

-- Yes/No
IF(Revenue > 0, 'Yes', 'No')

-- With calculation
IF(Quantity > 0, Revenue / Quantity, 0)

-- Boolean
IF(Is_Subscribed, 'Subscribed', 'Not subscribed')
```

---

## IFNULL
Default value if NULL.

**Syntax:**
```sql
IFNULL(expression, default_value)
```

**Examples:**
```sql
-- Default value for nulls
IFNULL(Campaign, 'Direct')

-- Default number
IFNULL(Discount, 0)

-- Default text
IFNULL(Notes, 'No notes')

-- In calculations
SUM(IFNULL(Revenue, 0))
```

---

## NULLIF
Returns NULL if two values are equal.

**Syntax:**
```sql
NULLIF(expression1, expression2)
```

**Main use:** Avoid division by zero.

**Examples:**
```sql
-- Avoid division by zero
Revenue / NULLIF(Sessions, 0)

-- Convert specific value to NULL
NULLIF(Status, 'Unknown')

-- Ignore specific values
AVG(NULLIF(Rating, 0))
```

---

## COALESCE
Returns the first non-null value from a list.

**Syntax:**
```sql
COALESCE(value1, value2, ..., valueN)
```

**Examples:**
```sql
-- First available source
COALESCE(Campaign, Medium, Source, 'Direct')

-- First available phone
COALESCE(Phone_Mobile, Phone_Home, Phone_Work, 'No phone')

-- Cascade of values
COALESCE(Preferred_Name, First_Name, Username, 'User')
```

---

## Comparison Operators

| Operator | Description | Example |
|----------|-------------|---------|
| `=` | Equal | `Status = 'Active'` |
| `!=` or `<>` | Not equal | `Country != 'USA'` |
| `>` | Greater than | `Revenue > 1000` |
| `>=` | Greater or equal | `Age >= 18` |
| `<` | Less than | `Price < 50` |
| `<=` | Less or equal | `Quantity <= 10` |

---

## Logical Operators

### AND
All conditions must be true.

```sql
CASE WHEN Country = 'USA' AND Revenue > 1000 THEN 'Target' END
```

### OR
At least one condition must be true.

```sql
CASE WHEN Source = 'google' OR Source = 'bing' THEN 'Search' END
```

### NOT
Negates a condition.

```sql
CASE WHEN NOT (Status = 'Cancelled') THEN 'Valid' END
```

### Combinations
```sql
CASE
    WHEN (Country = 'USA' OR Country = 'Canada') AND Revenue > 500
    THEN 'North America High Value'
    ELSE 'Other'
END
```

---

## Special Operators

### IN
Checks if value is in a list.

```sql
CASE WHEN Country IN ('USA', 'Canada', 'Mexico') THEN 'North America' END
```

### BETWEEN
Checks if within a range (inclusive).

```sql
CASE WHEN Age BETWEEN 18 AND 35 THEN 'Young Adult' END
```

### LIKE
Pattern matching with wildcards.

```sql
-- % = any number of characters
-- _ = single character

CASE WHEN Email LIKE '%@gmail.com' THEN 'Gmail' END
CASE WHEN Code LIKE 'PRO_%' THEN 'Product' END
```

### IS NULL / IS NOT NULL
Checks for null values.

```sql
CASE WHEN Email IS NOT NULL THEN 'Has email' ELSE 'No email' END
CASE WHEN Notes IS NULL THEN 'No notes' END
```

---

## Practical Examples

### Customer segmentation
```sql
CASE
    WHEN Total_Purchases >= 10 AND Total_Revenue >= 5000 THEN 'VIP'
    WHEN Total_Purchases >= 5 OR Total_Revenue >= 2000 THEN 'Regular'
    WHEN Total_Purchases >= 1 THEN 'New'
    ELSE 'Prospect'
END
```

### Order status
```sql
CASE
    WHEN Delivered_Date IS NOT NULL THEN 'Delivered'
    WHEN Shipped_Date IS NOT NULL THEN 'In Transit'
    WHEN Processed_Date IS NOT NULL THEN 'Processed'
    ELSE 'Pending'
END
```

### Product classification
```sql
CASE
    WHEN Category IN ('Electronics', 'Computers') THEN 'Technology'
    WHEN Category IN ('Clothing', 'Shoes', 'Accessories') THEN 'Fashion'
    WHEN Category IN ('Food', 'Beverages') THEN 'Food & Drink'
    ELSE 'Other'
END
```

### Safe conversion (avoid errors)
```sql
CASE
    WHEN Sessions > 0 THEN Revenue / Sessions
    ELSE 0
END
```

### Multiple metrics in one
```sql
CASE Metric_Selector
    WHEN 'Revenue' THEN SUM(Revenue)
    WHEN 'Orders' THEN COUNT(Order_ID)
    WHEN 'Avg Order' THEN AVG(Order_Value)
END
```
