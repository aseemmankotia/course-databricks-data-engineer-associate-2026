# Practice Test 2: Databricks Data Engineer Associate: Exam Prep

- **Questions:** 46
- **Time limit:** 90 minutes
- **Passing score:** 70%%

> Answers are in `answer-key-2.md`. Track results in `score-tracker.md`.

---

**Q1.** A team runs a nightly Lakeflow Job that ingests 2TB of data and finishes in 20 minutes. The job runs once per day, and the team wants to minimize both compute cost and cluster startup latency without managing infrastructure sizing manually. Which compute configuration BEST meets these requirements?

A) Serverless compute for the job, letting Databricks manage sizing and scaling automatically
B) An all-purpose cluster shared with interactive notebook users, kept running 24/7
C) A classic job cluster with a fixed worker count of 32 nodes, pinned for the pipeline
D) A single-node cluster configured with the largest available instance type

---

**Q2.** An engineer runs the following command on a production Delta table: VACUUM sales_transactions RETAIN 0 HOURS. Which statement correctly describes the outcome?

A) VACUUM ignores the RETAIN clause entirely and always applies the default 7-day retention period
B) The command immediately deletes all data files, including files referenced by the current table version
C) The command is rejected by default because it violates the safety check protecting the minimum 168-hour retention window, unless the check is explicitly disabled
D) The command succeeds silently and only removes files older than 30 days regardless of the specified value

---

**Q3.** A data platform team wants to enforce that only members of the 'finance_analysts' group can query the table finance.reporting.quarterly_revenue, regardless of which workspace or cluster is used to access it. Which Databricks capability should they configure to achieve this?

A) GRANT SELECT statements on the table issued through Unity Catalog, tied to the finance_analysts group
B) Cluster-level access control lists (ACLs) applied to the interactive cluster used for reporting
C) Notebook-level permissions restricting who can attach to the reporting notebook
D) Workspace-level folder permissions on the directory containing the reporting notebooks

---

**Q4.** In a medallion architecture pipeline, an engineer has raw JSON files landing in the bronze layer with duplicate records, inconsistent field names, and occasional malformed rows. Business users need clean, deduplicated, and conformed data joined with a reference dimension table before it can be aggregated for dashboards. In which layer should this deduplication, schema conformance, and joining logic be implemented?

A) Bronze layer, immediately as data is ingested from the source
B) Gold layer, where only final business-level aggregates are produced
C) Silver layer, as an intermediate cleansing and conforming stage
D) Landing zone, before any layer of the medallion architecture is applied

---

**Q5.** Which mechanism does Delta Lake use to provide ACID transaction guarantees and enable time travel queries against a table?

A) A separate relational metadata database that Databricks manages outside the storage layer
B) In-memory transaction tracking that is lost when the cluster restarts
C) An ordered transaction log of JSON commit files stored in the table's _delta_log directory
D) A hidden shadow copy of the entire table created before every write operation

---

**Q6.** A gold-layer table is queried almost exclusively with filters on the customer_region column, and the table has grown to hundreds of small files after months of incremental writes. Query performance has degraded significantly. Which command should be run to BEST address both the file fragmentation and the filtering performance for this workload?

A) VACUUM gold_customers RETAIN 168 HOURS
B) OPTIMIZE gold_customers ZORDER BY (customer_region)
C) ANALYZE TABLE gold_customers COMPUTE STATISTICS
D) CREATE BLOOM FILTER INDEX ON gold_customers FOR COLUMNS(customer_region)

---

**Q7.** A data engineering team runs `VACUUM sales_delta` every night with default settings on a Delta table that receives frequent MERGE updates. Nine days later, an engineer attempts `RESTORE TABLE sales_delta TO VERSION AS OF 42` to recover a version created 9 days ago, and the command fails because the underlying data files are missing. What is the root cause?

A) RESTORE TABLE only works with TIMESTAMP AS OF syntax, not VERSION AS OF, causing the failure
B) VACUUM deletes only the transaction log JSON files, and the missing referenced files are actually a metastore corruption issue unrelated to VACUUM
C) Delta Lake time travel is limited to 24 hours by default regardless of VACUUM settings
D) VACUUM's default retention threshold is 168 hours (7 days), so data files no longer referenced by the current table version and older than 7 days become eligible for permanent deletion

---

**Q8.** A streaming Auto Loader job ingests JSON files with cloudFiles.schemaEvolutionMode set to 'none', and cloudFiles.rescuedDataColumn is NOT configured. New source files begin arriving with a column that is not present in the target table's current inferred schema. What happens to the data in that new column?

