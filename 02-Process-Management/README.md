
# 2. Process Management

## 2.1 What is a Process?

A **Process** is a program that is currently in execution.

A process is an active entity, whereas a program is a passive entity stored on disk.

A process requires various resources such as:
- CPU time
- Main memory (RAM)
- I/O devices
- Files
- Registers

**Example:**

When we open Google Chrome, the operating system creates one or more processes to execute it.

### Characteristics of a Process

1. A process is an instance of a running program.
2. Every process has a unique Process ID (PID).
3. A process requires memory and CPU resources.
4. A process can create other processes.
5. A process moves through different execution states.

---

## 2.2 Program vs Process

| Program | Process |
|---|---|
| A program is a set of instructions. | A process is a program in execution. |
| It is a passive entity. | It is an active entity. |
| Stored on secondary storage. | Requires main memory for execution. |
| Does not have a process state. | Has different process states. |
| Does not have a PID. | Has a unique PID. |
| Example: Chrome executable file. | Example: Running Chrome application. |

**Important Interview Point:**

A single program can have multiple processes running simultaneously.

For example, multiple instances of a text editor can execute as separate processes.

---

## 2.3 Process Control Block (PCB)

A **Process Control Block (PCB)** is a data structure maintained by the operating system that stores all important information about a process.

Every process has its own PCB.

The OS uses the PCB to manage and track processes.

### Information Stored in PCB

1. **Process ID (PID):** Unique identification number of a process.
2. **Process State:** Current state of the process.
3. **Program Counter:** Address of the next instruction to execute.
4. **CPU Registers:** Stores register values of the process.
5. **CPU Scheduling Information:** Priority and scheduling queue details.
6. **Memory Management Information:** Page tables, segment tables, etc.
7. **Accounting Information:** CPU usage and execution time.
8. **I/O Status Information:** Allocated I/O devices and open files.

### PCB Diagram

```text
+----------------------------+
|    Process Control Block   |
+----------------------------+
| Process ID (PID)           |
+----------------------------+
| Process State              |
+----------------------------+
| Program Counter            |
+----------------------------+
| CPU Registers              |
+----------------------------+
| CPU Scheduling Information|
+----------------------------+
| Memory Management Info     |
+----------------------------+
| Accounting Information     |
+----------------------------+
| I/O Status Information     |
+----------------------------+
```

**Important:** PCB is essential for context switching.

---

## 2.4 Process States

A process passes through different states during its execution.

### Five Basic Process States

#### 1. New
The process is being created.

#### 2. Ready
The process is waiting in main memory for CPU allocation.

#### 3. Running
The CPU is currently executing the process.

#### 4. Waiting / Blocked
The process is waiting for an event, such as completion of an I/O operation.

#### 5. Terminated
The process has completed execution or has been stopped.

### Process State Transition Diagram

```text
             Admit
    +-----+ ---------> +-------+
    | New |            | Ready |
    +-----+            +-------+
                          |
                          | Scheduler Dispatch
                          v
                      +---------+
                      | Running |
                      +---------+
                       /   |   \
          I/O Request /    |    \ Exit
                     v     |     v
                 +---------+  +------------+
                 | Waiting |  | Terminated |
                 +---------+  +------------+
                     |
                     | I/O Completion
                     v
                  +-------+
                  | Ready |
                  +-------+

Running -> Ready : Interrupt / Time Quantum Expired
```

### State Transitions

| Transition | Explanation |
|---|---|
| New → Ready | Process is admitted into memory. |
| Ready → Running | CPU scheduler selects the process. |
| Running → Waiting | Process requests I/O or waits for an event. |
| Waiting → Ready | Required event or I/O operation completes. |
| Running → Ready | Process is preempted or its time quantum expires. |
| Running → Terminated | Process finishes execution. |

**Important Interview Point:**

A process in the Waiting state cannot directly move to the Running state. It must first move to the Ready state.

---

## 2.5 Process Scheduling Queues

The operating system maintains different queues to manage processes.

### 1. Job Queue

Contains all processes submitted to the system.

### 2. Ready Queue

Contains processes that are ready and waiting for CPU allocation.

### 3. Waiting / Device Queue

Contains processes waiting for I/O operations or other events.

### Example

Suppose three processes P1, P2 and P3 are present.

- P1 is executing on the CPU.
- P2 is waiting for CPU allocation.
- P3 is waiting for disk I/O.

Then:
- P1 → Running
- P2 → Ready Queue
- P3 → Device Queue

---

## 2.6 Types of Schedulers

A scheduler is an operating system component that selects processes for execution or resource allocation.

There are three major types of schedulers.

### 1. Long-Term Scheduler (Job Scheduler)

The Long-Term Scheduler selects processes from the job pool and loads them into main memory.

**Functions:**
- Controls the degree of multiprogramming.
- Selects which processes enter the system.
- Maintains a balance between CPU-bound and I/O-bound processes.

