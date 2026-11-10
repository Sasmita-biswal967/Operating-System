
# Unit 5: Process Synchronization

## 1. Introduction to Process Synchronization

Process synchronization is a mechanism used to coordinate multiple processes or threads executing concurrently while accessing shared resources.

When multiple processes access shared data simultaneously, the data may become inconsistent.

Synchronization ensures that:
- Shared data remains consistent.
- Only permitted processes access critical resources.
- Processes execute in a coordinated manner.
- Race conditions are avoided.

### Example

Suppose two processes access a shared variable:

```c
int count = 5;
```

Both processes execute:

```c
count = count + 1;
```

The expected final value is 7.

However, both processes may read `count = 5` before either writes the updated value.

Both then write 6.

Final value = 6 instead of 7.

This is an example of a **race condition**.

---

## 2. Race Condition

A race condition occurs when multiple processes or threads access and modify shared data concurrently, and the final result depends on the order of execution.

### Example

Initially:

```text
count = 5
```

Two threads execute `count++`.

| Step | Thread 1 | Thread 2 |
|---|---|---|
| 1 | Read count = 5 | |
| 2 | | Read count = 5 |
| 3 | Increment to 6 | |
| 4 | | Increment to 6 |
| 5 | Write 6 | |
| 6 | | Write 6 |

Final result:

```text
count = 6
```

Expected result:

```text
count = 7
```

This happens because the increment operation is not necessarily atomic.

### How to prevent race conditions?

- Mutex locks
- Semaphores
- Monitors
- Atomic operations
- Critical-section protection

---

## 3. Critical Section Problem

A critical section is a part of a program where shared resources or shared data are accessed.

Only one process should execute a critical section at a time when exclusive access is required.

### Structure of a Process

A process can be divided into four sections:

1. Entry Section
2. Critical Section
3. Exit Section
4. Remainder Section

```text
       +------------------+
       |   Entry Section  |
       +------------------+
                |
                v
       +------------------+
       | Critical Section |
       +------------------+
                |
                v
       +------------------+
       |   Exit Section   |
       +------------------+
                |
                v
       +------------------+
       | Remainder Section|
       +------------------+
                |
                |
                +------> Repeat
```

### Explanation

**1. Entry Section**

A process requests permission to enter the critical section.

**2. Critical Section**

The process accesses shared resources.

**3. Exit Section**

The process releases access to the critical section.

**4. Remainder Section**

The process performs other operations that do not require exclusive access to the shared resource.

---

## 4. Requirements of a Critical Section Solution

A correct solution to the critical section problem must satisfy three conditions.

### 4.1 Mutual Exclusion

Only one process can execute inside the critical section at a time.

If process P1 is executing in its critical section, no other process can enter its critical section for the same protected resource.

### 4.2 Progress

If no process is executing in the critical section and some processes want to enter, the selection of the next process must not be postponed indefinitely.

Only processes that want to enter the critical section should participate in this decision.

### 4.3 Bounded Waiting

There must be a limit on the number of times other processes can enter their critical sections after a process has requested entry.

This prevents starvation.

### Quick Comparison

| Condition | Meaning |
|---|---|
| Mutual Exclusion | Only one process enters at a time |
| Progress | Selection of the next process cannot be postponed indefinitely |
| Bounded Waiting | No process waits forever after requesting entry |

**Interview Tip:** Mutual exclusion, progress, and bounded waiting are the three essential requirements of the critical section problem.

---

## 5. Peterson's Solution

Peterson's solution is a software-based synchronization algorithm that solves the critical section problem for two processes.

It uses two shared variables:

```c
boolean flag[2] = {false, false};
int turn;
```

### Variables

- `flag[i] = true`: Process Pi wants to enter the critical section.
- `turn = j`: Gives process Pj priority when both processes want to enter.

### Algorithm for Process Pi

Here, `i` represents the current process and `j` represents the other process.

```c
do {
    flag[i] = true;
    turn = j;

    while (flag[j] && turn == j);

    // Critical Section

    flag[i] = false;

    // Remainder Section

} while (true);
```

### Working

1. Process Pi sets its flag to true, indicating its intention to enter.
2. It gives the other process priority by setting `turn = j`.
3. It waits if the other process also wants to enter and has priority.
4. Once the condition becomes false, it enters the critical section.
5. After completing execution, it sets its flag to false.

### Example

