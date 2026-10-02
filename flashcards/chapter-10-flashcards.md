## Chapter 10: Troubleshooting, Monitoring, and Performance Optimization — Flashcards

| # | Front (Question) | Back (Answer) |
|---|-----------------|---------------|
| 1 | Spark UI tab that shows per-task duration and shuffle metrics for each stage? | Stages tab. Sort tasks by duration to spot skew — a few tasks taking far longer with much larger shuffle read = skewed key distribution. |
| 2 | Signature of data skew in the Stages tab? | Wide variance in task duration within one stage plus disproportionately large shuffle read on the slow tasks. Fix: salt the key or repartition on higher cardinality. |
| 3 | What does 'Spill (Memory)' / 'Spill (Disk)' in stage metrics mean? | Partition data exceeded available executor memory and spilled to disk, slowing the stage. Fix: increase executor memory, reduce partition size, or repartition to smaller partitions. |
| 4 | Why does the small file problem hurt Spark performance? | Each file adds task-scheduling and open/metadata overhead, creating more tasks than necessary and slowing listing operations. Exam tip: OPTIMIZE compacts small files into larger ones. |
| 5 | Default target file size for Delta OPTIMIZE compaction? | ~1 GB (1073741824 bytes), set via spark.databricks.delta.optimize.maxFileSize. |
| 6 | Delta table properties that auto-manage file size at write time? | delta.autoOptimize.optimizeWrite (coalesces writes into fewer, larger files) and delta.autoOptimize.autoCompact (compacts small files after writes). Both boolean, default false unless set. |
| 7 | CACHE TABLE / persist() main trade-off? | Speeds repeated reads of data that fits in memory, but consumes cluster memory (can evict other cached data) and can serve stale results after underlying writes — must UNCACHE/REFRESH. |
| 8 | Command to remove a cached table from memory? | UNCACHE TABLE table_name; or df.unpersist() in code. Cached data does not auto-invalidate on source table changes. |
| 9 | Which Spark UI tab shows the annotated physical plan with per-operator metrics (rows, data size, spill) for a SQL query? | SQL tab — click a query to see the DAG with metrics on each node, key for locating spill/skew inside a specific query. |
| 10 | OPTIMIZE vs ZORDER BY — what's the difference? | OPTIMIZE alone only compacts small files. ZORDER BY col additionally co-locates related values in the same files, improving data-skipping for filters on that column. Only Z-order high-cardinality columns used in WHERE clauses. |
| 11 | Where to review historical query duration, rows scanned, and query text in Databricks SQL? | Query History — lists every executed query with duration/rows and links to the detailed query profile for diagnosing slow dashboards. |
| 12 | Simplest way to alert on-call staff on Databricks Job failure? | Configure the job's built-in 'On failure' email notification (or webhook) in the Jobs UI — no custom code needed. |
