## Chapter 4 Cheat Sheet — Auto Loader & Schema Evolution

### Core Syntax
```python
df = (spark.readStream
  .format("cloudFiles")
  .option("cloudFiles.format", "json")
  .option("cloudFiles.schemaLocation", "/path/schema")
  .option("cloudFiles.schemaEvolutionMode", "addNewColumns")
  .load("/path/source"))

(df.writeStream
  .option("checkpointLocation", "/path/checkpoint")
  .trigger(availableNow=True)
  .toTable("my_table"))
```

### File Discovery Modes
| Mode | Default? | How it works | Best for |
|---|---|---|---|
| Directory listing | Yes | Lists source path each batch | Low/medium file volume |
| File notification | No — opt-in (`cloudFiles.useNotifications=true`) | Cloud event queue (SQS/SNS, Event Grid) | Very high file volume, many nested dirs |

### Schema Evolution Modes (`cloudFiles.schemaEvolutionMode`)
| Mode | Behavior | New columns | Stream on drift |
|---|---|---|---|
| `addNewColumns` (default) | Adds new column to schema | Added | Fails once, auto-resumes |
| `rescue` | Captures unexpected data | Not added as real column | Continues running |
| `failOnNewColumns` | Requires manual fix | Not added | Fails, stays failed |
| `none` | Ignores drift | Dropped silently | Continues running |

### _rescued_data Column
- Captures type mismatches / unexpected fields as JSON.
- Present automatically when schema inference is used, or explicitly under `rescue` mode.
- Prevents silent data loss.

### Checkpoint Location
- Stores: processing offsets/state + inferred schema.
- Enables exactly-once semantics across restarts.
- One checkpoint per stream — never share between streams.

### Trigger Types
| Trigger | Behavior | Stops automatically? |
|---|---|---|
| `Trigger.Once()` | Single large batch of all available data | Yes (legacy) |
| `Trigger.AvailableNow()` | Multiple micro-batches of all available data | Yes (recommended) |
| `Trigger.ProcessingTime(...)` | Fixed interval micro-batches | No |
| `Trigger.Continuous()` | Low-latency continuous processing | No |

### Exam Traps
1. Assuming `addNewColumns` never interrupts the stream — it fails once intentionally to update schema.
2. Confusing `rescue` (keeps running, captures data) with `failOnNewColumns` (stops, needs manual fix).
3. Thinking file notification mode is the default — it is NOT; directory listing is default.
4. Forgetting `Trigger.Once()` is legacy in favor of `Trigger.AvailableNow()` for catch-up batch jobs.
