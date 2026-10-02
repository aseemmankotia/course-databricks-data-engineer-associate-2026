## Chapter 2: Delta Lake, Medallion Architecture, and Unity Catalog Basics — Practice Questions

**Q1.** An engineer deletes rows from a Silver table by mistake. The data must be permanently reverted to its state from two days ago, not just viewed. Which command accomplishes this?

A) RESTORE TABLE sales TO TIMESTAMP AS OF '2026-01-10'
B) SELECT * FROM sales TIMESTAMP AS OF '2026-01-10'
C) VACUUM sales RETAIN 48 HOURS
D) OPTIMIZE sales ZORDER BY (order_date)

<details>
<summary>Answer</summary>

**Correct: A**

RESTORE writes a new commit that reverts the current table state to the specified version/timestamp — it actually changes the live table, not just a query result.

**Why the others are wrong:**
- TIMESTAMP AS OF in a SELECT only reads historical data for that query; it does not modify the current table state.
- VACUUM deletes stale files based on retention and has nothing to do with restoring deleted rows.
- OPTIMIZE compacts files for performance; it does not recover deleted data.

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q2.** A streaming pipeline has produced millions of tiny files in a Delta table. Queries frequently filter on customer_id and event_date. Which single action BEST improves both file layout and query filter performance?

A) VACUUM RETAIN 0 HOURS
B) DESCRIBE HISTORY table
C) OPTIMIZE table ZORDER BY (customer_id, event_date)
D) ALTER TABLE table SET TBLPROPERTIES (delta.autoOptimize.optimizeWrite = true) only

<details>
<summary>Answer</summary>

**Correct: C**

OPTIMIZE compacts small files (bin-packing) and ZORDER co-locates data by the specified columns, enabling data skipping on those exact filter columns.

**Why the others are wrong:**
- VACUUM RETAIN 0 HOURS is dangerous — it removes retention safety and doesn't address file layout for query performance.
- DESCRIBE HISTORY only shows the transaction log; it performs no optimization.
- autoOptimize.optimizeWrite reduces future small-file creation on write but does not compact existing files or provide Z-order clustering.

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q3.** Before running VACUUM on a Bronze table, an engineer needs to preserve the ability to time travel 14 days back for an audit. What should they configure first?

A) Nothing — the default retention already covers 30 days
B) Set delta.logRetentionDuration to 7 days
C) Set delta.deletedFileRetentionDuration to 'interval 14 days' on the table before running VACUUM
D) Run VACUUM RETAIN 0 HOURS, then time travel will still work because the log is separate

<details>
<summary>Answer</summary>

**Correct: C**

deletedFileRetentionDuration controls how long VACUUM keeps files eligible for time travel; setting it to 14 days ensures files needed for that window aren't deleted.

**Why the others are wrong:**
- The default file retention respected by VACUUM is 7 days, not 30 — relying on defaults would break 14-day time travel.
- logRetentionDuration controls transaction log history, not physical data file cleanup performed by VACUUM.
- RETAIN 0 HOURS removes the safety window entirely and would delete files needed for time travel, breaking the audit requirement.

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q4.** Which medallion architecture layer should contain raw, unprocessed data exactly as received from the source system, including potential schema drift or malformed records?

A) Silver
B) Gold
C) Bronze
D) Staging

<details>
<summary>Answer</summary>

**Correct: C**

Bronze is the raw ingestion layer — data lands as-is from source with no cleaning or validation applied.

**Why the others are wrong:**
- Silver contains cleaned, deduplicated, validated data — not raw source data.
- Gold contains business-level aggregates for consumption, far downstream of raw ingestion.
- Staging is not a standard medallion layer name in Databricks terminology.

</details>

---

**Q5.** A business team needs pre-aggregated, dashboard-ready tables joined across multiple domains for executive reporting. Which medallion layer should serve this need?

A) Bronze
B) Silver
C) Landing
D) Gold

<details>
<summary>Answer</summary>

**Correct: D**

Gold tables are business-level, aggregated, and optimized for BI/reporting consumption, often joining data across domains.

**Why the others are wrong:**
- Bronze is raw, unaggregated data — unsuitable for direct BI consumption.
- Silver is cleaned and conformed at the row level but not yet aggregated for business reporting.
- Landing is not a standard medallion layer; it typically refers to the raw drop zone before ingestion into Bronze.

</details>

---

**Q6.** What is the default file retention threshold enforced by VACUUM, and what happens if you try to set it lower without additional configuration?

A) 7 days default; VACUUM throws an error unless spark.databricks.delta.retentionDurationCheck.enabled is set to false
B) 30 days default; the command fails silently and does nothing
C) 24 hours default; the command always succeeds regardless of value
D) There is no default; RETAIN must always be explicitly specified

<details>
<summary>Answer</summary>

**Correct: A**

VACUUM defaults to a 7-day (168-hour) retention safety threshold; specifying a shorter RETAIN period throws an error unless the retention duration check is explicitly disabled.

**Why the others are wrong:**
- 30 days is the default for transaction log retention (delta.logRetentionDuration), not the VACUUM file retention threshold.
- 24 hours is not the default and the command does not always succeed at low thresholds without disabling the safety check.
- VACUUM has a built-in default of 7 days even if RETAIN is omitted.

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q7.** In Unity Catalog, the fully qualified name main.sales.orders represents which components, in order?

A) database.catalog.table
B) workspace.catalog.table
C) schema.catalog.table
D) catalog.schema.table

<details>
<summary>Answer</summary>

**Correct: D**

Unity Catalog uses a three-level namespace: catalog.schema.table — here main is the catalog, sales is the schema, and orders is the table.

**Why the others are wrong:**
- There is no 'database' level above catalog in Unity Catalog's namespace hierarchy.
- Workspace is not part of the object namespace; Unity Catalog metastores can span multiple workspaces.
- The order is reversed — catalog comes first, then schema, then table.

</details>

---

**Q8.** What guarantee does the Delta Lake transaction log (_delta_log) provide for concurrent writes to the same table?

A) Eventual consistency only, with conflicts resolved by last-writer-wins
B) ACID transactions via ordered commit files and optimistic concurrency control
C) Atomicity only — isolation and durability are not guaranteed
D) No concurrency support; only a single writer is allowed per table at a time

<details>
<summary>Answer</summary>

**Correct: B**

The _delta_log records ordered JSON commits, and Delta uses optimistic concurrency control to validate and serialize concurrent writes, providing full ACID guarantees.

**Why the others are wrong:**
- Delta does not use last-writer-wins; conflicting transactions are detected and one writer retries or fails.
- Delta guarantees all four ACID properties — atomicity, consistency, isolation, and durability — not just atomicity.
- Multiple writers are supported concurrently; the transaction log is precisely what enables safe concurrent writes.

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

