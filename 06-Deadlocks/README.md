
# Unit 6: Deadlocks

## 1. Introduction to Deadlock

A deadlock is a situation in an operating system where a group of processes gets blocked indefinitely because every process is waiting for a resource held by another process in the group.

As a result, none of the processes can proceed.

### Example

Suppose there are two processes and two resources:

- Process P1 holds Resource R1 and requests R2.
- Process P2 holds Resource R2 and requests R1.

```text
P1 holds R1 and waits for R2
P2 holds R2 and waits for R1
```

Neither process can continue.

This situation is called **Deadlock**.

### Real-Life Example

Imagine two people:

- Person A has a pen and needs a notebook.
- Person B has a notebook and needs a pen.

Neither person is willing to release their resource.

Both wait forever.

---

## 2. Necessary Conditions for Deadlock

A deadlock can occur only when all four Coffman conditions hold simultaneously.

These are:

1. Mutual Exclusion
2. Hold and Wait
3. No Preemption
4. Circular Wait

### 2.1 Mutual Exclusion

At least one resource must be non-shareable.

Only one process can use that resource at a time.

Example:

A printer can be allocated to only one process at a time.

### 2.2 Hold and Wait

A process is holding at least one resource while waiting for additional resources held by other processes.

Example:

P1 holds R1 and waits for R2.

### 2.3 No Preemption

Resources cannot be forcibly taken away from a process.

A process must release its resources voluntarily after completing its task.

Example:

A printer allocated to a process cannot be forcibly taken away while it is printing.

### 2.4 Circular Wait

A circular chain of processes exists where each process waits for a resource held by the next process.

Example:

```text
P1 waits for R2
P2 waits for R3
P3 waits for R1
```

This creates a circular dependency.

### Summary

| Condition | Meaning |
|---|---|
| Mutual Exclusion | Resource cannot be shared simultaneously |
| Hold and Wait | Process holds one resource and waits for another |
| No Preemption | Resource cannot be forcibly taken away |
| Circular Wait | Circular chain of resource dependencies |

**Important Interview Point:**

All four conditions must be present simultaneously for deadlock to occur.

If even one condition is eliminated, deadlock can be prevented.

---

## 3. Resource Allocation Graph (RAG)

A Resource Allocation Graph is a directed graph used to represent resource allocation and requests among processes.

It helps identify possible deadlocks.

### Components of RAG

There are two types of nodes:

**1. Process Node**

Represented by a circle.

Example:

```text
(P1)
```

**2. Resource Node**

Represented by a rectangle.

Example:

```text
[R1]
```

Each dot inside a resource box represents one instance of that resource.

### Types of Edges

**1. Request Edge**

A directed edge from a process to a resource.

```text
P1 ---> R1
```

Meaning:

Process P1 is requesting Resource R1.

**2. Assignment Edge**

A directed edge from a resource to a process.

```text
R1 ---> P1
```

Meaning:

Resource R1 has been allocated to Process P1.

### Example 1: Deadlock

Suppose:

- R1 is allocated to P1.
- P1 requests R2.
- R2 is allocated to P2.
- P2 requests R1.

```text
[R1] ---> (P1) ---> [R2]
  ^                    |
  |                    |
  +------ (P2) <-------+
```

This represents a circular wait.

### Important Rules

| Graph Situation | Meaning |
|---|---|
| No cycle | No deadlock |
| Cycle with one instance per resource type | Deadlock exists |
| Cycle with multiple instances per resource type | Deadlock may or may not exist |

**Important:** When each resource type has exactly one instance, a cycle is both necessary and sufficient for deadlock.

When resource types have multiple instances, a cycle is necessary but not sufficient.

---

## 4. Methods for Handling Deadlocks

An operating system can handle deadlocks using four major approaches.

1. Deadlock Prevention
2. Deadlock Avoidance
3. Deadlock Detection
4. Deadlock Recovery

```text
             Deadlock Handling
                    |
       +------------+------------+
       |            |            |
       v            v            v
   Prevention   Avoidance    Detection
                                  |
                                  v
                               Recovery
```

### 4.1 Deadlock Prevention

Deadlock prevention ensures that at least one of the four necessary conditions for deadlock never occurs.

#### Method 1: Eliminate Mutual Exclusion

Make resources shareable wherever possible.

Example:

Use printer spooling to allow multiple processes to submit print jobs without directly holding the physical printer.

Limitation:

Some resources are inherently non-shareable.

#### Method 2: Eliminate Hold and Wait

