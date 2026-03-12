# Window Functions

Analytic functions for ranking, running totals, offsets, and partition-based calculations in SQL. Includes Databricks and Fabric platform-specific notes.

## Why Use Window Functions

- Window functions let you calculate aggregates, rankings, and offsets without collapsing row-level detail.
- They are often simpler than grouped subqueries or self-joins when you need both detail rows and summary values in the same result.
- **Databricks:** Fully supported with standard `OVER (...)` syntax.
- **Fabric:** Fully supported in SQL analytics endpoints and Warehouse with standard window function syntax.

#### Example: Using Subquery

```sql
SELECT
	a.name,
	a.region_id,
	a.population,
	b.region_pop,
	ROUND(a.population / b.region_pop * 100, 2) AS pct_of_region_total
FROM eba_countries a
LEFT JOIN (
	SELECT
		region_id,
		SUM(population) AS region_pop
	FROM eba_countries
	GROUP BY region_id
) b
	ON a.region_id = b.region_id;
```

#### Example: Using Window Function

```sql
SELECT
	name,
	population,
	region_id,
	ROUND(population / SUM(population) OVER (PARTITION BY region_id) * 100, 2) AS pct_of_region_total
FROM eba_countries;
```

---

*Part of Advanced SQL Reference Notes*
