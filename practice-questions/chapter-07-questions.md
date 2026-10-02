## Chapter 7: Silver/Gold Modeling: Views, Tables, and Materialized Views — Practice Questions

**Q1.** A data engineer runs CREATE TABLE sales_ext USING DELTA LOCATION 's3://company-data/sales/' AS SELECT * FROM bronze.sales. Later they run DROP TABLE sales_ext. What happens to the Parquet/Delta files at s3://company-data/sales/?

A) The files are permanently deleted immediately
B) The files are moved to the Unity Catalog trash folder for 30 days
C) The files remain in place; only the table's metadata entry is removed from the metastore
D) The command fails because external tables cannot be dropped

<details>
<summary>Answer</summary>

**Correct: C**

Because a LOCATION clause was specified, this is an external table. Dropping an external table only removes the catalog/metastore entry; the underlying data files are untouched.

**Why the others are wrong:**
- Immediate deletion of data is the behavior of a MANAGED table, not an external one
- There is no metastore 'trash folder' mechanism for external table data on DROP
- DROP TABLE works normally on external tables; it simply doesn't touch the data

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q2.** A gold-layer dashboard runs the same aggregation query thousands of times per hour against terabytes of silver data. Analysts accept results being up to 15 minutes stale. Which object type BEST fits this requirement?

A) A standard view
B) A temporary view
C) A materialized view
D) A CTAS-created table refreshed manually once per day

<details>
<summary>Answer</summary>

**Correct: C**

A materialized view precomputes and periodically refreshes results, giving fast repeated reads on large aggregations while tolerating some staleness — exactly the tradeoff described.

**Why the others are wrong:**
- A standard view recomputes the full aggregation on every query, which is expensive at thousands of queries/hour
- A temporary view is session-scoped and unsuitable for a shared dashboard
- A manually-refreshed CTAS table requires custom orchestration and won't meet the 15-minute freshness need automatically

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q3.** A data engineer wants to create silver.customers from bronze.raw_customers using CTAS, and also enforce that the email column can never be NULL. What is the correct approach?

A) Add a NOT NULL clause inside the CTAS SELECT statement
B) CTAS automatically infers and applies NOT NULL from the source column
C) Run the CTAS statement to create the table, then run ALTER TABLE silver.customers ALTER COLUMN email SET NOT NULL
D) Use CREATE TABLE with explicit schema and constraints instead of CTAS, since CTAS supports constraints natively

<details>
<summary>Answer</summary>

**Correct: C**

CTAS infers schema from the query result and does not support declaring constraints in the same statement. Constraints must be added afterward with ALTER TABLE.

**Why the others are wrong:**
- CTAS syntax has no clause for column constraints inside the SELECT
- CTAS never carries over constraint metadata from the source table
- This is backwards — the limitation exists precisely because CTAS lacks constraint support, so a separate CREATE TABLE isn't required, just an ALTER TABLE afterward

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q4.** A low-traffic reporting table must always reflect the absolute latest data in the underlying silver tables, with no acceptable staleness, and query volume is only a few times per day. Which object should be used?

A) A materialized view
B) A standard view
C) A managed Delta table populated by CTAS
D) An external table

<details>
<summary>Answer</summary>

**Correct: B**

A standard view is a stored query recomputed on every read, guaranteeing zero staleness. Since query volume is low, the recomputation cost is acceptable.

**Why the others are wrong:**
- A materialized view introduces refresh latency, violating the zero-staleness requirement
- A CTAS table is a point-in-time snapshot and would go stale immediately
- An external table describes storage location, not freshness — it still requires a refresh mechanism to stay current

</details>

---

**Q5.** Which statement correctly creates a Delta table from an existing query while also partitioning the output?

A) CREATE TABLE gold.daily_sales PARTITIONED BY (order_date) AS SELECT * FROM silver.sales
B) CREATE TABLE gold.daily_sales AS SELECT * FROM silver.sales PARTITION BY order_date
C) ALTER TABLE gold.daily_sales ADD PARTITION (order_date) AS SELECT * FROM silver.sales
D) CREATE TABLE gold.daily_sales USING DELTA PARTITIONED BY (order_date) AS SELECT * FROM silver.sales

<details>
<summary>Answer</summary>

**Correct: D**

Databricks CTAS syntax allows USING <format> and PARTITIONED BY (col) clauses before AS SELECT to define storage format and partitioning at creation time.

**Why the others are wrong:**
- This omits the required USING clause ordering pattern and is not valid CTAS syntax with PARTITIONED BY placement before AS
- 'PARTITION BY' inside the SELECT is not valid SQL syntax for CTAS
- ALTER TABLE cannot be combined with AS SELECT to add partitions retroactively this way

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q6.** Which SQL statement correctly adds a CHECK constraint requiring quantity to be greater than zero on an existing Delta table?

A) CREATE CONSTRAINT positive_qty ON orders CHECK (quantity > 0)
B) ALTER TABLE orders ADD CHECK CONSTRAINT (quantity > 0)
C) ALTER TABLE orders SET CONSTRAINT quantity > 0
D) ALTER TABLE orders ADD CONSTRAINT positive_qty CHECK (quantity > 0)

<details>
<summary>Answer</summary>

**Correct: D**

Delta Lake CHECK constraints are added with ALTER TABLE ... ADD CONSTRAINT constraint_name CHECK (boolean_expression), and are enforced on future writes.

**Why the others are wrong:**
- There is no standalone CREATE CONSTRAINT statement in Delta/Spark SQL
- This reverses the required keyword order (CONSTRAINT name must precede CHECK)
- This is not valid ALTER TABLE syntax for adding a constraint

</details>

---

**Q7.** By default, when a CREATE TABLE statement is executed in Unity Catalog without a LOCATION clause, what type of table is created and what happens on DROP TABLE?

A) An external table is created; DROP TABLE removes only the metadata
B) A view is created; DROP TABLE has no effect since no data is stored
C) A managed table is created; DROP TABLE only marks it for deletion after 30 days via VACUUM
D) A managed table is created; DROP TABLE removes both metadata and the underlying data files

<details>
<summary>Answer</summary>

**Correct: D**

Without a LOCATION clause, Unity Catalog creates a managed table whose data lives in the catalog's managed storage. DROP TABLE deletes both the metadata entry and the underlying data.

**Why the others are wrong:**
- Omitting LOCATION produces a managed table, not external
- This describes a view, but CREATE TABLE without USING VIEW creates a physical table
- DROP TABLE deletes managed table data immediately (subject to normal Delta transaction log cleanup, not a 30-day VACUUM delay before deletion)

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q8.** Which best describes how a materialized view differs from a standard view in Databricks?

A) A materialized view is session-scoped, while a standard view persists across sessions
B) A materialized view can only be created on external tables, while a standard view only works on managed tables
C) There is no functional difference; materialized view is just a syntax alias for view
D) A materialized view stores precomputed results that are periodically refreshed, while a standard view recomputes its query on every read

<details>
<summary>Answer</summary>

**Correct: D**

This is the core distinction tested: materialized views persist computed results to disk and refresh on a schedule/incrementally, trading some freshness for query performance, whereas views always recompute live.

**Why the others are wrong:**
- Session-scoping describes temporary views, not materialized views
- Materialized and standard views have no such table-type restriction
- They are functionally distinct objects with different storage and refresh behavior

</details>

---

