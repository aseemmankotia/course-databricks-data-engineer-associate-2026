# Practice Test 1: Databricks Data Engineer Associate: Exam Prep

- **Questions:** 46
- **Time limit:** 90 minutes
- **Passing score:** 70%%

> Answers are in `answer-key-1.md`. Track results in `score-tracker.md`.

---

**Q1.** A data engineer schedules a nightly ETL notebook that runs for 45 minutes once per day. There is no need for interactive access to the compute outside of this scheduled run. Which cluster configuration minimizes cost while meeting the requirement?

A) Configure the job to use a job cluster that Databricks automatically creates and terminates after the run completes
B) Provision an all-purpose cluster and leave it running continuously for the nightly job
C) Use a single-node all-purpose cluster pinned to prevent auto-termination
D) Use all-purpose compute configured for interactive notebook use and schedule the job against it

---

**Q2.** In Unity Catalog, a data engineer wants to fully qualify a table reference in a SQL query so that it unambiguously identifies the exact table across the metastore. Which three-level namespace format does Unity Catalog use?

A) metastore.catalog.table
B) catalog.schema.table
C) workspace.database.table
D) schema.catalog.table

---

**Q3.** A data engineer runs VACUUM on a Delta table without specifying a custom retention interval. What is the default minimum retention threshold Databricks enforces to protect files that may still be needed for time travel queries?

A) 24 hours
B) 30 days
C) 168 hours (7 days)
D) 0 hours (no minimum enforced)

---

**Q4.** In the medallion architecture used on Databricks, which layer is designed to store business-level aggregates and dimensional models optimized for BI reporting and machine learning consumption?

A) Bronze
B) Staging
C) Gold
D) Silver

---

**Q5.** A data engineer accidentally overwrites a production Delta table with corrupted data. No separate backup exists. Which Delta Lake capability lets the engineer query the table's state as it existed immediately before the overwrite?

A) Querying the table using time travel (e.g., VERSION AS OF or TIMESTAMP AS OF)
B) Z-Ordering the table on a high-cardinality column
C) Running OPTIMIZE to compact small files
D) Enabling Auto Loader schema evolution mode

---

**Q6.** A BI team connects dashboards to Databricks SQL and needs queries to return results within seconds after periods of inactivity, without managing or waiting on cluster startup time. Which compute option best meets this requirement?

A) A job cluster scheduled to run at fixed intervals
B) A classic SQL warehouse configured with a 10-minute auto-stop
C) An all-purpose interactive cluster shared by the BI team
D) A serverless SQL warehouse

---

**Q7.** A data engineering team runs a job that performs a large batch UPDATE on a Delta Lake table, followed shortly after by a separate MERGE operation. The Delta table's transaction log shows both operations succeeded. Which feature of Delta Lake guarantees that concurrent readers never see a partially written or inconsistent state during these operations?

A) Delta Lake's ACID transaction guarantees, implemented via the transaction log (_delta_log), ensure atomic and isolated writes
B) The Delta table automatically caches results in the Databricks SQL warehouse to prevent inconsistent reads
C) The cluster's autoscaling feature pauses reads while writes are in progress
D) VACUUM removes stale files before readers can access them, preventing inconsistency

---

**Q8.** A data engineer is using Auto Loader to ingest JSON files that occasionally include new columns added by the upstream source. The pipeline must automatically add these new columns to the target Delta table's schema without failing the stream. Which cloudFiles.schemaEvolutionMode value should be configured?

A) failOnNewColumns
B) none
C) rescue
D) addNewColumns

---

**Q9.** A nightly batch job uses COPY INTO to load new CSV files from cloud storage into a Delta table. Due to an orchestration bug, the job runs twice on the same day against the same source directory. What is the result?

A) The job fails with a duplicate file error on the second run
B) The second run duplicates every row from the previously loaded files
C) COPY INTO tracks which files have already been loaded and skips them on the second run, so no duplicate rows are added
D) COPY INTO only executes successfully the first time it is ever run against a table

---

**Q10.** A company wants to ingest data from Salesforce into Databricks using Databricks-managed connectors that handle authentication, incremental sync, and schema mapping with minimal custom code. Which capability should be used?

