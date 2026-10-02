## Chapter 12: Full Exam Simulation and Time-Management Strategy — Practice Questions

**Q1.** You have 90 minutes for 45 questions on the Databricks Certified Data Engineer Associate exam. At the 45-minute mark, you have answered 30 questions and flagged 6 for review. What should you do?

A) Immediately go back and re-read all 6 flagged questions before moving forward
B) Slow down significantly to double-check every remaining answer choice
C) Continue at your current pace since you are ahead of the roughly 2-minute-per-question budget
D) Skip the remaining unanswered questions and submit early to save time

<details>
<summary>Answer</summary>

**Correct: C**

At 45 minutes you've completed 30 of 45 questions, which is ahead of the 2-min/question pace (should be ~22-23 questions at 45 min). Continuing steady pace preserves time at the end for flagged-question review.

**Why the others are wrong:**
- Interrupting forward progress to revisit flags mid-exam breaks pacing and risks running out of time on unanswered questions
- Slowing down when already ahead of schedule wastes the time buffer you've built
- Submitting early abandons unanswered questions, guaranteeing lost points

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q2.** A scenario question describes a pipeline that must automatically detect and process only newly arrived files in cloud storage without manually tracking which files were already processed. Which keyword phrase in the question should you use to map this to the correct domain and answer?

A) 'cloud storage' maps to Unity Catalog external locations only
B) 'automatically detect' and 'newly arrived files' map to Auto Loader / incremental ingestion
C) 'pipeline' maps to Databricks SQL dashboards
D) 'process' maps to manual batch COPY INTO with no state tracking

<details>
<summary>Answer</summary>

**Correct: B**

Phrases like 'automatically detect new files' and 'avoid reprocessing' are the exam's signal for Auto Loader's checkpointed, incremental file discovery — recognizing this keyword pattern quickly routes you to the Data Ingestion domain and correct answer.

**Why the others are wrong:**
- 'Cloud storage' alone is too generic and appears in almost every ingestion question; it doesn't isolate the correct service
- 'Pipeline' is a broad term used across ETL, Jobs, and DLT questions — not a domain-mapping keyword
- COPY INTO does not track incremental new-file state the way the scenario requires, and lacks built-in schema evolution handling

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q3.** You encounter a dense scenario question about Delta Lake time travel with two answer options that both sound technically valid. You are unsure and it's taking over 2 minutes. What is the BEST strategy?

A) Flag the question, select your best guess now, and move on to protect your overall pacing
B) Spend as long as needed on this question since accuracy matters more than time
C) Leave it blank and come back only if time permits at the very end
D) Immediately switch to the other flagged question and abandon this one unanswered

<details>
<summary>Answer</summary>

**Correct: A**

Flagging with a placed guess ensures you never leave a blank (no penalty for wrong answers on this exam format) while preserving time budget; you can refine the guess later if time remains.

**Why the others are wrong:**
- Unlimited time on one question risks running out of time for the remaining ~40+ questions
- Leaving it blank risks losing the point entirely if you run out of time and never return
- There is no reason to abandon it unanswered — always lock in a guess before flagging

</details>

---

**Q4.** A question asks: 'Which approach is the BEST way to reduce small file problems in a Delta table receiving frequent micro-batch writes?' Two options — enabling Auto Optimize and manually running OPTIMIZE weekly — are both technically valid solutions. How should you interpret 'BEST' here?

A) Choose the option requiring the least operational overhead and most automation, since 'BEST' implies the optimal ongoing solution, not just any valid one
B) Choose whichever option appears first alphabetically since both are correct
C) Choose the manual option because it gives the engineer more control regardless of effort
D) Treat 'BEST' as identical to 'correct' and pick either option since both technically work

<details>
<summary>Answer</summary>

**Correct: A**

'BEST' signals the exam wants the most efficient, automated, production-appropriate answer among multiple technically-correct options — here, Auto Optimize (auto-compaction + optimized writes) over manual weekly OPTIMIZE.

