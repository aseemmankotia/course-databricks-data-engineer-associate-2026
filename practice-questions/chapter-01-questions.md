## Chapter 1: Platform Architecture, Workspace, and Compute — Practice Questions

**Q1.** A data engineering team runs a nightly ETL pipeline via a scheduled Job. No one needs to interact with the cluster outside the scheduled run, and the team wants to minimize DBU cost. Which compute type should back this Job?

A) A job cluster created and terminated automatically by the Job
B) An all-purpose cluster shared with the analytics team
C) A Databricks SQL warehouse set to Classic
D) A single-node all-purpose cluster with autotermination set to 10 minutes

<details>
<summary>Answer</summary>

**Correct: A**

Job clusters are created when the Job starts and terminated automatically when it finishes, and they are billed at a lower DBU rate than all-purpose clusters — the correct pattern for unattended scheduled workloads.

**Why the others are wrong:**
- All-purpose clusters are billed at a higher DBU rate and are meant for interactive, shared use — not scheduled automation.
- SQL warehouses run SQL against Databricks SQL, not general ETL notebooks or Spark jobs used for pipelines.
- Even with a short autotermination, an all-purpose cluster still bills at the higher all-purpose DBU rate and requires manual lifecycle management.

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q2.** A BI analyst needs to connect Tableau to Databricks for interactive dashboards and wants queries to start instantly with zero cluster startup wait and no infrastructure to manage. Which option BEST meets this requirement?

A) A Serverless Databricks SQL warehouse
B) An all-purpose cluster with autotermination disabled
C) A Classic Databricks SQL warehouse
D) A job cluster configured with autoscaling

<details>
<summary>Answer</summary>

**Correct: A**

Serverless SQL warehouses run entirely on Databricks-managed compute in the serverless data plane, giving near-instant startup with no cluster management — exactly what's needed for BI tools requiring immediate query response.

**Why the others are wrong:**
- All-purpose clusters take minutes to start and are not optimized for concurrent SQL/BI workloads.
- Classic SQL warehouses run in the customer's cloud account (classic data plane) and still incur cluster startup latency.
- Job clusters are for scheduled batch jobs, not interactive BI querying.

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q3.** Which component of the Databricks architecture resides in the control plane, hosted and managed by Databricks itself?

A) The customer's VMs running Spark executors
B) The customer's cloud storage account containing Delta tables
C) The notebook UI, job scheduler, and cluster manager
D) The Spark driver and worker nodes processing data

<details>
<summary>Answer</summary>

**Correct: C**

The control plane hosts backend services managed by Databricks: workspace UI, notebooks, job scheduler, and cluster manager. It never touches customer data directly.

**Why the others are wrong:**
- VMs running executors live in the data plane, in the customer's cloud account (except for serverless).
- Customer storage is part of the customer's cloud environment, outside Databricks-managed infrastructure.
- The driver and worker nodes are data plane compute resources, not control plane services.

</details>

---

**Q4.** Three data engineers are collaboratively exploring a new dataset in shared notebooks, running ad-hoc cells, installing libraries, and iterating throughout the day. Which compute type is appropriate?

A) A job cluster with a max of 1 worker
B) An all-purpose cluster shared by the three engineers
C) A Serverless SQL warehouse
D) A pool of terminated job clusters

<details>
<summary>Answer</summary>

**Correct: B**

All-purpose clusters are designed for interactive, collaborative notebook work where multiple users attach to the same cluster and iterate over time.

**Why the others are wrong:**
- Job clusters are ephemeral, created for a single job run and terminated after — not suited to open-ended interactive exploration.
- SQL warehouses run SQL queries against Databricks SQL, not general-purpose interactive notebooks with library installs.
- A pool of terminated clusters provides no running compute for interactive work; pools only reduce cluster startup time.

</details>

---

**Q5.** A company has strict compliance requirements mandating that all compute resources physically run inside their own cloud account, never inside Databricks-managed infrastructure. Which compute option satisfies this?

A) Serverless SQL warehouse
B) Serverless Jobs compute
C) Classic compute (all-purpose or job clusters in the classic data plane)
D) Serverless notebooks

<details>
<summary>Answer</summary>

**Correct: C**

Classic compute runs in the customer's own cloud account (the classic data plane), meeting requirements that prohibit compute from running in Databricks-managed infrastructure.

**Why the others are wrong:**
- Serverless SQL warehouses run in a Databricks-managed serverless data plane, not the customer's account.
- Serverless Jobs compute is also managed entirely by Databricks in its own cloud infrastructure.
- Serverless notebooks likewise execute on Databricks-managed serverless infrastructure, not customer-owned compute.

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q6.** What is the minimum autotermination duration you can configure for an all-purpose cluster in Databricks?

A) 30 minutes
B) 60 minutes
C) There is no minimum; it can be set to 0
D) 10 minutes

<details>
<summary>Answer</summary>

**Correct: D**

Databricks enforces a minimum autotermination value of 10 minutes for all-purpose clusters to prevent excessive churn while still allowing cost control.

**Why the others are wrong:**
- 30 minutes is a commonly assumed but incorrect value — it's not the enforced minimum.
- 60 minutes is closer to a reasonable default some teams choose, not the platform minimum.
- A value of 0 would terminate the cluster immediately after any idle moment and is not permitted.

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q7.** Which magic command should be used at the top of a notebook cell to execute another notebook's code within the current notebook's context?

A) %sh
B) %fs
C) %pip
D) %run

<details>
<summary>Answer</summary>

**Correct: D**

%run executes another notebook and imports its defined functions/variables into the calling notebook's session — used for modularizing shared setup code.

**Why the others are wrong:**
- %sh runs shell commands on the driver node, unrelated to running another notebook.
- %fs is shorthand for dbutils.fs filesystem commands, not notebook execution.
- %pip installs Python packages scoped to the notebook session, not notebook execution.

</details>

---

**Q8.** In the Databricks workspace object hierarchy, which of the following correctly describes where Git folders (formerly Repos) fit?

A) Git folders are a special cluster type used only for Jobs
B) Git folders replace the control plane's job scheduler
C) Git folders are a storage layer inside Unity Catalog
D) Git folders are workspace objects, similar to folders, that sync notebooks and files with a remote Git provider

<details>
<summary>Answer</summary>

**Correct: D**

Git folders are workspace objects that let you clone, commit, push, and pull notebooks and files to/from a Git provider like GitHub, GitLab, Bitbucket, or Azure DevOps directly within the workspace hierarchy.

**Why the others are wrong:**
- Git folders are not a compute or cluster construct at all.
- They have nothing to do with job scheduling; they're purely a workspace source-control feature.
- Unity Catalog governs data and AI assets, not workspace notebook version control.

</details>

---