A) DBFS mount points
B) Auto Loader with cloudFiles
C) COPY INTO
D) Lakeflow Connect

---

**Q11.** A streaming Auto Loader job that discovers millions of JSON files per day is causing excessive driver memory pressure during each micro-batch. Which configuration should be adjusted to limit the number of new files processed per trigger?

A) cloudFiles.inferColumnTypes
B) cloudFiles.schemaLocation
C) cloudFiles.useNotifications
D) cloudFiles.maxFilesPerTrigger

---

**Q12.** What is the key difference between COPY INTO and Auto Loader for ingesting files into Delta Lake?

A) Auto Loader requires Unity Catalog to function, while COPY INTO does not
B) COPY INTO can only write to external Delta tables, never managed tables
C) COPY INTO supports only Parquet files, while Auto Loader supports all file formats
D) COPY INTO is a SQL command best suited for periodically loading batches of new files, while Auto Loader is designed for continuous or scheduled incremental streaming ingestion with efficient, scalable file discovery

---

**Q13.** A data engineer ingests CSV files into a Delta table using Auto Loader and needs to capture records that don't conform to the expected schema instead of dropping them or failing the job. Which Auto Loader feature addresses this requirement?

A) The rescued data column (_rescued_data)
B) Setting cloudFiles.inferColumnTypes to false
C) Enabling mergeSchema on the write operation
D) Setting cloudFiles.schemaEvolutionMode to none

---

**Q14.** A data engineer configures Auto Loader to ingest a directory of JSON files that occasionally contain new fields added by an upstream application. The requirement is that the pipeline should not permanently fail when new fields appear, and the new fields should automatically be added to the target Delta table's schema so subsequent batches load successfully. Which schemaEvolutionMode setting should be used?

A) rescue
B) none
C) failOnNewColumns
D) addNewColumns

---

**Q15.** A data engineer schedules the same COPY INTO command to run once per day against a cloud storage folder that receives new CSV files daily. By default, how does COPY INTO behave regarding files that were already successfully loaded in a previous run?

A) COPY INTO automatically tracks which files have already been loaded and skips them, loading only new files by default
B) COPY INTO deletes source files immediately after loading them so they cannot be reloaded
C) COPY INTO reloads every file in the folder on each run unless a WHERE clause is added to filter by modification time
D) COPY INTO requires the FILES option to explicitly list which new files to load on each run

---

**Q16.** A pipeline receives daily CDC files for a silver 'customers' table. Each file contains inserts, updates, and deletes, identified by a boolean 'is_deleted' flag on the source records. Which single statement correctly applies all three change types to the target Delta table in one operation?

A) Run INSERT OVERWRITE to replace the entire target table with the day's CDC file
B) Run MERGE INTO customers t USING cdc s ON t.id = s.id WHEN MATCHED AND s.is_deleted THEN DELETE WHEN MATCHED THEN UPDATE SET * WHEN NOT MATCHED THEN INSERT *
C) Run a DELETE statement filtered on is_deleted, followed by a separate INSERT INTO statement for new rows
D) Run COPY INTO customers FROM cdc_path WITH (mergeSchema = true)

---

**Q17.** A gold-layer daily sales summary needs to be automatically and incrementally recomputed on a schedule by a Lakeflow Declarative Pipeline, with results persisted so downstream BI queries don't pay recomputation cost on every read. Which object type should be defined for this?

A) A temporary view
B) A standard managed Delta table populated by a manually triggered notebook job
C) A global temporary view
D) A materialized view

---

**Q18.** A bronze table contains duplicate rows per 'customer_id', distinguished only by differing 'load_timestamp' values. You must keep exactly one row per customer_id — the one with the latest load_timestamp — for the silver layer. Which PySpark code correctly does this?

A) df.dropDuplicates(["customer_id"])
B) df.groupBy("customer_id").agg(max("load_timestamp"))
C) w = Window.partitionBy("customer_id").orderBy(col("load_timestamp").desc()); df.withColumn("rn", row_number().over(w)).filter(col("rn")==1).drop("rn")
D) df.distinct()

---