A) The stream fails immediately with an UnknownFieldException
B) The new column's data is silently dropped and is not captured anywhere
C) The new column is automatically added to the schema and the stream restarts
D) The new column's data is automatically captured in a default _rescued_data column

---

**Q9.** A data engineer runs the exact same COPY INTO command twice against a Delta table, pointing at the same source directory whose contents have not changed since the first run. What happens on the second execution?

A) COPY INTO reloads and duplicates all rows because it always reprocesses matching files
B) COPY INTO throws an error stating the files were already loaded
C) COPY INTO skips the previously loaded files by tracking file load history in the target Delta table's transaction log, so no new rows are added
D) COPY INTO overwrites the entire table with the contents of the source files

---

**Q10.** A company needs to continuously ingest data from Salesforce into Delta tables with built-in change data capture handling and minimal custom engineering effort. Which approach BEST meets this requirement?

A) Use a Lakeflow Connect managed connector for Salesforce
B) Use Auto Loader with the cloudFiles format pointed at manually exported CSV files from Salesforce
C) Schedule an hourly COPY INTO job against a staging location
D) Write a custom PySpark JDBC job scheduled via Lakeflow Jobs

---

**Q11.** By default, when Auto Loader infers the schema of a CSV or JSON data source, what data type does it assign to all inferred columns unless cloudFiles.inferColumnTypes is explicitly set to true?

A) The most specific inferred type, such as IntegerType or DoubleType
B) VariantType
C) StringType
D) BinaryType

---

**Q12.** A data engineer previously loaded a batch of files using COPY INTO, but later discovers the source files contained corrupted values that have since been corrected in place at the same file paths. Which clause should be added to the COPY INTO statement to force these already-loaded files to be reprocessed?

A) COPY_OPTIONS ('force' = 'true')
B) FORMAT_OPTIONS ('mergeSchema' = 'true')
C) VALIDATE ALL
D) FILES ('overwrite' = 'true')

---

**Q13.** A company ingests files into a cloud storage bucket that receives millions of new files per day. An Auto Loader job using the default directory listing mode is experiencing increasing latency in discovering new files as the number of files grows. Which configuration change would BEST address this?

A) Increase cloudFiles.maxFilesPerTrigger so more files are processed per micro-batch
B) Replace Auto Loader with a scheduled COPY INTO job
C) Enable cloudFiles.useNotifications = true to switch to file notification mode using cloud queue services
D) Decrease the trigger interval so the stream checks for new files more frequently

---

**Q14.** A data engineer sets up an Auto Loader stream to ingest JSON files into a Delta table, using the default value for the 'cloudFiles.schemaEvolutionMode' option. Partway through the day, upstream systems begin emitting a new field that was not present in the original schema. What is the expected behavior?

A) The streaming query terminates with an UnknownFieldException; when the stream is restarted, the new column is automatically added to the target table's schema and ingestion resumes
B) The new field is captured in the '_rescued_data' column and the stream continues running without any interruption
C) The stream keeps running uninterrupted and automatically merges the new field into the table schema in real time
D) Auto Loader automatically creates a separate quarantine table to hold records containing the unrecognized field

---

**Q15.** An engineer schedules a job that runs 'COPY INTO my_table FROM "/mnt/raw/csv/" FILEFORMAT = CSV' once per day to load newly arrived files from a landing directory. One day the job is accidentally triggered twice in a row with no new files added to the directory in between. What happens on the second run?

A) COPY INTO tracks which files have already been loaded using metadata stored in the target Delta table's transaction log, so the second run loads zero new rows
B) COPY INTO reloads every file in the directory again because no CHECKPOINT_LOCATION was specified, resulting in duplicate rows in the target table
C) COPY INTO fails outright because it detects that the source directory contents are unchanged since the last run
D) COPY INTO requires the engineer to manually create and maintain a control table named '_copy_into_state' to track which files were already ingested

---

**Q16.** A data engineer needs to update a Delta table `customers_silver` from a CDC feed `customers_updates`. Records with `operation = 'DELETE'` should remove matching rows from the target, updates should overwrite existing rows, and new records should be inserted — all in a single MERGE operation. Which statement correctly implements this?

