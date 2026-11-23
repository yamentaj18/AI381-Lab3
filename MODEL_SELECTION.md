# Model selection

Fill this in after you have trained your candidates in MLflow. Short sentences are fine. Use numbers from your runs.

## The question
What does this model decide, and who is hurt by a wrong answer? (One or two sentences.)
This model predicts customer churn so the business can target at-risk customers with retention offers. False negatives hurt the business because we miss customers who end up churning, while false positives waste retention budget on loyal customers.

## Candidates
| Candidate | Why I tried it | AUC | F1 | F1 per slice (lowest) | p95 latency (ms) | Size (KB) | MLflow run ID |
|---|---|---|---|---|---|---|---|
| logreg | Simple linear baseline with fast inference and tiny size | 0.798 | 0.495 | see MLflow slice metrics | 4.7 | 4 | fc943c81 |
| random_forest | Tree ensemble to capture non-linear feature interactions | 0.759 | 0.454 | see MLflow slice metrics | 18.0 | 3160 | c451e41d |
| gradient_boosting | Boosted trees baseline for tabular churn prediction | 0.748 | 0.417 | see MLflow slice metrics | 5.2 | 142 | 1d61f4d1 |

## The champion
Which one did I pick, and what decided it? (Name the numbers that mattered and the ones I accepted losing.)
I picked `logreg` (MLflow version 2) because it achieved the highest F1 (0.495) and AUC (0.798), lowest p95 latency (4.7 ms, well within the 25.0 ms budget), and smallest size (4 KB). I accepted not using complex tree ensembles since they scored lower on F1/AUC and were larger or slower here.

## My promotion rules
- Latency budget (p95): `25.0` ms, because candidate p95 latency must stay within `LATENCY_BUDGET_MS` (`25.0 ms`) so API predictions remain fast.
- Minimum gain to replace the champion (F1 points): `0.005`, because a new candidate must beat the current champion's F1 by at least `MIN_GAIN` (`0.005`) to justify promoting a replacement.

## What I would check next
One thing that could make this choice wrong in production (for example a slice with few customers, or data that changes).
Data drift when new data (`data/churn_v2.csv`) or contract slices with fewer customers shift feature distributions and degrade F1.