Suppose both P0 and P1 want to enter simultaneously.

```text
P0: flag[0] = true
P1: flag[1] = true

P0: turn = 1
P1: turn = 0
```

The final value of `turn` determines which process waits.

The process that does not have priority waits until the other process exits.

### Advantages

- Provides mutual exclusion.
- Provides progress.
- Provides bounded waiting.
- Does not require special hardware instructions in its theoretical model.

### Limitations

- Works only for two processes.
- Uses busy waiting.
- Assumes atomic reads and writes.
- Modern CPUs and compilers may reorder memory operations, so practical implementations need appropriate memory-ordering guarantees.

**Important:** Peterson's solution is mainly used to understand synchronization concepts. It is not generally used as a production synchronization primitive.

---

## 6. Hardware Synchronization

Modern processors provide atomic hardware instructions to support synchronization.

An atomic operation executes as one indivisible operation.

### 6.1 Test-and-Set Instruction

Test-and-Set is an atomic hardware instruction used to implement locks.

It returns the old value of a lock and sets the lock to true.

```c
boolean testAndSet(boolean *target) {
    boolean old = *target;
    *target = true;
    return old;
}
```

### Using Test-and-Set for Mutual Exclusion

```c
boolean lock = false;

do {
    while (testAndSet(&lock));

    // Critical Section

    lock = false;

    // Remainder Section

} while (true);
```

### Working

- If the lock is false, the process acquires it.
- If the lock is true, another process holds it.
- The process keeps waiting until the lock becomes available.
- After leaving the critical section, it releases the lock.

### 6.2 Compare-and-Swap (CAS)

Compare-and-Swap is another atomic hardware instruction.

It compares the current value with an expected value.

If both match, it updates the value.

```c
int compareAndSwap(int *value, int expected, int newValue) {
    int old = *value;

    if (old == expected) {
        *value = newValue;
    }

    return old;
}
```

### Example

Initially:

```text
lock = 0
```

A process executes:

```text
CAS(lock, 0, 1)
```

If `lock == 0`, it changes the lock to 1 and acquires it.

If `lock == 1`, it fails to acquire the lock.

### Test-and-Set vs Compare-and-Swap

| Test-and-Set | Compare-and-Swap |
|---|---|
| Reads old value and sets a target to true | Compares with an expected value and conditionally updates |
| Commonly used to implement simple locks | Used for locks, atomic counters, and lock-free algorithms |
| Returns the old value | Returns the observed old value |

---

## 7. Mutex Locks

A mutex (Mutual Exclusion Lock) is a synchronization mechanism that ensures only one thread can access a protected critical section at a time.

It uses two basic operations:

- `lock()`
- `unlock()`

### Example

```c
mutex lock;

do {
    lock();

    // Critical Section

    unlock();

    // Remainder Section

} while (true);
```

### Working

1. A thread calls `lock()`.
2. If the mutex is available, the thread acquires it.
3. Other threads attempting to acquire the same mutex must wait.
4. The thread performs its critical-section operations.
5. It calls `unlock()` to release the mutex.

### Advantages

- Prevents race conditions.
- Provides mutual exclusion.
- Protects shared resources.

### Disadvantages

- Incorrect locking can cause deadlocks.
- Lock contention can reduce performance.
- A thread may wait for a long time.

### Types of Mutex Behavior

**Blocking Mutex:** A thread that cannot acquire the lock is put to sleep until it can proceed.

**Spinlock:** A thread repeatedly checks the lock until it becomes available.

---

## 8. Semaphore

A semaphore is a synchronization variable used to control access to shared resources and coordinate processes or threads.

It is manipulated using two atomic operations:

1. `wait()` — also called P(), down().
2. `signal()` — also called V(), up().

### 8.1 wait() Operation

The wait operation attempts to acquire a resource.

Conceptually:

```text
wait(S):
    If S is available:
        Decrease S
    Otherwise:
        Block the process
```

### 8.2 signal() Operation

The signal operation releases a resource or notifies another waiting process.

Conceptually:

```text
signal(S):
    Increase S or wake a waiting process
```

The wait and signal operations must execute atomically.

### Types of Semaphores

#### 1. Binary Semaphore

A binary semaphore can have values 0 or 1.

It is commonly used for signaling and controlling access to a single resource.

```text
S = 1  -> Resource available
S = 0  -> Resource unavailable
```

#### 2. Counting Semaphore

