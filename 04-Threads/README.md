
# 4. Threads and Multithreading

## 4.1 What is a Thread?

A **Thread** is the smallest unit of CPU execution within a process.

A thread is also called a **Lightweight Process (LWP)** because it requires fewer resources than a traditional process.

A process can contain multiple threads, and these threads can execute independently while sharing the resources of their process.

### Characteristics of a Thread

1. A thread exists within a process.
2. Every thread has its own Thread ID.
3. Each thread has its own program counter.
4. Each thread has its own register set.
5. Each thread has its own stack.
6. Threads of the same process share code, data, and open files.
7. Threads improve application responsiveness and concurrency.

### Example

Consider a web browser.

It may use different threads for:

- Loading web pages.
- Playing videos.
- Handling user interactions.
- Downloading files.
- Rendering web content.

These activities can make progress concurrently.

---

## 4.2 What is Multithreading?

**Multithreading** is the ability of a process to execute multiple threads concurrently.

Each thread performs a separate task within the same process.

### Example

Consider a music player application.

It may contain:

- Thread 1: Plays music.
- Thread 2: Downloads songs.
- Thread 3: Updates the user interface.
- Thread 4: Handles user input.

All these threads belong to the same process.

### Advantages of Multithreading

1. **Responsiveness:** Applications remain responsive even when one thread is blocked.
2. **Resource Sharing:** Threads share memory and resources.
3. **Economy:** Creating and switching threads is generally cheaper than creating and switching processes.
4. **Scalability:** Multiple threads can run in parallel on multicore processors.
5. **Better CPU Utilization:** CPU resources can be used more effectively.

---

## 4.3 Process vs Thread

This is one of the most frequently asked OS interview questions.

| Process | Thread |
|---|---|
| A program in execution. | Smallest unit of CPU execution. |
| Heavyweight entity. | Lightweight entity. |
| Has its own address space. | Shares the address space of its process. |
| Creation is relatively expensive. | Creation is generally cheaper. |
| Context switching is generally more expensive. | Context switching can be cheaper. |
| Processes are isolated from each other. | Threads share process resources. |
| Communication requires IPC mechanisms. | Threads can communicate through shared memory. |
| Failure is generally isolated to a process. | A faulty thread can affect the entire process. |

### Important Interview Point

Each thread has its own:

- Thread ID
- Program Counter
- Register Set
- Stack

Threads of the same process share:

- Code Section
- Data Section
- Heap
- Open Files
- Address Space

---

## 4.4 Single-Threaded vs Multithreaded Process

### Single-Threaded Process

A single-threaded process contains only one thread of execution.

It performs one sequence of instructions at a time.

Example:

A simple calculator application performing one task at a time.

### Multithreaded Process

A multithreaded process contains multiple threads that can make progress independently.

Example:

A browser downloading files while rendering a webpage.

### Difference

| Single-Threaded | Multithreaded |
|---|---|
| Contains one thread. | Contains multiple threads. |
| Limited concurrency. | Supports concurrency. |
| Blocking operation may stop the entire process's progress. | Other threads may continue executing. |
| Simpler design. | More complex synchronization. |

---

## 4.5 Types of Threads

There are two major types of threads.

### 1. User-Level Threads (ULT)

User-Level Threads are managed by a user-level thread library without requiring the kernel to manage each thread individually.

The kernel generally sees the process rather than its individual user threads.

### Working

1. Application creates user-level threads.
2. A thread library manages these threads.
3. The library performs scheduling among threads.
4. Threads execute according to the underlying kernel scheduling model.

### Advantages

- Fast thread creation.
- Fast thread switching.
- No kernel mode transition is needed for ordinary user-level thread management.
- Application can use customized scheduling.

### Disadvantages

- In a many-to-one model, a blocking system call can block the entire process.
- In a many-to-one model, multiple user threads cannot execute in parallel on multiple cores.
- Kernel does not independently schedule each user thread.

### 2. Kernel-Level Threads (KLT)

Kernel-Level Threads are managed directly by the operating system kernel.

The kernel knows about each thread and schedules them independently.

### Working

1. Application creates kernel threads.
2. Kernel maintains thread information.
3. Kernel schedules individual threads.
4. Threads execute according to CPU scheduling policies.

### Advantages