**Q19.** In a silver 'orders' table, the 'discount_pct' column is NULL whenever no discount was applied, which should be treated as 0. A gold aggregation computes SUM(discount_pct * amount), but rows with NULL discount_pct are producing NULL results that get excluded from the sum, undercounting revenue impact. Which transformation should be applied to 'discount_pct' before the aggregation?

A) df.filter(col("discount_pct").isNotNull())
B) df.withColumn("discount_pct", coalesce(col("discount_pct"), lit(0)))
C) df.na.drop()
D) df.na.fill("0", subset=["discount_pct"])

---

**Q20.** A bronze table has a column 'tags' of type ARRAY<STRING> with values like 'PROMO_10' and 'PROMO_20'. You need to produce a new array column where each element has the 'PROMO_' prefix stripped, preserving the array structure. Which Spark SQL function is designed for this?

A) EXPLODE(tags)
B) TRANSFORM(tags, x -> regexp_replace(x, 'PROMO_', ''))
C) ARRAY_CONTAINS(tags, 'PROMO_')
D) FLATTEN(tags)

---

**Q21.** A Structured Streaming job reads from Kafka and aggregates event counts using 5-minute tumbling windows on event_time. Events can legitimately arrive up to 10 minutes late due to producer buffering. Which code correctly bounds streaming state while still allowing those late events to be counted in their correct window?

A) df.groupBy(window("event_time", "5 minutes")).count()
B) df.withWatermark("event_time", "10 minutes").groupBy(window("event_time", "5 minutes")).count()
C) df.withWatermark("event_time", "5 minutes").groupBy(window("event_time", "10 minutes")).count()
D) df.dropDuplicates(["event_time"]).groupBy(window("event_time", "5 minutes")).count()

---

**Q22.** A data engineer needs to apply daily incremental updates from a CDC source into a Delta Lake silver table on the primary key customer_id. Existing customer_id values must be updated with new attribute values, and new customer_id values must be inserted. Historical rows not present in the daily batch must remain untouched. Which approach BEST meets these requirements?

A) Run INSERT OVERWRITE on the silver table using the daily batch data each day
B) Use COPY INTO to load the daily batch file into the silver table
C) Use MERGE INTO the silver table on customer_id, with WHEN MATCHED THEN UPDATE and WHEN NOT MATCHED THEN INSERT
D) Use CREATE OR REPLACE TABLE AS SELECT to rebuild the silver table from the source each day

---

**Q23.** A raw events table contains multiple rows per customer_id, each with a different event_timestamp. Which query correctly returns exactly one row per customer_id, keeping only the row with the most recent event_timestamp?

A) SELECT DISTINCT customer_id, * FROM events
B) SELECT * FROM events QUALIFY ROW_NUMBER() OVER (PARTITION BY customer_id ORDER BY event_timestamp DESC) = 1
C) SELECT * FROM events GROUP BY customer_id
D) SELECT * FROM events ORDER BY event_timestamp DESC LIMIT 1

---

**Q24.** A table has a column item_prices of type ARRAY<DOUBLE>. Without writing a UDF, which expression returns a new array containing only the elements greater than 100?

A) TRANSFORM(item_prices, price -> price > 100)
B) ARRAY_CONTAINS(item_prices, 100)
C) FILTER(item_prices, price -> price > 100)
D) EXISTS(item_prices, price -> price > 100)

---

**Q25.** A gold-layer aggregate table must be kept up to date automatically on a schedule, with Databricks managing incremental computation of the underlying data so downstream BI dashboards always query fast, precomputed results without the consuming team writing refresh logic. Which object type should be used?

A) A materialized view
B) A standard view defined with CREATE VIEW
C) A temporary view
D) A global temporary view

---

**Q26.** A Lakeflow job has the following task graph: Task A runs first. Task B and Task C both depend on Task A and run in parallel. Task D depends on both B and C, with its dependency condition set to 'All done'. During a job run, Task B fails but Task C succeeds. What happens to Task D?

A) Task D is skipped because Task B failed
B) Task D runs after both Task B and Task C finish, regardless of their success or failure
C) Task D fails immediately without waiting for Task B and Task C to finish
D) Task D automatically reruns Task B before it starts

---

**Q27.** A data engineer configures a task in a Lakeflow job to retry up to 2 times with a minimum interval of 5 minutes between attempts, and sets an email alert to fire 'on failure'. The task fails on the first attempt but succeeds on the retry. What happens regarding the alert?

