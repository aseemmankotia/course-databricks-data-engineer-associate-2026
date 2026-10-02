## Ch.10 Cheat Sheet — Troubleshooting, Monitoring, Optimization

### Spark UI Tabs
| Tab | Shows | Use For |
|---|---|---|
| Jobs | Job list, stage counts | High-level job status |
| Stages | Per-task duration, shuffle read/write, spill | **Skew & spill diagnosis** |
| SQL | Physical plan w/ per-operator metrics | Locating skew/spill inside a query |
| Executors | Memory, GC time, cores per executor | Resource pressure |
| Storage | Cached RDD/DataFrame sizes | Verify cache usage |

### Skew vs Spill Signals (Stages tab)
| Symptom | Meaning | Fix |
|---|---|---|
| 1-2 tasks run far longer, large shuffle read | Data skew | Salt key, repartition on higher-cardinality col |
| "Spill (Memory)/(Disk)" nonzero | Partition too big for memory | Increase executor memory, repartition smaller |
| Even task duration, all fast | Healthy stage | — |

### Small File Problem
- Too many tiny files → high task-scheduling & metadata overhead.
- Root causes: frequent small streaming appends, over-partitioning.
- **Fix:** `OPTIMIZE table_name` compacts files (~1GB target).
- Auto-manage: `delta.autoOptimize.optimizeWrite=true`, `delta.autoOptimize.autoCompact=true`.

### OPTIMIZE / ZORDER
```sql
OPTIMIZE sales_delta;
OPTIMIZE sales_delta ZORDER BY (customer_id);
```
- OPTIMIZE = file compaction only.
- ZORDER BY = compaction + data co-location for faster filtering (use high-cardinality filter/join columns).
- Config: `spark.databricks.delta.optimize.maxFileSize` (default ~1GB).

### CACHE / persist() Trade-offs
| Pro | Con |
|---|---|
| Fast repeated reads of hot data | Consumes cluster memory |
| Reduces storage I/O | Can evict other cached data |
| Good for small/medium reused datasets | Stale after underlying writes — must UNCACHE/REFRESH |

```sql
CACHE TABLE t;
UNCACHE TABLE t;
```
```python
df.persist(); df.unpersist()
```

### Monitoring & Alerts
| Tool | Purpose |
|---|---|
| Query History (Databricks SQL) | Past query duration, rows scanned, query text |
| Job Notifications (Jobs UI) | Email/webhook on start/success/**failure** |
| Cluster Event Log | Cluster lifecycle events, not query perf |

### Exam Traps
- Skew ≠ small files — different root causes, different fixes.
- Caching does **not** auto-refresh on table updates.
- ZORDER without OPTIMIZE does nothing — Z-order runs as part of OPTIMIZE.
- Job failure alerts are built-in — no custom code needed.
