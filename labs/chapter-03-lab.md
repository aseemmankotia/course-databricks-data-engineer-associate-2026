# Chapter 3 Lab: Batch Ingestion: Formats, COPY INTO, and Managed Connectors

**Estimated time:** 15 minutes

## Exercise

Use COPY INTO to load a folder of CSV files into a bronze Delta table, re-run the same COPY INTO statement, and verify no duplicate rows are loaded.

## Key takeaway

COPY INTO gives idempotent, incremental batch loading by tracking already-ingested files, while Lakeflow Connect handles managed source connectivity.