**Execution Frequency:** Relatively less frequent.

### 2. Short-Term Scheduler (CPU Scheduler)

The Short-Term Scheduler selects a process from the Ready Queue and allocates the CPU to it.

**Functions:**
- Selects the next process for execution.
- Decides CPU allocation.
- Plays an important role in CPU scheduling.

**Execution Frequency:** Very frequent.

### 3. Medium-Term Scheduler

The Medium-Term Scheduler temporarily removes processes from main memory and later brings them back.

This process is called **Swapping**.

**Functions:**
- Reduces the degree of multiprogramming.
- Manages memory pressure.
- Suspends and resumes processes.

### Comparison of Schedulers

| Long-Term | Short-Term | Medium-Term |
|---|---|---|
| Selects jobs from job pool. | Selects processes from ready queue. | Suspends and resumes processes. |
| Controls admission. | Allocates CPU. | Performs swapping. |
| Less frequent. | Very frequent. | Occasional. |
| Controls degree of multiprogramming. | Controls CPU execution. | Helps manage memory. |

---

## 2.7 Context Switching

**Context Switching** is the process of saving the state of the currently running process and loading the state of another process.

The OS uses the PCB to save and restore process information.

### Steps Involved

1. An interrupt or scheduling event occurs.
2. OS saves the current process context in its PCB.
3. The scheduler selects another process.
4. OS loads the selected process context from its PCB.
5. The selected process resumes execution.

### Example

Suppose P1 is running and P2 is waiting in the Ready Queue.

When P1's time quantum expires:

- The OS saves P1's context.
- P1 moves to the Ready state.
- P2 is selected.
- P2's context is restored.
- P2 starts executing.

### Context Switching Diagram

```text
       Process P1 Running
               |
               v
      Save P1 Context in PCB
               |
               v
       Scheduler Selects P2
               |
               v
      Restore P2 Context
               |
               v
       Process P2 Running
```

**Important Points:**

- Context switching introduces overhead.
- During context switching, CPU does not execute useful work for the switched processes.
- Context switching is essential for multitasking.
- It can happen due to interrupts, system calls, or scheduling decisions.

---

## 2.8 Process Creation

A process can create another process during execution.

The process that creates another process is called the **Parent Process**.

The newly created process is called the **Child Process**.

### Process Creation in UNIX/Linux

The `fork()` system call is used to create a new process.

Example:

```c
#include <stdio.h>
#include <unistd.h>

int main() {
    fork();

    printf("Hello World\n");

    return 0;
}
```

**Output:**

```text
Hello World
Hello World
```

Explanation:

- Initially, one process exists.
- `fork()` creates a child process.
- Both parent and child execute the next statement.
- Therefore, the message is printed twice.

**Important:** The exact order of execution is not guaranteed.

### Return Values of fork()

| Return Value | Meaning |
|---|---|
| 0 | Executed by child process. |
| Positive value | Executed by parent; value is child PID. |
| -1 | Process creation failed. |

---

## 2.9 Process Termination

Process termination occurs when a process completes execution or is stopped by the operating system.

### Reasons for Termination

1. Normal completion.
2. Execution error.
3. Fatal error.
4. Termination by another process.
5. User requests termination.
6. Parent process or OS terminates the process.

### exit() System Call

The `exit()` system call terminates the calling process and returns an exit status to the operating system.

Example:

```c
#include <stdlib.h>

int main() {
    exit(0);
}
```

Here, `0` indicates successful termination by convention.

---

## 2.10 Inter-Process Communication (IPC)

**Inter-Process Communication (IPC)** is a mechanism that allows different processes to communicate and exchange information with each other.

Processes may need to communicate to:
- Share data.
- Coordinate execution.
- Exchange messages.
- Synchronize activities.

### Two Main IPC Models

#### 1. Shared Memory

Shared Memory allows multiple processes to access a common memory region.

**Working:**
1. OS creates a shared memory region.
2. Multiple processes attach to that region.
3. Processes read and write shared data.
4. Synchronization mechanisms are used to prevent race conditions.

**Advantages:**
- Fast communication.
- Efficient for large data transfers.
- Less data copying after setup.

**Disadvantages:**
- Requires synchronization.
- Can cause race conditions.
- More complex to manage safely.

#### 2. Message Passing

Message Passing allows processes to communicate by sending and receiving messages.

**Operations:**
- send(message)
- receive(message)

**Advantages:**
- Easier process isolation.
- No shared address space is required.
- Useful in distributed systems.

**Disadvantages:**
- Can have communication overhead.
- Data copying may reduce performance.

### Shared Memory vs Message Passing

| Shared Memory | Message Passing |
|---|---|
| Uses a common memory region. | Uses send and receive operations. |
| Usually faster for large data transfers. | May involve communication overhead. |
| Requires explicit synchronization. | Communication is managed through messages. |
| Suitable for processes on the same machine. | Can also support distributed communication. |

