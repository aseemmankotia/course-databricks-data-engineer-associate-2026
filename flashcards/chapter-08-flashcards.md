## Chapter 8: Orchestrating Pipelines with Lakeflow Jobs — Flashcards

| # | Front (Question) | Back (Answer) |
|---|-----------------|---------------|
| 1 | List the task types available when building a Lakeflow Job. | Notebook, Python script, Python wheel, SQL, dbt, Pipeline, JAR, Spark Submit, Run Job (nested job), If/else condition, For each. Exam tip: Pipeline task = runs a Lakeflow Declarative Pipeline (formerly DLT). |
| 2 | What are the three trigger types for a Lakeflow Job? | Scheduled (cron-based), File arrival (event-based on new files in a storage location), Continuous (job always has an active run and auto-restarts on failure). |
| 3 | Default 'depends on' condition between tasks in a DAG? | 'All succeeded' — a task only runs after ALL upstream dependencies succeed. Alternate conditions exist (e.g., 'At least one succeeded', 'All done') for branching/error-handling logic. |
| 4 | What happens on Continuous trigger jobs when the active run fails? | Databricks automatically starts a new run to keep the job continuously active — no manual intervention or schedule wait needed. |
| 5 | Retry policy settings available on a task. | Maximum retries (number of extra attempts after initial failure), Minimum retry interval (wait time between attempts), and 'Retry on timeout' option. Total attempts = 1 + max retries. |
| 6 | Two notification mechanisms for job outcomes. | Email notifications (start/success/failure/streaming backlog) and Webhook (system destination) notifications for integrations like Slack, Teams, or PagerDuty. |
| 7 | Syntax to reference a job-level parameter in a task field. | {{job.parameters.<param_name>}} — resolves dynamically at run time; job parameters can be overridden per task. |
| 8 | How does a task pass a value to a downstream dependent task? | dbutils.jobs.taskValues.set(key, value) in the producing task; dbutils.jobs.taskValues.get(taskKey, key, default=..., debugValue=...) in the consuming task. |
| 9 | Task values best practice: what should they store? | Small key-value metadata (row counts, file paths, flags) — not large datasets. Large data should pass through storage (e.g., a table or file path reference), not taskValues. |
| 10 | Run Job task type — what is it used for? | Allows one Lakeflow Job to trigger another existing job as a task, enabling modular, reusable DAG composition across teams. |
| 11 | When would you use a File arrival trigger vs a Scheduled trigger? | File arrival: event-driven start when new files land in a monitored external location (avoids wasted runs). Scheduled: fixed cron-based cadence regardless of data arrival. |
| 12 | Pipeline task vs SQL task — key difference. | Pipeline task runs an entire Lakeflow Declarative Pipeline (multi-table DLT-style workflow). SQL task runs a single SQL query, file, or dashboard refresh — not a full pipeline. |
