# SmartTriage ER Optimizer

## Overview

SmartTriage is a scheduling system designed to optimize patient
treatment order in an emergency room (ER). The system simulates an ER
environment where patients arrive over time and must be assigned to
doctors with specific specializations. The objective is to minimize the
**Total ER Risk**, defined as:

Total ER Risk = Σ(severity × waiting_time)

where\
waiting_time = treatment_start_time − arrival_time

The ER contains three doctors:

-   **Doctor_T** -- TRAUMA specialist\
-   **Doctor_C** -- CARDIO specialist\
-   **Doctor_G** -- GENERAL doctor (can treat any specialization)

The algorithm generates a treatment schedule that respects
specialization constraints while minimizing cumulative patient risk.

------------------------------------------------------------------------

## Algorithm Description

The system uses an **event-driven greedy scheduling algorithm with
dynamic priority scoring**.

Instead of simulating every minute, the algorithm progresses between
important events such as:

-   Patient arrivals\
-   Doctors finishing treatments

Patients are placed into **specialization-based priority queues**
(TRAUMA, CARDIO, GENERAL). When a doctor becomes available, the
scheduler selects the compatible patient with the highest priority
score.

### Priority Function

priority = (severity / treatment_time) + α × waiting_time

Where:

-   **severity** prioritizes critical patients\
-   **treatment_time** discourages extremely long treatments from
    blocking doctors\
-   **waiting_time** increases urgency for patients who have waited
    longer\
-   **α** is a tuning parameter controlling the influence of waiting
    time

This scoring balances **medical urgency, fairness, and throughput
efficiency**.

The GENERAL doctor acts as a flexible resource and dynamically selects
the most critical patient across all queues when available.

------------------------------------------------------------------------

## Scheduling Workflow

1.  Patients are sorted by arrival time.
2.  The simulation begins at time 0 with all doctors idle.
3.  As patients arrive, they are inserted into their specialization
    queues.
4.  When a doctor becomes free, the algorithm selects the highest
    priority compatible patient.
5.  Treatment begins immediately if the patient has already arrived.
6.  Waiting time and risk are computed for each treated patient.
7.  The process continues until all patients are scheduled.

The final output is generated as `submission.json`, containing the
treatment schedule and estimated total risk.

------------------------------------------------------------------------

## Complexity Analysis

Let **n** be the number of patients.

Sorting patients by arrival time:

O(n log n)

Queue insertions and removals:

O(n log n)

Overall algorithm complexity:

O(n log n)

Space complexity:

O(n)

This efficiency allows the system to scale to large datasets with
hundreds of patients.

------------------------------------------------------------------------

## Key Design Decisions

**Event-driven simulation**\
The algorithm jumps directly between meaningful events instead of
iterating minute-by-minute, improving efficiency.

**Specialization-aware queues**\
Separate queues reduce unnecessary comparisons and ensure doctors treat
compatible patients.

**Dynamic priority scoring**\
Waiting time continuously increases priority, preventing patient
starvation.

**Greedy heuristic strategy**\
An exact optimization approach is computationally expensive. A
well-designed greedy heuristic provides strong performance while
maintaining practical runtime.

------------------------------------------------------------------------

## Conclusion

SmartTriage provides an efficient and scalable ER scheduling solution
that balances patient severity, waiting time, and treatment duration.
The approach minimizes total ER risk while ensuring fair and medically
appropriate patient prioritization.
