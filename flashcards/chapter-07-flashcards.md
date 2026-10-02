## Chapter 7: Silver/Gold Modeling: Views, Tables, and Materialized Views — Flashcards

| # | Front (Question) | Back (Answer) |
|---|-----------------|---------------|
| 1 | DROP TABLE on a MANAGED table — what happens to data? | Both metadata and underlying data files are deleted. Exam tip: managed = no LOCATION clause at creation. |
| 2 | DROP TABLE on an EXTERNAL table — what happens to data? | Only the metastore entry is removed; data files at the external LOCATION remain untouched. Exam tip: created with explicit LOCATION clause. |
| 3 | What is a standard VIEW in terms of storage and execution? | A stored query with no persisted data; fully recomputed against source tables on every read — always current, but costly at high query volume. |
| 4 | What is a MATERIALIZED VIEW? | A precomputed, stored result set that is refreshed periodically/incrementally (e.g., via Lakeflow Declarative Pipelines). Faster reads, some data staleness. |
| 5 | View vs Materialized View — freshness vs performance tradeoff? | View: always fresh, recomputed every query (slow at scale). Materialized view: fast reads, but data can be stale until next refresh. |
| 6 | Can CTAS (CREATE TABLE AS SELECT) declare an explicit schema or constraints? | No — CTAS infers schema entirely from the SELECT query result. Constraints must be added afterward via ALTER TABLE. |
| 7 | CTAS syntax with format and partitioning? | CREATE TABLE name USING DELTA PARTITIONED BY (col) AS SELECT ... — USING and PARTITIONED BY go before AS SELECT. |
| 8 | Syntax to add a CHECK constraint to an existing Delta table? | ALTER TABLE t ADD CONSTRAINT name CHECK (condition); — enforced on all future writes. |
| 9 | Are NOT NULL and CHECK constraints enforced in Delta Lake? | Yes — both are enforced; writes violating them fail. Exam trap: don't confuse with PRIMARY KEY/FOREIGN KEY. |
| 10 | Are PRIMARY KEY and FOREIGN KEY constraints enforced in Delta Lake? | No — they are informational/documentation only, not enforced at write time. Common exam trap: assuming they behave like RDBMS constraints. |
| 11 | Typical gold-layer object choice for a frequently-queried aggregated business metric? | Materialized view — precomputed aggregation, refreshed periodically, avoids recomputation cost on every dashboard hit. |
| 12 | What mechanism refreshes a materialized view in Databricks? | A managed pipeline (Lakeflow Declarative Pipelines / DLT) or scheduled refresh maintains materialized views, refreshing incrementally where possible. |
