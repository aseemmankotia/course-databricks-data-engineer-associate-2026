## Chapter 3: Batch Ingestion: Formats, COPY INTO, and Managed Connectors — Flashcards

| # | Front (Question) | Back (Answer) |
|---|-----------------|---------------|
| 1 | How does COPY INTO achieve idempotency across reruns? | It tracks which source files have already been loaded via metadata associated with the Delta transaction log, skipping already-loaded files on rerun. Exam tip: rerunning COPY INTO is safe by default — no duplicates. |
| 2 | COPY_OPTIONS setting to force COPY INTO to reload files it has already processed? | 'force' = 'true'. Exam tip: this is the ONLY way to reprocess previously loaded files — normally they're skipped. |
| 3 | COPY_OPTIONS setting to allow COPY INTO to add new columns found in source files to the target table? | 'mergeSchema' = 'true'. Exam tip: without this, new columns in source files cause the load to fail or be ignored. |
| 4 | FORMAT_OPTIONS key to treat the first row of a CSV as column headers in COPY INTO? | 'header' = 'true'. Exam tip: without it, headers are read as a data row and all columns default to string type. |
| 5 | COPY INTO vs Auto Loader — when to choose which? | COPY INTO: simple batch loads, low-to-moderate file counts (thousands). Auto Loader: continuous/streaming ingestion, scales to millions of files with cloud notification services. Exam tip: file-count scale is the deciding factor. |
| 6 | What clause lets you dry-run a COPY INTO statement without actually loading data? | VALIDATE clause (e.g., `COPY INTO ... VALIDATE ALL`). Exam tip: use it to check for parsing/schema errors before committing a real load. |
| 7 | Why does Parquet not require explicit schema definition on read? | Parquet embeds schema metadata directly in the file footer, so Spark reads it automatically. Exam tip: Parquet = self-describing, no inference scan needed. |
| 8 | Why is JSON schema inference more expensive than reading Parquet? | JSON is self-describing per-record but has no stored unified schema, so Spark must scan (sample) records to infer a merged schema — an extra read pass. Exam tip: for large JSON sets, provide an explicit schema to skip this cost. |
| 9 | Lakeflow Connect — what is it for? | Managed connectors for ingesting data directly from SaaS applications and databases (e.g., Salesforce, Workday, ServiceNow, SharePoint) into Delta tables with built-in schema/incremental handling. Exam tip: distinguish from Auto Loader (files) and COPY INTO (files). |
| 10 | Partner Connect — what is it for? | A workspace feature to quickly connect third-party partner tools (e.g., Fivetran, dbt, Rudderstack) to a Databricks workspace with minimal configuration. Exam tip: it's about linking external partner tools, not a native ingestion connector itself. |
| 11 | FORMAT_OPTIONS key to handle CSV/JSON records that span multiple lines? | 'multiLine' = 'true'. Exam tip: needed when a single record (row) contains embedded line breaks, otherwise parsing breaks mid-record. |
| 12 | What option in COPY INTO restricts which files in a source directory are loaded? | The 'pattern' option (e.g., PATTERN = '*.csv'). Exam tip: use it to filter file types/paths within a shared source location. |
