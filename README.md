# Calendar-Aware Sports Scheduling
## Overview

This project extends the RobinX/ITC2021 sports timetabling framework to incorporate calendar-aware scheduling with explicit travel feasibility constraints. Standard scheduling benchmarks such as RobinX treat time slots as abstract integer indices with no calendar semantics, meaning a schedule declared feasible by the model may be physically impossible to execute once mapped to a real calendar. This project addresses that gap by assigning concrete calendar windows to each time slot and introducing a synthetic travel distance matrix, enabling a novel travel feasibility constraint to be formally defined, enforced, and evaluated.

The project was developed as part of an MSc dissertation in Business Analytics and Management Science at the University of Southampton.


Research Contributions

Novel Constraint H3 — Travel Feasibility
Requires that the calendar gap between a team's consecutive fixtures is sufficient to cover the return journey home after the first fixture and the onward journey to the location of the second. This constraint has no equivalent in the existing RobinX taxonomy or any ITC2021 benchmark instance.

Novel Constraint S6 — Fixture Difficulty Spacing
Penalises schedules in which a team faces a disproportionate concentration of strong opponents within any three consecutive slots, capturing a competitive fairness consideration not represented in existing frameworks.

Three Progressive SA Variants
Evaluates whether targeted, constraint-specific repair logic improves on generic neighbourhood search, and whether that improvement compounds when multiple targeted mechanisms are combined.
