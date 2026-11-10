
# 3. CPU Scheduling Algorithms

## 3.1 What is CPU Scheduling?

CPU Scheduling is the process of selecting one process from the Ready Queue and allocating the CPU to it for execution.

The CPU Scheduler (Short-Term Scheduler) decides which process should execute next.

**Main Objective:** To maximize CPU utilization and system performance while minimizing waiting time, turnaround time, and response time.

### Why is CPU Scheduling Required?

- Multiple processes may be ready for execution.
- Only a limited number of processes can use a CPU core at a time.
- The OS must decide which process gets CPU access.
- It improves system performance and responsiveness.
- It ensures fair allocation of CPU resources.

---

## 3.2 CPU-I/O Burst Cycle

A process generally alternates between CPU execution and I/O operations.

This is called the CPU-I/O Burst Cycle.

### CPU Burst

The period during which a process executes instructions using the CPU.

### I/O Burst

The period during which a process performs or waits for input/output operations.

### Working

1. A process starts with a CPU burst.
2. It may request an I/O operation.
3. It waits for I/O completion.
4. It returns to the Ready Queue.
5. It gets CPU allocation again.
6. This continues until the process terminates.

### Types of Processes

**1. CPU-Bound Process**

A process that spends most of its time performing CPU computations.

Examples:
- Mathematical calculations.
- Scientific computations.
- Image processing.

**2. I/O-Bound Process**

A process that spends most of its time performing input/output operations.

Examples:
- Reading files.
- Database operations.
- Network communication.

---

## 3.3 Scheduling Criteria

CPU scheduling algorithms are evaluated using different performance metrics.

### 1. CPU Utilization

The percentage of time the CPU is busy executing processes.

**Goal:** Maximize CPU utilization.

### 2. Throughput

The number of processes completed per unit of time.

**Goal:** Maximize throughput.

### 3. Turnaround Time (TAT)

The total time taken by a process from its arrival to its completion.

**Formula:**

Turnaround Time = Completion Time - Arrival Time

TAT = CT - AT

### 4. Waiting Time (WT)

The total time a process spends waiting in the Ready Queue.

**Formula:**

Waiting Time = Turnaround Time - Burst Time

WT = TAT - BT

### 5. Response Time (RT)

The time between the arrival of a process and the first time it gets CPU execution.

**Formula:**

Response Time = First CPU Start Time - Arrival Time

RT = First Start Time - AT

### 6. Completion Time (CT)

The time at which a process finishes its execution.

### Important Formulas

| Metric | Formula |
|---|---|
| Turnaround Time | CT - AT |
| Waiting Time | TAT - BT |
| Response Time | First Start Time - AT |
| Average Waiting Time | Sum of WT / Number of Processes |
| Average Turnaround Time | Sum of TAT / Number of Processes |
| Average Response Time | Sum of RT / Number of Processes |

**Important:** These formulas assume each process has one CPU burst and no additional execution overhead.

---

## 3.4 Preemptive vs Non-Preemptive Scheduling

### Non-Preemptive Scheduling

In Non-Preemptive Scheduling, once a process gets the CPU, it continues execution until it completes or voluntarily enters the Waiting state.

The OS does not forcibly take the CPU away simply because another process arrives.

Examples:
- FCFS
- Non-Preemptive SJF
- Non-Preemptive Priority Scheduling

### Preemptive Scheduling

In Preemptive Scheduling, the OS can interrupt a running process and allocate the CPU to another process.

Examples:
- Round Robin
- Shortest Remaining Time First
- Preemptive Priority Scheduling

### Difference

| Preemptive Scheduling | Non-Preemptive Scheduling |
|---|---|
| CPU can be taken away from a process. | CPU is not forcibly taken away. |
| Better responsiveness. | May have poor responsiveness. |
| More context switching. | Generally fewer context switches. |
| More complex to implement. | Simpler to implement. |
| Useful in interactive systems. | Useful in simpler batch systems. |

---

# 3.5 First Come First Serve (FCFS)

FCFS is the simplest CPU scheduling algorithm.

The process that arrives first gets the CPU first.

It follows the FIFO (First In First Out) principle.

**Type:** Non-Preemptive

### Working

