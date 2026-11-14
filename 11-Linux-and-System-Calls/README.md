
# Unit 11: Linux, Unix Architecture and System Calls

## 1. Introduction to Linux

Linux is an open-source, Unix-like operating system based on the Linux kernel.

It is widely used in:
- Servers and cloud computing.
- Embedded systems.
- Supercomputers.
- Android devices.
- Software development.
- DevOps and cybersecurity.

### Important Features of Linux

1. **Open Source:** Source code is publicly available.
2. **Multiuser:** Multiple users can use system resources.
3. **Multitasking:** Multiple processes can execute concurrently.
4. **Portable:** Supports different hardware architectures.
5. **Secure:** Provides permissions and access-control mechanisms.
6. **Hierarchical File System:** Organizes files and directories in a tree.
7. **Networking Support:** Provides extensive networking functionality.
8. **Shell Support:** Offers command-line interfaces for system interaction.

### Linux vs Unix

| Linux | Unix |
|---|---|
| Open-source kernel | Historically developed as a proprietary or licensed OS family |
| Used in desktops, servers, embedded systems | Common in enterprise and specialized systems |
| Many distributions available | Many Unix variants exist |
| Examples: Ubuntu, Fedora | Examples: AIX, HP-UX |

**Important:** Linux is Unix-like, but Linux is not itself a certified Unix operating system.

---

## 2. Linux Architecture

Linux architecture can be understood through four major layers.

```text
+----------------------------------+
|          User Applications       |
| Browser, Editor, Compiler        |
+----------------------------------+
|          Shell / Utilities       |
| Bash, ls, grep, cat              |
+----------------------------------+
|          System Call Interface   |
| open(), read(), write(), fork()  |
+----------------------------------+
|          Linux Kernel            |
| Process, Memory, File, I/O Mgmt  |
+----------------------------------+
|          Hardware                |
| CPU, RAM, Disk, Network          |
+----------------------------------+
```

### 2.1 Hardware

Physical components of the computer:
- CPU.
- RAM.
- Storage.
- Network devices.
- Input/output devices.

### 2.2 Kernel

The kernel is the core component of the operating system.

It directly manages system resources and provides services to applications through controlled interfaces.

Major responsibilities:

- Process management.
- Memory management.
- File-system management.
- Device management.
- I/O management.
- Networking.
- Security and access control.

### 2.3 Shell

A shell is a command interpreter that accepts user commands and executes programs or built-in operations.

Examples:
- Bash.
- Zsh.
- Fish.

Example:

```bash
ls -l
```

The shell interprets this command and starts the appropriate utility.

### 2.4 System Utilities

Utilities are programs that perform common system operations.

Examples:
- `ls`
- `cp`
- `mv`
- `grep`
- `ps`
- `chmod`

---

## 3. Kernel Architecture

The Linux kernel is commonly described as a monolithic kernel with a modular design.

A monolithic kernel provides many operating-system services within kernel space.

Linux supports loadable kernel modules, which allow certain functionality, such as drivers, to be added or removed dynamically.

### Monolithic Kernel

```text
+--------------------------------+
|          User Space            |
|      Applications / Shell      |
+--------------------------------+
|        System Call Interface   |
+--------------------------------+
|          Kernel Space          |
|                                |
| Process Management             |
| Memory Management              |
| File Systems                   |
| Device Drivers                 |
| Networking                     |
| I/O Management                 |
|                                |
+--------------------------------+
|           Hardware             |
+--------------------------------+
```

### Advantages

- Efficient communication between kernel components.
- High performance for many operations.
- Flexible functionality through kernel modules.

### Disadvantages

- Bugs in kernel code can affect the entire system.
- Large kernel codebase.
- Kernel-level debugging can be complex.

### Monolithic vs Microkernel

| Monolithic Kernel | Microkernel |
|---|---|
| Many services run in kernel space | Only essential services run in kernel space |
| Direct communication between kernel components | More communication through message passing |
| Often lower communication overhead | Can have additional communication overhead |
| Large kernel functionality | Smaller kernel core |
| Example: Linux | Examples: MINIX 3, QNX |

---

## 4. User Mode and Kernel Mode

Modern operating systems use processor privilege levels to protect critical system operations.

### User Mode

Applications normally execute in user mode.

Characteristics:
- Restricted hardware access.
- Cannot execute privileged instructions.
- Must use system calls for protected operations.
- Failure of an ordinary user process usually does not crash the whole OS.

### Kernel Mode

The OS kernel executes in kernel mode.

