# Answer Key — Practice Test 2

**Q1. Correct: A**

Serverless compute for jobs eliminates manual cluster sizing, starts rapidly (no need to keep it running), and bills only for the compute used during the 20-minute run, matching a job that runs once daily and needs minimal management overhead.

---

**Q2. Correct: C**

Delta Lake enforces a safety check (spark.databricks.delta.retentionDurationCheck.enabled) that blocks VACUUM calls with a retention period below 168 hours (7 days) to prevent breaking time travel and concurrent readers/writers. Running with 0 hours retention fails unless this check is disabled via SQL configuration, and doing so is explicitly a documented but dangerous exam trap.

---

**Q3. Correct: A**

Unity Catalog governs data access centrally at the catalog.schema.table level using GRANT/REVOKE statements, and these permissions are enforced consistently across all workspaces and compute resources attached to the metastore, independent of which cluster or notebook is used.

---

**Q4. Correct: C**

The silver layer is designed to hold cleansed, deduplicated, and conformed data — including joins with reference/dimension tables — providing a validated foundation before gold-layer aggregation for business consumption.

---

**Q5. Correct: C**

Delta Lake records every write as an ordered, versioned commit in JSON (and periodic checkpoint) files inside the _delta_log directory alongside the data files. This transaction log provides atomicity, consistency, and the version history needed for time travel queries like VERSION AS OF or TIMESTAMP AS OF.

---

**Q6. Correct: B**

OPTIMIZE compacts small files into larger ones to reduce fragmentation, and ZORDER BY colocates related data for the specified column, enabling effective data skipping when queries filter on customer_region — directly addressing both stated problems.

---

**Q7. Correct: D**

VACUUM's default retention interval is 7 days (168 hours). Any data files no longer referenced by the current version of the table and older than that threshold become eligible for physical deletion. Since the MERGE-heavy table generates many stale files, running VACUUM nightly at the default retention removed files needed to reconstruct a version from 9 days prior, so RESTORE (and time travel) to that version fails once the files are gone.

---

**Q8. Correct: B**

With schemaEvolutionMode set to 'none', schema evolution is disabled and new columns are simply ignored. Unlike 'rescue' mode, this mode does not automatically capture unmatched data — the rescued data column only captures data if cloudFiles.rescuedDataColumn is explicitly configured.

---

**Q9. Correct: C**

COPY INTO is idempotent by design: it tracks which source files have already been loaded into the target table using metadata stored in the Delta transaction log, and automatically skips files it has already processed on subsequent runs.

---

**Q10. Correct: A**

Lakeflow Connect provides fully managed, prebuilt connectors for SaaS applications and databases such as Salesforce, handling authentication, incremental extraction, and CDC automatically with minimal engineering effort.

---

**Q11. Correct: C**

By default, cloudFiles.inferColumnTypes is false, so Auto Loader infers all columns as StringType during schema inference for CSV and JSON sources. Setting cloudFiles.inferColumnTypes to true is required to get more specific inferred types.

---

**Q12. Correct: A**

COPY_OPTIONS ('force' = 'true') tells COPY INTO to ignore its normal idempotency tracking and reprocess files it has already loaded, which is necessary when the same file paths contain corrected data.

---

**Q13. Correct: C**

File notification mode (cloudFiles.useNotifications = true) uses cloud provider event/queue services (e.g., AWS SQS/SNS, Azure Event Grid, GCP Pub/Sub) to discover new files incrementally and efficiently, avoiding the scalability limits of full directory listings at high file volumes.

---

**Q14. Correct: A**

The default schemaEvolutionMode is 'addNewColumns'. Under this mode, when Auto Loader detects a new column it stops the stream with an exception (schema evolution requires a restart), and once the stream is restarted the new column has already been added to the target schema so ingestion resumes automatically going forward.

---

**Q15. Correct: A**

COPY INTO is idempotent by design: Delta Lake records which source files have already been loaded as metadata within the target table's transaction log. Re-running the same COPY INTO command against the same source path will skip files that were already ingested, so no duplicate rows are produced.

---

**Q16. Correct: B**

Ordering the WHEN MATCHED clauses so the DELETE condition is evaluated first (with an AND predicate on operation) correctly routes delete records to DELETE while all other matches fall through to UPDATE, and unmatched rows are INSERTed — all atomically in one MERGE.