A) MERGE INTO customers_silver t USING customers_updates s ON t.id = s.id WHEN MATCHED THEN UPDATE SET * WHEN NOT MATCHED THEN INSERT *
B) MERGE INTO customers_silver t USING customers_updates s ON t.id = s.id WHEN MATCHED AND s.operation = 'DELETE' THEN DELETE WHEN MATCHED THEN UPDATE SET * WHEN NOT MATCHED THEN INSERT *
C) MERGE INTO customers_silver t USING customers_updates s ON t.id = s.id WHEN NOT MATCHED BY SOURCE THEN DELETE WHEN MATCHED THEN UPDATE SET *
D) Run one MERGE for inserts/updates, then a separate DELETE FROM customers_silver WHERE id IN (SELECT id FROM customers_updates WHERE operation = 'DELETE')

---

**Q17.** Table `events_raw` contains duplicate rows per `event_id` due to at-least-once delivery. To keep exactly one row per `event_id` — the one with the maximum `event_ts` — while preserving all other columns, which PySpark snippet is correct?

A) df.dropDuplicates(["event_id"])
B) df.groupBy("event_id").agg(max("event_ts").alias("event_ts"))
C) w = Window.partitionBy("event_id").orderBy(desc("event_ts")); df.withColumn("rn", rank().over(w)).filter("rn = 1").drop("rn")
D) w = Window.partitionBy("event_id").orderBy(desc("event_ts")); df.withColumn("rn", row_number().over(w)).filter("rn = 1").drop("rn")

---

**Q18.** A Lakeflow Job runs a Structured Streaming query reading from a bronze Delta table that must upsert aggregated results into a gold Delta table. Because MERGE is not natively supported as a streaming sink, which implementation correctly achieves incremental upserts?

A) streamDF.writeStream.format("delta").outputMode("complete").option("path", "/gold/sales_agg").start()
B) def upsert_to_delta(microBatchDF, batchId):
    dt = DeltaTable.forName(spark, "gold.sales_agg")
    dt.alias("t").merge(microBatchDF.alias("s"), "t.id = s.id").whenMatchedUpdateAll().whenNotMatchedInsertAll().execute()

streamDF.writeStream.foreachBatch(upsert_to_delta).outputMode("update").start()
C) streamDF.writeStream.format("delta").outputMode("append").table("gold.sales_agg")
D) gold_table = DeltaTable.forName(spark, "gold.sales_agg"); gold_table.alias("t").merge(streamDF.alias("s"), "t.id = s.id").whenMatchedUpdateAll().execute()

---

**Q19.** Column `tags` is an ARRAY<STRING>. You need a new column `tags_upper` containing only the tags longer than 3 characters, converted to uppercase, computed in a single SQL expression. Which expression is correct?

A) transform(filter(tags, x -> length(x) > 3), x -> upper(x)) AS tags_upper
B) transform(tags, x -> upper(x)) AS tags_upper
C) filter(tags, x -> length(x) > 3) AS tags_upper
D) array_sort(transform(tags, x -> upper(x))) AS tags_upper

---

**Q20.** In a Lakeflow Declarative Pipeline, a data engineer chooses CREATE OR REFRESH MATERIALIZED VIEW for one object and CREATE OR REFRESH STREAMING TABLE for another. What is the key operational difference between these two object types?

A) Materialized views cannot be queried directly by BI tools or ad-hoc SQL, while streaming tables can.
B) Streaming tables always fully recompute their contents on every pipeline run, while materialized views always process only new data incrementally.
C) Materialized views require a Unity Catalog managed storage location, while streaming tables can only be created in the hive_metastore.
D) Materialized views recompute their result (fully or incrementally, depending on the query) from their source each run to stay consistent with the defining query, while streaming tables use Structured Streaming to process new source records incrementally, assuming sources are append-only.

---

**Q21.** A silver-layer query LEFT JOINs `orders` to `customers` on `customer_id`. Some orders have a NULL `customer_id` from guest checkouts. When the result is grouped by `c.customer_name` to sum order totals, all guest orders disappear from the output instead of appearing as a distinct 'Guest' bucket. Without changing the join type, what fixes this?

A) Change the join to an INNER JOIN between orders and customers
B) Add a WHERE c.customer_id IS NOT NULL clause before the GROUP BY
C) Change the join to a FULL OUTER JOIN between orders and customers
D) SELECT COALESCE(c.customer_name, 'Guest') AS customer_name, SUM(o.total) FROM orders o LEFT JOIN customers c ON o.customer_id = c.customer_id GROUP BY COALESCE(c.customer_name, 'Guest')

---