Characteristics:
- Privileged instructions are available.
- Can manage hardware and memory.
- Handles system calls and interrupts.
- Can access protected kernel data.

### Comparison

| User Mode | Kernel Mode |
|---|---|
| Restricted privileges | High privileges |
| Runs applications | Runs kernel code |
| Cannot directly perform privileged operations | Can perform privileged operations |
| Uses system calls | Handles system calls |
| More isolated | Errors can affect the whole system |

### Mode Switching

When an application requests a protected service, the CPU switches from user mode to kernel mode.

Example:

```text
       User Application
              |
              v
        System Call
              |
              v
       Switch to Kernel Mode
              |
              v
        Kernel Executes
              |
              v
        Return to User Mode
```

---

## 5. System Calls

A system call is a controlled interface through which a user program requests a service from the operating system kernel.

Applications cannot directly perform every privileged operation.

They use system calls to request services such as:
- Creating processes.
- Reading files.
- Writing files.
- Allocating memory.
- Communicating with devices.
- Managing permissions.

### System Call Working

1. A user program calls a system-call wrapper or library function.
2. The system-call mechanism transfers control to the kernel.
3. The kernel identifies the requested operation.
4. The kernel performs the operation after checking permissions and arguments.
5. A result or error is returned to the user program.
6. Execution continues in user mode.

```text
+----------------------+
|   User Application   |
+----------------------+
           |
           v
+----------------------+
| System Call Wrapper  |
+----------------------+
           |
           v
+----------------------+
| System Call Interface|
+----------------------+
           |
           v
+----------------------+
|      Kernel          |
| Performs Operation   |
+----------------------+
           |
           v
     Return Result
```

**Important:** A system call generally causes a transition into kernel mode, but not every library function is itself a system call.

---

## 6. Categories of System Calls

System calls are commonly grouped into five categories.

### 6.1 Process Control

Used to create, execute, terminate, and manage processes.

Examples:

| System Call | Purpose |
|---|---|
| `fork()` | Creates a child process |
| `execve()` | Replaces the current process image |
| `wait()` | Waits for a child process |
| `exit()` | Terminates a process |
| `getpid()` | Returns process ID |
| `getppid()` | Returns parent process ID |

### 6.2 File Management

Used to create, open, read, write, and close files.

| System Call | Purpose |
|---|---|
| `open()` | Opens a file |
| `read()` | Reads data |
| `write()` | Writes data |
| `close()` | Closes a file descriptor |
| `lseek()` | Changes file offset |
| `unlink()` | Removes a directory entry |

### 6.3 Device Management

Used to interact with devices.

Examples:
- `ioctl()` – Performs device-specific control operations.
- `read()` – Reads from a device.
- `write()` – Writes to a device.

### 6.4 Information Maintenance

Used to retrieve or update system information.

Examples:
- `getpid()` – Gets process ID.
- `uname()` – Gets system information.
- `getuid()` – Gets user ID.
- `time()` – Retrieves time-related information.

### 6.5 Communication

Used for interprocess communication.

Examples:
- `pipe()`
- `socket()`
- `send()`
- `recv()`
- `shmget()` on systems supporting System V shared memory.

---

## 7. Important Linux System Calls

### 7.1 fork()

`fork()` creates a new process by duplicating the calling process.

The new process is called the child process, while the original is the parent process.

Example:

```c
#include <stdio.h>
#include <unistd.h>

int main() {
    fork();

    printf("Hello Linux\n");

    return 0;
}
```

In a simple example with one successful `fork()` and no other process creation, the print statement normally executes twice.

### Return Values of fork()

| Return Value | Meaning |
|---|---|
| Negative | Process creation failed |
| 0 | Executing in child process |
| Positive | Executing in parent; value is child PID |

### Process Creation Diagram

```text
       Parent Process
             |
           fork()
          /      \
         /        \
        v          v
    Parent       Child
        |          |
        v          v
     Execute     Execute
```

**Important:** The parent and child have separate process address spaces. Initially, memory is commonly implemented using copy-on-write.

### 7.2 exec()

The `exec` family of functions replaces the current process image with a new program.

Example:

```c
#include <unistd.h>

int main() {
    execlp("ls", "ls", "-l", NULL);

    return 0;
}
```

If `exec` succeeds, the original program image is replaced and execution does not continue at the next statement in the old program.

### 7.3 wait()

`wait()` allows a parent process to wait for a child process to terminate and collect its termination status.

Example:

