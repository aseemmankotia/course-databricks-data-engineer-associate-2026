## Chapter 11: Unity Catalog Governance and Security — Practice Questions

**Q1.** A retail company wants sales analysts to see only rows matching their assigned region, while regional managers see every row. Which approach correctly implements this in Unity Catalog?

A) Apply a table ACL with a row-level GRANT restricting SELECT to matching rows
B) Create a dynamic view with a WHERE clause using is_account_group_member() to filter rows, then grant SELECT on the view
C) Create a separate physical table per region and grant SELECT on the matching table only
D) Use a column-mask CASE WHEN expression to hide the region column

<details>
<summary>Answer</summary>

**Correct: B**

Unity Catalog implements row-level security exclusively through dynamic views: a WHERE clause checks group membership or current_user() and users query the view instead of the base table.

**Why the others are wrong:**
- GRANT statements operate at the object level (table/schema/catalog); there is no row-level GRANT syntax in Unity Catalog.
- Duplicating physical tables per region is not scalable and is not the governance mechanism Unity Catalog provides or the exam expects.
- Column masking hides column values, not rows — it doesn't solve a row-filtering requirement.

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q2.** A finance company must let only the 'finance-admins' group see full credit card numbers; everyone else should see a masked value. Which implementation is correct?

A) GRANT SELECT ON credit_card_number TO finance-admins; REVOKE SELECT ON credit_card_number FROM all_users
B) ALTER TABLE cards ALTER COLUMN credit_card_number SET MASKED
C) GRANT SELECT ON TABLE cards(credit_card_number) TO finance-admins
D) CREATE VIEW masked_cards AS SELECT customer_id, CASE WHEN is_account_group_member('finance-admins') THEN credit_card_number ELSE CONCAT('****-****-****-', RIGHT(credit_card_number,4)) END AS credit_card_number FROM cards; grant SELECT on the view to all users

<details>
<summary>Answer</summary>

**Correct: D**

Column-level masking is implemented with a dynamic view that conditionally returns the real or masked value based on group membership; users are granted access to the view, not the raw table.

**Why the others are wrong:**
- Unity Catalog GRANT statements do not support targeting an individual column name as the securable object.
- There is no ALTER TABLE ... SET MASKED syntax in standard Unity Catalog SQL for this exam's scope.
- GRANT SELECT ON TABLE table(column) is not valid Unity Catalog GRANT syntax.

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q3.** A company attaches dev, staging, and prod workspaces to the same Unity Catalog metastore. An engineer grants SELECT on catalog.schema.table to a group while working in the dev workspace. What happens in staging and prod?

A) The grant applies only in dev because ACLs are workspace-scoped
B) The grant applies immediately in all three workspaces because privileges are stored at the metastore level, not the workspace level
C) The grant must be manually re-issued in staging and prod to take effect
D) The grant is rejected because only the catalog owner can grant across workspaces

<details>
<summary>Answer</summary>

**Correct: B**

Unity Catalog privileges are stored centrally in the metastore that catalogs belong to. Since all three workspaces are attached to the same metastore, the grant is visible and enforced everywhere immediately.

**Why the others are wrong:**
- This describes legacy Hive metastore table ACLs, which ARE workspace-scoped — Unity Catalog is not.
- No re-issuing is needed since the grant lives in the shared metastore, not per-workspace metadata.
- Any principal with the appropriate GRANT privilege (not only the owner) can grant, and it is not blocked cross-workspace.

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q4.** Which Unity Catalog feature should you use to see how a downstream ML dashboard column depends on an upstream raw ingestion table?

A) DESCRIBE HISTORY on the raw table
B) Unity Catalog audit logs
C) Legacy Hive metastore table ACLs
D) Unity Catalog lineage graph in Catalog Explorer

<details>
<summary>Answer</summary>

**Correct: D**

The lineage graph automatically captures table- and column-level upstream/downstream dependencies across notebooks, jobs, pipelines, and dashboards, making it the correct tool for impact analysis.

