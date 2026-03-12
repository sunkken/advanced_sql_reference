# Advanced SQL Reference - Style Guide

**Project:** Building minimal SQL reference notes in `c:\Users\sundq\wprojects\advanced_sql`

**Target Platforms:** Databricks + Fabric/Power BI

**Files to Edit:** Markdown files only (ignore `slides/*.pptx`)

## Structure Template

Every entry follows this pattern:

```markdown
# [Topic Title]

Brief description (1 sentence). Includes Databricks and Fabric platform-specific notes.

## [Section Header]

### [Function/Feature Name]

- Short descriptive bullets
- **Databricks:** Platform-specific note
- **Fabric:** Platform-specific note

#### General Usage:

```sql
[syntax pattern]
```

#### Example:

(or #### Examples: for grouped entries)

```sql
[practical example]
```
```

## Key Principles

1. **Concise bullets** - Keep descriptions brief and practical
2. **Portable patterns** - Prefer `COALESCE`, `CASE` over proprietary functions when possible
3. **Consistent platform notes** - Every entry gets **Databricks:** and **Fabric:** bullets
4. **Group related functions** - Combine related items (e.g., LPAD/RPAD together)
5. **Platform differences** - Mention only when relevant, keep brief
6. **Document structure** - Add intro description at top, footer at bottom:
   ```markdown
   ---
   
   *Part of Advanced SQL Reference Notes*
   ```

## Decisions Made

- NVL included with note to prefer COALESCE
- Dynamic SQL examples included for PIVOT/UNPIVOT
- Examples use realistic table/column names from context
- Each function/feature has standardized Databricks and Fabric bullets
- Shared syntax/reference sections may use compact tables without usage/example blocks

## Completed Files

- **01-cleansing-and-manipulation.md:** COALESCE, NVL, TRIM, LPAD/RPAD, GREATEST/LEAST, PIVOT/UNPIVOT (with dynamic examples)
- **02-regex.md:** Metacharacter Reference, Character Classes, REGEXP_LIKE, REGEXP_INSTR, REGEXP_SUBSTR, REGEXP_REPLACE
- **03-window-functions.md:** Intro section added with subquery vs window-function comparison (function sections pending)

## Workflow Notes

- Use `multi_replace_string_in_file` for multiple independent edits
- Group related functions under section headers
- Add both static and dynamic examples when relevant (e.g., PIVOT/UNPIVOT)
- Examples should be practical and use realistic table/column names