1. Processes enter the Ready Queue.
2. The process with the earliest arrival gets the CPU.
3. It executes until completion.
4. The next process gets the CPU.

### Example

Consider the following processes:

| Process | Arrival Time | Burst Time |
|---|---:|---:|
| P1 | 0 | 5 |
| P2 | 1 | 3 |
| P3 | 2 | 8 |
| P4 | 3 | 6 |

### Gantt Chart

```text
0       5      8              16             22
|  P1   |  P2  |      P3      |      P4      |
```

### Calculation

| Process | AT | BT | CT | TAT = CT-AT | WT = TAT-BT |
|---|---:|---:|---:|---:|---:|
| P1 | 0 | 5 | 5 | 5 | 0 |
| P2 | 1 | 3 | 8 | 7 | 4 |
| P3 | 2 | 8 | 16 | 14 | 6 |
| P4 | 3 | 6 | 22 | 19 | 13 |

Average Waiting Time:

= (0 + 4 + 6 + 13) / 4

= 23 / 4

= **5.75 units**

Average Turnaround Time:

= (5 + 7 + 14 + 19) / 4

= 45 / 4

= **11.25 units**

### Advantages

- Simple to implement.
- Easy to understand.
- No starvation due to scheduling order.

### Disadvantages

- Convoy Effect.
- Poor average waiting time in some cases.
- Not suitable for interactive systems.

### Convoy Effect

Convoy Effect occurs when a long process holds the CPU and many shorter processes wait behind it.

**Example:**

A long CPU-bound process executes first, while several short I/O-bound processes remain waiting.

---

# 3.6 Shortest Job First (SJF)

Shortest Job First is a CPU scheduling algorithm in which the process having the smallest CPU Burst Time is selected first.

**Type:** Non-Preemptive

### Working

1. Check all processes currently present in the Ready Queue.
2. Select the process with the shortest Burst Time.
3. Execute it until completion.
4. Repeat the selection process.

**Important:** Only processes that have already arrived can be selected.

### Example

| Process | Arrival Time | Burst Time |
|---|---:|---:|
| P1 | 0 | 7 |
| P2 | 2 | 4 |
| P3 | 4 | 1 |
| P4 | 5 | 4 |

### Gantt Chart

```text
0          7   8       12       16
|    P1    | P3|   P2  |   P4   |
```

### Calculation

| Process | AT | BT | CT | TAT | WT |
|---|---:|---:|---:|---:|---:|
| P1 | 0 | 7 | 7 | 7 | 0 |
| P2 | 2 | 4 | 12 | 10 | 6 |
| P3 | 4 | 1 | 8 | 4 | 3 |
| P4 | 5 | 4 | 16 | 11 | 7 |

Average Waiting Time:

= (0 + 6 + 3 + 7) / 4

= **4 units**

Average Turnaround Time:

= (7 + 10 + 4 + 11) / 4

= **8 units**

### Advantages

- Minimizes average waiting time when all jobs are available together and burst times are known.
- Suitable for batch processing.
- Short processes finish quickly.

### Disadvantages

- Burst time must be estimated or known.
- Long processes may suffer starvation.
- Difficult to implement perfectly in real systems.

### Starvation in SJF

Starvation occurs when a process waits for an indefinitely long time because shorter processes keep arriving.

---

# 3.7 Shortest Remaining Time First (SRTF)

Shortest Remaining Time First is the preemptive version of SJF.

The process having the smallest remaining execution time is selected.

If a new process arrives with a shorter remaining time than the currently running process, the CPU is preempted.

**Type:** Preemptive

### Working

1. Check the processes that have arrived.
2. Select the process with the smallest remaining burst time.
3. Execute it.
4. If a new process arrives with a smaller remaining time, preempt the current process.
5. Continue until all processes complete.

### Example

| Process | Arrival Time | Burst Time |
|---|---:|---:|
| P1 | 0 | 8 |
| P2 | 1 | 4 |
| P3 | 2 | 2 |
| P4 | 3 | 1 |

### Gantt Chart

```text
0   1   2       4   5       8               15
|P1 |P2 |  P3   |P4 |  P2   |       P1      |
```

### Calculation

