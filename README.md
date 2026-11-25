# Calendar-Aware Sports Scheduling
## Overview

This project extends the RobinX/ITC2021 sports timetabling framework to incorporate calendar-aware scheduling with explicit travel feasibility constraints. Standard scheduling benchmarks such as RobinX treat time slots as abstract integer indices with no calendar semantics, meaning a schedule declared feasible by the model may be physically impossible to execute once mapped to a real calendar. This project addresses that gap by assigning concrete calendar windows to each time slot and introducing a synthetic travel distance matrix, enabling a novel travel feasibility constraint to be formally defined, enforced, and evaluated.

The project was developed as part of an MSc dissertation in Business Analytics and Management Science at the University of Southampton.


## Research Contributions

Novel Constraint H3 — Travel Feasibility
Requires that the calendar gap between a team's consecutive fixtures is sufficient to cover the return journey home after the first fixture and the onward journey to the location of the second. This constraint has no equivalent in the existing RobinX taxonomy or any ITC2021 benchmark instance.

Novel Constraint S6 — Fixture Difficulty Spacing
Penalises schedules in which a team faces a disproportionate concentration of strong opponents within any three consecutive slots, capturing a competitive fairness consideration not represented in existing frameworks.

Three Progressive SA Variants
Evaluates whether targeted, constraint-specific repair logic improves on generic neighbourhood search, and whether that improvement compounds when multiple targeted mechanisms are combined.

## Problem Setup
1 . Instance: ITC 2021 Test Instance 2 (6 teams, double round-robin, 10 slots, 30 fixtures)
2 . Calendar extension: 3-day contiguous windows per slot from 10 August 2024
3 . Travel matrix: Synthetic, symmetric, seeded for reproducibility
4 . Travel ranges tested: Short (1–2 days), Medium (2–3 days), Mixed (1–3 days)
5 . Strength profiles: Balanced, Skewed, Uniform
6 . Total datasets: 45
7 . Parameter configurations: 10
8 . Total optimisation runs: 1,350

## Constraints

| ID | Name                          | Type         | Penalty           |
|:---|:------------------------------|:-------------|:------------------|
| H1 | Round Balance and Compactness | Hard         | 999               |
| H3 | Travel Feasibility             | Hard (Novel) | 999               |
| H4 | Double Round-Robin Integrity  | Hard         | 999               |
| H5 | Home Away Balance              | Hard         | 999               |
| S5 | Rest Day Adequacy              | Soft         | 1 per day short   |
| S6 | Fixture Difficulty Spacing     | Soft (Novel) | 5 per unit excess |

## Algorithm Variants

### SA V1 — Baseline

Three generic neighbourhood moves in equal random proportion:

- Swap games between slots
- Flip home and away designation
- Shift game date within slot window

### SA V2 — Single Targeted Move

Augments V1 with a targeted H5 repair move that fires with probability **0.40** when H5 violations exist, directing a flip at the team and slot with the worst cumulative home-away imbalance.

### SA V3 — Multiple Targeted Moves

Extends V2 with two further targeted moves:

- **Targeted H3 move:** Pushes the most travel-constrained game to the last day of its window.
- **Targeted S6 move:** Attempts to relocate the strongest opponent fixture in the worst difficulty window to a different slot.

Move selection follows a cascading probability rule.

---

**Post-processing:** All variants use post-processing greedy H5 repair after the SA search completes.

## Key Findings

- **V1 achieves the best average improvement (74.98%)** across 45 datasets.
- **V2 achieves 72.88%** — not statistically significantly worse than V1, but delivers lower H3 and H5 violation counts, particularly on mixed-travel instances.
- **V3 achieves 67.05%** — statistically significantly worse than both V1 and V2 (*p* < 0.001 under both paired t-test and Wilcoxon signed-rank test).
- **H3 violations** are zero on all short-travel datasets and increase with travel distance, validating the novel constraint.
- **S6 remains structurally invariant** at 44.67 violations across all three variants and all 45 datasets, confirming it is a property of the six-team fixture structure rather than the algorithm.
- **V3's best configuration is overwhelmingly P7** (slowest cooling, highest iterations), yet still fails to match V1 and V2, confirming the underperformance is structural rather than a matter of search budget.

## Repository Structure

```text
├── TestInstance.xml              # ITC 2021 benchmark instance
├── Full_Instance.xml             # Enriched instance with travel matrix and initial schedule
├── instance_construction.py      # Instance construction, distance matrix, circle method baseline, constraint checker
├── sa_v1_experiments.py          # SA V1 across 45 datasets and 10 parameter configs
├── sa_v2_experiments.py          # SA V2 with targeted H5 move
├── sa_v3_experiments.py          # SA V3 with three targeted moves
├── statistical_tests.py          # Paired t-test and Wilcoxon signed-rank comparisons
├── sa_v1_results.xlsx            # V1 results exported to Excel
├── sa_v2_results.xlsx            # V2 results exported to Excel
├── sa_v3_results.xlsx            # V3 results exported to Excel
└── README.md

Requirements
- Python >= 3.9
- openpyxl
- scipy
- numpy

Install Dependencies

pip install openpyxl scipy numpy

How to Run
Step 1: Construct the Instance

python instance_construction.py


This loads TestInstance.xml, generates the distance matrix, builds the circle method initial schedule, runs the constraint checker, and saves everything to Full_Instance.xml.
Step 2: Run SA Experiments
python sa_v1_experiments.py
python sa_v2_experiments.py
python sa_v3_experiments.py


Each script runs the respective variant across all 45 datasets and 10 parameter configurations and exports results to Excel.
Step 3: Statistical Comparison
python statistical_tests.py


Runs paired t-tests and Wilcoxon signed-rank tests comparing V1, V2, and V3.

**Formatting improvements:**
- Consistent `##` and `###` heading hierarchy.
- Correct language identifiers for code blocks (`text` and `bash`).
- Filenames formatted as inline code.
- Removed unnecessary standalone `bash` labels.
- Consistent spacing between sections.


## Parameter Configurations

| Config | T₀    | α     | Iterations | Intent                 |
|:-------|:------|:------|-----------:|:-----------------------|
| P1     | 500   | 0.995 | 3,000      | Baseline               |
| P2     | 1,000 | 0.995 | 3,000      | Higher exploration     |
| P3     | 500   | 0.990 | 3,000      | Faster convergence     |
| P4     | 500   | 0.995 | 5,000      | Extended search        |
| P5     | 200   | 0.995 | 3,000      | Exploitation-focused   |
| P6     | 2,000 | 0.998 | 5,000      | High temp, slow cool   |
| P7     | 100   | 0.999 | 8,000      | Near-greedy, slow cool |
| P8     | 800   | 0.980 | 2,000      | Rapid convergence      |
| P9     | 1,500 | 0.992 | 10,000     | Maximum exploration    |
| P10    | 50    | 0.995 | 3,000      | Near-deterministic     |

## Citation

If you use this work, please cite:

Deshpande, Y.A. (2026) Calendar-Aware Sports Scheduling with Travel Feasibility Constraints. MSc Dissertation, University of Southampton.

## Acknowledgements

This project uses ITC 2021 Test Instance 2 from the RobinX benchmark library. Van Bulck, D., Goossens, D., Schönberger, J. and Guajardo, M. (2020) RobinX: a three-field classification and unified data format for round-robin sports timetabling. European Journal of Operational Research, 280(2), pp. 568–580.