---

**Q17. Correct: D**

row_number() over a window partitioned by event_id and ordered by event_ts descending assigns a unique rank per partition even when ties exist, guaranteeing exactly one row per event_id is kept while retaining all original columns.

---

**Q18. Correct: B**

foreachBatch exposes each micro-batch as a static DataFrame, allowing the MERGE API to be called against it; this is the documented pattern for performing upserts from a streaming source into a Delta sink.

---

**Q19. Correct: A**

filter() first removes elements not meeting the length condition, then transform() applies upper() to each remaining element, correctly composing the two higher-order functions to meet both requirements in one expression.

---

**Q20. Correct: D**

This is the documented distinction: materialized views maintain correctness of a query result over batch semantics (which may involve recomputation), whereas streaming tables leverage Structured Streaming's incremental, append-only processing model for near-real-time ingestion of new records.

---

**Q21. Correct: D**

GROUP BY treats NULL as its own group already, so guest orders were not actually being dropped by the join — they were grouped under a NULL customer_name label that is easy to overlook or filter out downstream. Wrapping the grouping/select column in COALESCE explicitly labels that NULL group as 'Guest', making it visible and correctly aggregated as a distinct, identifiable bucket.

---

**Q22. Correct: B**

Delta Lake's MERGE INTO throws 'multiple source rows matched' when more than one source row maps to the same target row. The documented fix is to collapse the source to one row per merge key (typically via ROW_NUMBER() over a window partitioned by the key and ordered by a recency column, filtering rn = 1) before the merge executes.

---

**Q23. Correct: C**

row_number() over a window partitioned by user_id and ordered by event_time descending assigns rn=1 to the most recent row for each user_id; filtering rn=1 deterministically keeps exactly one, the latest, row per user, which is the pattern the exam expects for deduplication-by-recency.

---

**Q24. Correct: B**

FILTER(array, lambda) is the correct Spark SQL higher-order function syntax; it takes the array and a lambda predicate expressed as element -> boolean_expression, returning only elements satisfying the condition, e.g. FILTER(items, i -> i.price > 100).

---

**Q25. Correct: C**

Materialized views in Lakeflow Declarative Pipelines are designed for queries involving aggregations and joins; the pipeline engine automatically applies incremental refresh where possible so results aren't fully recomputed on each run, and the results are persisted as physical data for fast downstream querying — exactly the requirement here.

---

**Q26. Correct: B**

File arrival triggers expose a 'Minimum time between triggers' setting (in seconds) that throttles how often the job can fire even if new files keep arriving, directly solving the over-triggering problem.

---

**Q27. Correct: A**

Lakeflow Jobs retry policy enforces a fixed minimum interval between the failed attempt and the next retry attempt, and stops after the configured max retries is reached — there is no automatic exponential backoff.

---

**Q28. Correct: B**

The For Each task in Lakeflow Jobs supports a configurable concurrency setting, but the maximum allowed value is capped at 100 concurrent iterations regardless of iteration count.

---

**Q29. Correct: D**

The Run Job task type lets one job invoke another existing job as a task, enabling modular, reusable workflows without duplicating task definitions — updates to the ingestion job automatically apply everywhere it's referenced.

---

**Q30. Correct: D**

dbutils.jobs.taskValues.set() in the upstream task publishes a named value scoped to that task run, and downstream If/else or notebook tasks can reference it directly via the {{tasks.<task_key>.values.<key>}} syntax in the task's condition or parameters.

---

**Q31. Correct: C**

The 'At least one failed' Run if condition triggers the dependent task only when one or more of its upstream dependencies fail, matching the requirement to run cleanup on failure and skip on full success.

---

**Q32. Correct: A**

The 'Run if dependencies' setting on a task overrides the default 'All succeeded' behavior. With 'At least one failed' selected, the task executes as soon as any of its listed upstream dependencies report a failed status, regardless of whether other upstream tasks succeeded. Here Transform failed, so the condition is met and Cleanup runs (useful for cleanup/notification tasks).

---

**Q33. Correct: B**

