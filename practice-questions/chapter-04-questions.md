## Chapter 4: Streaming Ingestion with Auto Loader and Schema Evolution — Practice Questions

**Q1.** A pipeline uses Auto Loader to ingest JSON files that may add new columns over time. The team wants the stream to automatically pick up new columns without silently dropping data, and the job is configured to auto-restart on failure. Which schemaEvolutionMode should be used?

A) rescue
B) failOnNewColumns
C) addNewColumns
D) none

<details>
<summary>Answer</summary>

**Correct: C**

addNewColumns is the default mode. When a new column is detected, Auto Loader intentionally fails the stream with an UnknownFieldException so the schema can be updated, then the stream restarts (automatically if the job has retries configured) and resumes with the new column included.

**Why the others are wrong:**
- rescue captures unexpected data into _rescued_data instead of adding it as a real column — the new column would not appear as a queryable field.
- failOnNewColumns stops the stream permanently on schema change and requires manual intervention — it does not auto-resume.
- none ignores new columns entirely, silently dropping the new data without any record of it.

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q2.** A company receives millions of small files per day across thousands of partitioned directories in cloud storage. Listing the directory tree on every micro-batch is becoming a bottleneck. Which Auto Loader configuration BEST addresses this?

A) Directory listing mode (default)
B) Trigger.Continuous()
C) File notification mode using cloud event queues
D) Increasing cloudFiles.maxFilesPerTrigger

<details>
<summary>Answer</summary>

**Correct: C**

File notification mode subscribes to cloud provider event notifications (e.g., SQS/SNS on AWS, Event Grid/Queue Storage on Azure) so Auto Loader only processes files it's told about, avoiding expensive full directory listings and scaling far better at high file volumes.

**Why the others are wrong:**
- Directory listing mode is the default and is exactly the approach causing the bottleneck at scale.
- Trigger.Continuous() controls processing latency, not how files are discovered, and is not supported for Auto Loader's typical batch-style incremental workloads.
- maxFilesPerTrigger limits batch size per micro-batch but does not change how the underlying file listing is performed.

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q3.** What is the primary purpose of the checkpoint location specified in an Auto Loader streaming write?

A) It stores a cached copy of the source data for faster reprocessing
B) It defines the output partitioning scheme for the target Delta table
C) It stores the cluster configuration used to run the stream
D) It tracks processed file state and schema information to guarantee exactly-once processing across restarts

<details>
<summary>Answer</summary>

**Correct: D**

The checkpoint location persists the stream's progress (which files/offsets have been processed) and the inferred schema, allowing the stream to resume exactly where it left off after a restart and avoid duplicate or missed processing.

**Why the others are wrong:**
- The checkpoint does not cache the actual source data, only metadata about progress and schema.
- Output partitioning is defined separately via .partitionBy() on the DataFrameWriter, not the checkpoint.
- Cluster configuration is unrelated to the checkpoint location — it's a Spark/cluster concern, not a streaming state concern.

</details>

---

**Q4.** During Auto Loader ingestion, a row arrives where a field's value has a different data type than the inferred schema expects (e.g., a string where an integer was expected). Assuming default rescue behavior is enabled, where does this malformed data end up?

A) The entire row is dropped silently
B) It is written to a _rescued_data column as unparsed JSON alongside the rest of the successfully parsed row
C) The stream immediately terminates with a fatal, unrecoverable error
D) It is converted to null and written to the original column

<details>
<summary>Answer</summary>

**Correct: B**

The _rescued_data column captures any data that doesn't match the inferred schema (type mismatches, extra fields, casing differences) as a JSON string, so no data is lost and it can be inspected or reprocessed later.

**Why the others are wrong:**
- Auto Loader is designed specifically to avoid silent data loss — that's the reason _rescued_data exists.
- This is not a fatal error scenario; the stream continues processing normally with the mismatch captured in _rescued_data.
- Auto Loader does not silently coerce to null and discard the original value — it preserves it in _rescued_data instead.

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q5.** A company wants a nightly job that processes all new files that have arrived since the last run, then stops the stream automatically once caught up, without leaving a cluster running continuously. Which trigger is recommended by Databricks for this pattern?

A) Trigger.Continuous()
B) Trigger.AvailableNow()
C) Trigger.ProcessingTime('1 hour')
D) Trigger.Once()

<details>
<summary>Answer</summary>

**Correct: B**

Trigger.AvailableNow() processes all currently available data in multiple micro-batches (respecting settings like maxFilesPerTrigger) and then stops, making it more efficient and scalable than Trigger.Once() for catching up on large backlogs. It is the Databricks-recommended replacement for Once() in this pattern.

**Why the others are wrong:**
- Trigger.Continuous() keeps the stream running indefinitely with low-latency processing — it does not stop after catching up.
- ProcessingTime triggers run continuously at fixed intervals rather than processing once and stopping.
- Trigger.Once() processes all available data in a single large batch and stops, but is less efficient than AvailableNow() and is now considered the legacy option for this use case.

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q6.** A regulated pipeline must never silently accept an unexpected new column — any schema drift should immediately halt ingestion for manual review. Which cloudFiles.schemaEvolutionMode setting satisfies this requirement?

A) addNewColumns
B) failOnNewColumns
C) rescue
D) none

<details>
<summary>Answer</summary>

**Correct: B**

failOnNewColumns causes the stream to fail and stop when a new column is detected, requiring a human to explicitly update the schema before restarting — matching the requirement for manual review before accepting drift.

**Why the others are wrong:**
- addNewColumns automatically incorporates new columns and resumes on its own, which does not require manual review.
- rescue silently captures new/unexpected data into _rescued_data and continues running without halting.
- none silently drops unexpected new data without failing or flagging it for review.

</details>

---

**Q7.** Which format string is used to instruct Spark's DataStreamReader to use Auto Loader for incremental file ingestion?

A) 'cloudFiles'
B) 'delta'
C) 'autoloader'
D) 'streamFiles'

<details>
<summary>Answer</summary>

**Correct: A**

Auto Loader is invoked with spark.readStream.format('cloudFiles'), the identifier the exam consistently references for this feature.

**Why the others are wrong:**
- 'delta' is used to stream from an existing Delta table, not to ingest raw files with Auto Loader.
- 'autoloader' is not a valid Spark format string — a common distractor based on the feature's marketing name.
- 'streamFiles' is not a real Spark/Databricks format identifier.

</details>

---

**Q8.** By default, how does Auto Loader discover new files when cloudFiles.useNotifications is not explicitly set?

A) It performs directory listing of the source path to detect new files
B) It uses cloud provider event notifications (file notification mode)
C) It polls a Kafka topic for file arrival events
D) It requires the user to manually specify each new file path

<details>
<summary>Answer</summary>

**Correct: A**

Directory listing mode is the default behavior — Auto Loader lists the source directory incrementally to identify new files. File notification mode must be explicitly enabled via cloudFiles.useNotifications=true along with additional cloud resource setup.

**Why the others are wrong:**
- File notification mode is opt-in, not the default — it requires explicit configuration and cloud permissions.
- Auto Loader has no built-in dependency on Kafka for file discovery; this is a distractor unrelated to the feature.
- Manual file path specification defeats the purpose of Auto Loader's automatic incremental discovery.

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

