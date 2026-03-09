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
| `\|` | Alternation (Or) | Matches one expression or another, for example `aa\|ab`. |
| `( ... )` | Grouping/Subexpression | Treats grouped expression as a single unit. |
| `\1` to `\9` | Backreference | Matches previously captured groups 1 through 9. |
| `\` | Escape Character | Escapes the following metacharacter so it is treated literally. |
| `^` | Beginning Anchor | Matches when expression occurs at the start of the string. |
| `$` | End Anchor | Matches when expression occurs at the end of the string. |

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

---

*Part of Advanced SQL Reference Notes*