- Multiple threads can execute in parallel on multiple cores.
- If one thread blocks, another thread can continue.
- Kernel independently schedules threads.

### Disadvantages

- Thread creation is relatively expensive.
- Thread management involves kernel operations.
- Context switching can involve kernel overhead.

### User-Level vs Kernel-Level Threads

| User-Level Threads | Kernel-Level Threads |
|---|---|
| Managed by thread library. | Managed by OS kernel. |
| Kernel may not know individual threads. | Kernel knows individual threads. |
| Fast management. | More management overhead. |
| A blocking system call can block the whole process in many-to-one implementations. | Blocking one thread does not necessarily block others. |
| Parallel execution depends on mapping model. | Can execute in parallel on multiple cores. |
| Scheduling can be handled by the thread library. | Scheduling is handled by the kernel. |

---

## 4.6 Multithreading Models

Multithreading models describe how user-level threads are mapped to kernel-level threads.

There are three traditional models.

### 1. Many-to-One Model

Many user-level threads are mapped to a single kernel thread.

### Diagram

```text
User-Level Threads

   T1     T2     T3
    \      |     /
     \     |    /
      \    |   /
       K1

Kernel-Level Thread
```

### Working

- Multiple user threads share one kernel thread.
- Thread management happens in user space.
- Only one thread can access the kernel through that kernel thread at a time.

### Advantages

- Fast thread management.
- Low overhead.
- Simple implementation.

### Disadvantages

- A blocking system call can block the entire process.
- Cannot achieve parallel execution across multiple CPU cores.

---

### 2. One-to-One Model

Each user-level thread is mapped to a separate kernel-level thread.

### Diagram

```text
User-Level Threads       Kernel Threads

      T1  -------------> K1

      T2  -------------> K2

      T3  -------------> K3
```

### Working

- Every user thread has a corresponding kernel thread.
- Kernel schedules each thread independently.
- Multiple threads can execute in parallel on multicore systems.

### Advantages

- Better concurrency.
- Supports parallelism.
- Blocking one thread does not block other threads.

### Disadvantages

- Higher overhead.
- Creating too many threads can consume significant kernel resources.

### Examples

- Windows.
- Linux implementations using native kernel threads.

---

### 3. Many-to-Many Model

Many user-level threads are mapped to a smaller or equal number of kernel-level threads.

### Diagram

```text
User-Level Threads       Kernel Threads

   T1 -----------\
                  \
   T2 -------------> K1

   T3 -------------> K2

   T4 -------------> K3
```

### Working

- Multiple user threads are multiplexed over multiple kernel threads.
- The number of kernel threads can be less than or equal to the number of user threads.
- The thread library manages user threads.
- The kernel schedules kernel threads.

### Advantages

- Supports concurrency and parallelism.
- More flexible than many-to-one.
- Blocking one thread does not necessarily block all threads.

### Disadvantages

- More complex implementation.
- Requires coordination between user-level and kernel-level thread management.

---

## 4.7 Comparison of Multithreading Models

| Feature | Many-to-One | One-to-One | Many-to-Many |
|---|---|---|---|
| Mapping | Many ULT to one KLT | One ULT to one KLT | Many ULT to multiple KLT |
| Parallelism | No | Yes | Yes |
| Blocking issue | Can block all threads | Other threads can continue | Other threads can continue if another KLT is available |
| Overhead | Low | High | Moderate |
| Complexity | Low | Moderate | High |

### Important Interview Point

- Many-to-One: Cannot achieve parallelism.
- One-to-One: Supports parallelism.
- Many-to-Many: Combines user-level flexibility with multiple kernel threads.

---

## 4.8 Thread Libraries

A Thread Library provides functions for creating and managing threads.

Common thread libraries include:

### 1. POSIX Pthreads

POSIX Pthreads is a standard thread API used in UNIX-like operating systems.

Common functions:

| Function | Purpose |
|---|---|
| `pthread_create()` | Creates a new thread. |
| `pthread_exit()` | Terminates the calling thread. |
| `pthread_join()` | Waits for a thread to finish. |
| `pthread_self()` | Returns the calling thread ID. |
| `pthread_mutex_lock()` | Locks a mutex. |
| `pthread_mutex_unlock()` | Unlocks a mutex. |

### Example: Creating a Thread in C

