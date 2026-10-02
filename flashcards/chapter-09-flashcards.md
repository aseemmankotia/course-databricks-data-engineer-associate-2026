## Chapter 9: Implementing CI/CD with Git Folders and Databricks Asset Bundles — Flashcards

| # | Front (Question) | Back (Answer) |
|---|-----------------|---------------|
| 1 | What file defines a Databricks Asset Bundle's configuration? | databricks.yml (located at the project root). Contains bundle, workspace, variables, targets, and resources sections. Exam tip: this is THE core file tested for bundle structure questions. |
| 2 | CLI command to deploy a bundle to a specific target named 'prod'? | databricks bundle deploy -t prod (or --target prod). Exam tip: -t/--target selects which environment config to use. |
| 3 | CLI command to check bundle YAML syntax and resolve variables WITHOUT deploying? | databricks bundle validate. Exam tip: run this in CI before 'deploy' to catch config errors early. |
| 4 | CLI command to execute an already-deployed job or pipeline from a bundle? | databricks bundle run <resource-name> -t <target>. Exam tip: 'run' ≠ 'deploy' — deploy creates/updates resources, run executes them. |
| 5 | CLI command to remove all resources deployed by a bundle target? | databricks bundle destroy -t <target>. Exam tip: used to clean up dev/test resources, tests whether you know deploy has a teardown counterpart. |
| 6 | Effect of mode: development on a bundle target? | Prefixes deployed resource names with the deploying user's identity and marks resources for isolated testing. Exam tip: enables safe per-developer sandboxes without name collisions. |
| 7 | Effect of mode: production on a bundle target? | Deploys resources with their exact configured names, no user prefix — intended for shared, stable environments. Exam tip: contrast directly with mode: development. |
| 8 | How do you parameterize a value (e.g., catalog name) that differs per environment in databricks.yml? | Define it under top-level 'variables:', reference it as ${var.name} in resources, then override the value under each target's own 'variables:' block. Exam tip: avoids duplicating full job definitions per environment. |
| 9 | Git folders vs Databricks Asset Bundles — what's the key difference? | Git folders (formerly Repos) provide source control integration in the workspace (clone, branch, pull). Bundles define and deploy jobs/pipelines/permissions as versioned config. Exam tip: they're complementary, not interchangeable. |
| 10 | What are the three sections typically present in every databricks.yml? | bundle (project name/identity), targets (per-environment settings like mode and workspace host), and resources (job/pipeline/etc. definitions). Exam tip: 'variables' is a common optional 4th section. |
| 11 | Typical branch-to-target CI/CD mapping pattern for bundles? | Feature branches → dev target; release branch → test/staging target; main branch → prod target. The CI pipeline selects target via 'databricks bundle deploy -t <target>' based on triggering branch. Exam tip: branching logic lives in CI automation, not inside databricks.yml. |
| 12 | Which Databricks feature/tool name replaced 'Repos' in the workspace UI? | Git folders. Functionally the same Git integration (clone, checkout branch, pull/push) — exam may reference either the old or new name. |
