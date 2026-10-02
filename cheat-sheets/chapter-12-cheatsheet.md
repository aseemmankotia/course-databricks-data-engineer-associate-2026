# Chapter 12 Cheat Sheet: Exam Simulation & Time Strategy

## Exam Facts
| Item | Value |
|---|---|
| Total questions | 45 |
| Time limit | 90 minutes |
| Passing score | 70% |
| Target pace | ~2 min/question |

## Pacing Checkpoints
| Time Elapsed | Questions You Should Have Answered |
|---|---|
| 22 min | ~11 |
| 45 min | ~22-23 |
| 68 min | ~34 |
| 90 min | 45 (all answered, flags reviewed) |

If you fall 3+ questions behind a checkpoint: stop deliberating, guess-and-flag, move faster.

## Flag-and-Review Rule
1. Never leave a question blank — always select a best guess first.
2. Flag if uncertain, then move on immediately.
3. Only revisit flags after all 45 are answered, using leftover time.
4. Re-reading a flagged question fresh often reveals the trap you missed the first time.

## Elimination Technique
- Discard options with extreme/absolute wording ("always", "never", "immediately reclaim ALL") — often traps (e.g., VACUUM RETAIN 0 HOURS).
- Discard options naming a deprecated or mismatched service for the scenario.
- Discard options that solve only PART of a multi-part requirement.
- When 2 options both seem valid, look for a qualifier word in the question (BEST, MOST, LEAST overhead) to break the tie.

## Keyword → Domain Map
| Scenario Keyword | Maps To |
|---|---|
| "automatically detect new files", "avoid reprocessing" | Auto Loader (Ingestion) |
| "once-daily", "scheduled batch", "no near-real-time" | COPY INTO on a Job |
| "lowest cost", "non-interactive", "nightly job" | Job Cluster |
| "small file problem", "frequent micro-batches" | Auto Optimize / OPTIMIZE |
| "reclaim storage", "remove stale files" | VACUUM (mind 168-hr default) |

## BEST vs Correct
- Multiple options can be technically correct.
- "BEST" = pick the most automated, lowest-overhead, or most exam-preferred approach.
- Treat qualifier words (BEST, MOST, LEAST) as the deciding filter, not decoration.

## Post-Exam Review Protocol
1. Score every practice exam by domain, not just overall %.
2. Any domain scoring 15+ points below your average = priority remediation target.
3. Redo missed questions' explanations, then complete a hands-on lab in that domain.
4. Re-simulate a full exam only after the weak domain is remediated — don't just repeat blind.
