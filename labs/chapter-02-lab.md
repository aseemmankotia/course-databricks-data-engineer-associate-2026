# Chapter 2 Lab: Delta Lake, Medallion Architecture, and Unity Catalog Basics

**Estimated time:** 20 minutes

## Exercise

Create a Delta table, make three versions of edits, query an earlier version with VERSION AS OF, run OPTIMIZE ZORDER BY, then run VACUUM and observe which files are removed.

## Key takeaway

Delta Lake's transaction log powers ACID guarantees and time travel, but VACUUM permanently prunes old files after the retention window.
