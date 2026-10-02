# Unity Catalog Governance & Security Cheat Sheet

## Object Hierarchy
| Level | Description |
|---|---|
| Metastore | Top-level governance container; one per region, attached to workspaces |
| Catalog | Top of the 3-part namespace |
| Schema | Groups tables/views (aka database) |
| Table/View | Leaf data object |

**Namespace format:** `catalog.schema.table` — metastore is NOT part of the 3-part name.

## GRANT / REVOKE Syntax
```sql
GRANT <privilege> ON <securable_type> <name> TO <principal>;
REVOKE <privilege> ON <securable_type> <name> FROM <principal>;
```
Common privileges: `USE CATALOG`, `USE SCHEMA`, `SELECT`, `MODIFY`, `CREATE TABLE`, `ALL PRIVILEGES`.

**Inheritance:** Grants flow downward — metastore → catalog → schema → table. Granting `SELECT` on a schema applies to all current AND future tables in it.

⚠️ Trap: `USAGE` is Snowflake terminology — Unity Catalog uses `USE CATALOG` / `USE SCHEMA`.
⚠️ Trap: No wildcard grants (`schema.*`) and no column-level GRANT target.

## Row-Level Security (Dynamic View)
```sql
CREATE VIEW v AS
SELECT * FROM t
WHERE is_account_group_member('region_east') OR region = current_user();
GRANT SELECT ON v TO analysts;
```

## Column Masking (Dynamic View)
```sql
CREATE VIEW masked AS
SELECT customer_id,
  CASE WHEN is_account_group_member('hr') THEN ssn ELSE 'REDACTED' END AS ssn
FROM employees;
```
Both patterns: grant access on the VIEW, never the base table.

## Lineage Graph
- Auto-captured for tables, columns, notebooks, jobs, DLT pipelines, dashboards.
- View via Catalog Explorer → object → **Lineage** tab.
- Use case: impact analysis ("what breaks if I change this column?").
- NOT the same as `DESCRIBE HISTORY` (single-table version history only).

## External Locations & Storage Credentials
| Object | Purpose | Create Order |
|---|---|---|
| Storage Credential | Wraps cloud IAM role/service principal | 1st |
| External Location | Registers cloud path + references credential | 2nd |

```sql
CREATE STORAGE CREDENTIAL cred WITH (...);
CREATE EXTERNAL LOCATION loc URL 's3://bucket/path' WITH (STORAGE CREDENTIAL cred);
```
⚠️ Trap: DBFS mounts bypass UC governance — avoid for UC-managed access.

## Legacy Table ACLs vs Unity Catalog
| Feature | Legacy (Hive) ACLs | Unity Catalog |
|---|---|---|
| Scope | Single workspace | Account-wide, cross-workspace |
| Lineage | None | Automatic, built-in |
| Row/Column security | Not supported | Dynamic views |
| Storage access | Direct keys/mounts | Storage credentials + external locations |

**Exam focus:** grant inheritance direction, dynamic-view syntax for masking/filtering, and that lineage is automatic and cross-object.
