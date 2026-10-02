## Chapter 9: Implementing CI/CD with Git Folders and Databricks Asset Bundles — Practice Questions

**Q1.** A data engineering team stores their Databricks Asset Bundle project in a Git repository with separate branches for development and production work. They want engineers to deploy their own isolated copies of jobs and pipelines when working on the 'dev' branch, but deploy shared, fixed-name resources when merging to 'main' for production. Which bundle configuration approach BEST meets this requirement?

A) Define a single target with mode: production and rely on Git branch protection rules to control deployments
B) Define separate targets in databricks.yml — a dev target with mode: development and a prod target with mode: production — and deploy each from its corresponding branch
C) Create two separate databricks.yml files, one per branch, and manually rename resources before each deploy
D) Use Git folders to clone the repository twice and manually edit resource names in the workspace UI before running jobs

<details>
<summary>Answer</summary>

**Correct: B**

Databricks Asset Bundles support multiple named targets in a single databricks.yml. mode: development automatically prefixes resource names with the deploying user and marks resources as development-only, giving each engineer an isolated environment, while mode: production deploys resources with their exact configured names for shared production use.

**Why the others are wrong:**
- A single production-mode target would create shared resource names for all developers, causing naming collisions and defeating isolated dev testing
- Maintaining two separate YAML files duplicates configuration and defeats the purpose of parameterized targets built into the bundle spec
- Git folders do not automatically manage bundle deployment targets or resource naming — that is the job of bundle targets and modes

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q2.** Which Databricks CLI command deploys a Databricks Asset Bundle specifically to the 'staging' target defined in databricks.yml?

A) databricks bundle deploy --env staging
B) databricks bundle deploy -t staging
C) databricks bundle run -t staging
D) databricks bundle validate --target staging

<details>
<summary>Answer</summary>

**Correct: B**

The correct syntax to deploy a bundle to a specific target is 'databricks bundle deploy -t <target-name>' (or the long form --target). This uploads bundle artifacts and creates/updates the resources defined for that target.

**Why the others are wrong:**
- --env is not a valid flag for the bundle CLI; targets are specified with -t or --target
- 'bundle run' executes a job or pipeline that has already been deployed — it does not deploy the bundle itself
- 'bundle validate' only checks the configuration syntax and resolves variables; it does not deploy any resources

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q3.** In a databricks.yml file, an engineer wants the catalog name used by a job to differ between the dev and prod targets, without duplicating the entire job definition. Which approach is correct?

A) Hard-code two separate job definitions, one referencing the dev catalog and one referencing the prod catalog
B) Define a variable in the 'variables' section, reference it in the job definition using ${var.catalog_name}, and override its value under each target's 'variables' block
C) Use environment variables in the shell before running databricks bundle deploy, since bundles cannot reference variables internally
D) Store the catalog name in a workspace secret and reference the secret scope directly inside the job's task parameters

<details>
<summary>Answer</summary>

**Correct: B**

Databricks Asset Bundles support a top-level 'variables' block that defines a variable and optional default; each target can override the variable's value under its own 'variables' section, and the job definition references it once using ${var.variable_name} substitution.

**Why the others are wrong:**
- Duplicating job definitions per target is exactly what bundle variables are designed to avoid and increases maintenance overhead
- Bundles fully support internal variable substitution via the 'variables' section — external shell environment variables are unnecessary for this purpose
- Secrets are for sensitive credentials, not for parameterizing non-sensitive configuration values like catalog names

</details>

---

**Q4.** What is a Databricks Asset Bundle?

A) A YAML-based configuration format that packages source code, jobs, pipelines, and target environment settings for version-controlled, repeatable deployment
B) A compressed archive of notebook exports used for manual backup
C) A Unity Catalog object that groups related tables and views for access control
D) A cluster policy template applied automatically to all jobs in a workspace

<details>
<summary>Answer</summary>

**Correct: A**

A Databricks Asset Bundle (DAB) is defined in databricks.yml and bundles together source files, job/pipeline resource definitions, and per-target deployment configuration so a project can be validated, deployed, and run consistently across environments via the CLI.

**Why the others are wrong:**
- Bundles are not a notebook export/backup mechanism — they define infrastructure-as-code style deployments
- Unity Catalog groupings (catalogs/schemas) are unrelated to Asset Bundles
- Cluster policies are a separate governance feature, not part of the bundle packaging concept