```c
#include <stdio.h>
#include <unistd.h>
#include <sys/wait.h>

int main() {
    pid_t pid = fork();

    if (pid == 0) {
        printf("Child Process\n");
    }
    else if (pid > 0) {
        wait(NULL);
        printf("Parent Process\n");
    }

    return 0;
}
```

The parent waits for the child before printing its message.

### 7.4 exit()

`exit()` terminates the calling process after performing normal process-exit handling.

Example:

```c
#include <stdlib.h>

int main() {
    exit(0);
}
```

Conventionally:
- `0` means successful termination.
- Non-zero indicates an error or abnormal application-specific status.

---

## 8. fork() vs exec() vs wait()

| fork() | exec() | wait() |
|---|---|---|
| Creates a child process | Replaces current process image | Waits for a child |
| Parent continues | Same process ID is retained | Parent may block |
| Returns in parent and child | Does not return on success | Collects child status |
| Used for process creation | Used for program execution | Used for process synchronization |

### Common Interview Question

**How do fork() and exec() work together?**

A common pattern is:

1. Parent calls `fork()`.
2. Child process is created.
3. Child calls an `exec()` function.
4. Child executes a new program.
5. Parent calls `wait()` if it needs to wait for the child.

---

## 9. Linux Process Commands

Linux provides commands to inspect and manage running processes.

### 9.1 ps

Displays process information.

```bash
ps
```

Useful variations:

```bash
ps -ef
ps aux
```

### 9.2 top

Displays processes and system resource usage in real time.

```bash
top
```

### 9.3 htop

An interactive process-monitoring utility, if installed.

```bash
htop
```

### 9.4 kill

Sends a signal to a process.

```bash
kill 1234
```

By default, `kill` sends SIGTERM.

Forceful termination:

```bash
kill -9 1234
```

`-9` sends SIGKILL, which cannot be caught or ignored by the target process.

### 9.5 nice

Starts a process with a specified scheduling niceness value.

```bash
nice -n 10 command
```

### 9.6 renice

Changes the niceness value of an existing process.

```bash
renice 5 -p 1234
```

### 9.7 jobs

Displays jobs associated with the current shell.

```bash
jobs
```

### 9.8 bg and fg

- `bg`: Resumes a suspended job in the background.
- `fg`: Brings a job to the foreground.

---

## 10. Linux File System Hierarchy

Linux uses a hierarchical directory structure starting from the root directory `/`.

### Important Directories

| Directory | Purpose |
|---|---|
| `/` | Root directory |
| `/home` | User home directories |
| `/root` | Root user's home directory |
| `/bin` | Essential user command binaries on systems using this layout |
| `/sbin` | System administration utilities on systems using this layout |
| `/etc` | System configuration files |
| `/var` | Variable data such as logs |
| `/tmp` | Temporary files |
| `/usr` | User-space programs and shared resources |
| `/dev` | Device files |
| `/proc` | Virtual filesystem exposing process and kernel information |
| `/sys` | Kernel and device information |
| `/boot` | Boot-related files |
| `/mnt` | Common temporary mount point |
| `/media` | Common mount location for removable media |

**Note:** On many modern Linux distributions, `/bin` and `/sbin` may be symbolic links into `/usr`.

### Directory Structure

```text
             /
             |
   +---------+----------+
   |         |          |
  home      etc        usr
   |         |          |
 User1    Config      bin
   |
 Documents
```

---

## 11. Important Linux Commands

### 11.1 Navigation Commands

| Command | Purpose |
|---|---|
| `pwd` | Displays current directory |
| `ls` | Lists files |
| `ls -l` | Detailed listing |
| `ls -a` | Shows hidden files |
| `cd` | Changes directory |
| `cd ..` | Moves to parent directory |
| `cd ~` | Moves to home directory |

### 11.2 File and Directory Commands

| Command | Purpose |
|---|---|
| `touch file.txt` | Creates an empty file or updates timestamps |
| `mkdir test` | Creates a directory |
| `rmdir test` | Removes an empty directory |
| `cp a.txt b.txt` | Copies a file |
| `mv a.txt b.txt` | Moves or renames a file |
| `rm a.txt` | Deletes a file |
| `rm -r test` | Removes a directory recursively |
| `cat file.txt` | Displays file content |
| `less file.txt` | Views file content page by page |
| `head file.txt` | Displays first lines |
| `tail file.txt` | Displays last lines |

### 11.3 Searching Commands

#### grep

Searches for a pattern in text.

```bash
grep "hello" file.txt
```

Case-insensitive search:

