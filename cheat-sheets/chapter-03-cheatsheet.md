## Chapter 3 Cheat Sheet — Batch Ingestion

### COPY INTO Syntax
```sql
COPY INTO target_table
FROM 's3://bucket/path/'
FILEFORMAT = CSV
PATTERN = '*.csv'
FORMAT_OPTIONS ('header' = 'true', 'inferSchema' = 'true')
COPY_OPTIONS ('mergeSchema' = 'true', 'force' = 'false');
```

### COPY_OPTIONS Keys
| Option | Effect |
|---|---|
| force = true | Reprocess already-loaded files (breaks idempotency) |
| mergeSchema = true | Allow new source columns to be added to target |

### Idempotency
- COPY INTO tracks loaded files via metadata tied to Delta transaction log.
- Rerunning the same command **skips already-loaded files** — safe by default.
- Use `VALIDATE` clause to dry-run without loading.

### Format-Specific Read Options
| Format | Key Options | Notes |
|---|---|---|
| CSV | header, inferSchema, delimiter, multiLine, escape | Not self-describing; defaults to all-string, no header without options |
| JSON | multiLine, inferSchema (default true) | Self-describing per record; inference requires a scan pass |
| Parquet | (none needed) | Schema embedded in file footer; no inference scan needed |

### Schema Inference vs Explicit Schema
| Aspect | Inference | Explicit |
|---|---|---|
| Read cost | Extra scan pass (CSV/JSON) | None — applied immediately |
| Consistency | May misinfer types across files | Guaranteed types |
| Definition | `.option("inferSchema","true")` | `StructType(...)` or DDL string via `.schema()` |

### COPY INTO vs Auto Loader vs Lakeflow Connect vs Partner Connect
| Feature | Best For |
|---|---|
| COPY INTO | Batch loads from cloud storage, low-moderate file counts (thousands) |
| Auto Loader | Streaming/incremental file ingestion, scales to millions of files |
| Lakeflow Connect | Managed connectors for SaaS/DB sources (Salesforce, Workday, ServiceNow, SharePoint) |
| Partner Connect | Quick-linking third-party partner tools (Fivetran, dbt) to workspace |

### Common Exam Traps
- Forgetting COPY INTO is idempotent by default (assuming reruns duplicate data).
- Confusing 'force' (reprocess files) with 'mergeSchema' (schema evolution).
- Assuming JSON/CSV auto-infer schema with zero configuration — CSV needs explicit options.
- Mixing up Lakeflow Connect (SaaS/DB connectors) with Auto Loader (cloud file ingestion).