Asset Bundles enforce stricter validation in `mode: production`. Because resources should not silently run as whichever developer happens to deploy, the bundle validator requires an explicit `run_as` (a user or, more commonly, a service principal) to be set for a production target. Without it, `databricks bundle deploy` fails during validation before any resources are created or updated.

---

**Q34. Correct: B**

Asset Bundles support variable substitution (`${var.my_variable}`) referenced inside job/pipeline/cluster definitions. Declaring defaults at the bundle level and overriding specific values under `targets.<env>.variables` lets one job definition serve every environment cleanly, which is the intended CI/CD pattern tested on the exam.

---

**Q35. Correct: B**

Development mode is designed for safe, isolated iteration: resource names are prefixed with an identifier tied to the deploying user (e.g., '[dev username] job_name') and job/pipeline schedules or triggers are paused by default so dev deployments don't unexpectedly run on a schedule or collide with other developers' resources.

---

**Q36. Correct: B**

When a Git source task references a branch, the job pulls the latest commit on that branch at run time, so unrelated merges silently change what production executes. Pinning to an immutable commit hash (or a Git tag created at release time) decouples the running job from ongoing branch activity, giving fully reproducible production deployments — this is the recommended CI/CD practice tested on the exam.

---

**Q37. Correct: A**

Repos (Git folders) provides source-code synchronization for notebooks and files inside the workspace tied to a Git repo — useful for editing and browsing version-controlled code. Asset Bundles go further: they define resources (jobs, DLT pipelines, cluster policies, permissions) as YAML alongside code, validated and deployed as a unit via the Databricks CLI, enabling true environment promotion (dev/staging/prod) — which is the core CI/CD tool tested in this domain.

---

**Q38. Correct: A**

One task with vastly larger shuffle read than its peers is the textbook signature of data skew: a small number of key values dominate the dataset, so their partition ends up with far more rows. Adaptive Query Execution's skew join optimization (spark.sql.adaptive.skewJoin.enabled) automatically splits oversized partitions, and manually salting the skewed key achieves the same result when AQE cannot detect it.

---

**Q39. Correct: C**

optimizeWrite coalesces output files during writes and autoCompact triggers small, automatic compactions after writes, while a periodically scheduled OPTIMIZE job bin-packs any remaining small files into properly sized ones — together these address both new and existing small-file accumulation.

---

**Q40. Correct: D**

Partition columns should have low-to-moderate cardinality, and event_date (about 1,095 distinct daily values over 3 years) is a good fit, producing a manageable number of partitions and enabling partition pruning on date-range filters. customer_id has far too many distinct values to partition on, but is an ideal ZORDER candidate: ZORDER clusters similar values together within files and populates min/max statistics used for data skipping on the customer_id filter.

---

**Q41. Correct: A**

The disk cache automatically stores copies of remote files on the local NVMe SSDs attached to cluster nodes as they are read, transparently accelerating subsequent reads of the same data on supported instance types — no explicit caching command is needed.

---

**Q42. Correct: C**

High memory and disk spill during a shuffle indicates that individual shuffle partitions are too large to fit in executor memory. Increasing spark.sql.shuffle.partitions divides the shuffle data into more, smaller partitions, reducing the amount of data each task must hold in memory and reducing the risk of executor memory pressure and eventual loss.

---

**Q43. Correct: D**

Unity Catalog row filters let you attach a SQL function to a table via ALTER TABLE ... SET ROW FILTER. The function can call is_account_group_member() to conditionally restrict rows only for members of a specific group while leaving full visibility for everyone else, all on a single physical table.

---

**Q44. Correct: D**

Unity Catalog enforces a privilege chain: to query a table, a principal needs USE CATALOG on the containing catalog and USE SCHEMA on the containing schema in addition to SELECT on the table itself. Missing either USE privilege blocks access even with SELECT granted.

---

**Q45. Correct: D**

Granting CREATE TABLE scoped to the specific schema (main.bronze) gives engineers exactly the ability to create tables there, without extending create rights to other schemas in the catalog or granting unrelated privileges like MODIFY or ownership.

---

**Q46. Correct: D**

Unity Catalog automatically captures table- and column-level lineage for any read/write operations executed on Unity Catalog-enabled compute, and surfaces it as an interactive upstream/downstream graph in Catalog Explorer, linking notebooks, jobs, dashboards, and other assets without additional setup.

---