**Why the others are wrong:**
- Answer order has no bearing on correctness
- Manual control isn't the deciding factor when the question asks for the BEST practice, which favors automation and lower maintenance
- 'BEST' and 'correct' are not interchangeable — multiple options can be correct, but only one is optimal

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q5.** A requirement states you must ingest CSV files landing in cloud object storage exactly once per day in a scheduled batch, with no need for near-real-time processing or schema inference automation. Which ingestion approach BEST fits?

A) COPY INTO run on a scheduled Job
B) Auto Loader with continuous trigger
C) Structured Streaming with trigger availableNow and a 1-second interval
D) Delta Live Tables in continuous pipeline mode

<details>
<summary>Answer</summary>

**Correct: A**

For simple, once-daily scheduled batch loads without automatic schema evolution needs, COPY INTO on a scheduled Job is the lightweight, idempotent, exam-recommended approach.

**Why the others are wrong:**
- Continuous-trigger Auto Loader is designed for ongoing incremental streaming ingestion, not a single daily scheduled batch — overkill and cost-inefficient
- A 1-second streaming trigger is for near-real-time use cases, contradicting the 'once per day' requirement
- DLT continuous mode is meant for always-on pipelines, not scheduled once-daily batch jobs

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q6.** During process-of-elimination on a VACUUM question, you see this option: 'Run VACUUM with a retention of 0 HOURS to immediately reclaim all storage.' Why should you eliminate this option in most exam scenarios?

A) Because VACUUM only accepts retention values in days, not hours
B) Because a retention below 168 hours (7 days, the default) risks breaking concurrent readers and time travel, and requires explicitly disabling the retention duration check
C) Because VACUUM cannot be run manually and only executes automatically
D) Because 0-hour retention is the recommended default configuration for production tables

<details>
<summary>Answer</summary>

**Correct: B**

VACUUM's default retention threshold is 168 hours (7 days); going below this without disabling the safety check (spark.databricks.delta.retentionDurationCheck.enabled) risks corrupting active readers or time-travel queries — a classic exam trap option.

**Why the others are wrong:**
- VACUUM retention can be specified in hours (RETAIN n HOURS), so this eliminated reasoning is factually wrong
- VACUUM can absolutely be run manually via SQL or the Delta API
- 0-hour retention is explicitly discouraged and requires bypassing a safety check — never the recommended default

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q7.** A scenario requires a recurring ETL workload to run on a fixed nightly schedule with no interactive use and the lowest possible compute cost. Which cluster configuration is correct?

A) A job cluster created and terminated automatically by the Databricks Job
B) An all-purpose cluster left running 24/7
C) A SQL warehouse in Serverless mode with auto-stop disabled
D) An interactive cluster shared across the team with autoscaling

<details>
<summary>Answer</summary>

**Correct: A**

Job clusters are spun up when the job starts and automatically terminated on completion, making them the cheapest option for scheduled, non-interactive nightly ETL — a heavily tested pricing/architecture distinction.

**Why the others are wrong:**
- An always-on all-purpose cluster incurs continuous cost even when idle — the opposite of the requirement
- Disabling auto-stop on a SQL warehouse defeats the purpose of minimizing cost for a nightly batch job
- Shared interactive clusters are billed for interactive use and are not optimized for isolated scheduled batch jobs

</details>

---

**Q8.** After finishing a full practice exam simulation and reviewing your domain-level score breakdown, you scored 90% on Data Ingestion but 55% on Data Governance. What is the BEST next step before your real exam attempt?

A) Retake the entire practice exam from scratch immediately without reviewing explanations
B) Focus targeted review and hands-on labs specifically on the Data Governance domain, using the explanations from missed questions
C) Ignore the weak domain since your overall score already meets the passing threshold
D) Spend equal additional study time across all domains regardless of the score breakdown

<details>
<summary>Answer</summary>

**Correct: B**

Domain-level score breakdowns exist to direct targeted remediation; the exam-day risk is concentrated in your weakest domain, so focused review of Data Governance (with explanations and labs) yields the highest score improvement per hour studied.

**Why the others are wrong:**
- Retaking blindly without reviewing explanations repeats the same knowledge gaps
- An overall passing score on a practice exam doesn't guarantee passing the real exam if one domain has its own weighted risk on test day
- Equal time distribution wastes effort on domains you've already mastered instead of closing the actual gap

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

