# Aggregation Functions - Looker Studio

Aggregation functions calculate values from multiple rows. They can only be used in **Metric** type fields.

## SUM
Sums all values.

**Syntax:**
```sql
SUM(numeric_field)
```

**Examples:**
```sql
-- Total sales
SUM(Revenue)

-- Total units sold
SUM(Quantity)

-- Conditional sum (requires previous calculated field)
SUM(CASE WHEN Status = 'Completed' THEN Revenue ELSE 0 END)
```

---

## AVG
Average of values.

**Syntax:**
```sql
AVG(numeric_field)
```

**Examples:**
```sql
-- Average price
AVG(Price)

-- Average duration
AVG(Session_Duration)

-- Average rating
AVG(Rating)
```

---

## COUNT
Counts the number of (non-null) values.

**Syntax:**
```sql
COUNT(field)
```

**Examples:**
```sql
-- Total records
COUNT(Record_ID)

-- Number of orders
COUNT(Order_ID)

-- Count rows with email
COUNT(Email)
```

---

## COUNT_DISTINCT
Counts unique values.

**Syntax:**
```sql
COUNT_DISTINCT(field)
```

**Examples:**
```sql
-- Unique customers
COUNT_DISTINCT(Customer_ID)

-- Unique countries
COUNT_DISTINCT(Country)

-- Unique sessions
COUNT_DISTINCT(Session_ID)

-- Unique products sold
COUNT_DISTINCT(Product_ID)
```

---

## MAX
Maximum value.

**Syntax:**
```sql
MAX(field)
```

**Examples:**
```sql
-- Highest sale
MAX(Revenue)

-- Latest date
MAX(Order_Date)

-- Highest price
MAX(Price)

-- Last update
MAX(Updated_At)
```

---

## MIN
Minimum value.

**Syntax:**
```sql
MIN(field)
```

**Examples:**
```sql
-- Lowest sale
MIN(Revenue)

-- First date
MIN(Created_At)

-- Lowest price
MIN(Price)

-- First record
MIN(Order_Date)
```

---

## MEDIAN
Median value (50th percentile).

**Syntax:**
```sql
MEDIAN(numeric_field)
```

**Examples:**
```sql
-- Median price
MEDIAN(Price)

-- Median revenue
MEDIAN(Revenue)

-- Median duration
MEDIAN(Time_On_Site)
```

---

## PERCENTILE
Calculates a specific percentile.

**Syntax:**
```sql
PERCENTILE(numeric_field, percentile_value)
```

**percentile_value:** Number between 0 and 100

**Examples:**
```sql
-- 25th percentile (Q1)
PERCENTILE(Revenue, 25)

-- 75th percentile (Q3)
PERCENTILE(Revenue, 75)

-- 90th percentile
PERCENTILE(Response_Time, 90)

-- 95th percentile (for outliers)
PERCENTILE(Order_Value, 95)
```

---

## STDDEV
Standard deviation.

**Syntax:**
```sql
STDDEV(numeric_field)
```

**Examples:**
```sql
-- Price deviation
STDDEV(Price)

-- Time variability
STDDEV(Processing_Time)
```

---

## VARIANCE
Variance of values.

**Syntax:**
```sql
VARIANCE(numeric_field)
```

**Examples:**
```sql
-- Revenue variance
VARIANCE(Revenue)

-- Rating variance
VARIANCE(Rating)
```

---

## Common Calculated Metrics

### Conversion rate
```sql
COUNT_DISTINCT(CASE WHEN Converted = TRUE THEN User_ID END) / COUNT_DISTINCT(User_ID)
```

### Average per customer
```sql
SUM(Revenue) / COUNT_DISTINCT(Customer_ID)
```

### Percentage of total
```sql
SUM(Revenue) / SUM(Total_Revenue) * 100
```

### Growth (requires period comparison)
```sql
(SUM(Current_Revenue) - SUM(Previous_Revenue)) / SUM(Previous_Revenue) * 100
```

---

## Useful Combinations

### Value range
```sql
MAX(Price) - MIN(Price)
```

### Interquartile range (IQR)
```sql
PERCENTILE(Value, 75) - PERCENTILE(Value, 25)
```

### Coefficient of variation
```sql
STDDEV(Price) / AVG(Price) * 100
```

---

## Important Notes

1. **Aggregation functions only work on Metric type fields**
2. **Aggregations cannot be nested** (e.g., `AVG(SUM(x))` doesn't work)
3. **NULL is ignored** in most aggregation functions
4. **COUNT vs COUNT_DISTINCT:** COUNT counts all rows, COUNT_DISTINCT only unique values
5. **Performance:** COUNT_DISTINCT can be slower on large datasets
