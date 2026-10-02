## Chapter 2: Delta Lake, Medallion Architecture, and Unity Catalog Basics — Flashcards

| # | Front (Question) | Back (Answer) |
|---|-----------------|---------------|
| 1 | How often does Delta Lake write a checkpoint file to _delta_log? | Every 10 commits, Delta writes a Parquet checkpoint summarizing table state. Exam tip: individual commits are JSON files; checkpoints speed up log replay for readers. |
| 2 | What concurrency control mechanism gives Delta Lake its ACID guarantees? | Optimistic concurrency control — writers proceed assuming no conflict, then validate against the log before committing; conflicting writers retry. Exam tip: this is why concurrent Delta writes remain atomic and isolated. |
| 3 | Syntax to query a Delta table as it existed 3 commits ago? | SELECT * FROM table_name VERSION AS OF 3; Exam tip: use TIMESTAMP AS OF 'yyyy-MM-dd HH:mm:ss' for time-based queries instead of version numbers. |
| 4 | Command to permanently roll a Delta table back to a prior version (not just query it)? | RESTORE TABLE table_name TO VERSION AS OF n; Exam tip: RESTORE writes a new commit and changes the live table; VERSION/TIMESTAMP AS OF in SELECT only reads history without altering the table. |
| 5 | What does OPTIMIZE do to a Delta table? | Bin-packs many small files into fewer, larger files to improve read throughput. Exam tip: run OPTIMIZE regularly after heavy small-batch or streaming writes. |
| 6 | What does ZORDER BY do when combined with OPTIMIZE? | Co-locates related data within files by the specified column(s), improving data-skipping on filters. Exam tip: pick high-cardinality columns used in WHERE clauses, e.g. OPTIMIZE t ZORDER BY (col). |
| 7 | Default file retention period enforced by VACUUM? | 7 days (168 hours). Exam tip: this default protects concurrent readers and time-travel queries from having their files removed mid-query. |
| 8 | Spark config required to VACUUM with a retention period below the 7-day safety threshold? | SET spark.databricks.delta.retentionDurationCheck.enabled = false; Exam tip: lowering retention below default risks breaking time travel and long-running readers. |
| 9 | Purpose of the Bronze layer in medallion architecture? | Stores raw, unprocessed data exactly as ingested from source, including possible schema drift or bad records. Exam tip: no cleaning or dedup happens at Bronze. |
| 10 | Purpose of the Silver layer in medallion architecture? | Cleaned, validated, conformed data — deduped, filtered, and joined at the row level. Exam tip: scenarios mentioning 'quality checks' or 'deduplication' map to Silver. |
| 11 | Purpose of the Gold layer in medallion architecture? | Business-level aggregates, dashboard-ready or feature-store tables, often joined across domains. Exam tip: 'BI reporting' and 'aggregated KPI' scenarios point to Gold. |
| 12 | Format of a fully qualified table name in Unity Catalog? | catalog.schema.table (three-level namespace), e.g. main.sales.orders. Exam tip: Unity Catalog adds a catalog level above the traditional two-level schema.table used by the legacy Hive metastore. |