**Q22.** A pipeline runs a daily MERGE INTO statement that upserts customer records into a Delta table using customer_id as the match key. The job fails with an error indicating that multiple source rows matched the same target row. The source DataFrame can contain more than one update for the same customer_id on a given day. Which approach BEST resolves this?

A) Add a WHERE clause to the MERGE statement that filters out NULL customer_id values before matching
B) Deduplicate the source DataFrame by customer_id using a ROW_NUMBER() window function ordered by the update timestamp, keeping only the latest row per customer_id, before running the MERGE
C) Change all AND conditions in the merge predicate to OR to loosen the matching logic
D) Increase spark.sql.shuffle.partitions so Spark can process duplicate keys across more partitions

---

**Q23.** A DataFrame `events_df` has columns user_id, event_time, and value. You need to keep only the single most recent row per user_id based on event_time. Which code correctly and reliably achieves this?

A) events_df.dropDuplicates(["user_id"])
B) events_df.groupBy("user_id").max("event_time")
C) events_df.withColumn("rn", F.row_number().over(Window.partitionBy("user_id").orderBy(F.col("event_time").desc()))).filter("rn = 1").drop("rn")
D) events_df.orderBy(F.col("event_time").desc()).limit(1)

---

**Q24.** A gold Delta table has a column `items` of type ARRAY<STRUCT<name:STRING, price:DOUBLE>>. Using Spark SQL higher-order functions, which expression correctly returns, for each row, only the struct elements of `items` where price is greater than 100?

A) SELECT items[items.price > 100] AS items FROM gold_table
B) SELECT FILTER(items, i -> i.price > 100) AS items FROM gold_table
C) SELECT ARRAY_FILTER(items WHERE price > 100) AS items FROM gold_table
D) SELECT TRANSFORM(items, i -> i.price) WHERE i.price > 100

---

**Q25.** You are building a gold-layer object in a Lakeflow Declarative Pipeline that must contain an aggregation (e.g., daily revenue totals) computed from an upstream streaming table. The object must refresh efficiently as new data arrives — avoiding a full recomputation from scratch on every run — and must persist results for downstream BI dashboards to query with low latency. Which object type should you define for this gold layer?

A) A standard SQL view (CREATE VIEW)
B) A Python temporary view cached only for the current session
C) A materialized view
D) A streaming table defined directly on the aggregation logic

---

**Q26.** A Lakeflow Job uses a file arrival trigger on a Volume path where upstream systems continuously drop small files every few seconds. The engineer notices the job is starting far more often than needed, consuming cluster startup overhead. Which trigger setting should be adjusted to reduce run frequency without disabling the file arrival trigger?

A) Switch the cluster to a pool-backed cluster
B) Increase the 'Minimum time between triggers' value in the trigger configuration
C) Add a Wait task at the start of the job to delay execution
D) Change the job to a continuous trigger instead of file arrival

---

**Q27.** A task is configured with Max retries = 2 and Minimum retry interval = 2 minutes. The task fails on its first attempt after running for 10 minutes. Which statement correctly describes the retry behavior?

A) Databricks waits at least 2 minutes after the failure before starting a retry, and will attempt this up to 2 times
B) Databricks doubles the interval exponentially for each subsequent retry attempt
C) The job aborts immediately because the run duration exceeded the retry interval threshold
D) Databricks retries immediately since the interval only applies between two consecutive successful runs

---

**Q28.** An engineer configures a For Each task to iterate over 500 partition values, invoking the same notebook task once per value. To avoid overwhelming the cluster, they want to cap how many iterations run in parallel. What is the maximum concurrency value that can be set for a For Each task?

A) 500
B) 100
C) 1000
D) No limit — concurrency is bounded only by cluster size

---

**Q29.** A team has an existing ingestion job used by three separate downstream Lakeflow Jobs. Instead of duplicating the ingestion tasks into each of the three jobs, the team wants a single source of truth for the ingestion logic while still triggering it as part of each downstream workflow. Which approach BEST achieves this?

A) Clone the ingestion job into each of the three downstream jobs
B) Use a shared cluster policy across all three jobs so they reuse the same compute
C) Copy the ingestion notebook into each downstream job's Notebook task
D) Add a Run Job task in each downstream job that references the existing ingestion job

---

**Q30.** Task A runs a notebook that computes a validation record count. This value must control whether an If/else condition task allows Task C to run downstream. Which implementation correctly wires this together?

