
# 1. Introduction to Operating Systems

## 1.1 What is an Operating System?

An **Operating System (OS)** is system software that acts as an interface between the user and computer hardware. It manages hardware and software resources and provides an environment for executing programs.

**Examples:**
- Windows
- Linux
- macOS
- Android
- iOS

**Main Objective:** To make the computer system convenient, efficient, and secure to use.

---

## 1.2 Why Do We Need an Operating System?

Without an operating system, users and applications would need to interact directly with computer hardware.

An OS is required for the following purposes:

1. **Resource Management:** Manages CPU, memory, storage, and I/O devices.
2. **Process Management:** Handles process creation, scheduling, and termination.
3. **Memory Management:** Allocates and deallocates memory to processes.
4. **File Management:** Organizes, stores, and retrieves files.
5. **Security:** Protects the system against unauthorized access.
6. **Device Management:** Controls and coordinates hardware devices.
7. **User Interface:** Provides CLI or GUI for user interaction.

---

## 1.3 Functions of an Operating System

| Function | Description |
|---|---|
| Process Management | Creates, schedules, and terminates processes. |
| Memory Management | Allocates and manages main memory (RAM). |
| File Management | Creates, deletes, and organizes files. |
| Device Management | Manages input/output devices. |
| Security | Protects system resources and data. |
| CPU Scheduling | Decides which process gets CPU time. |
| Error Detection | Detects hardware and software errors. |
| Resource Allocation | Distributes resources among processes. |

---

## 1.4 Types of Operating Systems

### 1.4.1 Batch Operating System

A Batch Operating System executes a collection of similar jobs in batches without requiring direct user interaction during execution.

**Working:**
1. Users submit jobs to the system.
2. Similar jobs are grouped into batches.
3. The OS executes jobs one after another.
4. Results are generated after execution.

**Advantages:**
- Suitable for repetitive tasks.
- Reduces manual intervention.
- Efficient for processing large batches.

**Disadvantages:**
- No direct user interaction.
- Difficult to debug.
- Long turnaround time.

**Example:** Early IBM mainframe systems.

### 1.4.2 Multiprogramming Operating System

Multiprogramming allows multiple programs to reside in main memory simultaneously.

When one process waits for an I/O operation, the CPU executes another process instead of remaining idle.

**Main Objective:** Maximize CPU utilization.

**Example:**
- Process P1 waits for disk I/O.
- CPU switches to Process P2.
- Process P2 executes while P1 waits.

### 1.4.3 Multitasking Operating System

Multitasking allows multiple tasks to make progress apparently simultaneously by rapidly switching the CPU between them.

**Example:**
- Listening to music.
- Browsing the internet.
- Downloading files.

All these tasks can run concurrently from the user's perspective.

### 1.4.4 Multiprocessing Operating System

Multiprocessing uses two or more CPUs or processing cores to execute processes concurrently.

**Advantages:**
- Higher throughput.
- Better performance.
- Parallel execution.
- Improved reliability.

### 1.4.5 Time-Sharing Operating System

A Time-Sharing OS divides CPU time into small intervals called **Time Quantum** or **Time Slice**.

Each process gets the CPU for a limited amount of time.

After the time quantum expires, the CPU switches to another process.

**Example:** Multi-user UNIX systems.

**Advantages:**
- Quick response time.
- Supports multiple users.
- Fair CPU allocation.

### 1.4.6 Distributed Operating System

A Distributed OS manages multiple independent computers and makes them work together as a unified system.

**Advantages:**
- Resource sharing.
- High reliability.
- Scalability.
- Better performance.

### 1.4.7 Real-Time Operating System (RTOS)

An RTOS processes data and responds to events within a specified time constraint.

**Types:**

**1. Hard Real-Time OS**
- Missing a deadline is considered a system failure.
- Deadlines must be strictly followed.
- Example: Aircraft control systems.

**2. Soft Real-Time OS**
- Missing a deadline occasionally is tolerable.
- Performance degrades but the system continues working.
- Example: Multimedia streaming systems.

---

## 1.5 Kernel

The **Kernel** is the core component of an operating system that manages hardware resources and provides essential services to applications.

**Responsibilities of Kernel:**
- Process scheduling.
- Memory management.
- Device management.
- Interrupt handling.
- System call handling.

### Types of Kernel

#### 1. Monolithic Kernel

In a Monolithic Kernel, most operating system services run in kernel space.

**Examples:** Linux, traditional UNIX.

**Advantages:**
- High performance.
- Direct communication between components.
- Efficient execution.

**Disadvantages:**
- Large kernel size.
- Errors in kernel components can affect the entire system.
- Difficult to maintain.

#### 2. Microkernel

A Microkernel keeps only essential services in kernel space. Other operating system services run in user space.

**Example:** MINIX 3.

**Advantages:**
- Better security.
- Better fault isolation.
- Easier maintenance.

