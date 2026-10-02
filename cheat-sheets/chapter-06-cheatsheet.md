## MERGE INTO Syntax
```sql
MERGE INTO target t
USING source s
ON t.id = s.id
WHEN MATCHED AND s.op = 'DELETE' THEN DELETE
WHEN MATCHED AND s.op = 'UPDATE' THEN UPDATE SET *
WHEN NOT MATCHED AND s.op = 'INSERT' THEN INSERT *
WHEN NOT MATCHED BY SOURCE THEN UPDATE SET t.is_deleted = true
```

| Clause | Applies To | Common Use |
|---|---|---|
| WHEN MATCHED | rows in both | UPDATE or DELETE |
| WHEN NOT MATCHED | rows only in source | INSERT |
| WHEN NOT MATCHED BY SOURCE | rows only in target | soft-delete / flag missing rows |

Multiple `WHEN MATCHED` clauses evaluate top-to-bottom; first true condition wins. `UPDATE SET *` / `INSERT *` map columns by name.

## foreachBatch Pattern (Streaming Upserts)
```python
def upsert_to_delta(batch_df, batch_id):
    (DeltaTable.forName(spark, "target")
        .alias("t")
        .merge(batch_df.alias("s"), "t.id = s.id")
        .whenMatchedUpdateAll()
        .whenNotMatchedInsertAll()
        .execute())

(streamDF.writeStream
    .foreachBatch(upsert_to_delta)
    .outputMode("update")
    .start())
```
MERGE is batch-only; `foreachBatch` is the only way to apply it per micro-batch. Ensure idempotency using Delta's `txnVersion`/`txnAppId` options since batches can be retried after failure.

## Trigger Types

| Trigger | Behavior | Use Case |
|---|---|---|
| (default, none) | runs micro-batches back-to-back, ASAP | low-latency continuous ingestion |
| `processingTime="N seconds"` | fixed-interval micro-batches | predictable, throttled processing |
| `once=True` | single batch, all data, then stops | **deprecated** |
| `availableNow=True` | multiple micro-batches over all available data, then stops | recommended for backlogs/scheduled jobs |
| `continuous="1 second"` | experimental low-latency (~1ms) continuous mode | ultra-low-latency use cases |

## Watermarking
```python
(streamDF
    .withWatermark("event_time", "15 minutes")
    .groupBy(window("event_time", "10 minutes"))
    .count())
```
- Bounds state size; drops data later than `max(event_time) - threshold`.
- Late data beyond threshold is **silently dropped**, not errored.
- Required for unbounded stateful aggregations/dedup to avoid infinite state growth.
- Use `outputMode("append")` so only finalized windows (post-watermark) emit.