A) Task A returns the count via the notebook's exit() string, and the If/else task parses the run output using the REST API at runtime
B) Task A writes the count to a file in DBFS, and the If/else task runs a shell command to read the file
C) Task A sets a job parameter with dbutils.widgets.set(), and all downstream tasks automatically inherit the updated value
D) Task A calls dbutils.jobs.taskValues.set("row_count", n), and the If/else task condition references {{tasks.task_a.values.row_count}}

---

**Q31.** A workflow has Task X depending on upstream Tasks A, B, and C, which run in parallel. The engineer wants Task X to execute a cleanup/alerting step whenever at least one of A, B, or C fails, but Task X should be skipped if all three succeed. Which 'Run if' dependency setting on Task X achieves this?

A) None failed
B) All done
C) At least one failed
D) All succeeded

---

**Q32.** A Lakeflow Job has three tasks: Extract runs first. Transform depends on Extract. Cleanup depends on Transform, with its 'Run if dependencies' condition set to 'At least one failed'. During a run, Extract succeeds but Transform fails. What happens to Cleanup?

A) Cleanup runs, because at least one of its upstream dependencies (Transform) failed, satisfying the condition
B) Cleanup is skipped because Transform, its direct dependency, did not succeed
C) Cleanup runs only after Transform is manually repaired and succeeds
D) The entire job aborts immediately and Cleanup is marked as Skipped in the run graph

---

**Q33.** A data engineering team's databricks.yml defines a 'prod' target with `mode: production` but does not include a `run_as` field anywhere in that target. The team runs `databricks bundle deploy -t prod`. What happens?

A) The deploy succeeds and all jobs run as the identity of whichever engineer executed the deploy command
B) The deploy fails bundle validation because production mode requires an explicitly configured run_as identity
C) The deploy succeeds and Databricks automatically generates a new service principal to own the resources
D) The deploy succeeds but all jobs are created in a paused state until run_as is added

---

**Q34.** A team maintains one job definition in an Asset Bundle but needs different cluster node types and a different input table parameter for dev versus prod, without duplicating the job's YAML block. Which approach best satisfies this?

A) Maintain two separate Git branches, one per environment, each with its own hardcoded job definition
B) Define bundle variables in the top-level `variables` block and override their values under each target's `variables` section
C) Use the Databricks Repos API to inject parameters into the notebook at runtime based on branch name
D) Hardcode both environments' values into the job's `parameters` field and select the correct one manually before each deploy

---

**Q35.** In a databricks.yml target block, setting `mode: development` produces which default behavior compared to `mode: production`?

A) It automatically enables autoscaling on every cluster referenced by the bundle's resources
B) It prefixes deployed resource names with the deploying user's identity and pauses schedules/triggers by default
C) It encrypts all secrets referenced by the bundle using a workspace-managed key
D) It requires a service principal to be specified before any resource can be deployed

---

**Q36.** A production Lakeflow Job's Git source task is configured to reference `branch: main`. After several unrelated merges to main, the team notices the production job's behavior changed even though no one modified the job definition or triggered a redeploy. What is the best fix to make production runs reproducible?

A) Disable Git integration entirely and move the notebook to DBFS so it can never change unexpectedly
B) Pin the job's git_source to a specific commit hash or Git tag instead of tracking a branch
C) Increase the job's retry count so failed runs caused by code drift are automatically retried
D) Switch the task's source from Git provider to a Databricks Repos path pointing at the same branch

---

**Q37.** What is the key architectural difference between Databricks Repos (Git folders) and Databricks Asset Bundles in a CI/CD workflow?

A) Repos syncs workspace files/notebooks with a Git repository, while Asset Bundles is an infrastructure-as-code framework that packages code together with job, pipeline, and cluster configuration for versioned, CLI-driven deployment across environments
B) Repos can only sync SQL files while Asset Bundles can only sync notebooks
C) Asset Bundles is only usable within the Databricks UI, while Repos is only usable via the CLI
D) Repos and Asset Bundles are two names for the same underlying feature with no functional difference

---

**Q38.** In the Spark UI, a stage's task detail table shows 199 tasks completing in under 3 seconds each, but one task runs for 9 minutes with a Shuffle Read Size of 48 GB compared to roughly 200 MB for every other task in the stage. What is the most likely root cause and the best fix?

A) Data skew on the join or group-by key is sending a disproportionate amount of data to one partition; enable adaptive query execution skew join handling or salt the key
B) The driver does not have enough memory to coordinate the stage; increase driver memory in the cluster configuration
C) Too many small files are being scanned; run OPTIMIZE with ZORDER on the source table
D) The broadcast join threshold is set too high, causing the driver to run out of memory while collecting the broadcast table