```c
#include <stdio.h>
#include <pthread.h>

void* display(void* arg) {
    printf("Hello from thread\n");
    return NULL;
}

int main() {
    pthread_t t1;

    pthread_create(&t1, NULL, display, NULL);

    pthread_join(t1, NULL);

    printf("Main thread finished\n");

    return 0;
}
```

### Explanation

1. `pthread_t` declares a thread identifier.
2. `pthread_create()` creates a new thread.
3. `display()` is executed by the new thread.
4. `pthread_join()` waits for the thread to complete.
5. The main thread continues after the child thread finishes.

### 2. Windows Thread Library

Windows provides its own API for thread creation and management.

Common functions:

- `CreateThread()`
- `ExitThread()`
- `WaitForSingleObject()`
- `GetCurrentThreadId()`

### 3. Java Thread Library

Java supports multithreading through the `Thread` class and the `Runnable` interface.

Example:

```java
class MyThread extends Thread {

    public void run() {
        System.out.println("Thread is running");
    }

    public static void main(String[] args) {

        MyThread t1 = new MyThread();

        t1.start();
    }
}
```

**Important:**

- `start()` creates a new execution thread and invokes `run()`.
- Calling `run()` directly does not create a new thread.

---

## 4.9 Thread Scheduling

Thread Scheduling determines which thread gets CPU time.

Threads may be scheduled using:

1. Process Contention Scope (PCS).
2. System Contention Scope (SCS).

### 1. Process Contention Scope (PCS)

In PCS, user-level threads compete with other threads within the same process.

The thread library performs the scheduling.

### 2. System Contention Scope (SCS)

In SCS, kernel-level threads compete with all other kernel-level threads in the system for CPU allocation.

The operating system kernel performs the scheduling.

### PCS vs SCS

| PCS | SCS |
|---|---|
| Threads compete within one process. | Threads compete system-wide. |
| Managed by thread library. | Managed by OS kernel. |
| User-level thread scheduling. | Kernel-level thread scheduling. |

---

## 4.10 Thread Cancellation

Thread Cancellation means terminating a thread before it completes its execution.

There are two major types.

### 1. Asynchronous Cancellation

A thread is terminated immediately by another thread.

**Disadvantage:** It may leave shared resources in an inconsistent state.

### 2. Deferred Cancellation

The target thread checks for cancellation requests at designated cancellation points and terminates safely.

**Advantage:** Allows cleanup operations before termination.

### Difference

| Asynchronous Cancellation | Deferred Cancellation |
|---|---|
| Immediate termination. | Termination at a cancellation point. |
| Less safe. | Safer. |
| Can interrupt critical operations. | Allows cleanup before termination. |

---

## 4.11 Thread Pools

A **Thread Pool** is a collection of pre-created threads that are available to execute tasks.

Instead of creating a new thread for every task, an application submits tasks to the pool.

### Working

1. A thread pool is created.
2. Multiple worker threads wait for tasks.
3. Tasks are submitted to the pool.
4. Available threads execute tasks.
5. Completed threads return to the pool.

### Advantages

- Reduces thread creation overhead.
- Improves performance for repeated tasks.
- Controls the number of active threads.
- Supports efficient task management.

### Example

A web server can use a thread pool to handle multiple client requests.

---

## 4.12 Threading Issues

Multithreaded applications introduce several challenges.

### 1. Race Condition

A race condition occurs when multiple threads access shared data concurrently and the final result depends on the order of execution.

Example:

Two threads increment the same shared variable simultaneously.

Without synchronization, an update may be lost.

### 2. Deadlock

Deadlock occurs when two or more threads wait indefinitely for resources held by one another.

Example:

- Thread T1 holds Lock A and waits for Lock B.
- Thread T2 holds Lock B and waits for Lock A.

Neither can proceed.

### 3. Synchronization

Synchronization coordinates thread execution and protects shared resources from inconsistent updates.

Common synchronization mechanisms:

- Mutex.
- Semaphore.
- Monitor.
- Condition Variable.

### 4. Thread Safety

Thread Safety means that a function or program works correctly when multiple threads execute it concurrently.

### 5. Thread Starvation

Thread Starvation occurs when a thread waits indefinitely because other threads continuously receive the resources or CPU time it needs.

---

## 4.13 Concurrency vs Parallelism

