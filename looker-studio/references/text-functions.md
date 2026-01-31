# Text Functions - Looker Studio

## CONCAT
Combines multiple text strings.

**Syntax:**
```sql
CONCAT(string1, string2, ...)
```

**Examples:**
```sql
-- Full name
CONCAT(First_Name, ' ', Last_Name)

-- URL with parameter
CONCAT('https://example.com/user/', User_ID)

-- Label with value
CONCAT('Total: $', CAST(Revenue AS TEXT))

-- Multiple fields
CONCAT(City, ', ', State, ' - ', Country)
```

---

## UPPER / LOWER
Converts to uppercase or lowercase.

**Syntax:**
```sql
UPPER(string)
LOWER(string)
```

**Examples:**
```sql
-- All uppercase
UPPER(Country)  -- 'usa' → 'USA'

-- All lowercase
LOWER(Email)    -- 'User@Email.COM' → 'user@email.com'

-- Normalize for comparisons
CASE WHEN LOWER(Status) = 'active' THEN 'Active' ELSE 'Inactive' END
```

---

## TRIM / LTRIM / RTRIM
Removes whitespace.

**Syntax:**
```sql
TRIM(string)   -- Both sides
LTRIM(string)  -- Left only
RTRIM(string)  -- Right only
```

**Examples:**
```sql
-- Clean spaces
TRIM(Customer_Name)

-- Leading spaces only
LTRIM(Code)

-- Trailing spaces only
RTRIM(Description)
```

---

## SUBSTR / SUBSTRING
Extracts part of a string.

**Syntax:**
```sql
SUBSTR(string, start_position, length)
```

**Note:** Positions start at 1 (not 0).

**Examples:**
```sql
-- First 3 characters
SUBSTR(Product_Code, 1, 3)

-- Characters 5 to 10
SUBSTR(Description, 5, 6)

-- Country code (first 2)
SUBSTR(Phone, 1, 2)

-- From position 3 to end
SUBSTR(Text, 3)
```

---

## LEFT / RIGHT
Extracts characters from start or end.

**Syntax:**
```sql
LEFT(string, number_of_chars)
RIGHT(string, number_of_chars)
```

**Examples:**
```sql
-- First 5 characters
LEFT(Product_ID, 5)

-- Last 4 characters
RIGHT(Order_Number, 4)

-- Area code
LEFT(Phone, 3)

-- File extension
RIGHT(Filename, 3)
```

---

## LENGTH
Counts the number of characters.

**Syntax:**
```sql
LENGTH(string)
```

**Examples:**
```sql
-- Name length
LENGTH(Customer_Name)

-- Validate minimum length
CASE WHEN LENGTH(Password) < 8 THEN 'Weak' ELSE 'OK' END

-- Filter empty fields
CASE WHEN LENGTH(TRIM(Notes)) > 0 THEN 'Has notes' ELSE 'No notes' END
```

---

## REPLACE
Replaces text within a string.

**Syntax:**
```sql
REPLACE(string, old_text, new_text)
```

**Examples:**
```sql
-- Replace spaces with dashes
REPLACE(Name, ' ', '-')

-- Clean characters
REPLACE(Phone, '-', '')

-- Fix text
REPLACE(Description, 'old', 'new')

-- Multiple nested replacements
REPLACE(REPLACE(Text, 'a', ''), 'e', '')
```

---

## CONTAINS_TEXT
Checks if a string contains specific text.

**Syntax:**
```sql
CONTAINS_TEXT(string, search_text)
```

**Returns:** TRUE or FALSE

**Examples:**
```sql
-- Check if contains "@gmail"
CASE WHEN CONTAINS_TEXT(Email, '@gmail') THEN 'Gmail' ELSE 'Other' END

-- Search keyword
CASE WHEN CONTAINS_TEXT(LOWER(Description), 'urgent') THEN 'Priority' ELSE 'Normal' END
```

---

## STARTS_WITH / ENDS_WITH
Checks string start or end.

**Syntax:**
```sql
STARTS_WITH(string, prefix)
ENDS_WITH(string, suffix)
```