</details>

---

**Q5.** A team currently uses Git folders (formerly Repos) in the Databricks workspace to check out feature branches and run notebooks manually for testing before merging to main. Which limitation of this approach explains why the team should adopt Databricks Asset Bundles for their CI/CD pipeline?

A) Git folders cannot be connected to any Git provider other than GitHub
B) Git folders permanently lock a branch once a notebook is run from it
C) Git folders require Unity Catalog to be disabled for the workspace
D) Git folders provide source control and branch checkout in the workspace, but do not define or manage the deployment of jobs, pipelines, and permissions as versioned, repeatable configuration

<details>
<summary>Answer</summary>

**Correct: D**

Git folders handle source control integration — cloning repos, switching branches, pulling changes — inside the workspace. They do not, however, declare or deploy job/pipeline resources or environment-specific settings; that is the role of Asset Bundles, which is why bundles are used alongside Git folders for full CI/CD.

**Why the others are wrong:**
- Git folders support multiple providers including GitHub, GitLab, Bitbucket, and Azure DevOps
- Running a notebook from a branch does not lock the branch in any way
- Git folders and Unity Catalog are unrelated features with no such dependency

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q6.** An engineer runs 'databricks bundle deploy -t prod' and the job resource is created in the workspace with the exact name specified in databricks.yml, with no username prefix added. What target setting caused this behavior?

A) mode: production
B) mode: development
C) run_as: service_principal
D) The default target with no mode specified always suppresses prefixes

<details>
<summary>Answer</summary>

**Correct: A**

When a target is configured with mode: production, the bundle deploys resources using their exact configured names without any per-user prefix, matching the behavior of a shared, stable production environment.

**Why the others are wrong:**
- mode: development adds a username-based prefix and tags resources for isolated per-developer testing — the opposite of what was observed
- run_as controls the identity resources execute as, not whether names are prefixed
- Without an explicit mode, the CLI does not guarantee production-style naming; the mode field must be set explicitly to control this behavior

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q7.** A company wants to validate a Databricks Asset Bundle's configuration for syntax and unresolved variable errors as part of an automated pipeline step, before any resources are deployed. Which command should run in that pipeline step?

A) databricks bundle validate
B) databricks bundle deploy --dry-run
C) databricks bundle run --check
D) databricks bundle summary

<details>
<summary>Answer</summary>

**Correct: A**

'databricks bundle validate' checks the databricks.yml configuration for syntax errors and resolves variables/target settings without deploying any resources, making it the correct step to run early in an automated CI pipeline.

**Why the others are wrong:**
- There is no supported --dry-run flag for 'bundle deploy'; validate is the dedicated command for this purpose
- 'bundle run --check' is not a valid command; 'bundle run' executes an already-deployed job or pipeline
- 'bundle summary' displays information about a deployed bundle's resources; it does not validate configuration prior to deployment

</details>

---

**Q8.** A retail company follows a branch-based promotion workflow: feature branches deploy to a dev target, a 'release' branch deploys to a test target, and main deploys to prod. Which combination of practices correctly implements this using Databricks Asset Bundles and Git folders?

A) Use a single target in databricks.yml and rely on Git folders to automatically detect the branch and choose the correct workspace path
B) Manually copy databricks.yml into three different repositories, one per environment, and deploy each independently
C) Define dev, test, and prod targets in databricks.yml, and configure the CI pipeline to run 'databricks bundle deploy -t <target>' with the target matching the branch that triggered the pipeline
D) Store all three environments' job definitions in one target's resources block and use if/else logic inside notebook code to select the environment at runtime

<details>
<summary>Answer</summary>

**Correct: C**

The standard bundle-based CI/CD pattern defines one target per environment in a single databricks.yml, and the CI/CD automation (e.g., a pipeline job) selects which target to deploy to using the -t flag based on which branch triggered the run — cleanly separating environment config from source branching logic.

**Why the others are wrong:**
- A single target cannot represent three environment-specific configurations, and Git folders do not perform target selection — that logic lives in the CI/CD pipeline and bundle targets
- Duplicating databricks.yml across repos breaks single-source-of-truth version control and complicates keeping configurations in sync
- Embedding environment branching logic inside notebook code is fragile, hard to test, and defeats the purpose of declarative, environment-scoped bundle targets

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