Require a process to request all required resources before execution begins.

Alternatively, require it to release currently held resources before requesting new ones.

Advantages:
- Prevents hold and wait.
- Can prevent deadlock.

Disadvantages:
- Poor resource utilization.
- Processes may hold resources without using them.
- Starvation may occur.

#### Method 3: Eliminate No Preemption

If a process requests a resource that is unavailable, it may be required to release the resources it already holds.

Those resources can be reallocated.

Limitation:

This approach is not suitable for every resource, such as a printer in the middle of a printing operation.

#### Method 4: Eliminate Circular Wait

Assign a fixed ordering to all resource types.

Processes must request resources only in increasing order.

Example:

```text
R1 < R2 < R3 < R4
```

A process holding R1 can request R2.

A process holding R3 cannot request R2.

This prevents circular waiting.

### Deadlock Prevention Summary

| Condition | Prevention Method |
|---|---|
| Mutual Exclusion | Make resources shareable where possible |
| Hold and Wait | Request all resources together |
| No Preemption | Allow resource preemption where possible |
| Circular Wait | Impose resource ordering |

---

## 5. Deadlock Avoidance

Deadlock avoidance is a technique in which the operating system examines each resource request before granting it.

A request is granted only if the resulting system state remains safe.

### Safe State

A system is in a safe state if there exists at least one sequence in which every process can obtain its remaining required resources, finish execution, and release its resources.

This sequence is called a **Safe Sequence**.

Example:

```text
P2 -> P1 -> P3
```

If all processes can finish in this order, the system is safe.

### Unsafe State

A system is in an unsafe state if no safe sequence exists.

An unsafe state does not necessarily mean that deadlock has already occurred.

It means the system cannot guarantee that all processes will finish.

### Important Difference

| Safe State | Unsafe State |
|---|---|
| At least one safe sequence exists | No safe sequence exists |
| Deadlock can be avoided | Deadlock may become possible |
| System can guarantee completion under the model | System cannot guarantee completion |

**Important Interview Question:**

Does an unsafe state always mean deadlock?

Answer: No. An unsafe state means deadlock is possible, not that deadlock has already occurred.

---

## 6. Banker's Algorithm

Banker's Algorithm is a deadlock avoidance algorithm.

It was proposed by Edsger W. Dijkstra.

It checks whether allocating resources to a process will leave the system in a safe state.

If the system remains safe, the request can be granted.

Otherwise, the request must be delayed.

### Why is it called Banker's Algorithm?

A bank gives loans only when it can ensure that all customers can eventually receive the resources they need and the bank does not enter an unsafe state.

Similarly, an operating system allocates resources only if the resulting state is safe.

### Data Structures Used

Banker's Algorithm uses the following data structures.

#### 1. Available

Available[j] represents the number of available instances of resource type j.

#### 2. Max

Max[i][j] represents the maximum number of instances of resource type j that process Pi may need.

#### 3. Allocation

Allocation[i][j] represents the number of instances of resource type j currently allocated to process Pi.

#### 4. Need

Need[i][j] represents the remaining resources required by process Pi.

Formula:

```text
Need = Max - Allocation
```

### Banker's Algorithm Formula

```text
Need[i][j] = Max[i][j] - Allocation[i][j]
```

### Safety Algorithm

The safety algorithm checks whether a safe sequence exists.

Steps:

1. Initialize Work = Available.
2. Initialize Finish[i] = false for every process.
3. Find a process Pi such that:
   - Finish[i] == false
   - Need[i] <= Work
4. If found:
   - Work = Work + Allocation[i]
   - Finish[i] = true
   - Add Pi to the safe sequence.
5. Repeat until no more processes can be selected.
6. If all Finish[i] are true, the system is safe.
7. Otherwise, the system is unsafe.

### Resource Request Algorithm

Suppose process Pi requests resources represented by Request[i].

Steps:

1. Check whether Request[i] <= Need[i].
   - If false, the process has exceeded its declared maximum.
2. Check whether Request[i] <= Available.
   - If false, the process must wait.
3. Temporarily allocate resources:
   - Available = Available - Request[i]
   - Allocation[i] = Allocation[i] + Request[i]
   - Need[i] = Need[i] - Request[i]
4. Run the safety algorithm.
5. If the system remains safe, grant the request.
6. Otherwise, restore the previous state and make the process wait.

---

## 7. Solved Numerical: Banker's Algorithm

Consider a system containing:

- 5 processes: P0, P1, P2, P3, P4
- 3 resource types: A, B, C

