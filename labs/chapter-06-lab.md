# Chapter 6 Lab: Incremental Processing: MERGE and Structured Streaming

**Estimated time:** 20 minutes

## Exercise

Write a Structured Streaming job that reads from a bronze table and uses foreachBatch to MERGE upserts into a silver Delta table, then verify updated and inserted rows in a single run.

## Key takeaway

MERGE INTO combined with foreachBatch is the standard pattern for applying incremental upserts within a Structured Streaming pipeline.