| Process | AT | BT | CT | TAT | WT |
|---|---:|---:|---:|---:|---:|
| P1 | 0 | 8 | 15 | 15 | 7 |
| P2 | 1 | 4 | 8 | 7 | 3 |
| P3 | 2 | 2 | 4 | 2 | 0 |
| P4 | 3 | 1 | 5 | 2 | 1 |

Average Waiting Time:

= (7 + 3 + 0 + 1) / 4

= **2.75 units**

Average Turnaround Time:

= (15 + 7 + 2 + 2) / 4

= **6.5 units**

### Advantages

- Good average waiting time.
- Short processes get CPU quickly.
- Better responsiveness than non-preemptive SJF in many workloads.

### Disadvantages

- More context switching.
- Burst time estimation is required.
- Long processes may suffer starvation.

---

# 3.8 Priority Scheduling

In Priority Scheduling, each process is assigned a priority.

The CPU is allocated to the process having the highest priority.

**Note:** In this chapter, a smaller priority number represents a higher priority.

### Types

1. Preemptive Priority Scheduling.
2. Non-Preemptive Priority Scheduling.

### Non-Preemptive Priority Scheduling

Once a process starts executing, it continues until completion.

A newly arriving higher-priority process cannot interrupt it.

### Preemptive Priority Scheduling

If a process with a higher priority arrives, it can interrupt the currently running process.

### Example

| Process | Arrival Time | Burst Time | Priority |
|---|---:|---:|---:|
| P1 | 0 | 4 | 3 |
| P2 | 0 | 3 | 1 |
| P3 | 0 | 2 | 4 |
| P4 | 0 | 1 | 2 |

### Gantt Chart (Non-Preemptive)

```text
0      3       4          8        10
|  P2  |  P4   |    P1    |   P3   |
```

### Calculation

| Process | AT | BT | Priority | CT | TAT | WT |
|---|---:|---:|---:|---:|---:|---:|
| P1 | 0 | 4 | 3 | 8 | 8 | 4 |
| P2 | 0 | 3 | 1 | 3 | 3 | 0 |
| P3 | 0 | 2 | 4 | 10 | 10 | 8 |
| P4 | 0 | 1 | 2 | 4 | 4 | 3 |

Average Waiting Time:

= (4 + 0 + 8 + 3) / 4

= **3.75 units**

Average Turnaround Time:

= (8 + 3 + 10 + 4) / 4

= **6.25 units**

### Starvation

A low-priority process may wait indefinitely if higher-priority processes keep arriving.

### Aging

Aging is a technique used to prevent starvation.

The priority of a process is gradually increased as its waiting time increases.

### Advantages

- Important processes get CPU first.
- Flexible priority-based execution.
- Useful when some tasks are more important than others.

### Disadvantages

- Starvation is possible.
- Priority assignment can be difficult.
- Aging may be required.

---

# 3.9 Round Robin Scheduling (RR)

Round Robin is a preemptive CPU scheduling algorithm designed mainly for time-sharing systems.

Each process receives a fixed amount of CPU time called the **Time Quantum**.

If the process does not finish within the time quantum, it is moved to the end of the Ready Queue.

**Type:** Preemptive

### Working

1. Processes enter the Ready Queue.
2. The first process gets the CPU.
3. It executes for at most one time quantum.
4. If completed, it leaves the system.
5. Otherwise, it goes to the end of the Ready Queue.
6. The process is selected again when its turn arrives.

### Example

Time Quantum = 2

| Process | Arrival Time | Burst Time |
|---|---:|---:|
| P1 | 0 | 5 |
| P2 | 0 | 4 |
| P3 | 0 | 2 |

### Gantt Chart

```text
0     2     4     6     8     9
| P1  | P2  | P3  | P1  | P2  |P1|
```

### Calculation

| Process | AT | BT | CT | TAT | WT |
|---|---:|---:|---:|---:|---:|
| P1 | 0 | 5 | 9 | 9 | 4 |
| P2 | 0 | 4 | 8 | 8 | 4 |
| P3 | 0 | 2 | 6 | 6 | 4 |

Average Waiting Time:

= (4 + 4 + 4) / 3

= **4 units**

Average Turnaround Time:

= (9 + 8 + 6) / 3

= **7.67 units**

### Effect of Time Quantum

