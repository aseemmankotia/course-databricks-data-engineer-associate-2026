# Chapter 11 Lab: Unity Catalog Governance and Security

**Estimated time:** 15 minutes

## Exercise

Create a schema, grant USAGE and SELECT privileges to a group at the schema level, then build a dynamic view that masks a sensitive column for users not in an authorized group using is_member().

## Key takeaway

Unity Catalog privileges cascade through the catalog.schema.table hierarchy, and dynamic views provide row/column-level security without duplicating data.