A counting semaphore can have a non-negative count representing the number of available instances of a resource.

Example:

Suppose there are 5 printers.

```text
S = 5
```

Each process that acquires a printer performs:

```text
wait(S)
```

When a process releases a printer:

```text
signal(S)
```

The semaphore controls access to the five available printers.

### Binary vs Counting Semaphore

| Binary Semaphore | Counting Semaphore |
|---|---|
| Represents a binary state | Represents multiple resource instances |
| Values are 0 or 1 | Values represent available resources |
| Used for signaling or simple access control | Used for resource counting |
| Controls one logical availability state | Controls multiple instances of a resource |

---

## 9. Mutex vs Semaphore

This is one of the most frequently asked OS interview questions.

| Mutex | Semaphore |
|---|---|
| Locking mechanism | Signaling and resource-counting mechanism |
| Usually has ownership | Does not inherently have ownership |
| Usually locked and unlocked by the same thread | A different thread may signal, depending on the API and use |
| Commonly used for mutual exclusion | Used for mutual exclusion, signaling, and resource management |
| Usually protects a critical section | Can control access to multiple resource instances |
| Generally has locked/unlocked states | Binary or counting |

### Example

**Mutex:** Only one thread can modify a shared bank account balance at a time.

**Counting Semaphore:** If a system has 10 identical database connections, a counting semaphore can limit concurrent access to 10 connections.

**Important:** A binary semaphore and a mutex may look similar, but they are not identical. A mutex generally has ownership semantics, while a semaphore is a signaling/counting mechanism.

---

## 10. Classical Synchronization Problems

Three classical problems are commonly used to explain synchronization.

1. Producer-Consumer Problem
2. Readers-Writers Problem
3. Dining Philosophers Problem

### 10.1 Producer-Consumer Problem

Also called the Bounded Buffer Problem.

There are two types of processes:

**Producer:**
- Produces items.
- Places items into a shared buffer.
- Must not insert items when the buffer is full.

**Consumer:**
- Removes items from the buffer.
- Consumes produced items.
- Must not remove items when the buffer is empty.

### Problem

Suppose the buffer has a fixed capacity N.

We must ensure:

- Producer does not insert into a full buffer.
- Consumer does not remove from an empty buffer.
- Producer and consumer do not modify the buffer simultaneously.

### Semaphores Used

```text
mutex = 1
empty = N
full = 0
```

| Semaphore | Purpose |
|---|---|
| mutex | Protects the shared buffer |
| empty | Counts empty buffer slots |
| full | Counts filled buffer slots |

### Producer Algorithm

```c
do {
    produce_item();

    wait(empty);
    wait(mutex);

    add_item_to_buffer();

    signal(mutex);
    signal(full);

} while (true);
```

### Consumer Algorithm

```c
do {
    wait(full);
    wait(mutex);

    remove_item_from_buffer();

    signal(mutex);
    signal(empty);

    consume_item();

} while (true);
```

### Explanation

Producer:

1. Produces an item.
2. Waits for an empty slot.
3. Acquires the mutex.
4. Inserts the item.
5. Releases the mutex.
6. Signals that one more item is available.

Consumer:

1. Waits until an item is available.
2. Acquires the mutex.
3. Removes an item.
4. Releases the mutex.
5. Signals that one more slot is empty.
6. Consumes the item.

### Important Interview Point

The order of semaphore operations matters.

The producer must wait on `empty` before acquiring `mutex`. Similarly, the consumer must wait on `full` before acquiring `mutex`.

This avoids holding the buffer lock while waiting for a resource that cannot become available.

---

### 10.2 Readers-Writers Problem

The Readers-Writers Problem involves a shared resource, such as a database or file.

There are two types of processes:

**Readers:**
- Only read shared data.
- Multiple readers can access the resource simultaneously.

**Writers:**
- Modify shared data.
- Only one writer should access the resource at a time.
- No reader should read while a writer is modifying the resource.

### Conditions

1. Multiple readers can read simultaneously.
2. Only one writer can write at a time.
3. Readers and writers cannot access the resource simultaneously.

### Semaphore Variables

```c
semaphore mutex = 1;
semaphore rw_mutex = 1;

int read_count = 0;
```

- `mutex` protects the `read_count` variable.
- `rw_mutex` controls access to the shared resource.
- `read_count` tracks the number of active readers.

### Reader Algorithm

