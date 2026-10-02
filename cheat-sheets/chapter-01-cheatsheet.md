## Chapter 1 Cheat Sheet: Platform Architecture, Workspace, Compute

### Control Plane vs Data Plane
| Plane | Hosted By | Contains | Notes |
|---|---|---|---|
| Control Plane | Databricks | Workspace UI, notebooks (code/metadata), job scheduler, cluster manager | Never touches raw customer data |
| Data Plane (classic) | Customer's cloud account | Driver + worker VMs, actual data processing | Required for compliance where compute must stay in-account |
| Data Plane (serverless) | Databricks | Managed compute for serverless SQL/Jobs/Notebooks | No customer VM management, instant start |

### Compute Types
| Type | Use Case | Lifecycle | DBU Rate |
|---|---|---|---|
| All-purpose cluster | Interactive, shared notebooks | Manual start/stop or autotermination | Highest |
| Job cluster | Scheduled/automated Job runs | Auto-created and auto-terminated per run | Lower than all-purpose |
| Serverless compute | Instant-start SQL, Jobs, notebooks | Fully managed by Databricks | Varies, no cluster mgmt overhead |

### Autotermination
- Minimum allowed: **10 minutes**
- UI-suggested default: 120 minutes of inactivity
- Exam trap: don't confuse minimum (10) with suggested default (120)

### Databricks SQL Warehouse Types
| Type | Data Plane | Startup | Management |
|---|---|---|---|
| Serverless | Databricks-managed | Near-instant | None required |
| Pro | Classic (customer account) | Slower | Adds predictive I/O features |
| Classic | Classic (customer account) | Slower | Full customer-managed compute |

### Magic Commands
| Command | Purpose |
|---|---|
| %python / %sql / %scala / %r | Language override for a cell |
| %md | Render cell as Markdown |
| %fs | dbutils.fs shorthand (filesystem ops) |
| %sh | Shell commands on the driver node |
| %run | Execute another notebook, import its context |
| %pip | Install Python packages scoped to notebook |

### Workspace Object Hierarchy
Workspace (root) → Folders → Notebooks / Git folders (Repos) / Libraries / Files

### Git Folders (Repos)
- Sync notebooks/files with GitHub, GitLab, Bitbucket, Azure DevOps
- Supports clone, commit, push, pull directly in workspace UI
- Not a compute construct — purely version control integration

### Exam Gotchas
1. Job clusters ≠ all-purpose clusters for cost — exam scenarios about scheduled/automated pipelines expect **job cluster**.
2. "Instant start, no infra management" scenario language → **serverless**, not classic.
3. Compliance requiring compute inside customer's own cloud account → **classic (not serverless) compute**.