---

## 2.11 Orphan Process

An **Orphan Process** is a child process whose parent process terminates before the child process.

In UNIX-like systems, orphan processes are typically adopted by a designated system process or a subreaper.

### Example

```text
Parent Process
      |
      v
Child Process

Parent terminates first.

Child Process continues execution.
```

**Important Points:**
- An orphan process is still running.
- It is not necessarily an error.
- It is typically adopted by another process.

---

## 2.12 Zombie Process

A **Zombie Process** is a process that has completed execution, but its parent has not yet collected its termination status using `wait()` or a related system call.

A zombie process has finished execution but retains a process table entry.

### Example

```text
Parent Process
      |
      v
Child Process
      |
      v
Child terminates
      |
      v
Exit status remains uncollected
      |
      v
Zombie Process
```

### How to Prevent Zombie Processes?

The parent should collect the child's exit status using:

- `wait()`
- `waitpid()`

### Orphan vs Zombie Process

| Orphan Process | Zombie Process |
|---|---|
| Parent terminates before child. | Child terminates before parent collects its status. |
| Child is still executing. | Child has already terminated. |
| Gets adopted by another process. | Remains as a process table entry. |
| Not necessarily problematic. | Many zombies can consume process table resources. |

---

## 2.13 Important Process Management Terms

### 1. PID (Process ID)

A unique numerical identifier assigned to each process by the OS.

### 2. PPID (Parent Process ID)

The Process ID of the parent process.

### 3. CPU-Bound Process

A process that spends most of its time performing CPU computations.

**Example:** Mathematical calculations and data processing.

### 4. I/O-Bound Process

A process that spends most of its time performing input/output operations.

**Example:** Reading files and waiting for network responses.

### 5. Degree of Multiprogramming

The number of processes present in main memory at a given time.

### 6. Dispatcher

The dispatcher is the OS component that gives control of the CPU to the process selected by the short-term scheduler.

Its functions include:
- Context switching.
- Switching to user mode.
- Jumping to the correct instruction in the selected process.

### 7. Dispatch Latency

The time required by the dispatcher to stop one process and start another.

---

## 2.14 Important Interview Questions

### Basic Questions

1. What is a process?
2. What is the difference between a program and a process?
3. What is a PCB?
4. What information is stored in a PCB?
5. What are the five basic process states?
6. What is a Ready Queue?
7. What is a Waiting Queue?
8. What is a Process ID?
9. What is a Parent Process?
10. What is a Child Process?

### Intermediate Questions

11. What is context switching?
12. Why is context switching considered overhead?
13. What are the different types of schedulers?
14. What is the difference between long-term and short-term schedulers?
15. What is the role of a medium-term scheduler?
16. What is swapping?
17. What is IPC?
18. What is the difference between shared memory and message passing?
19. What is an orphan process?
20. What is a zombie process?

### Advanced / Scenario-Based Questions

21. Can a process move directly from Waiting to Running?
22. Can a process have multiple child processes?
23. What happens when `fork()` is called?
24. What happens if a parent terminates before its child?
25. What happens if a child terminates before its parent calls `wait()`?
26. Can two processes have the same program code?
27. Can multiple processes execute the same program?
28. What is the difference between a CPU-bound and I/O-bound process?
29. What is dispatch latency?
30. What happens during a context switch?

---

## 2.15 Quick Revision Notes

- **Process:** A program in execution.
- **PCB:** Data structure containing process information.
- **PID:** Unique process identifier.
- **PPID:** Parent process identifier.
- **New:** Process is being created.
- **Ready:** Waiting for CPU allocation.
- **Running:** Currently executing.
- **Waiting:** Waiting for an event or I/O.
- **Terminated:** Execution has finished.
- **Context Switching:** Saving and restoring process context.
- **Long-Term Scheduler:** Selects jobs for admission.
- **Short-Term Scheduler:** Selects the next process for CPU.
- **Medium-Term Scheduler:** Handles process suspension and swapping.
- **IPC:** Communication between processes.
- **Orphan:** Child whose parent terminates first.
- **Zombie:** Terminated child whose exit status has not been collected.
- **Dispatcher:** Transfers CPU control to the selected process.

---

## 2.16 Important Placement Points

1. A process is an active entity; a program is a passive entity.
2. Each process has its own PCB.
3. PCB stores the process state and CPU context.
4. Context switching is necessary for multitasking.
5. Context switching introduces overhead.
6. The short-term scheduler selects processes from the Ready Queue.
7. The long-term scheduler controls the degree of multiprogramming.
8. The medium-term scheduler handles swapping.
9. `fork()` creates a child process in UNIX/Linux.
10. Shared memory and message passing are major IPC models.
11. An orphan process is still executing.
12. A zombie process has already terminated.
13. A process in the Waiting state must return to Ready before being scheduled for execution.

---
