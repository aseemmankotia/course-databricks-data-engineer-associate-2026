## Chapter 4: Streaming Ingestion with Auto Loader and Schema Evolution — Flashcards

| # | Front (Question) | Back (Answer) |
|---|-----------------|---------------|
| 1 | What format string enables Auto Loader in spark.readStream? | 'cloudFiles'. Exam tip: this is the single most tested syntax element in the ingestion domain. |
| 2 | Directory listing vs file notification mode — key difference? | Directory listing (default) lists the source path each batch; simpler, no extra setup. File notification uses cloud queue/event services (SQS/SNS, Event Grid) — scales better for very high file volumes but needs extra cloud permissions/resources. |
| 3 | Default cloudFiles.schemaEvolutionMode value? | addNewColumns — new columns are added automatically; the stream fails once to pick up the new schema, then resumes (auto-restarts if job retries are configured). |
| 4 | schemaEvolutionMode = 'rescue' behavior? | New/mismatched data is captured in the _rescued_data column as JSON instead of being added as a real schema column. Stream keeps running without failing. |
| 5 | schemaEvolutionMode = 'failOnNewColumns' behavior? | Stream fails and stops on any new column; requires manual schema update before restart. Use for regulated/strict pipelines needing human review of drift. |
| 6 | schemaEvolutionMode = 'none' behavior? | New columns are silently ignored/dropped — no failure, no rescue capture. Riskiest option: can cause silent data loss. |
| 7 | What does the _rescued_data column store? | Data that doesn't match the inferred schema (type mismatches, extra/unexpected fields), stored as a JSON string so no data is lost. |
| 8 | Purpose of the checkpoint location in Auto Loader streams? | Stores processed-file state/offsets and inferred schema, enabling exactly-once processing and correct resume after restart. |
| 9 | Trigger.Once() vs Trigger.AvailableNow() — which is recommended? | AvailableNow() is recommended: processes all available data across multiple micro-batches (respects batch-size limits like maxFilesPerTrigger) then stops. Once() forces a single large batch and is considered the legacy option. |
| 10 | Which trigger type keeps the stream running indefinitely with low latency? | Trigger.Continuous() — processes data continuously; does NOT stop automatically like Once()/AvailableNow(). |
| 11 | How do you enable file notification mode? | Set option 'cloudFiles.useNotifications' = 'true' plus configure cloud-specific resources (e.g., AWS SNS/SQS, Azure Event Grid/Queue Storage) and permissions for Auto Loader to create/manage them. |
| 12 | Exam gotcha: does addNewColumns mode ever stop the stream? | Yes — briefly. It intentionally fails the stream once when a new column is first detected so it can update the schema, then it resumes (automatically if job retries are set). Students often wrongly assume it never interrupts processing. |