A) No failure alert is sent, because the task ultimately succeeded after the retry
B) An alert email is sent because the task failed at least once
C) An alert email is sent only after both retries are exhausted, regardless of the final outcome
D) The job stops immediately after the first failure and no retry is attempted

---

**Q28.** A team wants a Lakeflow job to start processing automatically as soon as new files are uploaded to a cloud storage location, without running on a fixed schedule or continuously polling with a running cluster. Which trigger type should they configure?

A) Continuous trigger
B) Scheduled (cron) trigger
C) File arrival trigger
D) Manual trigger only

---

**Q29.** A job must run the same notebook task once for each of 15 table names stored in a list, passing a different table name as a parameter on each iteration. Which Lakeflow Jobs feature correctly satisfies this requirement?

A) Use a For each task that iterates over the list and runs the notebook task once per item
B) Configure 15 separate jobs, one per table
C) Use a Run Job task that triggers the same job 15 times sequentially
D) Manually re-run the job 15 times from the Jobs UI, changing the parameter each time

---

**Q30.** A job defines a job-level parameter named 'env' with a default value of 'dev'. A notebook task in the job needs to read this parameter's value at runtime. Which approach correctly retrieves it inside the notebook?

A) Reference it directly as a bare Python variable named env with no additional setup
B) Read it using spark.conf.get('env') from the cluster's Spark configuration
C) Use dbutils.jobs.taskValues.get(taskKey='env')
D) Use dbutils.widgets.get('env') after the task's parameters are mapped to {{job.parameters.env}}

---

**Q31.** In Lakeflow Jobs, at what level is a task's retry policy (number of retries and minimum retry interval) configured?

A) At the workspace level, applying to every job in the workspace
B) At the job level only, applying uniformly to every task in that job
C) At the individual task level, so different tasks in the same job can have different retry settings
D) Retries are not configurable; Databricks automatically retries every failed task exactly once

---

**Q32.** A data engineer configures a Lakeflow Job with a single task that runs a notebook. The task is set with 'Retry policy: 2 retries' and 'Retry on timeout' enabled. During a scheduled run, the notebook fails due to a transient cluster startup error. What happens next?

A) The job immediately alerts the on-call team and does not retry since retries only apply to timeout failures
B) The job automatically increases the cluster size before retrying to avoid future startup errors
C) The job retries the task up to 2 additional times before marking the run as failed if all attempts fail
D) The job skips the failed task and continues to downstream dependent tasks marked as optional

---

**Q33.** A data engineering team wants to deploy the same Lakeflow Job definition to dev, staging, and prod workspaces, each with different cluster sizes and job schedules, from a single source-controlled project. Which approach BEST meets this requirement?

A) Manually copy notebooks between workspaces using Git folders
B) Use the Databricks REST API to manually clone the job JSON into each workspace
C) Use Databricks Asset Bundles with multiple targets defined in databricks.yml
D) Store separate copies of the job configuration in each workspace's DBFS root

---

**Q34.** A team wants their CI/CD pipeline to run 'databricks bundle validate' on every pull request and 'databricks bundle deploy --target prod' only after a merge to the main branch. Which setup accomplishes this?

A) A GitHub Actions workflow triggered on pull_request and push events that calls Databricks CLI bundle commands
B) A Git folders webhook that automatically merges commits directly into the production cluster
C) Unity Catalog access control lists applied to the main branch
D) Configuring pipeline triggers only inside the Databricks workspace UI with no external CI system

---

**Q35.** Which Databricks feature is used to sync a folder of notebooks and files in a workspace with a remote Git repository for version control?

A) Git folders (Repos)
B) Unity Catalog volumes
C) DBFS root storage
D) Databricks Asset Bundles

---

**Q36.** In a Databricks Asset Bundle's databricks.yml file, which top-level key defines environment-specific overrides such as workspace host, mode, and variable values for dev versus prod?

A) resources
B) artifacts
C) workspace
D) targets

---

**Q37.** What is the primary distinction between Git folders and Databricks Asset Bundles?