**If Time Quantum is very large:**
- Round Robin behaves similarly to FCFS.

**If Time Quantum is very small:**
- More context switching occurs.
- Context-switching overhead increases.

### Advantages

- Fair CPU allocation.
- Good response time for interactive systems.
- Prevents indefinite CPU monopolization by a single process.

### Disadvantages

- Performance depends on time quantum.
- Too many context switches can reduce efficiency.
- Average waiting time may be high.

---

# 3.10 Multilevel Queue Scheduling

Multilevel Queue Scheduling divides the Ready Queue into multiple separate queues based on process characteristics.

Each queue can have its own scheduling algorithm.

### Example Queues

1. System Processes
2. Interactive Processes
3. Batch Processes
4. Student Processes

### Working

- Processes are permanently assigned to a particular queue in the basic multilevel queue approach.
- Each queue uses its own scheduling policy.
- CPU allocation between queues can use fixed priority or time sharing.

### Example

| Queue | Process Type | Scheduling Algorithm |
|---|---|---|
| Q1 | System Processes | Priority |
| Q2 | Interactive Processes | Round Robin |
| Q3 | Batch Processes | FCFS |

### Advantages

- Separates processes based on their requirements.
- Different queues can use different scheduling algorithms.
- Easy to classify processes.

### Disadvantages

- Lower-priority queues may suffer starvation.
- Processes generally cannot move between queues in the basic approach.

---

# 3.11 Multilevel Feedback Queue Scheduling (MLFQ)

Multilevel Feedback Queue Scheduling is an advanced scheduling algorithm in which processes can move between different queues based on their behavior.

Unlike Multilevel Queue Scheduling, processes are not permanently assigned to one queue.

### Working

1. New processes enter the highest-priority queue.
2. Higher-priority queues are scheduled first.
3. A process using its entire time quantum may be moved to a lower-priority queue.
4. Processes that frequently yield or wait for I/O may remain in higher-priority queues, depending on the policy.
5. Aging or priority boosting can move waiting processes upward.

### Example

| Queue | Priority | Scheduling |
|---|---|---|
| Q1 | Highest | Round Robin, Quantum = 4 |
| Q2 | Medium | Round Robin, Quantum = 8 |
| Q3 | Lowest | FCFS |

### Advantages

- Flexible scheduling.
- Good for interactive workloads.
- Adapts to process behavior.

### Disadvantages

- Complex implementation.
- Difficult to configure queue parameters.
- Poor configuration can cause starvation.

---

# 3.12 Starvation and Aging

### Starvation

Starvation occurs when a process waits for an indefinitely long time because other processes continuously receive resources before it.

Common in:
- Priority Scheduling.
- SJF.
- Multilevel Queue Scheduling.

### Aging

Aging is a technique used to prevent starvation by gradually increasing the priority of waiting processes.

**Example:**

If a process has been waiting for a long time, its priority is increased until it eventually gets CPU access.

---

# 3.13 Important Comparison of CPU Scheduling Algorithms

| Algorithm | Type | Selection Criteria |
|---|---|---|
| FCFS | Non-Preemptive | Arrival Time |
| SJF | Non-Preemptive | Shortest Burst Time |
| SRTF | Preemptive | Shortest Remaining Time |
| Priority | Both | Highest Priority |
| Round Robin | Preemptive | Time Quantum |
| Multilevel Queue | Usually Both | Queue Priority |
| Multilevel Feedback Queue | Usually Preemptive | Dynamic Queue Priority |

### Key Differences

| FCFS | SJF | Round Robin |
|---|---|---|
| Based on arrival time. | Based on burst time. | Based on time quantum. |
| Non-preemptive. | Non-preemptive. | Preemptive. |
| Convoy effect possible. | Starvation possible. | No indefinite CPU monopolization with finite quantum and fair queueing. |
| Simple implementation. | Requires burst estimation. | Requires quantum selection. |

---

# 3.14 Solving CPU Scheduling Numericals

Follow these steps for every CPU scheduling problem:

1. Write all process details.
2. Identify the scheduling algorithm.
3. Check whether it is preemptive or non-preemptive.
4. Consider arrival times.
5. Draw the Gantt chart.
6. Calculate Completion Time (CT).
7. Calculate Turnaround Time (TAT).
8. Calculate Waiting Time (WT).
9. Calculate Response Time (RT), if required.
10. Calculate averages.