**Disadvantages:**
- Communication overhead.
- Potential performance reduction.

---

## 1.6 User Mode vs Kernel Mode

Modern operating systems use two execution modes to provide protection and security.

| User Mode | Kernel Mode |
|---|---|
| Restricted privileges | Full privileged access |
| Runs user applications | Runs kernel code |
| Cannot directly access protected hardware | Can access hardware |
| Limited instruction access | Can execute privileged instructions |
| Application failure usually affects the application | Kernel failure can crash the OS |

**Important:** Applications request privileged operations through system calls.

---

## 1.7 System Calls

A **System Call** is a mechanism through which a user-level program requests a service from the operating system kernel.

### Examples of System Calls

| System Call | Purpose |
|---|---|
| `fork()` | Creates a new process. |
| `exec()` | Replaces the current process image. |
| `wait()` | Waits for a child process. |
| `read()` | Reads data from a file or device. |
| `write()` | Writes data to a file or device. |
| `open()` | Opens a file. |
| `close()` | Closes a file descriptor. |
| `getpid()` | Returns the process ID. |

### Types of System Calls

1. Process Control
2. File Management
3. Device Management
4. Information Maintenance
5. Communication
6. Protection

### How Does a System Call Work?

1. A user program requests an OS service.
2. The system call interface receives the request.
3. A controlled transition from user mode to kernel mode occurs.
4. The kernel performs the requested operation.
5. The result is returned to the user program.
6. Execution resumes in user mode.

---

## 1.8 Booting Process

Booting is the process of starting a computer and loading the operating system into memory.

### Steps Involved in Booting

1. **Power ON:** Computer hardware receives power.
2. **BIOS/UEFI:** Initializes hardware components.
3. **Bootloader:** Identifies and loads the operating system kernel.
4. **Kernel Loading:** Kernel is loaded into main memory.
5. **Hardware Initialization:** Kernel initializes memory and device drivers.
6. **System Services:** Essential services and processes are started.
7. **Login Screen:** User interface becomes available.

### Types of Booting

**1. Cold Booting**
- Starting the computer from a completely powered-off state.

**2. Warm Booting**
- Restarting the computer without completely removing power.

---

## 1.9 Important Differences for Interviews

### Multiprogramming vs Multitasking

| Multiprogramming | Multitasking |
|---|---|
| Multiple programs reside in memory. | Multiple tasks make progress. |
| CPU switches when a process waits, traditionally. | CPU switches based on scheduling and time quantum. |
| Focuses on CPU utilization. | Focuses on responsiveness. |

### Multiprocessing vs Multitasking

| Multiprocessing | Multitasking |
|---|---|
| Uses multiple processing units. | Multiple tasks share CPU execution time. |
| Can achieve parallel execution. | Can work on a single CPU. |
| Improves parallel processing capability. | Improves responsiveness. |

### Hard Real-Time vs Soft Real-Time OS

| Hard Real-Time OS | Soft Real-Time OS |
|---|---|
| Deadlines must be met. | Occasional deadline misses are tolerable. |
| Missing a deadline is unacceptable. | Missing a deadline degrades performance. |
| Used in aircraft control systems. | Used in multimedia systems. |

---

## 1.10 Important OS Interview Questions

1. What is an Operating System?
2. Why do we need an OS?
3. What are the major functions of an OS?
4. What is the difference between multitasking and multiprogramming?
5. What is multiprocessing?
6. What is a Real-Time Operating System?
7. What is the difference between hard and soft real-time systems?
8. What is a kernel?
9. What is the difference between monolithic and microkernel?
10. What is a system call?
11. What happens when a system call is executed?
12. What is the difference between user mode and kernel mode?
13. What happens during the booting process?
14. What is the difference between cold booting and warm booting?
15. What is the difference between a program and a process?

---

## 1.11 Quick Revision Notes

- **Operating System:** Interface between user and hardware.
- **Kernel:** Core component of the OS.
- **System Call:** Interface for requesting kernel services.
- **Multiprogramming:** Multiple programs reside in memory.
- **Multitasking:** Multiple tasks make progress through CPU scheduling.
- **Multiprocessing:** Uses multiple processing units.
- **Time Sharing:** CPU time is divided into time quanta.
- **RTOS:** Operating system with timing constraints.
- **Booting:** Process of loading the OS into memory.
- **User Mode:** Restricted execution mode.
- **Kernel Mode:** Privileged execution mode.

---

## 1.12 Important Placement Points

1. OS acts as an interface between hardware and applications.
2. Kernel is the most important component of an OS.
3. A system call causes a controlled transition into kernel mode.
4. Multiprogramming improves CPU utilization.
5. Time-sharing improves responsiveness.
6. Multiprocessing enables parallel execution.
7. A hard real-time system cannot tolerate missed deadlines.
8. Linux uses a monolithic kernel with modular support.

---
