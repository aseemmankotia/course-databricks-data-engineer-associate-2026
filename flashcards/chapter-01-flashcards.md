## Chapter 1: Platform Architecture, Workspace, and Compute — Flashcards

| # | Front (Question) | Back (Answer) |
|---|-----------------|---------------|
| 1 | Minimum autotermination value allowed for an all-purpose cluster? | 10 minutes. UI suggests a default of 120 minutes of inactivity, but you can set as low as 10. Exam tip: 'no minimum' or '30 min' are wrong-answer traps. |
| 2 | What is a DBU? | A Databricks Unit — a normalized unit of processing capability per hour used for billing, independent of the underlying cloud VM cost. Exam tip: DBU rate differs by compute type (All-Purpose > Jobs > SQL, roughly). |
| 3 | All-purpose cluster vs Job cluster: key difference? | All-purpose: long-running, interactive, shareable, higher DBU rate. Job cluster: created per Job run, auto-terminates when done, lower DBU rate. Exam tip: choose job clusters for scheduled/automated workloads. |
| 4 | Classic vs Serverless Databricks SQL warehouse? | Classic: compute runs in customer's cloud account (classic data plane), slower startup. Serverless: compute is fully managed by Databricks, near-instant startup, no infra management. Exam tip: 'instant start, zero management' = serverless. |
| 5 | Magic command to execute another notebook and import its context? | %run ./other_notebook — shares variables/functions with the calling notebook. Different from %sh (shell) and %fs (dbutils.fs shorthand). |
| 6 | Magic command for running filesystem commands (dbutils.fs shorthand)? | %fs — e.g. %fs ls /mnt/data. Exam tip: don't confuse with %sh, which runs OS shell commands on the driver. |
| 7 | Magic command to render a cell as Markdown documentation? | %md — used for headers, notes, and formatted documentation inside a notebook, not executed as code. |
| 8 | What lives in the Databricks control plane? | Workspace UI, notebooks (code/metadata), job scheduler, cluster manager, and backend services — all hosted and managed by Databricks. Never processes customer data directly. |
| 9 | What lives in the (classic) Databricks data plane? | The actual compute resources — driver and worker VMs — running in the customer's own cloud account, processing and storing customer data. Contrast with serverless data plane, which is Databricks-managed. |
| 10 | Job cluster DBU rate vs All-Purpose cluster DBU rate — which is cheaper? | Job clusters bill at a lower DBU rate than all-purpose clusters for the same node types. Exam tip: cost-optimization scenarios for scheduled workloads point to job clusters. |
| 11 | What are Git folders (formerly Repos) used for? | Workspace objects that integrate with a Git provider (GitHub, GitLab, Bitbucket, Azure DevOps) to clone, commit, push, and pull notebooks/files for version control directly in the workspace. |
| 12 | Basic Databricks workspace object hierarchy order? | Workspace (root) → Folders → Notebooks / Git folders / Libraries / Files. Notebooks contain cells that can mix languages via magic commands (%python, %sql, %scala, %r). |
