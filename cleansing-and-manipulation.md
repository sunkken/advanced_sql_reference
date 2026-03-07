# Cleansing and Manipulation

## NULL Handling

### COALESCE

- Returns the first non-`NULL` value from the list of expressions.
- Use to handle `NULL` values and define a hierarchy of preferences.

#### General Usage:

```sql
COALESCE(value_1, value_2, ..., fallback_value)
```

#### Example:

```sql
SELECT store_name, web_address, COALESCE(web_address, store_name)
FROM stores;
```

### NVL

- Replaces `NULL` with a fallback value (commonly used in Oracle SQL).
- In general, use `COALESCE` instead for better portability.
- `expression` and `replacement` should be the same data type or implicitly convertible to a compatible type.

#### General Usage:

```sql
NVL(expression, replacement)
```

#### Example:

```sql
SELECT emp.*, NVL(comm, 0)
FROM emp;
```

## String Padding and Trimming

### TRIM

- Removes specified characters from the start, end, or both sides of a string.
- Commonly used to clean up extra whitespace.
- Databricks commonly uses `trim(text_value)` (or `ltrim`/`rtrim`) instead of the full `LEADING ... FROM` form.

#### General Usage:

```sql
TRIM([LEADING | TRAILING | BOTH] trim_character FROM text_value)
```

#### Example:

```sql
SELECT TRIM(LEADING ' ' FROM '  whitespace  ')
FROM dual;
```

### LPAD and RPAD

- `LPAD` adds characters to the left and `RPAD` adds characters to the right until the target length is reached.
- If the text is longer than the target length, result is truncated.
- Databricks uses the same core patterns: `lpad(text_value, length, pad_string)` and `rpad(text_value, length, pad_string)`.

#### General Usage:

```sql
LPAD(text_value, length, pad_string)
RPAD(text_value, length, pad_string)
```

#### Examples:

```sql
SELECT LPAD(empno, 5, 0)
FROM emp;

SELECT RPAD(sal, length(sal) + 3, 0)
FROM emp;
```