A) Git folders sync notebooks and files with a Git repository for version control, while Asset Bundles package and deploy jobs, pipelines, and infrastructure as code
B) They are the same feature, just renamed between Databricks releases
C) Asset Bundles only work with GitHub, while Git folders support all Git providers
D) Git folders have been fully replaced by Asset Bundles and are no longer available

---

**Q38.** A streaming job continuously appends small batches of data to a Delta table every few seconds. Over time, downstream queries against this table have become significantly slower due to a large number of small underlying files. What should the data engineer do to resolve this?

A) Run OPTIMIZE on the table to compact small files into larger ones
B) Increase the cluster size to add more executors
C) Enable Photon acceleration on the cluster
D) Cache the table using CACHE TABLE

---

**Q39.** A data engineer partitions a 200GB Delta table by a column with roughly 1 million distinct values (a user ID). After this change, query performance gets worse and the table directory contains an enormous number of very small files. What is the most likely cause?

A) The table needs Z-Ordering applied instead of any partitioning
B) The partition column has excessively high cardinality, creating too many tiny partitions
C) The cluster does not have enough worker nodes to process the query
D) The table needs to be cached in memory before querying

---

**Q40.** A data engineer wants the OPTIMIZE command to produce larger compacted files, targeting approximately 1GB per file instead of the Delta Lake default target size. Which table property should be set to achieve this?

A) delta.tuneFileSizesForRewrites
B) delta.targetFileSize
C) delta.autoOptimize.optimizeWrite
D) delta.deletedFileRetentionDuration

---

**Q41.** While troubleshooting a slow job in the Spark UI, a data engineer notices the Stages tab reports significant 'spill (disk)' for one of the stages. What does this indicate, and what is the best remedy?

A) The job completed successfully, so this metric can be safely ignored
B) The data needs to be cached using CACHE TABLE before the job runs again
C) Executor memory is insufficient for the shuffle or sort operation, and increasing executor memory or reducing partition size would help
D) The cluster needs Photon acceleration enabled to eliminate the spill

---

**Q42.** A data engineer wants to view real-time hardware utilization metrics such as CPU load, memory usage, and network I/O for the nodes in a running Databricks cluster while diagnosing a performance issue. Where should they look?

A) The Delta Lake transaction log for the table being processed
B) The Unity Catalog audit log for the workspace
C) The output of the DESCRIBE HISTORY command
D) The cluster's Metrics tab, which provides Ganglia-based hardware monitoring

---

**Q43.** A company stores employee salary data in the `hr.employees.compensation` table. Only members of the `hr_analysts` group should see actual salary values; all other users should see NULL for that column, while still being able to query the rest of the table. Which approach BEST implements this in Unity Catalog?

A) ALTER TABLE hr.employees.compensation SET TBLPROPERTIES ('salary.masked' = 'true')
B) Create a view that selects CASE WHEN is_member('hr_analysts') THEN salary ELSE NULL END AS salary, and grant SELECT on the view instead of the base table
C) GRANT SELECT ON TABLE hr.employees.compensation TO `hr_analysts` and REVOKE SELECT FROM all other users
D) Define the salary column with a MASKED data type in the CREATE TABLE statement

---

**Q44.** What is the correct object hierarchy in Unity Catalog, from the highest (top) level to the lowest?

A) Workspace > Catalog > Schema > Table
B) Metastore > Schema > Catalog > Table
C) Catalog > Metastore > Schema > Table
D) Metastore > Catalog > Schema > Table

---

**Q45.** Which SQL statement correctly grants the `analysts` group permission to query data from the table main.sales.orders in Unity Catalog?

A) GRANT SELECT TO `analysts` ON TABLE main.sales.orders;
B) GRANT SELECT ON TABLE main.sales.orders TO `analysts`;
C) ALTER TABLE main.sales.orders ADD ACCESS FOR `analysts`;
D) GRANT READ ON TABLE main.sales.orders TO `analysts`;

---

**Q46.** A data engineer wants to identify every downstream table, view, and dashboard that would be affected before dropping a column from a Delta table. Which Unity Catalog capability should they check first?

A) The Query History page in the SQL editor
B) The Lineage tab in Catalog Explorer for that table
C) The system.access.audit logs table
D) The output of DESCRIBE HISTORY on the table

---

