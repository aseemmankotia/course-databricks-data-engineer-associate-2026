## Chapter 6: Incremental Processing: MERGE and Structured Streaming — Practice Questions

**Q1.** A source table contains CDC records with an operation column `op` valued 'INSERT', 'UPDATE', or 'DELETE'. Which MERGE INTO statement correctly handles all three operations against Delta target table `t` using source alias `s`?

A) MERGE INTO target t USING source s ON t.id = s.id WHEN MATCHED AND s.op='DELETE' THEN DELETE WHEN MATCHED AND s.op='UPDATE' THEN UPDATE SET * WHEN NOT MATCHED AND s.op='INSERT' THEN INSERT *
B) MERGE INTO target t USING source s ON t.id = s.id WHEN MATCHED THEN UPDATE SET *
C) MERGE INTO target t USING source s ON t.id = s.id WHEN NOT MATCHED THEN INSERT *
D) UPDATE target SET * FROM source WHERE target.id = source.id

<details>
<summary>Answer</summary>

**Correct: A**

Only this statement branches on the op column combined with MATCHED/NOT MATCHED conditions to correctly delete, update, and insert rows respectively.

**Why the others are wrong:**
- Only handles updates for matched rows; ignores inserts and deletes entirely.
- Only handles new rows; ignores updates and deletes for existing matches.
- Not valid Delta/SQL MERGE syntax and provides no conditional branching by operation type.

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q2.** A streaming DataFrame needs MERGE INTO logic applied to a Delta table on every micro-batch. Which method should be used?

A) writeStream.foreachBatch(merge_function)
B) writeStream.format("delta").outputMode("update")
C) writeStream.trigger(Trigger.AvailableNow())
D) DataFrame.write.format("delta").mode("merge")

<details>
<summary>Answer</summary>

**Correct: A**

foreachBatch exposes each micro-batch as a static batch DataFrame, allowing batch-only operations like MERGE INTO to run inside a custom function.

**Why the others are wrong:**
- outputMode controls how results are emitted, not how MERGE logic is applied; MERGE isn't natively supported in streaming writes.
- Trigger only controls when micro-batches run, not how data is written.
- "merge" is not a valid Spark write mode.

</details>

---

**Q3.** A pipeline must process all currently available data in the source and then stop automatically, rather than running continuously. Which trigger should be configured?

A) trigger(processingTime="10 seconds")
B) trigger(once=True)
C) trigger(continuous="1 second")
D) trigger(availableNow=True)

<details>
<summary>Answer</summary>

**Correct: D**

Trigger.AvailableNow() processes all currently available data across multiple micro-batches and then stops; it is the recommended replacement for the deprecated Trigger.Once().

**Why the others are wrong:**
- Runs continuously at a fixed interval and never stops on its own.
- Processes all data in a single large batch and is deprecated, risking driver memory pressure on large backlogs.
- Continuous trigger enables experimental low-latency processing and does not stop after available data is processed.

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q4.** What is the primary purpose of applying withWatermark() in a Structured Streaming query?

A) To increase parallelism of micro-batch processing
B) To configure the query to run only once
C) To bound state size by tracking event-time lag and dropping data older than the threshold
D) To encrypt streaming state checkpoints

<details>
<summary>Answer</summary>

**Correct: C**

Watermarking tells Spark how late data can arrive relative to the max event time seen, allowing stateful operators to bound and clean up state.

**Why the others are wrong:**
- Watermarking is unrelated to task parallelism.
- That behavior is controlled by trigger configuration, not watermarking.
- No such encryption feature exists tied to watermarking.

</details>

---

**Q5.** A streaming aggregation uses withWatermark("event_time", "15 minutes") with a 10-minute tumbling window. A record arrives with an event_time 20 minutes older than the current max event_time seen by the engine. What happens to this record?

A) It is included in the aggregation and updates the corresponding window
B) It is buffered indefinitely until a matching window closes
C) It is dropped because it arrived later than the watermark threshold allows
D) It triggers an error and stops the stream

<details>
<summary>Answer</summary>

**Correct: C**

Since the record's lateness (20 minutes) exceeds the watermark threshold (15 minutes), Spark drops it from the stateful aggregation.

**Why the others are wrong:**
- Late data beyond the watermark threshold is not incorporated into windowed aggregates.
- State for closed windows is not retained indefinitely; the watermark defines the cutoff.
- Late data is silently dropped, not treated as an error.

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q6.** Which PySpark code correctly configures a streaming write to trigger a micro-batch every 30 seconds?

A) .trigger(Trigger.ProcessingTime("30 seconds"))
B) .option("triggerInterval", "30 seconds")
C) .trigger(processingTime="30 seconds")
D) .trigger(continuous="30 seconds")

<details>
<summary>Answer</summary>

**Correct: C**

In PySpark, the trigger() method accepts a processingTime keyword argument as a string interval.

**Why the others are wrong:**
- Trigger.ProcessingTime is a Scala API class, not valid PySpark syntax.
- There is no such option key for configuring trigger intervals.
- continuous configures the experimental low-latency continuous processing mode, a different execution model from fixed-interval micro-batches.

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q7.** A Delta table must support soft deletes, marking deleted source records with is_deleted = true instead of physically removing rows. Which MERGE clause achieves this?

A) WHEN MATCHED AND s.op = 'DELETE' THEN DELETE
B) WHEN NOT MATCHED THEN INSERT (is_deleted) VALUES (true)
C) WHEN MATCHED AND s.op = 'DELETE' THEN UPDATE SET t.is_deleted = true
D) WHEN MATCHED THEN DELETE

<details>
<summary>Answer</summary>

**Correct: C**

This clause updates a flag column on matched rows marked for deletion instead of removing the row, implementing a soft delete.

**Why the others are wrong:**
- Performs a hard delete, physically removing the row.
- Applies to unmatched (new) rows, not to marking existing rows as deleted.
- Deletes all matched rows unconditionally, regardless of the operation type.

</details>

---

**Q8.** A foreachBatch function running MERGE operations must be idempotent because Spark may reprocess the same micro-batch after a failure. Which built-in Delta Lake mechanism guarantees idempotent writes across batch retries?

A) Manually deduplicating rows using DISTINCT before every merge
B) Increasing the checkpoint interval to reduce retry frequency
C) Using VACUUM to remove duplicate files after each batch
D) Setting txnVersion and txnAppId options on the write so Delta tracks completed transactions

<details>
<summary>Answer</summary>

**Correct: D**

Delta Lake's txnVersion/txnAppId options let the engine record which batch IDs have already been committed, preventing duplicate application of the same micro-batch.

**Why the others are wrong:**
- Removes duplicates within a single batch but does not prevent reprocessing an entire batch after a restart.
- Checkpoint interval affects fault-tolerance tracking but doesn't itself guarantee idempotent writes.
- VACUUM removes stale data files for storage cleanup; it is unrelated to transactional idempotency.

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

