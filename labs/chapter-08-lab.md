# Chapter 8 Lab: Orchestrating Pipelines with Lakeflow Jobs

**Estimated time:** 20 minutes

## Exercise

Build a Lakeflow Job with three tasks — ingest, transform, and aggregate — where transform depends on ingest and aggregate depends on transform, pass a parameter into the ingest task, and configure a retry policy plus a failure alert.

## Key takeaway

Lakeflow Jobs orchestrate multi-task DAGs with configurable triggers, retries, and alerts, and require explicit task values or parameters to pass data between tasks.