### Important Formulas

```text
Turnaround Time = Completion Time - Arrival Time

TAT = CT - AT

Waiting Time = Turnaround Time - Burst Time

WT = TAT - BT

Response Time = First Start Time - Arrival Time

RT = First Start Time - AT

Average Waiting Time = Total Waiting Time / Number of Processes

Average Turnaround Time = Total Turnaround Time / Number of Processes
```

**Important:** If a process is preempted, its waiting time is the total time spent waiting in the Ready Queue, not just its first waiting period.

---

# 3.15 Important Placement Interview Questions

## Basic Questions

1. What is CPU Scheduling?
2. Why is CPU Scheduling required?
3. What is a CPU Burst?
4. What is an I/O Burst?
5. What is the difference between CPU-bound and I/O-bound processes?
6. What is CPU Utilization?
7. What is Throughput?
8. What is Turnaround Time?
9. What is Waiting Time?
10. What is Response Time?

## Intermediate Questions

11. What is the difference between preemptive and non-preemptive scheduling?
12. Explain FCFS Scheduling.
13. What is the Convoy Effect?
14. Explain SJF Scheduling.
15. Why does SJF minimize average waiting time when all processes are available together?
16. What is SRTF?
17. What is the difference between SJF and SRTF?
18. Explain Priority Scheduling.
19. What is Starvation?
20. How does Aging prevent starvation?
21. Explain Round Robin Scheduling.
22. What is Time Quantum?
23. What happens when the time quantum is too small?
24. What happens when the time quantum is very large?
25. What is Multilevel Queue Scheduling?
26. What is Multilevel Feedback Queue Scheduling?

## Advanced / Scenario-Based Questions

27. Which scheduling algorithm is best for interactive systems?
28. Which scheduling algorithm can suffer from the Convoy Effect?
29. Which scheduling algorithms can suffer from starvation?
30. Can SJF be preemptive?
31. Why is SRTF called the preemptive version of SJF?
32. Can Round Robin behave like FCFS?
33. Which scheduling algorithm is suitable for time-sharing systems?
34. What is Dispatch Latency?
35. How does context-switching overhead affect CPU scheduling?
36. Is the shortest job always the first job to execute in SJF?
37. What happens when two processes have the same priority?
38. How does the OS estimate CPU burst time?
39. What is the difference between Multilevel Queue and Multilevel Feedback Queue?
40. Can a process move between queues in MLFQ?

---

# 3.16 Quick Revision Notes

- **CPU Scheduling:** Selecting a process from the Ready Queue.
- **CPU Burst:** Time spent executing on the CPU.
- **I/O Burst:** Time spent performing or waiting for I/O.
- **FCFS:** First process to arrive executes first.
- **SJF:** Process with the shortest burst time executes first.
- **SRTF:** Process with the shortest remaining time executes first.
- **Priority Scheduling:** Highest-priority process executes first.
- **Round Robin:** Each process receives a fixed time quantum.
- **Multilevel Queue:** Separate queues for different process types.
- **MLFQ:** Processes can move between queues.
- **Starvation:** A process waits indefinitely.
- **Aging:** Gradually increases the priority of waiting processes.
- **Convoy Effect:** Short processes wait behind a long process.

---

# 3.17 Important Placement Points

1. FCFS is non-preemptive.
2. SJF is non-preemptive.
3. SRTF is preemptive.
4. Round Robin is preemptive.
5. SJF gives minimum average waiting time when all jobs are available together and burst times are known.
6. SJF and Priority Scheduling can cause starvation.
7. Aging is used to prevent starvation.
8. Round Robin is suitable for time-sharing systems.
9. A very large time quantum makes Round Robin behave like FCFS.
10. A very small time quantum increases context-switching overhead.
11. Multilevel Queue Scheduling uses fixed queue assignment in its basic form.
12. MLFQ allows processes to move between queues.
13. Turnaround Time = Completion Time - Arrival Time.
14. Waiting Time = Turnaround Time - Burst Time.
15. Response Time = First CPU Start Time - Arrival Time.

---