```bash
grep -i "hello" file.txt
```

Recursive search:

```bash
grep -r "hello" .
```

#### find

Searches for files and directories.

```bash
find . -name "*.txt"
```

Find directories:

```bash
find . -type d
```

### 11.4 System Information Commands

| Command | Purpose |
|---|---|
| `uname -a` | Displays kernel/system information |
| `date` | Displays date and time |
| `whoami` | Displays current username |
| `id` | Displays user and group IDs |
| `free -h` | Displays memory usage |
| `df -h` | Displays file-system disk usage |
| `du -sh folder` | Displays folder size |
| `uptime` | Displays system uptime and load averages |

---

## 12. File Permissions in Linux

Linux uses a permission system to control access to files and directories.

### Three Permission Categories

1. Owner.
2. Group.
3. Others.

### Three Basic Permissions

| Permission | Symbol | Numeric Value |
|---|---|---:|
| Read | r | 4 |
| Write | w | 2 |
| Execute | x | 1 |

### Example

```text
-rwxr-xr--
```

Breakdown:

```text
- | rwx | r-x | r--
    Owner Group Others
```

- Owner: Read, write, execute.
- Group: Read, execute.
- Others: Read only.

### Numeric Permissions

| Permission | Value |
|---|---:|
| rwx | 7 |
| rw- | 6 |
| r-x | 5 |
| r-- | 4 |
| -wx | 3 |
| -w- | 2 |
| --x | 1 |
| --- | 0 |

Example:

```bash
chmod 755 script.sh
```

Meaning:

- Owner: 7 = rwx.
- Group: 5 = r-x.
- Others: 5 = r-x.

### chmod

Changes file permissions.

```bash
chmod 644 notes.txt
chmod 755 script.sh
```

Symbolic mode:

```bash
chmod u+x script.sh
chmod g-w notes.txt
chmod o+r notes.txt
```

### chown

Changes file ownership.

```bash
sudo chown user1 notes.txt
```

Change both owner and group:

```bash
sudo chown user1:developers notes.txt
```

### umask

`umask` defines permission bits that are removed from the default permissions when new files or directories are created.

Example:

```bash
umask 022
```

Typical default permissions before applying umask are:
- Files: 666.
- Directories: 777.

For `umask 022`:

```text
File:      666 - 022 = 644
Directory: 777 - 022 = 755
```

The operation is effectively a bit-mask operation rather than ordinary decimal subtraction.

---

## 13. Pipes in Linux

A pipe allows the output of one command to become the input of another command.

The pipe symbol is:

```bash
|
```

Example:

```bash
ls -l | grep ".txt"
```

Working:

1. `ls -l` produces a directory listing.
2. The pipe sends its standard output to the next command's standard input.
3. `grep` filters lines containing `.txt`.

### Another Example

```bash
cat file.txt | wc -l
```

Counts the number of lines in a file.

A simpler equivalent is:

```bash
wc -l < file.txt
```

### Pipe Diagram

```text
+-----------+       +-----------+
| Command 1 | ----> | Command 2 |
+-----------+ Pipe  +-----------+
   Output             Input
```

### Types of Pipes

**Anonymous Pipe:**
- Commonly created using `pipe()`.
- Usually used between related processes.
- Provides a byte stream.

**Named Pipe (FIFO):**
- Has a name in the file system.
- Can be used by unrelated processes.
- Created using `mkfifo`.

Example:

```bash
mkfifo mypipe
```

---

## 14. Input and Output Redirection

Redirection changes where a command reads input or sends output.

### 14.1 Standard Streams

| Stream | File Descriptor | Meaning |
|---|---:|---|
| stdin | 0 | Standard Input |
| stdout | 1 | Standard Output |
| stderr | 2 | Standard Error |

### 14.2 Output Redirection

```bash
ls > output.txt
```

Writes standard output to a file, replacing its previous contents.

Append output:

```bash
ls >> output.txt
```

### 14.3 Input Redirection

```bash
sort < names.txt
```

Reads input from `names.txt`.

### 14.4 Error Redirection

```bash
command 2> error.txt
```

Redirects standard error.

Redirect both standard output and standard error:

```bash
command > output.txt 2>&1
```

### Redirection Comparison

| Operator | Purpose |
|---|---|
| `>` | Redirect stdout, overwrite |
| `>>` | Redirect stdout, append |
| `<` | Redirect stdin |
| `2>` | Redirect stderr |
| `2>>` | Append stderr |
| `2>&1` | Send stderr to the current stdout destination |

