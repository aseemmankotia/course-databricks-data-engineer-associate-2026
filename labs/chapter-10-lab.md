# Chapter 10 Lab: Troubleshooting, Monitoring, and Performance Optimization

**Estimated time:** 20 minutes

## Exercise

Run a job that produces significant data skew, open the Spark UI Stages tab, identify the longest-running task and shuffle spill, then apply OPTIMIZE and compare file counts before and after.

## Key takeaway

Task-level duration skew in the Spark UI signals data skew, and OPTIMIZE — not VACUUM — is the correct fix for the small-file problem.