### Available Resources

```text
Available = [3, 3, 2]
```

### Allocation Matrix

| Process | A | B | C |
|---|---:|---:|---:|
| P0 | 0 | 1 | 0 |
| P1 | 2 | 0 | 0 |
| P2 | 3 | 0 | 2 |
| P3 | 2 | 1 | 1 |
| P4 | 0 | 0 | 2 |

### Maximum Matrix

| Process | A | B | C |
|---|---:|---:|---:|
| P0 | 7 | 5 | 3 |
| P1 | 3 | 2 | 2 |
| P2 | 9 | 0 | 2 |
| P3 | 2 | 2 | 2 |
| P4 | 4 | 3 | 3 |

### Step 1: Calculate Need Matrix

Formula:

```text
Need = Max - Allocation
```

For P0:

```text
Need[P0] = [7, 5, 3] - [0, 1, 0]
         = [7, 4, 3]
```

For P1:

```text
Need[P1] = [3, 2, 2] - [2, 0, 0]
         = [1, 2, 2]
```

For P2:

```text
Need[P2] = [9, 0, 2] - [3, 0, 2]
         = [6, 0, 0]
```

For P3:

```text
Need[P3] = [2, 2, 2] - [2, 1, 1]
         = [0, 1, 1]
```

For P4:

```text
Need[P4] = [4, 3, 3] - [0, 0, 2]
         = [4, 3, 1]
```

### Need Matrix

| Process | A | B | C |
|---|---:|---:|---:|
| P0 | 7 | 4 | 3 |
| P1 | 1 | 2 | 2 |
| P2 | 6 | 0 | 0 |
| P3 | 0 | 1 | 1 |
| P4 | 4 | 3 | 1 |

### Step 2: Find Safe Sequence

Initially:

```text
Work = Available = [3, 3, 2]
```

Check processes one by one.

**Process P0**

```text
Need[P0] = [7, 4, 3]
Work      = [3, 3, 2]
```

P0 cannot execute because its Need is greater than Work.

**Process P1**

```text
Need[P1] = [1, 2, 2]
Work      = [3, 3, 2]
```

P1 can execute because:

```text
[1, 2, 2] <= [3, 3, 2]
```

After P1 finishes, it releases its allocated resources.

```text
Work = Work + Allocation[P1]

Work = [3, 3, 2] + [2, 0, 0]

Work = [5, 3, 2]
```

Safe sequence so far:

```text
P1
```

**Process P3**

```text
Need[P3] = [0, 1, 1]
Work      = [5, 3, 2]
```

P3 can execute.

After P3 finishes:

```text
Work = [5, 3, 2] + [2, 1, 1]

Work = [7, 4, 3]
```

Safe sequence:

```text
P1 -> P3
```

**Process P4**

```text
Need[P4] = [4, 3, 1]
Work      = [7, 4, 3]
```

P4 can execute.

After P4 finishes:

```text
Work = [7, 4, 3] + [0, 0, 2]

Work = [7, 4, 5]
```

Safe sequence:

```text
P1 -> P3 -> P4
```

**Process P0**

```text
Need[P0] = [7, 4, 3]
Work      = [7, 4, 5]
```

P0 can execute.

After P0 finishes:

```text
Work = [7, 4, 5] + [0, 1, 0]

Work = [7, 5, 5]
```

Safe sequence:

```text
P1 -> P3 -> P4 -> P0
```

**Process P2**

```text
Need[P2] = [6, 0, 0]
Work      = [7, 5, 5]
```

P2 can execute.

After P2 finishes:

```text
Work = [7, 5, 5] + [3, 0, 2]

Work = [10, 5, 7]
```

### Final Result

<text>
Safe Sequence:
</text>

```text
P1 -> P3 -> P4 -> P0 -> P2
```

<text>
The system is in a SAFE STATE.
</text>

All processes can complete execution.

---

## 8. Deadlock Detection

Deadlock detection is a technique in which the operating system allows deadlocks to occur and then checks whether any deadlock exists.

Unlike prevention and avoidance, it does not try to stop every unsafe allocation.

### Detection with Single Instance of Each Resource Type

A Wait-For Graph can be used.

### Wait-For Graph

A Wait-For Graph is derived from a Resource Allocation Graph by removing resource nodes.

An edge:

```text
P1 ---> P2
```

means P1 is waiting for a resource currently held by P2.

### Example

```text
P1 ---> P2
 ^       |
 |       v
 +-------P3
```

If the wait-for graph contains a cycle, a deadlock exists.

