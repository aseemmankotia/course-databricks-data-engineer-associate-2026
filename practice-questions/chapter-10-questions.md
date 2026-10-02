## Chapter 10: Troubleshooting, Monitoring, and Performance Optimization — Practice Questions

**Q1.** A stage in the Spark UI shows 199 tasks completing in under 2 minutes and 1 task still running after 45 minutes, with that task's shuffle read size far larger than the others. What is happening and what is the BEST fix?

A) The small file problem — run OPTIMIZE on the source table
B) Disk spill only — increasing executor memory alone will always fix it
C) A network partition — restart the cluster
D) Data skew on the join/group key — repartition on a higher-cardinality column or salt the skewed key

<details>
<summary>Answer</summary>

**Correct: D**

One task with a disproportionately large shuffle read compared to peers is the textbook signature of data skew: one key (or few keys) dominates the partition, so one task does most of the work. Salting the key or repartitioning distributes the load evenly.

**Why the others are wrong:**
- OPTIMIZE fixes file layout on disk, not runtime shuffle imbalance during a job — it wouldn't affect an already-running stage's task distribution
- Spill is a symptom that can accompany skew but the root cause here is the uneven key distribution, not just insufficient memory — more memory only masks it temporarily
- Nothing in the metrics (task duration + shuffle size disparity) suggests a network fault

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q2.** A Delta table receiving frequent small streaming appends now has thousands of tiny files and query latency has degraded significantly. Which action BEST resolves this?

A) Increase spark.sql.shuffle.partitions on the cluster
B) Run OPTIMIZE on the table (optionally enable auto compaction) to compact small files
C) CACHE TABLE the dataset in memory
D) Convert the table to CSV format

<details>
<summary>Answer</summary>

**Correct: B**

OPTIMIZE compacts many small files into fewer larger ones (~1GB target), directly solving the small-file problem. Enabling delta.autoOptimize.autoCompact prevents recurrence from future streaming writes.

**Why the others are wrong:**
- Shuffle partitions affect in-job parallelism, not the number of files already stored on disk
- Caching speeds up repeated reads of the same data in memory but does nothing about file layout or the underlying metadata overhead
- CSV has no compaction mechanism and lacks Delta's transaction log, making the problem worse, not better

</details>

---

**Q3.** An analyst runs the same aggregation query dozens of times per hour against a 2GB dataset that fits easily in cluster memory and rarely changes. Which technique BEST improves repeated query performance?

A) CACHE TABLE the dataset
B) Run OPTIMIZE with ZORDER BY on the join column
C) Increase the Delta target file size property
D) Set up Auto Loader for incremental ingestion

<details>
<summary>Answer</summary>

**Correct: A**

Caching keeps the small, frequently-reused, rarely-changing dataset in memory, avoiding repeated storage reads for every query — exactly the scenario where cache/persist trade-offs favor caching.

**Why the others are wrong:**
- ZORDER improves data-skipping for large tables with selective filters, not repeated full reads of a dataset that already fits in memory
- File size tuning addresses storage layout, not repeated in-memory query performance
- Auto Loader is for ingesting new files, unrelated to speeding up repeated ad-hoc queries on existing data

</details>

---

**Q4.** Which Spark UI tab shows the physical query plan for a SQL statement along with per-operator metrics such as rows processed, data size, and spill?

A) Jobs tab
B) Stages tab
C) Executors tab
D) SQL tab

<details>
<summary>Answer</summary>

**Correct: D**

The SQL tab visualizes the physical DAG for a specific query with metrics annotated directly on each node, making it the primary place to diagnose where spill, skew, or excessive shuffle occurs within a query.

**Why the others are wrong:**
- Jobs tab shows job-level status and stage counts, not per-operator query metrics
- Stages tab shows task-level duration and shuffle metrics for a stage but not the annotated logical/physical query plan
- Executors tab shows resource usage per executor (memory, cores, GC time), not query-specific metrics

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q5.** What is a key trade-off of using CACHE TABLE or persist() in Databricks?

A) Cached data automatically refreshes on every underlying write, eliminating staleness risk
B) It permanently persists data to DBFS regardless of cluster restarts
C) It consumes cluster memory, may evict other cached data, and can return stale results if the source table changes
D) It disables predicate pushdown for all subsequent queries

<details>
<summary>Answer</summary>

**Correct: C**

Caching stores data in executor memory/disk for the life of the cluster session. It competes with other memory needs and does not auto-invalidate — if the underlying table is updated, cached results become stale until UNCACHE/REFRESH.

**Why the others are wrong:**
- Caching does not auto-refresh; that's precisely the staleness risk students must know
- Cached data lives in the cluster's memory/local storage and is lost when the cluster terminates — it is not written to DBFS by default
- Caching has no effect on predicate pushdown behavior

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q6.** Which Spark configuration controls the default target file size (approximately 1 GB) used during Delta's OPTIMIZE compaction?

A) spark.sql.files.maxPartitionBytes
B) spark.databricks.delta.optimize.maxFileSize
C) spark.sql.shuffle.partitions
D) spark.databricks.delta.retentionDurationCheck.enabled

<details>
<summary>Answer</summary>

**Correct: B**

spark.databricks.delta.optimize.maxFileSize sets the target size (default ~1GB / 1073741824 bytes) that OPTIMIZE aims for when compacting files.

**Why the others are wrong:**
- spark.sql.files.maxPartitionBytes controls read-side split size when scanning files, not OPTIMIZE's write-side compaction target
- spark.sql.shuffle.partitions controls the number of partitions after a shuffle in a job, unrelated to file compaction
- That retention property controls whether VACUUM enforces a minimum retention period, unrelated to file sizing

</details>

---

**Q7.** A dashboard built on Databricks SQL has become slow, and the team wants to see historical query duration, rows scanned, and the exact query text to diagnose which query is the bottleneck. Which feature should they use?

A) Query History
B) Cluster Event Log
C) Ganglia metrics
D) DBFS file browser

<details>
<summary>Answer</summary>

**Correct: A**

Query History in Databricks SQL lists every executed query with duration, rows returned, and links to the query profile for detailed diagnosis — the intended tool for this exact scenario.

**Why the others are wrong:**
- Cluster Event Log tracks cluster lifecycle events (start, resize, terminate), not individual query performance
- Ganglia metrics show host-level resource utilization (CPU, memory, network), not per-query text or duration
- The DBFS browser is for viewing files in storage, not query performance data

</details>

---

**Q8.** A nightly Databricks Job must notify the on-call engineer by email immediately if the job fails, with minimal setup effort. What is the simplest built-in solution?

A) Configure the job's 'On failure' email notification setting in the Jobs UI
B) Write custom failure-logging code to a Delta table and check it manually each morning
C) CACHE TABLE the job's output to detect failures
D) Enable Auto Loader schema evolution on the job's source tables

<details>
<summary>Answer</summary>

**Correct: A**

Databricks Jobs supports built-in email (and webhook) notifications on job start, success, or failure, configured directly in the Jobs UI — no custom code required.

**Why the others are wrong:**
- Custom logging requires manual review and doesn't provide real-time alerting, contradicting the 'immediately notify' requirement
- Caching is unrelated to job status monitoring or alerting
- Auto Loader schema evolution manages ingestion schema drift, not job failure notifications

</details>

---

