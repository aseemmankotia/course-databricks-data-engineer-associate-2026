## Chapter 5 Cheat Sheet — Spark SQL & PySpark Core Transformations
**Exam Domain:** Data Transformation and Modeling (22%)

### Join Types & Row-Count Effects
| Join | Returns | Row count risk |
|---|---|---|
| inner | matching rows both sides | inflates if keys duplicated |
| left | all left + matched right (nulls if no match) | inflates if right has dup keys |
| right | all right + matched left | inflates if left has dup keys |
| full | all rows both sides, nulls where unmatched | inflates on either side's dup keys |
| left_semi | left rows WITH a match, left columns only | never inflates (max = left row count) |
| left_anti | left rows WITHOUT a match, left columns only | shrinks or equal, never inflates |

**Gotcha:** Any join type except semi/anti can multiply row count when the join key isn't unique on the joined side — dedupe before joining if row count must stay bounded.

### Window Ranking Functions
| Function | Ties behavior | Example on [10,10,20] |
|---|---|---|
| row_number() | always unique, arbitrary tie order | 1,2,3 |
| rank() | same rank, skips next number(s) | 1,1,3 |
| dense_rank() | same rank, no gap | 1,1,2 |

```python
from pyspark.sql.window import Window
from pyspark.sql.functions import row_number, col

w = Window.partitionBy("customer_id").orderBy(col("updated_at").desc())
df.withColumn("rn", row_number().over(w)).filter("rn = 1")
```

### Null-Safe Operations
| Need | Use |
|---|---|
| Replace null with default | `coalesce(col("x"), lit(0))` |
| Check for null | `col("x").isNull()` / `.isNotNull()` |
| Join where nulls should match | `col("a") <=> col("b")` |
| Standard equality (drops null matches) | `col("a") == col("b")` |

**Trap:** `col("x") == None` never works — always returns null/false. Use `isNull()`.

### Higher-Order Array Functions
| Function | Returns | Use case |
|---|---|---|
| `transform(arr, x -> expr)` | new array, same length | map every element |
| `filter(arr, x -> cond)` | new array, ≤ length | keep matching elements |
| `exists(arr, x -> cond)` | boolean | does ANY element match? |

### GroupBy Aggregations
```python
df.groupBy("dept").agg(sum("salary").alias("total"), avg("salary").alias("avg"))
```
Exam note: `groupBy().max()` returns only the aggregated column — use window `row_number()` if you need the FULL row tied to the max value.

### Quick Decision Rules
- Need rows in A but not B → **left_anti**
- Need rows in A that DO exist in B, A's columns only → **left_semi**
- Need latest/first record per key → **window row_number() == 1**
- Need arbitrary de-dup by full row equality → **dropDuplicates()**
- Need dedup by specific key(s), any row → **dropDuplicates([cols])**
- Nulls should be treated as equal in a join → **`<=>`**
