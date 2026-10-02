# Chapter 7 Lab: Silver/Gold Modeling: Views, Tables, and Materialized Views

**Estimated time:** 15 minutes

## Exercise

Create a standard view and a materialized view over the same silver table aggregation, then compare query latency and refresh behavior after new data is appended to the source table.

## Key takeaway

Pick views for lightweight recomputed queries, materialized views for precomputed high-performance aggregates, and be deliberate about managed vs external tables to avoid unintended data loss.
