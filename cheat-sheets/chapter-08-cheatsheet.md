## Lakeflow Jobs Cheat Sheet

### Task Types
| Task Type | Use Case |
|---|---|
| Notebook | Run a Databricks notebook |
| Python script / wheel | Run standalone Python code/package |
| SQL | Run a query, file, alert, or dashboard refresh |
| dbt | Run dbt commands against a dbt project |
| Pipeline | Run a Lakeflow Declarative Pipeline (formerly DLT) |
| JAR / Spark Submit | Run compiled Scala/Java or raw Spark jobs |
| Run Job | Trigger another job as a nested task |
| If/else, For each | Conditional branching / looping over a list |

### Trigger Types
| Trigger | Behavior |
|---|---|
| Scheduled | Cron-based fixed cadence |
| File arrival | Starts run when new files detected in a monitored storage location |
| Continuous | Job always has one active run; auto-restarts on failure |

### DAG Dependency Conditions
- Default: **All succeeded** — task runs only if every upstream task succeeded.
- Alternatives: At least one succeeded, All done, At least one failed, All failed — used for cleanup/error-handling branches.
- A task with no 'depends on' entries runs independently of failures elsewhere in the DAG.

### Retry Policy
- **Maximum retries**: extra attempts after the first failure.
- **Minimum retry interval**: wait time between attempts.
- **Retry on timeout**: optionally retry if the task times out.
- Total attempts = 1 (initial) + max retries.

### Alerting
| Type | Use Case |
|---|---|
| Email notification | Notify on run start / success / failure / streaming backlog |
| Webhook (system destination) | Integrate with Slack, Teams, PagerDuty, generic webhook |

### Parameters
- **Job parameters**: defined at job level, referenced as `{{job.parameters.<name>}}`; can be overridden per task.
- **Dynamic value references**: e.g., `{{job.start_time}}`, `{{job.id}}` for runtime context.

### Passing Data Between Tasks
```python
# Producer task
dbutils.jobs.taskValues.set(key="row_count", value=42)

# Consumer task
count = dbutils.jobs.taskValues.get(taskKey="ProducerTaskName", key="row_count", default=0, debugValue=0)
```
Use task values for small metadata only — not large datasets.

### Exam Traps
- Confusing 'depends on: All succeeded' with skipping unrelated independent tasks — independent tasks are unaffected by sibling failures.
- Assuming Continuous trigger jobs stop permanently on failure — they auto-restart.
- Forgetting total attempts = 1 + max retries, not just max retries.
