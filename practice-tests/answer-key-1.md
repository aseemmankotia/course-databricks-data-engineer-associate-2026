# Answer Key — Practice Test 1

**Q1. Correct: A**

Job clusters are created automatically when a Lakeflow Job runs and are terminated immediately when the job finishes, incurring lower job-compute DBU rates and no idle cost between runs.

---

**Q2. Correct: B**

Unity Catalog organizes data using a three-level namespace: catalog (top-level container), schema (equivalent to a database), and table, e.g. main.sales.orders.

---

**Q3. Correct: C**

Delta Lake's default retention threshold for VACUUM is 168 hours (7 days). Attempting to set a shorter retention period fails unless the safety check (spark.databricks.delta.retentionDurationCheck.enabled) is explicitly disabled.

---

**Q4. Correct: C**

The Gold layer contains curated, business-level aggregates and dimensional models built for direct consumption by BI dashboards, reporting, and ML feature pipelines.

---

**Q5. Correct: A**

Delta Lake's transaction log retains historical versions of the table, enabling time travel queries with VERSION AS OF or TIMESTAMP AS OF to read the table exactly as it existed at a prior point, without needing a separate backup.

---

**Q6. Correct: D**

Serverless SQL warehouses use a pool of pre-provisioned, instantly available compute managed by Databricks, eliminating cold-start delays and letting dashboards resume querying within seconds after idle periods.

---

**Q7. Correct: A**

Delta Lake provides ACID transactions through its transaction log (_delta_log), which records every change as an ordered, atomic commit. This ensures that readers always see a consistent snapshot of the table, either before or after a write completes, never a partial state.

---

**Q8. Correct: D**

addNewColumns is the default and correct mode when new columns should be automatically merged into the target table schema, restarting the stream to pick up the change without data loss.

---

**Q9. Correct: C**

COPY INTO maintains metadata about which source files have already been loaded into the target table, making repeated executions idempotent — already-processed files are automatically skipped.

---

**Q10. Correct: D**

Lakeflow Connect provides Databricks-managed connectors for SaaS applications and databases (such as Salesforce) that handle authentication, incremental extraction, and schema mapping without requiring custom ingestion code.

---

**Q11. Correct: D**

cloudFiles.maxFilesPerTrigger caps the number of new files processed in a single micro-batch, reducing the volume of file metadata the driver must handle at once and easing memory pressure.

---

**Q12. Correct: D**

COPY INTO is a simple, idempotent SQL command intended for periodic batch loads of new files, while Auto Loader is built for continuously or repeatedly discovering and incrementally ingesting large volumes of new files efficiently, including streaming use cases.

---

**Q13. Correct: A**

Auto Loader automatically captures any data that doesn't match the inferred or provided schema — such as malformed or extra fields — into a _rescued_data column, preserving it instead of dropping or failing on it.

---

**Q14. Correct: D**

"addNewColumns" is the default and recommended mode for this scenario: when Auto Loader detects a new column, it throws an UnknownFieldException to stop the current stream (forcing awareness of the schema change), updates the schema metadata to include the new column, and on stream restart processing continues normally with the new column included going forward.

---

**Q15. Correct: A**

COPY INTO maintains internal metadata tracking which source files have already been successfully loaded into the target table. On subsequent runs, it automatically skips previously loaded files and only ingests new files that appear in the source location, making it idempotent and safe to schedule repeatedly.

---

**Q16. Correct: B**

MERGE INTO is purpose-built for CDC/upsert patterns and lets you combine conditional DELETE, UPDATE, and INSERT clauses against the same target in a single atomic transaction, keyed on the matched condition.

---

**Q17. Correct: D**

Materialized views in Lakeflow Declarative Pipelines are declaratively defined, automatically and incrementally refreshed by the pipeline engine on each update, and their results are physically persisted for fast downstream reads.

---

**Q18. Correct: C**

Partitioning by customer_id and ordering by load_timestamp descending, then filtering row_number()==1, deterministically keeps the single most recent row per customer while retaining all other columns.

---

**Q19. Correct: B**

coalesce(discount_pct, lit(0)) replaces NULL with a properly-typed numeric 0, preserving every order row while making the multiplication and SUM behave correctly for undiscounted orders.

---

**Q20. Correct: B**

TRANSFORM is a higher-order function that applies a lambda expression to every element of an array and returns a new array of the same cardinality, which is exactly what's needed to strip a prefix from each tag while keeping the array shape.

---

**Q21. Correct: B**

The watermark threshold must be at least as large as the maximum expected lateness (10 minutes) so late events are still admitted into their 5-minute window's aggregation, while state for windows older than the watermark is safely dropped, bounding memory use.

---

**Q22. Correct: C**

MERGE INTO is the standard Delta Lake construct for performing upserts. Matching on the primary key with WHEN MATCHED THEN UPDATE and WHEN NOT MATCHED THEN INSERT updates existing rows and inserts new ones while leaving unmatched historical rows untouched.

---

**Q23. Correct: B**

