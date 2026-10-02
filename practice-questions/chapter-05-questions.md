## Chapter 5: Spark SQL and PySpark Core Transformations — Practice Questions

**Q1.** A DataFrame `orders` has 100,000 rows. A DataFrame `customers` has 10,000 rows, but 500 customer_ids appear twice due to a duplicate load. You run `orders.join(customers, "customer_id", "inner")`. What happens to the row count of the result if some of those duplicated customer_ids match orders?

A) The result always has exactly 100,000 rows because inner joins never change row count
B) Spark automatically deduplicates the customers table before joining
C) The result will have MORE than 100,000 rows because matching orders are duplicated once per matching customer row
D) The join fails with a duplicate key exception

<details>
<summary>Answer</summary>

**Correct: C**

A join is a cartesian match on the key — if a key appears twice on the right side, every matching left row is emitted twice, inflating the row count. This is the #1 exam gotcha on join row-count effects.

**Why the others are wrong:**
- Inner joins can shrink OR grow row count depending on key cardinality on both sides
- Spark never silently deduplicates data during a join
- Spark does not throw an error for duplicate keys in a standard join — it just multiplies matches

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q2.** You need to find all customer_ids in `customers` that do NOT have any matching order in `orders`, without pulling in any columns from `orders`. Which join type BEST accomplishes this?

A) left_semi
B) left_anti
C) full_outer with a null filter afterward
D) right

<details>
<summary>Answer</summary>

**Correct: B**

left_anti returns only rows from the left DataFrame that have NO match in the right DataFrame, and never includes right-side columns — exactly the 'exists in A but not B' pattern.

**Why the others are wrong:**
- left_semi returns left rows that DO have a match, the opposite of what's needed
- full_outer plus a filter works technically but requires extra steps and null-checking logic the exam prefers you avoid
- right join returns matched and unmatched rows from customers relative to orders being on the right, which is the wrong orientation and includes matches too

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q3.** You join two DataFrames on a column that can legitimately contain NULL values, and you want two NULLs in that column to be treated as equal (matching each other) during the join. Which operator should you use in the join condition?

A) == (standard equality)
B) <=> (null-safe equality)
C) isNull() combined with OR
D) coalesce() on both columns before comparing with ==

<details>
<summary>Answer</summary>

**Correct: B**

The <=> operator (EQNULLSAFE) returns true when both sides are NULL, unlike standard equality which returns NULL (treated as false) when comparing NULLs — this is the standard null-safe join pattern tested on the exam.

**Why the others are wrong:**
- Standard == returns NULL (not true) for NULL-to-NULL comparisons, so those rows are dropped from the join
- Combining isNull() with OR is a workaround but is not the idiomatic null-safe operator and is more error-prone
- coalescing both columns to a sentinel value works but risks false matches and isn't the built-in null-safe mechanism

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q4.** You need to keep only the most recent record per customer_id from a table with duplicate customer_id rows, based on the maximum `updated_at` timestamp. Which approach is correct?

A) df.groupBy("customer_id").max("updated_at")
B) df.dropDuplicates(["customer_id"])
C) Use Window.partitionBy("customer_id").orderBy(col("updated_at").desc()), add row_number(), then filter where row_number == 1
D) df.distinct()

<details>
<summary>Answer</summary>

**Correct: C**

row_number() over a partition ordered by the tiebreaker column, filtered to row_number == 1, is the standard deduplication pattern that lets you keep the FULL row (not just the aggregated column) for the most recent record.

**Why the others are wrong:**
- groupBy().max() only returns the max timestamp value, not the full row associated with it
- dropDuplicates(["customer_id"]) keeps an arbitrary row per customer_id, not necessarily the most recent one
- distinct() only removes exact duplicate rows across all columns, not duplicate keys with differing values

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q5.** Which window ranking function should you use if you want ties to receive the SAME rank but want NO gap in the ranking sequence afterward (e.g., 1, 2, 2, 3 instead of 1, 2, 2, 4)?

A) row_number()
B) rank()
C) ntile()
D) dense_rank()

<details>
<summary>Answer</summary>

**Correct: D**

dense_rank() assigns the same rank to ties but does not skip subsequent rank values, unlike rank() which leaves gaps equal to the number of tied rows.

**Why the others are wrong:**
- row_number() always assigns a unique sequential number even to ties, ignoring value equality entirely
- rank() assigns the same rank to ties but skips the next rank number(s), producing gaps
- ntile() divides rows into a specified number of buckets and does not relate to tie-handling at all

</details>

---

**Q6.** You want to check whether ANY element in an array column `scores` is greater than 90, returning a boolean column. Which higher-order function is the correct choice?

A) transform(scores, x -> x > 90)
B) filter(scores, x -> x > 90)
C) array_contains(scores, 90)
D) exists(scores, x -> x > 90)

<details>
<summary>Answer</summary>

**Correct: D**

exists() applies a boolean predicate to each array element and returns true if ANY element satisfies it — exactly matching the requirement for a single boolean result.

**Why the others are wrong:**
- transform() returns a new array of the same length with the lambda applied to each element (an array of booleans, not a single boolean)
- filter() returns a new array containing only elements matching the predicate, not a single boolean
- array_contains() checks for an exact value match, not a conditional comparison like greater-than

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q7.** Which code correctly replaces NULL values in column `discount` with 0, keeping non-null values unchanged?

A) df.withColumn("discount", when(col("discount").isNull(), 0))
B) df.filter(col("discount").isNotNull())
C) df.withColumn("discount", coalesce(col("discount"), lit(0)))
D) df.withColumn("discount", col("discount") == 0)

<details>
<summary>Answer</summary>

**Correct: C**

coalesce() returns the first non-null value from its arguments in order, so coalesce(col, lit(0)) returns the original value if present, otherwise 0 — the standard null-replacement pattern.

**Why the others are wrong:**
- when(...).isNull(), 0) without an .otherwise() clause returns NULL for all non-matching rows, wiping out existing values
- filter(isNotNull()) removes rows with nulls entirely instead of replacing the value, changing row count
- == 0 performs a boolean comparison, not a value replacement, and returns null/false rather than the desired numeric value

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q8.** What is the primary functional difference between `rank()` and `dense_rank()` window functions in Spark SQL?

A) rank() requires an ORDER BY clause while dense_rank() does not
B) rank() leaves gaps in the ranking sequence after ties; dense_rank() does not
C) dense_rank() can only be used with PARTITION BY, while rank() cannot
D) There is no difference; they are aliases for the same function

<details>
<summary>Answer</summary>

**Correct: B**

Both functions assign equal ranks to tied rows, but rank() skips subsequent rank numbers equal to the count of ties (creating gaps), while dense_rank() keeps ranks sequential with no gaps.

**Why the others are wrong:**
- Both rank() and dense_rank() require an ORDER BY clause in their window specification
- Both functions support PARTITION BY identically; this restriction does not exist
- They produce different outputs whenever ties occur, so they are not interchangeable

</details>

---

