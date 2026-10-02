## Chapter 2 Cheat Sheet: Delta Lake, Medallion Architecture, Unity Catalog

### Delta Transaction Log (_delta_log)
| Component | Detail |
|---|---|
| Commit files | Ordered JSON files, one per transaction |
| Checkpoint | Parquet file written every 10 commits, summarizes state |
| Concurrency | Optimistic concurrency control — conflicting writers retry |
| Guarantees | Full ACID: atomicity, consistency, isolation, durability |

### Time Travel
| Command | Effect |
|---|---|
| `SELECT * FROM t VERSION AS OF 5` | Reads historical snapshot, does not alter table |
| `SELECT * FROM t TIMESTAMP AS OF '2026-01-10'` | Reads snapshot as of timestamp |
| `RESTORE TABLE t TO VERSION AS OF 5` | Writes new commit reverting table to that version — permanent |
| `DESCRIBE HISTORY t` | Shows commit log: version, timestamp, operation |

### OPTIMIZE and Z-ORDER
| Command | Purpose |
|---|---|
| `OPTIMIZE t` | Bin-packs small files into larger ones |
| `OPTIMIZE t ZORDER BY (col1, col2)` | Co-locates data by column for better data skipping on filters |
| When to run | After heavy small-batch/streaming writes, before frequent filtered reads |

### VACUUM
| Setting | Value |
|---|---|
| Default retention | 7 days (168 hours) |
| Below-default override | `SET spark.databricks.delta.retentionDurationCheck.enabled = false` |
| Risk of low retention | Breaks time travel + concurrent long-running reads |
| `delta.logRetentionDuration` | Controls transaction log history (separate from VACUUM file retention) |
| `delta.deletedFileRetentionDuration` | Table property controlling how long deleted files are kept before VACUUM can remove them |

### Medallion Architecture
| Layer | Content | Typical Ops |
|---|---|---|
| Bronze | Raw, as-ingested data, schema drift allowed | Append-only ingestion |
| Silver | Cleaned, deduped, validated, conformed | Joins, filters, quality checks |
| Gold | Aggregated, business-level, BI-ready | Aggregations, cross-domain joins |

### Unity Catalog Namespace
| Level | Example |
|---|---|
| Catalog | `main` |
| Schema | `sales` |
| Table | `orders` |
| Full reference | `main.sales.orders` |

### Common Exam Traps
- Confusing `SELECT ... VERSION AS OF` (read-only) with `RESTORE` (permanent rollback)
- Assuming VACUUM default retention is 30 days (that's log retention, not file retention)
- Forgetting ZORDER requires OPTIMIZE to take effect
- Mixing up two-level Hive Metastore names with three-level Unity Catalog names