These concepts are frequently asked in technical interviews.

### Concurrency

Concurrency means multiple tasks make progress during overlapping periods of time.

They may execute by switching between tasks on a single CPU core.

### Parallelism

Parallelism means multiple tasks execute simultaneously at the same instant using multiple processing units or cores.

### Difference

| Concurrency | Parallelism |
|---|---|
| Multiple tasks make progress. | Multiple tasks execute simultaneously. |
| Can happen on one CPU core. | Requires multiple execution units. |
| Focuses on task coordination. | Focuses on simultaneous execution. |
| Does not always require simultaneous execution. | Requires simultaneous execution. |

### Example

**Concurrency:**

One CPU core switches between Thread A and Thread B.

**Parallelism:**

Thread A executes on Core 1 while Thread B executes on Core 2.

---

## 4.14 Important Comparison: Process vs Multithreading

| Process-Based Execution | Thread-Based Execution |
|---|---|
| Multiple independent processes. | Multiple threads within a process. |
| Separate address spaces. | Shared process address space. |
| IPC required for communication. | Shared memory can be used. |
| Higher resource overhead generally. | Lower resource overhead generally. |
| Better isolation. | Less isolation. |

---

## 4.15 Important Placement Interview Questions

### Basic Questions

1. What is a thread?
2. Why is a thread called a lightweight process?
3. What is multithreading?
4. What are the advantages of multithreading?
5. What is the difference between a process and a thread?
6. What resources are shared between threads?
7. What resources are private to each thread?
8. What is a single-threaded process?
9. What is a multithreaded process?
10. What is a thread ID?

### Intermediate Questions

11. What are user-level threads?
12. What are kernel-level threads?
13. What is the difference between ULT and KLT?
14. Explain the many-to-one model.
15. Explain the one-to-one model.
16. Explain the many-to-many model.
17. What is a thread library?
18. What is POSIX Pthreads?
19. What is thread cancellation?
20. What is a thread pool?
21. What is Process Contention Scope?
22. What is System Contention Scope?

### Advanced / Scenario-Based Questions

23. Can multiple threads of one process run in parallel?
24. What happens if one thread makes a blocking system call?
25. Can threads share the same stack?
26. What happens when a thread terminates?
27. What is a race condition?
28. What is thread safety?
29. What is the difference between concurrency and parallelism?
30. Can a single-core CPU support multithreading?
31. Why is thread creation generally cheaper than process creation?
32. What is the difference between asynchronous and deferred cancellation?
33. What is thread starvation?
34. How can deadlock occur between threads?
35. Why do we need synchronization in multithreaded applications?

---

## 4.16 Quick Revision Notes

- **Thread:** Smallest unit of CPU execution.
- **LWP:** Lightweight Process.
- **Multithreading:** Multiple threads within one process.
- **ULT:** User-Level Thread managed by a thread library.
- **KLT:** Kernel-Level Thread managed by the OS.
- **Many-to-One:** Many user threads mapped to one kernel thread.
- **One-to-One:** Each user thread mapped to one kernel thread.
- **Many-to-Many:** Many user threads mapped to multiple kernel threads.
- **Thread Pool:** Collection of reusable worker threads.
- **Race Condition:** Result depends on concurrent execution order.
- **Thread Safety:** Correct behavior under concurrent execution.
- **Concurrency:** Multiple tasks make progress.
- **Parallelism:** Multiple tasks execute simultaneously.
- **PCS:** Threads compete within the same process.
- **SCS:** Threads compete system-wide.

---

## 4.17 Important Placement Points

1. A thread is the smallest unit of CPU execution.
2. Threads belonging to the same process share code, data, and heap.
3. Each thread has its own stack, registers, and program counter.
4. Thread creation is generally cheaper than process creation.
5. User-level threads are managed by a thread library.
6. Kernel-level threads are managed by the OS.
7. Many-to-One does not support parallel execution of its user threads.
8. One-to-One supports parallel execution on multicore systems.
9. Many-to-Many maps multiple user threads to multiple kernel threads.
10. A blocking system call can block the entire process in a Many-to-One model.
11. Thread pools reduce repeated thread creation overhead.
12. Concurrency does not necessarily mean parallelism.
13. Race conditions can be prevented using synchronization.
14. Thread safety is important when multiple threads access shared data.

---