---

## 15. Environment Variables

Environment variables store values used by the shell and processes.

Examples:

```bash
echo $HOME
echo $PATH
echo $USER
```

### Important Variables

| Variable | Purpose |
|---|---|
| `HOME` | User's home directory |
| `PATH` | Directories searched for executable commands |
| `USER` | Current username in common environments |
| `SHELL` | User's configured login shell |
| `PWD` | Current working directory |

### Creating a Variable

```bash
name="Linux"
echo $name
```

Exporting a variable:

```bash
export name="Linux"
```

Exported variables are inherited by child processes.

---

## 16. Shell Scripting Basics

A shell script is a file containing commands that can be executed by a shell.

### Example

```bash
#!/bin/bash

echo "Hello Linux"

name="Operating Systems"

echo $name
```

Save it as:

```text
hello.sh
```

Give execute permission:

```bash
chmod +x hello.sh
```

Run it:

```bash
./hello.sh
```

### Conditional Statements

```bash
#!/bin/bash

read -p "Enter a number: " n

if [ "$n" -gt 0 ]; then
    echo "Positive"
elif [ "$n" -lt 0 ]; then
    echo "Negative"
else
    echo "Zero"
fi
```

### For Loop

```bash
#!/bin/bash

for i in 1 2 3 4 5
do
    echo "$i"
done
```

### While Loop

```bash
#!/bin/bash

i=1

while [ "$i" -le 5 ]
do
    echo "$i"
    i=$((i + 1))
done
```

---

## 17. Interprocess Communication in Linux

Interprocess Communication (IPC) allows processes to exchange information and coordinate their activities.

Linux supports several IPC mechanisms.

### 17.1 Pipes

Pipes allow data transfer between processes through a byte stream.

### 17.2 Named Pipes

Named pipes allow unrelated processes to communicate through a FIFO special file.

### 17.3 Message Queues

Message queues allow processes to exchange structured messages.

### 17.4 Shared Memory

Shared memory allows multiple processes to access a common memory region.

It can provide high-speed communication because processes can access the same memory instead of repeatedly copying data through a kernel-managed message path.

However, synchronization is required to avoid race conditions.

### 17.5 Semaphores

Semaphores are synchronization mechanisms used to control access to shared resources.

### 17.6 Sockets

Sockets allow communication between processes on the same machine or across a network.

### IPC Comparison

| IPC Mechanism | Main Purpose |
|---|---|
| Pipe | Byte-stream communication |
| Named Pipe | Communication through a named FIFO |
| Message Queue | Exchange messages |
| Shared Memory | Share a memory region |
| Semaphore | Synchronization |
| Socket | Local or network communication |

---

## 18. Important Linux Process States

A process can move between different states during its lifetime.

Common Linux process states include:

| State | Meaning |
|---|---|
| R | Running or runnable |
| S | Interruptible sleep |
| D | Uninterruptible sleep, commonly waiting for I/O |
| T | Stopped or traced |
| Z | Zombie |

### Zombie Process

A zombie process is a terminated child process whose exit status has not yet been collected by its parent using a wait operation.

It no longer executes, but a process-table entry remains until the parent collects its status.

### Orphan Process

An orphan process is a child process whose parent terminates before it does.

On Linux, orphan processes are reparented to an appropriate system process or subreaper.

### Zombie vs Orphan

| Zombie | Orphan |
|---|---|
| Child has terminated | Child is still running |
| Parent has not collected exit status | Original parent has terminated |
| Occupies a process-table entry | Continues normal execution |
| Resolved when status is collected | Reparented to another process |

---

# 19. Important Interview Questions and Answers

### Q1. What is Linux?

Linux is an open-source, Unix-like operating system kernel used in many operating-system distributions.

### Q2. What is a kernel?

The kernel is the core part of an OS that manages CPU, memory, devices, processes, and system resources.

### Q3. What is a shell?

A shell is a command interpreter that accepts user commands and executes programs or built-in operations.

### Q4. What is a system call?

A system call is a controlled interface through which a user program requests services from the kernel.

### Q5. What is the difference between a system call and a library function?

A system call requests a kernel service. A library function is a reusable function provided by a library; it may invoke a system call, but it may also perform work entirely in user space.

### Q6. What does fork() do?

`fork()` creates a child process from the calling process.

### Q7. What does exec() do?

An `exec()` function replaces the current process image with a new program.

### Q8. Does exec() create a new process?

No. On success, it replaces the current process image while retaining the process ID.

### Q9. What is wait()?

