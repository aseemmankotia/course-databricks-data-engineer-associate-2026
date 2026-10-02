## Chapter 8: Orchestrating Pipelines with Lakeflow Jobs — Practice Questions

**Q1.** In a Lakeflow Job, Task_D depends on both Task_B and Task_C (default 'All succeeded' condition), and Task_B depends on Task_A. Task_A succeeds, but Task_B fails. Task_C has no dependencies and runs independently. What happens?

A) Task_C runs normally; Task_D is skipped because not all of its dependencies succeeded
B) Task_C is skipped because Task_B failed
C) The entire job run is immediately cancelled, including Task_C
D) Task_D runs anyway using the last successful output of Task_B

<details>
<summary>Answer</summary>

**Correct: A**

Tasks only wait on their own upstream dependencies. Task_C has none, so it runs regardless of Task_B's status. Task_D's default 'All succeeded' condition means it is skipped since Task_B failed.

**Why the others are wrong:**
- Task_C has no dependency relationship to Task_B, so it is unaffected
- A single task failure does not cancel the whole run unless configured; unrelated branches continue
- Jobs do not reuse stale outputs from failed tasks — Task_D simply doesn't run

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q2.** A pipeline must start processing as soon as new Parquet files land in a cloud storage location, but should not run on a fixed schedule when no files arrive. Which trigger type should be configured?

A) Scheduled trigger with a 1-minute cron expression
B) Continuous trigger
C) Manual trigger with an external polling script
D) File arrival trigger

<details>
<summary>Answer</summary>

**Correct: D**

File arrival triggers natively monitor an external storage location and start a job run when new files are detected, avoiding unnecessary runs.

**Why the others are wrong:**
- A 1-minute cron schedule wastes runs when no new data has arrived
- Continuous trigger keeps the job perpetually running rather than reacting to specific file events
- Building a custom polling script duplicates built-in functionality unnecessarily

</details>

---

**Q3.** A task is configured with 'Maximum retries' set to 2 and 'Minimum retry interval' set to 30 seconds. The task fails on its first attempt and fails again on the retry. How many total attempts does the task make before the run is marked failed?

A) 1
B) 2
C) Unlimited until it succeeds
D) 3

<details>
<summary>Answer</summary>

**Correct: D**

Maximum retries of 2 means the original attempt plus up to 2 additional retries, for a total of 3 attempts, each spaced at least 30 seconds apart.

**Why the others are wrong:**
- 1 ignores the configured retries entirely
- 2 counts only the retries and omits the original attempt
- Retries are bounded by the configured maximum, not unlimited

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q4.** A team wants to include an existing dbt project's models as one step in a larger Lakeflow Job DAG, alongside notebook and pipeline tasks. Which task type should they add?

A) Notebook task pointing to a converted dbt notebook
B) SQL task
C) Pipeline task
D) dbt task

<details>
<summary>Answer</summary>

**Correct: D**

The dbt task type natively runs dbt commands (e.g., dbt run) against an existing dbt project without requiring conversion to notebooks.

**Why the others are wrong:**
- Converting dbt models to notebooks is unnecessary rework the dbt task avoids
- SQL tasks run individual queries or dashboards, not full dbt projects
- Pipeline tasks run Lakeflow Declarative Pipelines, not dbt projects

</details>

---

**Q5.** A Lakeflow Job is configured with a Continuous trigger. The active run fails due to a transient cluster error. What is the default behavior?

A) The job is automatically disabled and requires manual re-enabling
B) Databricks automatically starts a new run to replace the failed one
C) The job waits until the next scheduled cron time to restart
D) The failed run is retried indefinitely on the same cluster without starting a new run

<details>
<summary>Answer</summary>

**Correct: B**

Continuous trigger jobs are designed to always have an active run; if a run fails, Databricks automatically starts a new run to maintain continuous processing.

**Why the others are wrong:**
- The job is not disabled automatically on transient failure
- Continuous jobs are not driven by a cron schedule
- A new run is started rather than endlessly retrying the identical failed run

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q6.** A team wants job failure alerts delivered to a PagerDuty on-call channel in addition to their team's email distribution list. What should they configure?

A) Only an email notification, since PagerDuty forwards emails
B) A custom notebook task at the end of the DAG that always runs
C) A webhook (system destination) notification in addition to the email notification
D) Increase the retry count so failures rarely need alerting

<details>
<summary>Answer</summary>

**Correct: C**

Webhook notifications (system destinations) integrate directly with tools like PagerDuty, Slack, and Microsoft Teams, and can be configured alongside email notifications on job outcomes.

**Why the others are wrong:**
- Relying on email forwarding is fragile and not the intended integration path
- A notebook task adds complexity and doesn't integrate with PagerDuty's alerting API
- Retries reduce alert frequency but don't provide the required PagerDuty routing

</details>

---

**Q7.** A job defines a job-level parameter named 'environment'. A downstream notebook task needs to reference this value. Which syntax correctly retrieves it inside the notebook task's parameter field?

A) {{job.parameters.environment}}
B) {{task.environment}}
C) dbutils.jobs.taskValues.get(key='environment')
D) ${environment}

<details>
<summary>Answer</summary>

**Correct: A**

Job-level parameters are referenced using the dynamic value reference syntax {{job.parameters.<param_name>}}, which resolves at runtime for any task.

**Why the others are wrong:**
- {{task.environment}} is not a valid dynamic reference pattern for job parameters
- dbutils.jobs.taskValues.get retrieves task values passed between tasks, not job parameters
- ${environment} is not the syntax used by Lakeflow Jobs for parameter substitution

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q8.** What is the primary purpose of dbutils.jobs.taskValues.set() and .get() in a multi-task Lakeflow Job?

A) To pass small key-value data (e.g., a row count or file path) from one task to a dependent task
B) To store large output datasets for downstream tasks to consume
C) To define the job-level parameters used across all tasks
D) To configure the retry policy dynamically at runtime

<details>
<summary>Answer</summary>

**Correct: A**

Task values let a producing task set small pieces of data that a downstream task can retrieve by referencing the producing task's key, enabling lightweight data passing across the DAG.

**Why the others are wrong:**
- Task values are not designed for large datasets — they should hold small metadata values
- Job parameters are defined separately at the job/task settings level, not via taskValues
- Retry policies are configured in job settings, not via taskValues calls

</details>

---

