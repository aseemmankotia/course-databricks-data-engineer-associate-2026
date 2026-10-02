## Chapter 6: Incremental Processing: MERGE and Structured Streaming — Flashcards

| # | Front (Question) | Back (Answer) |
|---|-----------------|---------------|
| 1 | Which method applies MERGE INTO logic inside a Structured Streaming query? | foreachBatch(batchDF, batchId) — MERGE INTO is batch-only, so it must run inside the foreachBatch function on the micro-batch DataFrame. |
| 2 | Trigger.AvailableNow() vs Trigger.Once()? | AvailableNow processes all available data across multiple micro-batches (recommended); Once processes it all in a single batch and is deprecated. |
| 3 | Default Structured Streaming trigger behavior (no trigger specified)? | Runs micro-batches back-to-back as fast as possible — no fixed interval. |
| 4 | PySpark syntax to trigger a micro-batch every 10 seconds | .trigger(processingTime='10 seconds'). Note: Trigger.ProcessingTime(...) class syntax is Scala-only. |
| 5 | Purpose of withWatermark(eventTimeCol, delayThreshold)? | Bounds state size for stateful ops (aggregations, dedup) by dropping data older than max-event-time minus the threshold. |
| 6 | What happens to data arriving later than the watermark threshold? | It is silently dropped from stateful aggregations — no error is raised. |
| 7 | MERGE INTO soft delete pattern | WHEN MATCHED AND s.op='DELETE' THEN UPDATE SET t.is_deleted = true — flags instead of physically removing the row. |
| 8 | MERGE INTO WHEN NOT MATCHED BY SOURCE clause purpose? | Handles target rows absent from the source (e.g., delete/flag rows no longer present) — used for full-sync merges. |
| 9 | Why must foreachBatch MERGE logic be idempotent? | Spark may re-execute the same micro-batch after a failure/restart; use Delta's txnVersion/txnAppId options to guarantee idempotency per batch. |
| 10 | outputMode typically used with watermarked streaming aggregations? | 'append' mode — emits finalized aggregate rows only after the watermark passes the window's end time. |
| 11 | MERGE INTO ... WHEN MATCHED THEN UPDATE SET * shorthand meaning? | Updates all matching target columns from corresponding source columns by name — common shorthand for full-row upserts. |
| 12 | MERGE INTO evaluation order for multiple WHEN MATCHED clauses? | Clauses are evaluated top-to-bottom; the first clause whose condition is true executes for that row. |