```c
do {
    wait(mutex);

    read_count++;

    if (read_count == 1) {
        wait(rw_mutex);
    }

    signal(mutex);

    // Reading is performed here

    wait(mutex);

    read_count--;

    if (read_count == 0) {
        signal(rw_mutex);
    }

    signal(mutex);

} while (true);
```

### Writer Algorithm

```c
do {
    wait(rw_mutex);

    // Writing is performed here

    signal(rw_mutex);

} while (true);
```

### Working

- The first reader locks the shared resource against writers.
- Additional readers can read simultaneously.
- When a reader finishes, it decreases `read_count`.
- The last reader releases the shared resource.
- A writer acquires exclusive access before writing.

### Disadvantage

This reader-preference solution can cause writer starvation.

If readers keep arriving continuously, a writer may wait indefinitely.

### Types of Readers-Writers Solutions

| Type | Description |
|---|---|
| Reader Preference | Readers get priority; writers may starve |
| Writer Preference | Writers get priority; readers may starve |
| Fair Solution | Attempts to serve readers and writers fairly |

---

### 10.3 Dining Philosophers Problem

The Dining Philosophers Problem is a classical synchronization problem.

Suppose five philosophers sit around a circular table.

Between every two philosophers, there is one fork.

Each philosopher alternates between:

- Thinking
- Eating

To eat, a philosopher needs both the left and right forks.

### Problem

If every philosopher picks up their left fork simultaneously, every philosopher waits for the right fork.

No philosopher can continue.

This can result in deadlock.

### Illustration

```text
             P0
          F0    F1

       P4          P1
       F4          F2

          P3 -- F3 -- P2
```

Each philosopher needs two adjacent forks.

### Basic Solution

Use one binary semaphore for each fork.

```c
semaphore fork[5] = {1, 1, 1, 1, 1};
```

A philosopher attempts to acquire both forks before eating.

However, simply picking up the left fork and then the right fork can cause deadlock.

### Solutions to Dining Philosophers Problem

**1. Allow at most N-1 philosophers to attempt eating**

Only four philosophers are allowed to compete for forks at a time.

This prevents circular waiting in the five-philosopher example.

**2. Resource Ordering**

Assign a unique number to each fork.

Every philosopher must acquire the lower-numbered fork first and then the higher-numbered fork.

This prevents circular wait.

**3. Asymmetric Solution**

Odd-numbered philosophers pick up the left fork first, while even-numbered philosophers pick up the right fork first.

**4. Waiter or Arbitrator**

A waiter grants permission to acquire forks only when both forks are available.

### Important Point

The Dining Philosophers Problem demonstrates:
- Deadlock
- Resource allocation
- Mutual exclusion
- Synchronization
- Starvation

---

## 11. Monitors

A monitor is a high-level synchronization construct that provides controlled access to shared data.

It combines:

- Shared variables
- Procedures
- Initialization code
- Condition variables

Only one process or thread can be active inside a monitor at a time.

### Structure

```text
       +-------------------------+
       |         Monitor         |
       |                         |
       |   Shared Variables      |
       |                         |
       |   Procedures            |
       |                         |
       |   Condition Variables   |
       |                         |
       +-------------------------+
```

### Example

```text
monitor BankAccount {

    int balance = 1000;

    deposit(amount) {
        balance = balance + amount;
    }

    withdraw(amount) {
        if (balance >= amount) {
            balance = balance - amount;
        }
    }
}
```

The monitor ensures that only one thread executes inside it at a time.

### Advantages

- Provides automatic mutual exclusion.
- Makes synchronization easier to manage.
- Encapsulates shared data.
- Reduces synchronization errors.

### Disadvantages

- Requires language or runtime support.
- May introduce overhead.
- Incorrect condition-variable usage can still cause bugs.

---

## 12. Condition Variables

Condition variables are synchronization mechanisms used to make threads wait until a particular condition becomes true.

They are generally used with a mutex.

Two common operations are:

- `wait()`
- `signal()`

### wait()

A thread:

1. Releases the associated mutex atomically with beginning to wait.
2. Blocks until notified.
3. Reacquires the mutex before returning.

### signal()

Wakes one waiting thread, if one exists.

The exact wake-up semantics depend on the synchronization API.

### Example

```c
lock(mutex);

while (buffer_is_empty()) {
    wait(condition, mutex);
}

remove_item();

unlock(mutex);
```

