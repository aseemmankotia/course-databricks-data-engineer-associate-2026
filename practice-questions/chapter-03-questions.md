## Chapter 3: Batch Ingestion: Formats, COPY INTO, and Managed Connectors — Practice Questions

**Q1.** A data engineer runs a COPY INTO command to load files from cloud storage into a Delta table. The job fails halfway through due to a cluster restart. The engineer reruns the exact same COPY INTO command. What happens?

A) All files in the source location are reloaded, creating duplicate rows for files already committed
B) COPY INTO skips files that were already successfully loaded and only loads the remaining new files
C) The command fails because COPY INTO cannot be run twice against the same table
D) COPY INTO truncates the target table before reloading all files from the source location

<details>
<summary>Answer</summary>

**Correct: B**

COPY INTO tracks which files have already been loaded into the target table using metadata stored alongside the Delta transaction log, so reruns are idempotent and only process files not yet committed.

**Why the others are wrong:**
- This describes non-idempotent behavior, which contradicts COPY INTO's core file-tracking design
- COPY INTO is explicitly designed to be safely rerun; it does not error out on repeated execution
- COPY INTO never truncates the target table automatically — that would require a separate DELETE/overwrite operation

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q2.** A company needs to ingest roughly 800 CSV files per day, arriving once daily as a batch, into a Delta table. Which approach is the BEST fit?

A) Auto Loader with cloudFiles format and a continuous streaming job
B) Lakeflow Connect managed connector
C) COPY INTO run as a scheduled batch job
D) Partner Connect integration with a third-party ETL tool

<details>
<summary>Answer</summary>

**Correct: C**

COPY INTO is designed for batch loading from cloud storage at moderate file counts (low thousands) and is simpler to operate than Auto Loader when there is no need for continuous, low-latency streaming ingestion.

**Why the others are wrong:**
- Auto Loader adds streaming/checkpoint complexity that is unnecessary for a once-daily batch of a few hundred files, and it is optimized for scaling to millions of files
- Lakeflow Connect targets SaaS/database source connectors (e.g., Salesforce, Workday), not raw file ingestion from cloud storage
- Partner Connect is for linking third-party partner tools to a workspace, not for direct file ingestion

</details>

---

**Q3.** An engineer loads a CSV file with `spark.read.csv(path)` without specifying options. The resulting DataFrame shows all columns as strings, and the header row appears as the first data row. What is the cause?

A) The default options do not enable header recognition or schema inference, so both must be explicitly set
B) CSV files cannot have schemas inferred under any circumstances
C) Spark automatically infers schema for CSV but never for JSON or Parquet
D) The file must be converted to Parquet before schema inference will work

<details>
<summary>Answer</summary>

**Correct: A**

CSV is not self-describing, so by default Spark treats the first row as data and assigns StringType to all columns unless `.option("header", "true")` and `.option("inferSchema", "true")` are explicitly set.

**Why the others are wrong:**
- Schema inference works for CSV, just not by default without the inferSchema option
- Parquet embeds schema natively (always inferred automatically) and JSON is self-describing and also supports inference by default — this reverses the actual behavior
- Converting to Parquet is unnecessary; setting the correct read options solves the problem directly

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q4.** A company wants to continuously sync data from Salesforce into Delta tables with minimal custom code and built-in schema handling. Which Databricks feature is the BEST fit?

A) COPY INTO with a scheduled job
B) Auto Loader with cloudFiles.format set to 'json'
C) Partner Connect linked to a generic JDBC driver
D) A Lakeflow Connect managed connector for Salesforce

<details>
<summary>Answer</summary>

**Correct: D**

Lakeflow Connect provides managed, native connectors for SaaS and database sources such as Salesforce, ServiceNow, Workday, and SharePoint, handling authentication, schema, and incremental sync automatically.

**Why the others are wrong:**
- COPY INTO ingests files from cloud object storage, not directly from a SaaS API like Salesforce
- Auto Loader ingests files landing in cloud storage; it has no native Salesforce API integration
- Partner Connect helps link partner tools to a workspace but is not itself a managed data connector for Salesforce

</details>

---

**Q5.** A team loads Parquet files into a Delta table but a similarly structured JSON dataset requires an extra read-time cost that the Parquet load does not. What explains this difference?

A) JSON files cannot be read by Spark without a custom parser
B) Parquet embeds schema metadata in the file itself, while JSON schema must be inferred by scanning records, adding overhead
C) Parquet always requires an explicit schema, while JSON never does
D) JSON files are always larger than Parquet, causing the extra cost

<details>
<summary>Answer</summary>

**Correct: B**

Parquet stores its schema in file footers, so no data scan is needed to determine types. JSON is self-describing per record but Spark must sample/scan records to infer a unified schema, which adds a read pass.

**Why the others are wrong:**
- Spark has native JSON support via spark.read.json without any custom parser
- This reverses the actual behavior — Parquet does NOT require an explicit schema because it's embedded, while JSON schema inference is optional but costly
- File size is unrelated to the schema-inference overhead being described

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q6.** A COPY INTO job needs to load new files whose schema has an additional column compared to the existing target Delta table. Which COPY_OPTIONS setting allows this to succeed without manually altering the table first?

A) 'force' = 'true'
B) 'mergeSchema' = 'true'
C) 'overwriteSchema' = 'true'
D) 'validate' = 'true'

<details>
<summary>Answer</summary>

**Correct: B**

Setting COPY_OPTIONS ('mergeSchema' = 'true') allows COPY INTO to evolve the target table schema by adding new columns found in the source files.

**Why the others are wrong:**
- 'force' = 'true' forces reloading of already-loaded files; it does not handle schema evolution
- 'overwriteSchema' is a Delta write-mode option, not a valid COPY INTO COPY_OPTIONS key
- VALIDATE is a clause used to dry-run a COPY INTO statement, not a schema evolution option

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q7.** Which FORMAT_OPTIONS setting is required in a COPY INTO statement to correctly parse a CSV file where the first row contains column names?

A) 'inferSchema' = 'true'
B) 'multiLine' = 'true'
C) 'header' = 'true'
D) 'mergeSchema' = 'true'

<details>
<summary>Answer</summary>

**Correct: C**

FORMAT_OPTIONS ('header' = 'true') tells COPY INTO to treat the first row of each CSV file as column headers rather than data.

**Why the others are wrong:**
- inferSchema controls type inference, not header recognition, and is not a standard COPY INTO CSV format option
- multiLine controls whether records can span multiple lines (relevant to JSON/CSV with embedded newlines), not header parsing
- mergeSchema is a COPY_OPTIONS setting for schema evolution, not a CSV FORMAT_OPTIONS setting

</details>

---

**Q8.** What is the key practical difference between schema inference and explicit schema definition when reading source files in Databricks?

A) Explicit schema definition is only possible for Parquet files
B) Schema inference requires Spark to scan data to determine types, while explicit schema is supplied upfront, avoiding the extra scan and guaranteeing type consistency
C) Schema inference always produces more accurate types than an explicit schema
D) Explicit schema definition cannot be used with COPY INTO

<details>
<summary>Answer</summary>

**Correct: B**

Explicit schemas (via StructType or DDL string) are provided by the developer and applied immediately, skipping the inference scan and avoiding inconsistent type guesses across files — a common exam distinction.

**Why the others are wrong:**
- Explicit schemas can be defined for CSV, JSON, and Parquet reads alike
- Inference can misclassify types (e.g., mixed nulls/numbers as strings), so it is not inherently more accurate than a well-defined explicit schema
- COPY INTO supports explicit schema definition on the target table before load

</details>

---