QUALIFY filters the result of a window function directly in Spark SQL. Partitioning by customer_id and ordering by event_timestamp DESC assigns row number 1 to the most recent event per customer, and filtering to = 1 returns exactly one row per customer_id.

---

**Q24. Correct: C**

FILTER is a Spark SQL higher-order function that applies a lambda predicate to each array element and returns a new array containing only the elements for which the predicate evaluates true, exactly matching the requirement.

---

**Q25. Correct: A**

A materialized view precomputes and stores query results, and Databricks (via Lakeflow Declarative Pipelines) automatically and incrementally refreshes it on a schedule, giving BI consumers fast query performance without needing to manage refresh logic themselves.

---

**Q26. Correct: B**

The 'All done' dependency condition means Task D runs once all upstream tasks have completed, whether they succeeded, failed, or were skipped.

---

**Q27. Correct: A**

Failure alerts are based on the final status of the task run. Since the retry succeeded, the task's overall result is success, so no failure notification is triggered.

---

**Q28. Correct: C**

A file arrival trigger monitors a specified storage location and starts the job automatically when new files are detected, without needing a fixed schedule.

---

**Q29. Correct: A**

The For each task type is designed to loop over a collection of input values and execute a nested task once per value, automatically passing each value as a parameter.

---

**Q30. Correct: D**

Job parameters are passed into a notebook task as key-value pairs; the notebook retrieves the value using dbutils.widgets.get() once the task's parameter is mapped to {{job.parameters.env}}.

---

**Q31. Correct: C**

Retry policies are set per task, letting different tasks within the same job have distinct retry counts and intervals based on their individual reliability needs.

---

**Q32. Correct: C**

When a retry policy is configured with a number of retries, the task will re-attempt execution that many times (regardless of whether the failure was a timeout or another transient error) before the run is finally marked as failed, unless a retry succeeds.

---

**Q33. Correct: C**

Databricks Asset Bundles let you define a project once in databricks.yml and use the 'targets' section to override cluster size, schedule, and workspace host per environment (dev/staging/prod), enabling consistent, repeatable deployment from a single source-controlled codebase.

---

**Q34. Correct: A**

GitHub Actions (or any CI runner) can invoke the Databricks CLI to run 'bundle validate' on pull_request events and 'bundle deploy --target prod' on push to main, which is the standard pattern for automating Asset Bundle deployment through CI/CD.

---

**Q35. Correct: A**

Git folders (formerly called Repos) integrate a workspace folder with a Git provider such as GitHub, GitLab, Bitbucket, or Azure DevOps, allowing users to clone, commit, push, pull, and manage branches directly from the Databricks UI.

---

**Q36. Correct: D**

The 'targets' section in databricks.yml defines named deployment environments (e.g., dev, staging, prod), each of which can override the workspace host, run-as identity, mode (development/production), and variable values used elsewhere in the bundle.

---

**Q37. Correct: A**

Git folders provide interactive version control of notebooks/files inside the workspace, while Asset Bundles are a CLI/YAML-driven infrastructure-as-code tool for defining, validating, and deploying jobs, pipelines, and other resources across environments — they solve different but complementary problems.

---

**Q38. Correct: A**

OPTIMIZE compacts the many small files produced by frequent streaming writes into fewer, larger files, which directly improves read performance by reducing file-listing and open/close overhead.

---

**Q39. Correct: B**

Partitioning on a high-cardinality column like user ID creates one partition (directory) per distinct value, resulting in an excessive number of tiny files and poor performance from file-listing overhead and metadata bloat.

---

**Q40. Correct: B**

delta.targetFileSize is the Delta table property that explicitly controls the target size of files produced by OPTIMIZE, allowing engineers to tune compaction output size.

---

**Q41. Correct: C**

Disk spill occurs when an operation such as a shuffle or sort cannot fit its intermediate data in available executor memory, forcing Spark to write to disk, which is slow. Increasing executor memory or reducing the size of partitions (e.g., via repartitioning or increasing shuffle partitions) reduces the memory pressure causing the spill.

---

**Q42. Correct: D**

The Metrics tab on a Databricks cluster (built on Ganglia-based monitoring) displays real-time hardware-level metrics like CPU, memory, and network I/O across the cluster's nodes, which is the correct place to diagnose resource bottlenecks.

---

**Q43. Correct: B**

Dynamic views using CASE WHEN combined with is_member() (or current_user()) let you conditionally return real values or NULL per user/group at query time, enabling column-level masking while keeping the rest of the table accessible to everyone.

---

**Q44. Correct: D**

Unity Catalog's three-level namespace sits under a single account-level metastore: Metastore is the top-level container, which holds catalogs, which hold schemas (databases), which hold tables/views.

---

**Q45. Correct: B**

Unity Catalog GRANT syntax follows the pattern GRANT <privilege> ON <securable> TO <principal>. SELECT is the correct privilege name for read access on a table.

---

**Q46. Correct: B**

Unity Catalog automatically captures table- and column-level lineage across notebooks, jobs, and dashboards. The Lineage tab in Catalog Explorer visualizes upstream and downstream dependencies, letting engineers assess impact before making schema changes.

---