### Why use while instead of if?

A condition should be checked again after waking because:

- Another thread may have consumed the resource first.
- A wake-up does not guarantee the condition is true.
- Some systems permit spurious wake-ups.

Therefore, always recheck the condition using a `while` loop.

---

## 13. Spinlock vs Blocking Mutex

| Spinlock | Blocking Mutex |
|---|---|
| Repeatedly checks whether lock is available | Thread sleeps or blocks while waiting |
| Uses busy waiting | Avoids continuous CPU polling while blocked |
| Suitable for very short critical sections | Suitable for longer waiting periods |
| Can waste CPU time | May involve context-switching overhead |
| Useful in certain kernel and multiprocessor situations | Common in general application synchronization |

### What is Busy Waiting?

Busy waiting occurs when a process continuously checks a condition instead of sleeping or blocking.

Example:

```c
while (lock == true) {
    // Keep checking
}
```

This wastes CPU cycles if the wait is long.

---

## 14. Deadlock, Starvation and Livelock

These are related synchronization problems.

### Deadlock

A deadlock occurs when a group of processes waits indefinitely because each process is waiting for a resource held by another process in the group.

Example:

- P1 holds Resource A and waits for Resource B.
- P2 holds Resource B and waits for Resource A.

Neither can proceed.

### Starvation

Starvation occurs when a process waits indefinitely because other processes repeatedly get access to the required resource.

Example:

A low-priority process may never execute if higher-priority processes keep arriving.

### Livelock

Livelock occurs when processes remain active and repeatedly change their states in response to one another, but no useful progress is made.

### Comparison

| Deadlock | Starvation | Livelock |
|---|---|---|
| Processes are blocked | A process waits indefinitely | Processes remain active |
| No progress due to circular dependency | Unfair resource allocation | Repeated reactions prevent progress |
| Processes cannot proceed | Other processes may proceed | Processes keep executing but make no useful progress |

---

## 15. Atomic Operations

An atomic operation is an operation that executes completely or not at all from the perspective of other threads.

It cannot be observed in a partially completed state.

### Example

Consider:

```c
count++;
```

This may internally involve:

1. Read count.
2. Increment the value.
3. Write the updated value.

It is not necessarily atomic.

An atomic increment operation ensures that the update is performed indivisibly.

### Uses

- Implementing locks
- Managing shared counters
- Synchronization
- Lock-free programming
- Concurrent data structures

---

## 16. Important Differences for Interviews

### Process Synchronization vs Process Communication

| Process Synchronization | Process Communication |
|---|---|
| Coordinates execution | Exchanges information |
| Prevents inconsistent shared data access | Transfers data between processes |
| Uses locks, semaphores, monitors | Uses pipes, sockets, shared memory, message queues |
| Focuses on coordination and ordering | Focuses on information exchange |

### Binary Semaphore vs Mutex

| Binary Semaphore | Mutex |
|---|---|
| Can be used for signaling | Primarily used for mutual exclusion |
| No inherent ownership | Has ownership semantics |
| May be signaled by another thread | Normally unlocked by its owner |
| Has binary availability state | Has locked/unlocked state |

### Semaphore vs Monitor

| Semaphore | Monitor |
|---|---|
| Low-level synchronization primitive | High-level synchronization construct |
| Uses wait and signal operations | Uses procedures and condition variables |
| Programmer manages synchronization operations | Mutual exclusion is generally managed automatically |
| Can be used for resource counting | Encapsulates shared resources and operations |

---

## 17. Important Interview Questions and Answers

### Q1. What is process synchronization?

Process synchronization coordinates concurrent processes or threads to ensure consistent access to shared resources.

### Q2. What is a race condition?

A race condition occurs when the final result depends on the unpredictable execution order of concurrent processes accessing shared data.

### Q3. What is a critical section?

A critical section is a part of a program where shared resources are accessed or modified.

### Q4. What are the three requirements of a critical section solution?

1. Mutual Exclusion
2. Progress
3. Bounded Waiting

### Q5. What is Peterson's solution?

Peterson's solution is a software-based algorithm that provides mutual exclusion, progress, and bounded waiting for two processes.

### Q6. What is a semaphore?

A semaphore is a synchronization variable controlled using atomic wait and signal operations.

### Q7. What is the difference between binary and counting semaphores?

A binary semaphore represents one availability state, while a counting semaphore tracks multiple available resource instances.