This method applies when each resource type has a single instance.

### Detection with Multiple Instances

For multiple instances of resource types, a detection algorithm similar to the Banker's safety algorithm is used.

Data structures:

- Available
- Allocation
- Request

Here, Request[i] represents the current outstanding resource request of process Pi.

### Detection Algorithm

1. Initialize Work = Available.
2. For each process:
   - If Allocation[i] is zero, set Finish[i] = true.
   - Otherwise, set Finish[i] = false.
3. Find a process Pi where:
   - Finish[i] == false
   - Request[i] <= Work
4. If found:
   - Work = Work + Allocation[i]
   - Finish[i] = true
   - Repeat the search.
5. If no such process can be found, stop.
6. Any process with Finish[i] == false is considered deadlocked.

---

## 9. Deadlock Recovery

Once a deadlock is detected, the operating system must recover from it.

Two common approaches are:

### 9.1 Process Termination

The operating system can terminate processes to break the deadlock.

Two methods:

**Method 1: Abort All Deadlocked Processes**

Terminate all processes involved in the deadlock.

Advantages:
- Simple.
- Quickly removes the deadlock.

Disadvantages:
- Significant loss of work.
- May require restarting many processes.

**Method 2: Abort One Process at a Time**

Terminate one process at a time until the deadlock cycle is removed.

Advantages:
- Less work may be lost.

Disadvantages:
- Requires repeated deadlock detection.
- Recovery may take longer.

### 9.2 Resource Preemption

The operating system takes resources away from some processes and reallocates them.

Important issues:

**1. Selecting a Victim**

Choose a process whose resource preemption has the lowest cost.

**2. Rollback**

The process may need to return to a previous safe checkpoint.

**3. Starvation**

The same process should not repeatedly be selected as the victim.

A cost-based victim-selection policy can help prevent starvation.

---

## 10. Deadlock Prevention vs Avoidance vs Detection

This is one of the most important placement interview questions.

| Feature | Prevention | Avoidance | Detection |
|---|---|---|---|
| Basic idea | Break at least one necessary condition | Grant only safe requests | Allow deadlock and detect it |
| Resource allocation | Restricted | Based on safety check | Generally unrestricted |
| Knowledge required | Resource properties and allocation rules | Maximum resource requirements | Current allocation and outstanding requests |
| Overhead | Depends on prevention policy | Safety checks for requests | Periodic or on-demand detection |
| Deadlock | Prevented by design | Avoided under model assumptions | May occur |
| Example | Resource ordering | Banker's Algorithm | Wait-For Graph |

### Recovery vs Prevention

| Prevention | Recovery |
|---|---|
| Stops deadlock from occurring | Resolves an existing deadlock |
| Changes resource allocation rules | Terminates processes or preempts resources |
| Applied before deadlock | Applied after detection |

---

## 11. Deadlock vs Starvation vs Livelock

| Deadlock | Starvation | Livelock |
|---|---|---|
| Processes are blocked indefinitely | A process waits indefinitely for resources or CPU time | Processes remain active but make no useful progress |
| Usually involves circular waiting | Caused by unfair allocation or scheduling | Caused by repeated reactions |
| Other processes in the deadlock group cannot proceed | Other processes may continue | Processes keep changing state |
| Example: P1 waits for P2 and P2 waits for P1 | Low-priority process never gets CPU | Two processes repeatedly release and retry resources |

---

## 12. Important Formulas

### Need Matrix

```text
Need = Max - Allocation
```

### Available Resources

For each resource type j:

```text
Available[j] = Total[j] - Sum of Allocation[i][j]
```

### Work Update in Safety Algorithm

```text
Work = Work + Allocation[i]
```

### Resource Request Validation

```text
Request[i] <= Need[i]
```

and

```text
Request[i] <= Available
```

Both must be satisfied before temporarily allocating the requested resources.

---

## 13. Important Interview Questions and Answers

### Q1. What is deadlock?

Deadlock is a condition where a group of processes waits indefinitely for resources held by one another.

### Q2. What are the four necessary conditions for deadlock?

1. Mutual Exclusion
2. Hold and Wait
3. No Preemption
4. Circular Wait

### Q3. What is a Resource Allocation Graph?

It is a directed graph representing processes, resources, resource requests, and resource allocations.

### Q4. What is the difference between a request edge and an assignment edge?

- Request edge: Process to Resource.
- Assignment edge: Resource to Process.

### Q5. What is a safe state?

A safe state is one in which at least one safe sequence exists that allows every process to finish.

