# Chapter 5 Lab: Spark SQL and PySpark Core Transformations

**Estimated time:** 20 minutes

## Exercise

In a notebook, deduplicate a table of customer records to keep only the latest record per customer_id using ROW_NUMBER() OVER (PARTITION BY customer_id ORDER BY updated_at DESC) and filter to row number 1.

## Key takeaway

Window functions provide reliable ranking and deduplication logic that plain DISTINCT or GROUP BY cannot express, and null-safe equality avoids silent row loss.