---

**Q39.** A Delta table is loaded continuously via Auto Loader and has accumulated hundreds of thousands of small Parquet part-files. Downstream BI queries against this table are now slow because of file-listing and open overhead. Which change provides the best long-term fix?

A) Run VACUUM on the table daily to remove the small files
B) Increase maxFilesPerTrigger on the Auto Loader stream so each micro-batch reads more files at once
C) Enable delta.autoOptimize.optimizeWrite and delta.autoOptimize.autoCompact table properties, and schedule a periodic OPTIMIZE job
D) Convert the table from Delta format to plain Parquet so Spark can read files faster without transaction log overhead

---

**Q40.** A 5 TB Delta table is queried almost exclusively with filters on customer_id (a very high-cardinality column) and a date range on event_date (roughly 3 years of daily data). Which layout strategy best supports efficient file pruning for both filter patterns?

A) Partition the table by customer_id and apply ZORDER BY event_date within each partition
B) Partition the table by both customer_id and event_date together
C) Apply ZORDER BY on both customer_id and event_date, using no partitioning at all
D) Partition the table by event_date and apply ZORDER BY customer_id within each partition

---

**Q41.** On a Databricks all-purpose cluster with local NVMe storage attached to the worker nodes, which caching mechanism automatically stores copies of remote Parquet or Delta files on local disk to accelerate repeated reads, without any explicit CACHE TABLE or .cache() call from the user?

A) The Delta/IO disk cache (formerly called Databricks IO cache)
B) The SQL warehouse result cache
C) Spark's in-memory RDD/DataFrame cache created by calling .persist()
D) Broadcast variable caching used during broadcast hash joins

---

**Q42.** A job is failing intermittently with ExecutorLostFailure errors. In the Spark UI stage detail page for the failing stage, the 'Shuffle Read' column shows large values under both 'Spill (Memory)' and 'Spill (Disk)' for most tasks, right before executors are lost. What configuration change is most likely to resolve this?

A) Decrease spark.sql.shuffle.partitions so fewer, larger partitions reduce shuffle coordination overhead
B) Disable adaptive query execution so Spark does not attempt to coalesce shuffle partitions at runtime
C) Increase spark.sql.shuffle.partitions so each shuffle partition holds less data and fits within executor memory
D) Raise spark.sql.autoBroadcastJoinThreshold so the join is executed as a broadcast join instead of a shuffle join

---

**Q43.** A data engineering team must ensure that analysts in the 'eu_analysts' group can only see rows where region = 'EU' in the customer_orders table, while all other users continue to see every row in that same table. Which approach BEST satisfies this requirement in Unity Catalog?

A) Create a separate managed table containing only EU data and grant SELECT on it exclusively to eu_analysts
B) Use a COLUMN MASK function to hide the region column for members of eu_analysts
C) Include a WHERE clause directly inside the GRANT SELECT statement issued to eu_analysts
D) Apply a ROW FILTER function using ALTER TABLE ... SET ROW FILTER that checks is_account_group_member('eu_analysts') against the region column

---

**Q44.** A user already has the SELECT privilege on a table located in the 'sales' schema of the 'prod' catalog, but running a query against it fails with PERMISSION_DENIED. What additional privileges are minimally required to resolve this?

A) OWNER privilege on the prod catalog
B) MODIFY privilege on the sales schema
C) ALL PRIVILEGES on the sales schema
D) USE CATALOG privilege on prod AND USE SCHEMA privilege on sales

---

**Q45.** Which SQL statement correctly grants ONLY the ability to create tables within the schema 'bronze' in catalog 'main' to the group 'engineers', without granting broader catalog-level or schema-wide privileges?

A) GRANT ALL PRIVILEGES ON SCHEMA main.bronze TO `engineers`;
B) ALTER SCHEMA main.bronze OWNER TO `engineers`;
C) GRANT CREATE TABLE ON CATALOG main TO `engineers`;
D) GRANT CREATE TABLE ON SCHEMA main.bronze TO `engineers`;

---

**Q46.** A data engineer wants to view the full upstream and downstream lineage for a Delta table — including which notebooks, jobs, and dashboards read from or write to it. Which capability provides this without any extra configuration, assuming the workloads ran on Unity Catalog-enabled compute?

A) The Delta Lake transaction log viewed via DESCRIBE HISTORY
B) Table access audit logs from the account console
C) Querying system.information_schema.tables
D) Unity Catalog's automatic lineage graph available in Catalog Explorer

---

