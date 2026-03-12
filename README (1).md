# 🏥 SmartTriage ER Optimizer

SmartTriage is a scheduling system designed to optimize patient
treatment in an Emergency Room (ER). The goal is to assign patients to
available doctors while minimizing the **total medical risk caused by
waiting time**.

The system reads patient data from a CSV file, applies a priority-based
scheduling algorithm, and generates a treatment schedule in JSON format.

------------------------------------------------------------------------

# Algorithm Overview

The algorithm simulates an emergency room environment where patients
arrive over time and doctors treat them based on specialization.

Each patient has:

-   **Severity** (how critical the condition is)
-   **Arrival time**
-   **Treatment duration**
-   **Required specialization**

Doctors also have fixed specializations (TRAUMA, CARDIO, GENERAL) and
can only treat patients that match their expertise.

Patients are stored in **priority queues (heaps)** based on
specialization. The algorithm calculates a **dynamic priority score**
for each patient:

priority = (severity / treatment_time) + α × waiting_time

Where:

-   `severity` represents urgency
-   `treatment_time` favors shorter treatments for faster throughput
-   `waiting_time` increases priority as patients wait
-   `α` (alpha) controls how strongly waiting affects priority

This approach ensures:

-   Critical patients are treated quickly
-   Long-waiting patients eventually get priority
-   The system avoids starvation.

The scheduling loop simulates ER events:

1.  Patients are added to queues when they arrive.
2.  Available doctors select the highest-priority patient from the
    relevant queue.
3.  The **General doctor** can choose the most critical patient from any
    queue.
4.  The treatment is scheduled, doctor availability is updated, and risk
    is accumulated.

Total ER risk is computed as:

risk = severity × waiting_time

The final output includes the full treatment schedule and the estimated
total risk.

------------------------------------------------------------------------

# Time Complexity

Let **n** be the number of patients.

Major operations include:

  Operation                     Complexity
  ----------------------------- ------------
  Reading patients              O(n)
  Sorting patients by arrival   O(n log n)
  Heap insertions               O(n log n)
  Heap removals                 O(n log n)

Overall complexity:

Time Complexity: O(n log n)

Space complexity:

Space Complexity: O(n)

Priority queues allow efficient retrieval of the highest-priority
patient, making the system scalable for large ER datasets.

------------------------------------------------------------------------

# Key Design Decisions

### 1. Priority Queue Scheduling

Patients are managed using **heap-based priority queues**, enabling fast
selection of the most critical patient.

### 2. Dynamic Priority Adjustment

Patient priority increases as waiting time grows, preventing starvation
and improving fairness.

### 3. Specialization-Based Queues

Separate queues for TRAUMA, CARDIO, and GENERAL ensure that patients are
treated by appropriate specialists.

### 4. Flexible General Doctor

The General doctor can treat patients from any queue by selecting the
most critical case.

### 5. Event-Driven Simulation

The system advances time only when key events occur (patient arrival or
doctor availability), making the simulation efficient.

------------------------------------------------------------------------

# Output

The algorithm generates a JSON file:

submission.json

Containing:

-   treatment schedule
-   assigned doctors
-   start and end times
-   estimated total ER risk

This schedule represents an optimized treatment plan for the emergency
room.
