## Chapter 5: Spark SQL and PySpark Core Transformations — Flashcards

| # | Front (Question) | Back (Answer) |
|---|-----------------|---------------|
| 1 | Difference between left_semi and left_anti join? | left_semi: returns left rows WITH a match (no right columns). left_anti: returns left rows WITHOUT a match. Exam tip: neither ever includes columns from the right DataFrame. |
| 2 | row_number() vs rank() vs dense_rank() on ties [10,10,20]? | row_number(): 1,2,3 (always unique). rank(): 1,1,3 (gap after tie). dense_rank(): 1,1,2 (no gap). Exam tests exact numeric output on tie scenarios. |
| 3 | Standard PySpark dedup pattern (keep latest row per key)? | w = Window.partitionBy('id').orderBy(col('updated_at').desc()); df.withColumn('rn', row_number().over(w)).filter('rn = 1') |
| 4 | Null-safe equality operator in Spark SQL/DataFrame API? | <=> (EQNULLSAFE). Treats NULL <=> NULL as true, unlike == which returns NULL for that comparison. Used in join conditions to avoid dropping null-key matches. |
| 5 | coalesce(col1, col2, ...) behavior? | Returns the first non-null value among its arguments, left to right. Common use: coalesce(col('x'), lit(0)) to fill nulls with a default. |
| 6 | Higher-order function to test if ANY array element matches a condition? | exists(array_col, x -> condition). Returns a single boolean. Contrast with filter() which returns a filtered array, and transform() which returns a mapped array. |
| 7 | transform() vs filter() higher-order array functions? | transform(arr, x -> expr): maps each element, returns array of same length. filter(arr, x -> cond): returns array with only elements matching cond (length may shrink). |
| 8 | Inner join with duplicate keys on one side — row count effect? | Row count MULTIPLIES: each matching left row is repeated once per matching right-side duplicate. Exam gotcha: joins can inflate row counts, not just filter them. |
| 9 | Import required to build a WindowSpec in PySpark? | from pyspark.sql.window import Window — then Window.partitionBy(...).orderBy(...). Required before using row_number(), rank(), dense_rank(), lag(), lead(). |
| 10 | dropDuplicates(['col']) vs window-based dedup — when to use which? | dropDuplicates(['col']) keeps an ARBITRARY row per key (no order guarantee). Window row_number()==1 lets you deterministically keep the row matching a specific ordering (e.g. latest timestamp). |
| 11 | df.filter(col('x').isNull()) vs df.filter(col('x') == None)? | isNull() is correct — SQL/Spark NULL comparisons with == always return NULL (falsy), so col('x') == None never matches rows. Common exam trap. |
| 12 | full outer join + isNull() filter pattern — what does it find? | Rows present in only ONE of the two DataFrames (non-matching on both sides). Slower/more verbose alternative to left_anti/right_anti unions; exam favors anti joins for single-sided lookups. |