**Examples:**
```sql
-- Starts with "PRO-"
CASE WHEN STARTS_WITH(Product_Code, 'PRO-') THEN 'Product' ELSE 'Other' END

-- Ends with ".com"
CASE WHEN ENDS_WITH(LOWER(Email), '.com') THEN 'COM' ELSE 'Other domain' END
```

---

## REGEXP Functions (Regular Expressions)

### REGEXP_MATCH
Checks if matches a pattern.

**Syntax:**
```sql
REGEXP_MATCH(string, pattern)
```

**Examples:**
```sql
-- Valid email
REGEXP_MATCH(Email, r'^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$')

-- Numbers only
REGEXP_MATCH(Phone, r'^[0-9]+$')

-- Date format YYYY-MM-DD
REGEXP_MATCH(Date_Text, r'^\d{4}-\d{2}-\d{2}$')
```

---

### REGEXP_EXTRACT
Extracts text matching a pattern.

**Syntax:**
```sql
REGEXP_EXTRACT(string, pattern)
```

**Examples:**
```sql
-- Extract domain from email
REGEXP_EXTRACT(Email, r'@(.+)$')

-- Extract numbers from text
REGEXP_EXTRACT(Mixed_Text, r'(\d+)')

-- Extract country code
REGEXP_EXTRACT(Phone, r'^\+(\d{1,3})')

-- Extract first value before pipe
REGEXP_EXTRACT(Pipe_Text, r'^([^|]+)')
```

---

### REGEXP_REPLACE
Replaces text using a pattern.

**Syntax:**
```sql
REGEXP_REPLACE(string, pattern, replacement)
```

**Examples:**
```sql
-- Remove everything except numbers
REGEXP_REPLACE(Phone, r'[^0-9]', '')

-- Remove multiple spaces
REGEXP_REPLACE(Text, r'\s+', ' ')

-- Mask part of email
REGEXP_REPLACE(Email, r'^(.{3}).*(@.*)$', r'\1***\2')

-- Clean special characters
REGEXP_REPLACE(Name, r'[^a-zA-Z\s]', '')
```

---

### REGEXP_CONTAINS
Checks if contains a pattern.

**Syntax:**
```sql
REGEXP_CONTAINS(string, pattern)
```

**Examples:**
```sql
-- Contains numbers
REGEXP_CONTAINS(Text, r'\d')

-- Contains email
REGEXP_CONTAINS(Text, r'[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+')

-- Contains URL
REGEXP_CONTAINS(Text, r'https?://')
```

---

## Useful REGEX Patterns

| Pattern | Description |
|---------|-------------|
| `r'\d'` | Any digit |
| `r'\d+'` | One or more digits |
| `r'\w'` | Letter, digit, or underscore |
| `r'\s'` | Whitespace |
| `r'^'` | Start of string |
| `r'$'` | End of string |
| `r'.'` | Any character |
| `r'[abc]'` | a, b, or c |
| `r'[^abc]'` | Anything except a, b, c |
| `r'a*'` | Zero or more "a" |
| `r'a+'` | One or more "a" |
| `r'a?'` | Zero or one "a" |
| `r'(group)'` | Capture group |

---

## Practical Examples

### Extract username from email
```sql
REGEXP_EXTRACT(Email, r'^([^@]+)')
```

### Format phone number
```sql
CONCAT(
  '(',
  SUBSTR(REGEXP_REPLACE(Phone, r'[^0-9]', ''), 1, 3),
  ') ',
  SUBSTR(REGEXP_REPLACE(Phone, r'[^0-9]', ''), 4, 3),
  '-',
  SUBSTR(REGEXP_REPLACE(Phone, r'[^0-9]', ''), 7, 4)
)
```

### Capitalize first letter
```sql
CONCAT(
  UPPER(LEFT(Name, 1)),
  LOWER(SUBSTR(Name, 2))
)
```

### Clean and normalize text
```sql
TRIM(LOWER(REGEXP_REPLACE(Text, r'\s+', ' ')))
```

### Extract file extension
```sql
REGEXP_EXTRACT(Filename, r'\.([^.]+)$')
```
