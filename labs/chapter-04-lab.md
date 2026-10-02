# Chapter 4 Lab: Streaming Ingestion with Auto Loader and Schema Evolution

**Estimated time:** 20 minutes

## Exercise

Configure an Auto Loader stream reading JSON files into a bronze Delta table with schema evolution enabled, add a new column to incoming files mid-stream, and observe the _rescued_data behavior.

## Key takeaway

Auto Loader's cloudFiles format combined with a durable checkpoint gives incremental, schema-evolving streaming ingestion with exactly-once guarantees.
