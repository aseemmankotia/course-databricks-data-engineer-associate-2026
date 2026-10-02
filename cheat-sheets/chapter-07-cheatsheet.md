## Chapter 7 Cheat Sheet: Views, Tables, Materialized Views

### Managed vs External Tables
| Aspect | Managed Table | External Table |
|---|---|---|
| Created via | `CREATE TABLE t ...` (no LOCATION) | `CREATE TABLE t ... LOCATION 'path'` |
| Data location | Catalog-managed storage | User-specified external path |
| `DROP TABLE` effect | Deletes metadata **and** data files | Deletes metadata **only** |
| Common exam trap | Assuming external table DROP deletes data | Assuming managed table data survives DROP |

### View vs Materialized View vs Table
| Aspect | View | Materialized View | Table |
|---|---|---|---|
| Stores data? | No | Yes (precomputed) | Yes |
| Freshness | Always current (recomputed each read) | Periodic/incremental refresh | Static until rewritten/appended |
| Query cost | Recomputed every time — costly at scale | Cheap reads, refresh cost separate | Cheap reads |
| Best for | Low query volume, must be exact | High query volume, tolerable staleness | Standard storage layer |
| Refresh mechanism | N/A | Lakeflow Declarative Pipelines / DBSQL refresh | Manual/scheduled ETL |

### CTAS (CREATE TABLE AS SELECT)
```sql
CREATE TABLE gold.daily_sales
USING DELTA
PARTITIONED BY (order_date)
AS SELECT order_date, SUM(amount) AS total
FROM silver.sales
GROUP BY order_date;
```
- Schema is **inferred** from the SELECT — cannot declare types or constraints inline.
- Constraints must be added **after** creation:
```sql
ALTER TABLE gold.daily_sales ALTER COLUMN total SET NOT NULL;
ALTER TABLE gold.daily_sales ADD CONSTRAINT positive_total CHECK (total >= 0);
```

### Table Constraints
| Constraint | Enforced? | Notes |
|---|---|---|
| NOT NULL | Yes | Blocks NULL writes |
| CHECK | Yes | Blocks writes violating boolean expression |
| PRIMARY KEY | No (informational) | Documents intent only |
| FOREIGN KEY | No (informational) | Documents intent only |

### Gold-Layer Pattern
- Silver → Gold aggregations typically built as **materialized views** or CTAS-refreshed tables.
- Choose materialized view when: high read frequency + tolerable staleness + expensive aggregation.
- Choose plain view when: low read frequency + must reflect latest data exactly.
- Choose CTAS table when: need a physical, versionable snapshot with full control over refresh schedule.

### Quick Decision Rule
- **Need always-fresh, low-traffic?** → View
- **Need fast reads, some staleness OK?** → Materialized View
- **Need a durable, independently-managed snapshot?** → CTAS Table
- **Data owned/managed elsewhere, catalog just registers it?** → External Table