`wait()` allows a parent process to wait for a child process and collect its termination status.

### Q10. What is the difference between fork() and exec()?

`fork()` creates a child process, whereas `exec()` replaces the current process image with a new program.

### Q11. What is a zombie process?

A zombie is a terminated child process whose exit status has not yet been collected by its parent.

### Q12. What is an orphan process?

An orphan is a child process whose original parent has terminated while the child is still running.

### Q13. What is the difference between user mode and kernel mode?

User mode has restricted privileges, while kernel mode permits privileged OS operations.

### Q14. What is a pipe?

A pipe is an IPC mechanism that transfers data as a byte stream between processes.

### Q15. What is the difference between a pipe and a named pipe?

An ordinary anonymous pipe is commonly used between related processes. A named pipe has a file-system name and can be used by unrelated processes.

### Q16. What is the purpose of chmod?

`chmod` changes file and directory permissions.

### Q17. What does chmod 755 mean?

Owner gets read, write, execute; group and others get read and execute.

### Q18. What is the difference between > and >>?

`>` overwrites the output file, while `>>` appends output to the file.

### Q19. What is the difference between stdout and stderr?

stdout is standard output, while stderr is standard error. They are separate output streams.

### Q20. What is the Linux kernel type?

Linux is commonly classified as a monolithic kernel with modular capabilities.

### Q21. What is IPC?

IPC stands for Interprocess Communication. It allows processes to exchange information and synchronize.

### Q22. What is shared memory?

Shared memory is an IPC mechanism in which multiple processes access a common memory region.

### Q23. What is a device driver?

A device driver is software that enables the operating system to communicate with a hardware device.

### Q24. What is the purpose of the PATH variable?

PATH contains directories that the shell searches when locating executable commands.

### Q25. What is the difference between ps and top?

`ps` displays a process snapshot, whereas `top` provides an updating, interactive process and resource monitor.

---

# 20. Practical Linux Commands for Placements

| Task | Command |
|---|---|
| Current directory | `pwd` |
| List files | `ls -la` |
| Create directory | `mkdir test` |
| Create file | `touch a.txt` |
| Copy file | `cp a.txt b.txt` |
| Rename file | `mv a.txt b.txt` |
| Delete file | `rm a.txt` |
| Search text | `grep "word" file.txt` |
| Find file | `find . -name "a.txt"` |
| Current user | `whoami` |
| Show processes | `ps aux` |
| Live process view | `top` |
| Kill process | `kill PID` |
| Disk usage | `df -h` |
| Folder size | `du -sh folder` |
| Memory usage | `free -h` |
| Change permission | `chmod 755 file` |
| Change owner | `chown user file` |
| Show kernel version | `uname -r` |
| Show current date | `date` |

---

# 21. Quick Revision Notes

| Topic | Key Point |
|---|---|
| Linux | Open-source Unix-like OS kernel |
| Kernel | Core resource manager |
| Shell | Command interpreter |
| System Call | Interface to kernel services |
| User Mode | Restricted execution mode |
| Kernel Mode | Privileged execution mode |
| fork() | Creates child process |
| exec() | Replaces process image |
| wait() | Collects child status |
| exit() | Terminates process |
| getpid() | Returns process ID |
| chmod | Changes permissions |
| chown | Changes ownership |
| grep | Searches text patterns |
| find | Searches files |
| Pipe | Connects command output to input |
| Redirection | Changes input/output destination |
| IPC | Communication between processes |
| Shared Memory | Common memory region |
| Zombie | Terminated child not yet collected |
| Orphan | Child whose original parent terminated |
| Device Driver | Software interface to hardware |

---

# 22. Most Important Topics for Placements

Focus on these concepts:

1. Linux architecture.
2. Kernel vs Shell.
3. Monolithic vs Microkernel.
4. User mode vs Kernel mode.
5. System calls and their categories.
6. fork(), exec(), wait(), and exit().
7. fork() output-based questions.
8. Zombie vs Orphan processes.
9. Linux file permissions and chmod.
10. Pipes and redirection.
11. Linux process-management commands.
12. IPC mechanisms.
13. Linux directory structure.
14. Environment variables.
15. Shell scripting basics.

## Final Placement Tip

The most important Linux concepts for OS interviews are:

- `fork()` creates a child process.
- `exec()` replaces the current process image.
- `wait()` allows a parent to collect a child's termination status.
- System calls provide controlled access to kernel services.
- Pipes, shared memory, message queues, and sockets are IPC mechanisms.
