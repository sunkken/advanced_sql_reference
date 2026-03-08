# Cleansing and Manipulation

Common SQL functions for data cleansing, string manipulation, NULL handling, and table reshaping. Includes Databricks and Fabric platform-specific notes.

## NULL Handling

### COALESCE

- Returns the first non-`NULL` value from the list of expressions.
- Use to handle `NULL` values and define a hierarchy of preferences.
- **Databricks:** Fully supported.
- **Fabric:** Fully supported.

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
- **Databricks:** Supported for Oracle compatibility.
- **Fabric:** Use `ISNULL(expression, replacement)` for T-SQL equivalent.

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
- **Databricks:** Commonly uses `trim(text_value)` (or `ltrim`/`rtrim`); full `LEADING`/`TRAILING` syntax supported.
- **Fabric:** Supports `TRIM`, `LTRIM`, `RTRIM`; full `LEADING`/`TRAILING` syntax available in modern T-SQL versions.

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
- **Databricks:** Fully supported with `lpad(text_value, length, pad_string)` and `rpad(text_value, length, pad_string)`.
- **Fabric:** Not native in T-SQL; use `REPLICATE` + `RIGHT`/`LEFT` or `FORMAT` for padding.

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

## GREATEST and LEAST

- `GREATEST` returns the largest value, and `LEAST` returns the smallest value from a list of expressions.
- Useful for simple floor/ceiling style checks in a query.
- **Databricks:** Fully supported.
- **Fabric:** Available in SQL Server 2022+; for older versions use `CASE` statements (e.g., `CASE WHEN a > b THEN a ELSE b END`).

#### General Usage:

```sql
GREATEST(value_1, value_2, ..., value_n)
LEAST(value_1, value_2, ..., value_n)
```

#### Examples:

```sql
SELECT emp.*, LEAST(sal, 3000)
FROM emp;

SELECT emp.*, GREATEST(sal, 1000)
FROM emp;
```

## Pivot and Unpivot

### PIVOT

- Turns row values into columns using an aggregate.
- Useful for quick cross-tab summaries.
- Column list in `IN (...)` must be statically specified; cannot use subqueries or variables.
- **Dynamic workarounds:** PySpark `.groupBy().pivot()` (Databricks), dynamic SQL with `STRING_AGG` (Fabric), or `CASE` + `GROUP BY` (portable).
- **Databricks:** Fully supported; `.groupBy().pivot()` determines pivot values automatically.
- **Fabric:** Fully supported; dynamic SQL requires building column list at runtime.

#### General Usage:

```sql
SELECT *
FROM (
  SELECT group_column, pivot_column, measure_column
  FROM source_table
)
PIVOT (
  aggregate_function(measure_column)
  FOR pivot_column IN (pivot_value_1, pivot_value_2, ...)
);
```

#### Example:

```sql
SELECT *
FROM (
  SELECT sub_region_id, region_id, population
  FROM eba_countries
)
PIVOT (
  SUM(population)
  FOR region_id IN (10, 20, 30, 40, 50)
);
```

#### Dynamic PIVOT:

**Databricks (PySpark):**

```python
# Use DataFrame API - pivot values determined automatically at runtime
df = spark.table("eba_countries")
result = df.groupBy("sub_region_id").pivot("region_id").sum("population")
```

**Fabric (T-SQL):**

```sql
-- Build column list dynamically from data
DECLARE @columns NVARCHAR(MAX);
SELECT @columns = STRING_AGG(CAST(region_id AS NVARCHAR(10)), ', ')
FROM (SELECT DISTINCT region_id FROM eba_countries) AS regions;

-- Execute dynamic PIVOT
DECLARE @sql NVARCHAR(MAX);
SET @sql = N'SELECT * FROM (
  SELECT sub_region_id, region_id, population FROM eba_countries
) PIVOT (SUM(population) FOR region_id IN (' + @columns + ')) AS pvt';
EXEC sp_executesql @sql;
```

### UNPIVOT

- Turns columns into rows, the reverse of `PIVOT`.
- Useful for normalizing wide tables into a long format.
- Column list in `IN (...)` must be statically specified.
- **Dynamic workarounds:** `.melt()` DataFrame API (Databricks), dynamic SQL with schema queries (Fabric), or application-layer generation.
- **Databricks:** Fully supported; `.melt()` handles column lists dynamically.
- **Fabric:** Fully supported; dynamic SQL queries schema to build column list.

#### General Usage:

```sql
SELECT *
FROM source_table
UNPIVOT (
  value_column FOR name_column IN (col_1, col_2, ...)
);
```

#### Example:

```sql
SELECT *
FROM avg_test_scores
UNPIVOT (
  avg_score FOR subject IN (MATHS AS 'Mathematics', SCIENCE AS 'Science', ENGLISH AS 'English')
);
```

#### Dynamic UNPIVOT:

**Databricks (PySpark):**

```python
# Unpivot all columns except specified ID columns
df = spark.table("avg_test_scores")
result = df.melt(ids=['student_id'], variableColumnName='subject', valueColumnName='avg_score')
```

**Fabric (T-SQL):**

```sql
-- Build column list dynamically from schema
DECLARE @columns NVARCHAR(MAX);
SELECT @columns = STRING_AGG(QUOTENAME(COLUMN_NAME), ', ')
FROM INFORMATION_SCHEMA.COLUMNS
WHERE TABLE_NAME = 'avg_test_scores' 
  AND COLUMN_NAME NOT IN ('student_id');

-- Execute dynamic UNPIVOT
DECLARE @sql NVARCHAR(MAX);
SET @sql = N'SELECT * FROM avg_test_scores
UNPIVOT (avg_score FOR subject IN (' + @columns + ')) AS upvt';
EXEC sp_executesql @sql;
```

---

*Part of Advanced SQL Reference Notes*