**Why the others are wrong:**
- DESCRIBE HISTORY only shows version history for a single Delta table, not cross-object dependencies.
- Audit logs record who accessed what and when — they do not show data flow dependencies.
- Legacy table ACLs have no lineage capability at all.

</details>

---

**Q5.** A team needs centralized, account-wide access control across multiple workspaces for a Delta table, along with automatic lineage tracking and row-level filtering. Which governance model satisfies this?

A) Legacy Hive metastore table ACLs
B) Cluster-level access control lists
C) Unity Catalog
D) AWS IAM instance profiles attached to clusters

<details>
<summary>Answer</summary>

**Correct: C**

Unity Catalog is the only option providing centralized, account-level governance across workspaces plus built-in lineage and dynamic-view based row/column security.

**Why the others are wrong:**
- Legacy table ACLs are workspace-scoped with no lineage or cross-workspace grants.
- Cluster ACLs control who can attach to or manage a cluster, not data-object access.
- IAM instance profiles control cloud storage access at the compute level, not fine-grained data governance.

</details>

---

**Q6.** Assuming the group 'analysts' already has USE CATALOG and USE SCHEMA privileges, which statement correctly grants SELECT on every current and future table in the schema main.sales?

A) GRANT SELECT ON SCHEMA main.sales TO analysts;
B) GRANT SELECT ON TABLE main.sales.* TO analysts;
C) ALTER SCHEMA main.sales GRANT SELECT TO analysts;
D) GRANT USAGE ON SCHEMA main.sales TO analysts;

<details>
<summary>Answer</summary>

**Correct: A**

Granting a privilege at the schema level in Unity Catalog cascades (is inherited) to all tables currently in the schema and any created afterward.

**Why the others are wrong:**
- Wildcard syntax (schema.*) is not valid in Unity Catalog GRANT statements.
- ALTER SCHEMA ... GRANT is not valid Unity Catalog GRANT syntax.
- USAGE is a Snowflake privilege name; Unity Catalog's equivalent privilege is USE SCHEMA, not USAGE.

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q7.** To allow Unity Catalog to read and write data in an external S3 bucket without exposing AWS keys to end users, which two objects must an admin create, and in what order?

A) Create a STORAGE CREDENTIAL wrapping an IAM role first, then an EXTERNAL LOCATION referencing that credential and the S3 path
B) Create an EXTERNAL LOCATION first, then a STORAGE CREDENTIAL that references the location's path
C) Create a DBFS mount point, then grant SELECT on the mount
D) Embed the IAM access key and secret directly in the CREATE TABLE LOCATION clause

<details>
<summary>Answer</summary>

**Correct: A**

A storage credential encapsulates the cloud IAM role/service principal and must exist before an external location can reference it to register the storage path for governed access.

**Why the others are wrong:**
- The dependency order is reversed — an external location cannot exist without a storage credential to reference.
- DBFS mounts bypass Unity Catalog's centralized governance model and are discouraged for UC-managed access.
- Embedding raw credentials in SQL statements is insecure and defeats the purpose of storage credentials.

</details>

---

**Q8.** What is the correct hierarchical order of Unity Catalog's governance object model, from the topmost container down to a table?

A) Workspace > Catalog > Schema > Table
B) Account > Metastore > Table > Schema
C) Catalog > Metastore > Schema > Table
D) Metastore > Catalog > Schema > Table

<details>
<summary>Answer</summary>

**Correct: D**

The metastore is the top-level governance container that holds catalogs; catalogs contain schemas, which contain tables — this forms the basis of the catalog.schema.table three-level namespace.

**Why the others are wrong:**
- A workspace is not part of the object hierarchy; it is merely attached to a metastore.
- Schema and table order is reversed and metastore/table relationship is incorrect.
- Metastore cannot come after catalog — it is the container that holds catalogs, not the reverse.

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

