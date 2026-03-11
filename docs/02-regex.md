# Regex

Regular expression pattern matching functions for advanced string filtering and extraction in SQL. Includes Databricks and Fabric platform-specific notes.

## Metacharacter Reference

### Core Metacharacters

- Quick reference for the most common regex operators used in SQL pattern functions.

| Metacharacter | Operator Name | Description |
|---|---|---|
| `.` | Any Character (Dot) | Matches any single character. |
| `+` | One or More (Plus Quantifier) | Matches one or more occurrences of the preceding subexpression. |
| `?` | Zero or One (Question Mark Quantifier) | Matches zero or one occurrence of the preceding subexpression. |
| `*` | Zero or More (Star Quantifier) | Matches zero or more occurrences of the preceding subexpression. |
| `\d` | Digit Character Class | Matches any digit character (`0-9`). |
| `\w` | Word Character Class | Matches word characters (letters, digits, underscore). |
| `{m}` | Exact Count Interval | Matches exactly `m` occurrences of the preceding subexpression. |
| `{m,}` | At-Least Count Interval | Matches at least `m` occurrences of the preceding subexpression. |
| `{m,n}` | Between Count Interval | Matches at least `m` but not more than `n` occurrences. |
| `[ ... ]` | Matching Character List | Matches any character from the listed set or range. |
| `[^ ... ]` | Non-Matching Character List | Matches any character not in the listed set or range. |
| &#124; | Alternation (Or) | Matches one expression or another, for example `aa` or `ab`. |
| `( ... )` | Grouping/Subexpression | Treats grouped expression as a single unit. |
| `\1` to `\9` | Backreference | Matches previously captured groups 1 through 9. |
| `\` | Escape Character | Escapes the following metacharacter so it is treated literally. |
| `^` | Beginning Anchor | Matches when expression occurs at the start of the string. |
| `$` | End Anchor | Matches when expression occurs at the end of the string. |

### Character Classes

- Common POSIX-style character classes used inside bracket expressions.

| Class | Meaning | Example |
|---|---|---|
| `[[:alnum:]]` | Alphanumeric characters | `[[:alnum:]]+` |
| `[[:alpha:]]` | Alphabetic characters | `[[:alpha:]]{3}` |
| `[[:digit:]]` | Numeric digits | `[[:digit:]]{5}` |
| `[[:lower:]]` | Lowercase alphabetic characters | `[[:lower:]]+` |
| `[[:upper:]]` | Uppercase alphabetic characters | `[[:upper:]]+` |
| `[[:space:]]` | Whitespace characters | `[[:space:]]+` |
| `[[:punct:]]` | Punctuation characters | `[[:punct:]]` |

## Pattern Matching Functions

### REGEXP_LIKE

- Returns `TRUE` when a string matches a regular expression pattern.
- Useful for validating text formats in `WHERE`/`HAVING` filters, flagging matches in `SELECT`, and conditional logic in `CASE` expressions.
- **Databricks:** Supported as `regexp_like(string, pattern)`.
- **Fabric:** `REGEXP_LIKE` is not native in SQL Warehouse T-SQL; use `LIKE`/`PATINDEX` or Spark runtime alternatives when regex is required.

#### General Usage:

```sql
REGEXP_LIKE(string_expression, pattern)
```

#### Example:

```sql
SELECT *
FROM stores
WHERE REGEXP_LIKE(physical_address, '\w{2} \d{5}$');
```

### REGEXP_INSTR

- Returns the position of the first substring that matches a regular expression pattern.
- Useful for detecting whether a pattern exists and locating it in `WHERE`, `SELECT`, or `CASE` expressions.
- **Databricks:** Supported as `regexp_instr(string, pattern)`.
- **Fabric:** `REGEXP_INSTR` is not native in SQL Warehouse T-SQL; `PATINDEX` can cover simpler wildcard searches but is not a full regex equivalent.

#### General Usage:

```sql
REGEXP_INSTR(string_expression, pattern)
```

#### Example:

```sql
SELECT name, REGEXP_INSTR(name, '\(.+\)') AS parenthesis_pos
FROM eba_countries
WHERE REGEXP_INSTR(name, '\(.+\)') > 0;
```

### REGEXP_SUBSTR

- Returns the substring that matches a regular expression pattern.
- Useful for extracting tokens (for example first word, code parts, or bracketed values) in `SELECT` projections and transformation logic.
- **Databricks:** Supported as `regexp_substr(string, pattern)`.
- **Fabric:** `REGEXP_SUBSTR` is not native in SQL Warehouse T-SQL; use `PATINDEX` + `SUBSTRING` for simpler wildcard cases or Spark runtime alternatives for full regex extraction.

#### General Usage:

```sql
REGEXP_SUBSTR(string_expression, pattern)
```

#### Example:

```sql
SELECT name, REGEXP_SUBSTR(name, '^\w+')
FROM eba_countries;
```

### REGEXP_REPLACE

- Returns a string where substrings matching a regular expression are replaced with another value.
- Useful for standardizing text, removing unwanted characters, and reformatting values with capture groups.
- **Databricks:** Supported as `regexp_replace(string, pattern, replacement)`.
- **Fabric:** `REGEXP_REPLACE` is not native in SQL Warehouse T-SQL; use `REPLACE` for literal substitutions or Spark runtime alternatives for full regex replacement.

#### General Usage:

```sql
REGEXP_REPLACE(string_expression, pattern, replacement)
```

#### Examples:

```sql
SELECT
    email_address,
    REGEXP_REPLACE(email_address, '\.', '-') AS updated_email
FROM customers;

SELECT
    email_address,
    REGEXP_REPLACE(email_address, '(\w+)\.(\w+)(@.+)', '\2.\1\3') AS swapped_email
FROM customers;
```

---

*Part of Advanced SQL Reference Notes*