### Q8. What is the difference between mutex and semaphore?

A mutex generally provides ownership-based mutual exclusion, while a semaphore supports signaling and resource counting without inherent ownership.

### Q9. What is busy waiting?

Busy waiting occurs when a process repeatedly checks a condition instead of blocking or sleeping.

### Q10. What is a spinlock?

A spinlock is a lock in which a waiting thread repeatedly checks whether the lock has become available.

### Q11. What is the Producer-Consumer Problem?

It is a synchronization problem in which producers add items to a bounded buffer and consumers remove items without causing buffer overflow, underflow, or concurrent modification.

### Q12. What is the Readers-Writers Problem?

It is a synchronization problem where multiple readers may access shared data simultaneously, but writers require exclusive access.

### Q13. What is the Dining Philosophers Problem?

It is a classical synchronization problem demonstrating deadlock and resource allocation when multiple processes compete for shared resources.

### Q14. What is a monitor?

A monitor is a high-level synchronization construct that encapsulates shared data and operations while providing mutual exclusion.

### Q15. Why is Peterson's solution not commonly used in modern systems?

It supports only two processes, uses busy waiting, and requires appropriate memory-ordering guarantees on modern processors.

### Q16. Can a semaphore cause deadlock?

Yes. Incorrect ordering or usage of semaphore operations can cause deadlock.

### Q17. Can starvation occur in the Readers-Writers Problem?

Yes. In a reader-preference solution, continuous arrival of readers can starve writers.

### Q18. What is the purpose of the mutex semaphore in the Producer-Consumer Problem?

It ensures that only one producer or consumer modifies the shared buffer at a time.

### Q19. Why do condition variables use a while loop instead of an if statement?

Because waking up does not guarantee that the required condition is still true. The thread must recheck the condition after reacquiring the mutex.

### Q20. What is an atomic operation?

An atomic operation executes indivisibly, so other threads cannot observe it in a partially completed state.

---

## 18. Quick Revision Notes

| Concept | Key Point |
|---|---|
| Synchronization | Coordinates concurrent execution |
| Race Condition | Result depends on execution order |
| Critical Section | Code accessing shared resources |
| Mutual Exclusion | Only one process enters at a time |
| Progress | Selection cannot be postponed indefinitely |
| Bounded Waiting | Prevents indefinite waiting |
| Peterson's Solution | Software solution for two processes |
| Test-and-Set | Atomic hardware instruction |
| Compare-and-Swap | Atomic conditional update |
| Mutex | Ownership-based mutual exclusion |
| Semaphore | Uses wait() and signal() |
| Binary Semaphore | Binary availability state |
| Counting Semaphore | Tracks resource instances |
| Monitor | High-level synchronization construct |
| Condition Variable | Allows waiting for a condition |
| Spinlock | Uses busy waiting |
| Deadlock | Processes wait indefinitely |
| Starvation | A process is denied resources indefinitely |
| Livelock | Processes remain active without useful progress |

---

## 19. Most Important Topics for Placements

Focus especially on:

1. Race conditions and critical-section problem.
2. Three requirements of critical-section solutions.
3. Peterson's solution with a dry run.
4. Mutex vs semaphore.
5. Binary vs counting semaphore.
6. Producer-Consumer Problem with semaphore code.
7. Readers-Writers Problem with semaphore code.
8. Dining Philosophers Problem and deadlock prevention.
9. Monitors and condition variables.
10. Deadlock vs starvation vs livelock.

### Placement Preparation Tip

For interviews, do not just memorize definitions.

Be able to explain:
- Why race conditions occur.
- How mutual exclusion prevents them.
- How semaphore values change during Producer-Consumer execution.
- Why reader-preference can starve writers.
- How Dining Philosophers can cause deadlock.
- Why a condition variable should be checked inside a while loop.

---

## 20. Unit 5 Summary

Process synchronization is essential in operating systems because multiple processes and threads execute concurrently and may access shared resources.

The most important synchronization mechanisms are:

- Peterson's Solution
- Mutex Locks
- Semaphores
- Hardware Atomic Instructions
- Monitors
- Condition Variables

These mechanisms help maintain data consistency, prevent race conditions, and coordinate concurrent execution.

**Remember:** Synchronization is not just about preventing simultaneous execution. It is about ensuring correct and predictable behavior when processes interact with shared resources.