### Q6. Does an unsafe state always mean deadlock?

No. An unsafe state means deadlock is possible, not that it has already occurred.

### Q7. What is a safe sequence?

A safe sequence is an ordering of processes in which each process can obtain its remaining resources, complete execution, and release its resources.

### Q8. What is Banker's Algorithm?

It is a deadlock avoidance algorithm that grants resource requests only when the resulting system state is safe.

### Q9. Why is it called Banker's Algorithm?

It resembles a bank granting loans only when it can still satisfy the needs of all customers under the model.

### Q10. What is the difference between deadlock prevention and avoidance?

Prevention breaks at least one necessary deadlock condition, while avoidance checks whether each allocation keeps the system in a safe state.

### Q11. What is a Wait-For Graph?

A graph showing which process is waiting for another process. A cycle indicates deadlock when each resource type has one instance.

### Q12. Can a Resource Allocation Graph have a cycle without deadlock?

Yes, when resource types have multiple instances, a cycle does not necessarily imply deadlock.

### Q13. What is deadlock recovery?

It is the process of removing an existing deadlock through process termination or resource preemption.

### Q14. What is resource preemption?

It is the act of taking a resource away from a process and reallocating it to another process.

### Q15. What is rollback?

Rollback restores a process to a previously saved checkpoint so that execution can restart from that point.

### Q16. How can circular wait be prevented?

Assign a fixed ordering to resource types and require processes to request resources only in that order.

### Q17. Which algorithm is used for deadlock avoidance?

Banker's Algorithm.

### Q18. Which graph is used for deadlock detection with single-instance resource types?

Wait-For Graph.

### Q19. What is the difference between deadlock and starvation?

Deadlock blocks a group of processes due to circular resource dependencies. Starvation occurs when a process repeatedly fails to obtain the resources it needs while other processes continue.

### Q20. Can deadlock occur with only one process?

A classical resource deadlock requires a circular dependency among processes. A single process can block waiting for an unavailable resource, but that is not the usual multi-process deadlock situation.

---

## 14. Quick Revision Notes

| Concept | Key Point |
|---|---|
| Deadlock | Processes wait indefinitely for one another |
| Coffman Conditions | Four necessary conditions |
| Mutual Exclusion | Resource is non-shareable |
| Hold and Wait | Holds resources while requesting more |
| No Preemption | Resources cannot be forcibly taken |
| Circular Wait | Circular chain of waiting processes |
| RAG | Represents resource allocation |
| Request Edge | Process -> Resource |
| Assignment Edge | Resource -> Process |
| Safe State | At least one safe sequence exists |
| Unsafe State | No safe sequence exists |
| Banker's Algorithm | Deadlock avoidance |
| Wait-For Graph | Detects deadlock for single-instance resource types |
| Prevention | Break at least one necessary condition |
| Avoidance | Grant only safe requests |
| Detection | Identify existing deadlocks |
| Recovery | Terminate processes or preempt resources |
| Rollback | Return to a previous checkpoint |

---

## 15. Most Important Topics for Placements

Focus especially on:

1. Four necessary conditions of deadlock.
2. Resource Allocation Graph and cycle detection.
3. Difference between safe and unsafe states.
4. Deadlock prevention vs avoidance.
5. Banker's Algorithm.
6. Solving Need Matrix numericals.
7. Finding safe sequences.
8. Resource Request Algorithm.
9. Wait-For Graph.
10. Deadlock detection and recovery.

### Numerical Practice Checklist

Make sure you can solve these independently:

- Calculate Need from Max and Allocation.
- Calculate Available from Total and Allocation.
- Find a safe sequence using Banker's Algorithm.
- Check whether a resource request can be granted.
- Identify deadlocked processes using the detection algorithm.
- Identify cycles in Resource Allocation Graphs.

---

## 16. Unit 6 Summary

Deadlock is a situation where processes wait indefinitely for resources held by one another.

The four necessary conditions are:

1. Mutual Exclusion
2. Hold and Wait
3. No Preemption
4. Circular Wait

The four major approaches to handling deadlocks are:

- Prevention
- Avoidance
- Detection
- Recovery

**Remember for placements:**

- Banker's Algorithm is used for deadlock avoidance.
- Wait-For Graph is used for deadlock detection with single-instance resource types.
- A safe state guarantees at least one safe sequence.
- An unsafe state does not necessarily mean deadlock.
- A cycle in a Resource Allocation Graph guarantees deadlock only when each resource type has a single instance.
