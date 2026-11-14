
# Unit 9: File System Management

## 1. Introduction to File Systems

A **File System** is a method used by an operating system to organize, store, retrieve, name, and manage data on storage devices such as HDDs, SSDs, and USB drives.

It provides an interface between users/applications and physical storage.

### Why Do We Need a File System?

Without a file system, applications would need to manage the physical locations of data directly.

A file system provides:

- Data organization.
- File naming and directory management.
- File creation and deletion.
- File access and modification.
- Storage-space allocation.
- File protection and security.
- Data consistency and recovery.

### Basic File System Architecture

```text
+-----------------------------+
|      User Applications      |
+-----------------------------+
|       System Calls          |
| open(), read(), write()     |
+-----------------------------+
|     Virtual File System     |
+-----------------------------+
|   Logical File System       |
| Metadata, Directories       |
+-----------------------------+
|   File Organization Module  |
| Allocation, Free Space      |
+-----------------------------+
|      Basic File System      |
| Block Read / Write          |
+-----------------------------+
|       I/O Control            |
+-----------------------------+
|      Storage Device         |
|       HDD / SSD             |
+-----------------------------+
```

---

## 2. File Concept

A **file** is a named collection of related information stored on secondary storage.

Examples:
- `notes.txt`
- `program.java`
- `photo.jpg`
- `database.db`

### 2.1 File Attributes

Every file has metadata describing its properties.

| Attribute | Description |
|---|---|
| Name | Human-readable file name |
| Identifier | Unique internal identification |
| Type | File format or type |
| Location | Storage location of the file |
| Size | Current file size |
| Protection | Access permissions |
| Time and Date | Creation, modification, access times |
| Owner | User who owns the file |

**Important:** File attributes are metadata. They describe the file rather than its actual contents.

### 2.2 File Operations

The operating system provides system calls to perform file operations.

| Operation | Description |
|---|---|
| Create | Creates a new file |
| Open | Opens a file for access |
| Read | Reads data from a file |
| Write | Writes data into a file |
| Append | Adds data at the end |
| Seek | Changes the current file position |
| Close | Closes an opened file |
| Delete | Removes a file |
| Rename | Changes the file name |
| Truncate | Removes file contents while retaining the file |

### Example: File Handling

```c
FILE *fp;

fp = fopen("notes.txt", "w");

fprintf(fp, "Operating Systems");

fclose(fp);
```

Here:
- `fopen()` opens or creates a file.
- `fprintf()` writes data.
- `fclose()` closes the file.

### 2.3 File Types

| File Type | Example |
|---|---|
| Text | `.txt`, `.csv` |
| Executable | `.exe` |
| Source Code | `.c`, `.java`, `.cpp` |
| Object File | `.obj`, `.o` |
| Image | `.jpg`, `.png` |
| Audio | `.mp3`, `.wav` |
| Video | `.mp4`, `.mkv` |

---

## 3. File Access Methods

File access methods define how data is read from or written to a file.

There are three major methods.

### 3.1 Sequential Access

Data is accessed in a sequential order, one record after another.

```text
Record 1 -> Record 2 -> Record 3 -> Record 4
```

Characteristics:
- Records are processed in order.
- To reach a later record, earlier records may need to be traversed.
- Suitable for text files, logs, and tape storage.

Example: Reading a file line by line.

### 3.2 Direct Access / Random Access

Any block or record can be accessed directly using its position or address.

```text
+----------+----------+----------+----------+
| Block 0  | Block 1  | Block 2  | Block 3  |
+----------+----------+----------+----------+
                         |
                         v
                  Access Block 2
```

Characteristics:
- No need to read all previous records.
- Supports quick access to a specific location.
- Common in databases and disk-based storage.

### 3.3 Indexed Access

An index is maintained to locate records quickly.

```text
              INDEX
        +----------------+
        | Record 1 -> 10 |
        | Record 2 -> 25 |
        | Record 3 -> 40 |
        +----------------+
                 |
                 v
          Actual Data Blocks
```

Characteristics:
- Uses an index to locate records.
- Supports efficient searching.
- Requires additional storage for the index.

### Comparison

| Feature | Sequential | Direct | Indexed |
|---|---|---|---|
| Access | In order | Any location | Through index |
| Speed for random lookup | Slow | Fast | Fast |
| Extra index required | No | No | Yes |
| Best suited for | Logs | Databases | Large indexed files |

---

## 4. Directory Structure

A **directory** is a structure that stores information about files and helps organize them.

It maintains file names and references to their metadata.

### 4.1 Single-Level Directory

All files are stored in a single directory.

```text
       Root
         |
   +-----+-----+
   |     |     |
  A.txt B.txt C.txt
```

Advantages:
- Simple to implement.
- Easy to understand.

Disadvantages:
- File-name conflicts.
- Difficult to organize many files.
- Not suitable for multiple users.

### 4.2 Two-Level Directory

Each user has a separate directory.

```text
             Root
              |
       +------+------+
       |             |
     User A        User B
       |             |
   +---+---+     +---+---+
   |       |     |       |
 A.txt   B.txt  A.txt   C.txt
```

Advantages:
- Different users can have files with the same name.
- Better user separation.

Disadvantages:
- Limited file sharing.
- Less flexible organization within each user directory.

### 4.3 Tree-Structured Directory

Directories can contain files and subdirectories.

```text
             Root
               |
       +-------+-------+
       |               |
     Home             System
       |               |
   +---+---+         Kernel
   |       |
 User1   User2
   |
 +---+---+
 |       |
Docs    Images
```

Advantages:
- Hierarchical organization.
- Easy grouping of related files.
- Supports absolute and relative paths.

Example:

```text
/home/user1/docs/notes.txt
```

### 4.4 Acyclic Graph Directory

Allows shared files or directories through links but does not allow cycles.

```text
          Root
           |
       +---+---+
       |       |
      Dir A   Dir B
       |       |
       +---+---+
           |
       Shared File
```

Advantages:
- Supports sharing.
- Avoids duplicate copies of shared files.

Disadvantages:
- Requires link management.
- Deletion and reference management become more complex.

### 4.5 General Graph Directory

Allows links that can create cycles.

```text
      A ------> B
      ^         |
      |         |
      +---------+
```

Advantages:
- Flexible sharing and linking.

Disadvantages:
- Cycles can cause infinite traversal.
- Garbage collection or cycle detection may be required.

### Directory Structure Comparison

| Structure | Main Feature |
|---|---|
| Single-Level | One directory for all files |
| Two-Level | Separate directory for each user |
| Tree | Hierarchical structure |
| Acyclic Graph | Sharing without cycles |
| General Graph | Sharing with possible cycles |

---

## 5. File Paths

A path specifies the location of a file within a directory structure.

### 5.1 Absolute Path

An absolute path starts from the root directory.

Example:

```text
/home/user/docs/notes.txt
```

### 5.2 Relative Path

A relative path starts from the current working directory.

Example:

```text
docs/notes.txt
```

If the current directory is `/home/user`, both paths refer to the same file.

### Important Symbols

| Symbol | Meaning |
|---|---|
| `/` | Root directory in Unix/Linux |
| `.` | Current directory |
| `..` | Parent directory |
| `~` | User's home directory in common Unix shells |

---

## 6. File System Mounting

**Mounting** is the process of attaching a file system on a storage device to an existing directory in the operating system's directory tree.

The directory where the file system is attached is called the **mount point**.

Example:

```text
        Root /
          |
    +-----+------+
    |            |
   home         mnt
                 |
              USB Drive
```

In Linux, a USB drive might be mounted at:

```bash
/mnt/usb
```

### Why Is Mounting Needed?

- Makes storage devices accessible.
- Integrates multiple file systems into one directory tree.
- Allows users to access external storage.

### Unmounting

Unmounting safely detaches a mounted file system.

Example:

```bash
umount /mnt/usb
```

It helps ensure pending writes are completed before removing a device.

---

## 7. File Allocation Methods

File allocation determines how file blocks are stored on a disk.

The three traditional allocation methods are:

1. Contiguous allocation.
2. Linked allocation.
3. Indexed allocation.

### 7.1 Contiguous Allocation

In contiguous allocation, a file occupies consecutive disk blocks.

Example:

A file requires 4 blocks and starts at block 5.

```text
Disk Blocks:

0  1  2  3  4  5  6  7  8  9
             |  |  |  |
             +--+--+--+
               File A
```

File A occupies blocks 5, 6, 7, and 8.

The directory stores:
- Starting block.
- Length of the file in blocks.

**Advantages:**
- Simple implementation.
- Excellent sequential access.
- Fast direct access.
- Minimal metadata overhead.

**Disadvantages:**
- External fragmentation.
- File growth is difficult.
- May require relocation when a file expands.

#### External Fragmentation

External fragmentation occurs when free space is divided into small non-contiguous pieces.

Example:

```text
[Used][Free][Used][Free][Used][Free]
```

Even if total free space is sufficient, a large contiguous file may not fit.

### 7.2 Linked Allocation

In linked allocation, file blocks can be located anywhere on the disk. Each block contains a pointer to the next block.

Example:

```text
Block 4 -> Block 9 -> Block 2 -> Block 15 -> NULL
```

Each block contains:
- File data.
- Pointer to the next block.

**Advantages:**
- No external fragmentation.
- Files can grow easily.
- Simple free-block allocation.

**Disadvantages:**
- Random access is slow.
- Pointer storage creates overhead.
- A corrupted pointer can break the chain.

#### File Allocation Table (FAT)

FAT is a variation of linked allocation.

Instead of storing the next-block pointer inside every data block, pointers are maintained in a separate table.

Example:

```text
FAT Table

Block     Next Block
  4           9
  9           2
  2          15
 15          EOF
```

The table makes it easier to follow a file's block chain.

### 7.3 Indexed Allocation

In indexed allocation, each file has an index block containing the addresses of its data blocks.

Example:

```text
         Index Block
       +-------------+
       |      4      |
       |      9      |
       |      2      |
       |     15      |
       +-------------+
          |   |   |
          v   v   v
       Data Blocks
```

**Advantages:**
- Supports direct access.
- No external fragmentation.
- Files can grow without requiring contiguous blocks.

**Disadvantages:**
- Index block requires additional space.
- Small files may waste index-block space.
- Large files may need multiple levels of indexing.

### 7.4 Multilevel Indexed Allocation

For very large files, one index block may not be sufficient.

Multilevel indexing uses multiple levels of index blocks.

```text
           Inode
             |
      Single Indirect
             |
         Data Blocks

           Inode
             |
      Double Indirect
             |
       Indirect Blocks
             |
         Data Blocks
```

A triple-indirect pointer introduces another level.

### 7.5 Extent-Based Allocation

An extent is a group of consecutive disk blocks.

A file can be represented by multiple extents instead of one continuous region.

Example:

```text
File A:
Extent 1: Blocks 4-7
Extent 2: Blocks 15-18
Extent 3: Blocks 25-26
```

Advantages:
- Efficient sequential access.
- Supports file growth.
- Reduces metadata compared with storing every block separately.

Modern file systems commonly use extent-based allocation or related techniques.

### Allocation Methods Comparison

| Feature | Contiguous | Linked | Indexed |
|---|---|---|---|
| Storage | Consecutive blocks | Scattered blocks | Scattered blocks |
| Sequential access | Excellent | Good | Good |
| Direct access | Excellent | Poor | Good |
| External fragmentation | Yes | No | No |
| File growth | Difficult | Easy | Easy |
| Pointer overhead | Low | Per-block pointers | Index block overhead |

**Interview Tip:** If asked which allocation method supports efficient direct access, indexed allocation is a strong answer. Contiguous allocation also supports direct access, but it suffers from external fragmentation and file-growth problems.

---

## 8. Free Space Management

The operating system must track unused disk blocks so they can be allocated to new files.

Free-space management methods include:

### 8.1 Bit Vector / Bitmap

Each disk block is represented by one bit.

- `0` may represent a free block.
- `1` may represent an allocated block.

The exact convention depends on the implementation.

Example:

```text
Block:  0 1 2 3 4 5 6 7
Bit:    1 1 0 0 1 0 1 0
```

Here, blocks 2, 3, 5, and 7 are free if `0` means free.

**Advantages:**
- Easy to find free blocks.
- Efficient to find consecutive free blocks.
- Compact representation.

**Disadvantages:**
- Bitmap may become large for huge disks.
- Bitmap access and updates require management.

### 8.2 Linked List

All free blocks are linked together.

```text
Free List:

Block 3 -> Block 8 -> Block 12 -> Block 20 -> NULL
```

**Advantages:**
- Simple implementation.
- No large bitmap required.

**Disadvantages:**
- Difficult to find consecutive free blocks.
- Traversal can be slow.

### 8.3 Grouping

The first free block stores addresses of several other free blocks.

The last entry points to another block containing more free-block addresses.

**Advantage:** Multiple free blocks can be identified with fewer traversals.

### 8.4 Counting

Instead of storing every free block separately, the system stores:

- Starting address of a free sequence.
- Number of consecutive free blocks.

Example:

```text
Starting Block = 10
Count = 5
```

This represents blocks 10, 11, 12, 13, and 14.

**Advantage:** Efficient when free space appears in consecutive runs.

### Comparison

| Method | Main Idea |
|---|---|
| Bitmap | One bit per block |
| Linked List | Free blocks linked together |
| Grouping | One free block stores addresses of others |
| Counting | Stores starting block and run length |

---

## 9. Inode and File Control Block (FCB)

An **inode (index node)** is a data structure used by Unix-like file systems to store metadata about a file and information needed to locate its data blocks.

An inode typically stores:

- File type.
- File permissions.
- Owner ID.
- Group ID.
- File size.
- Timestamps.
- Link count.
- Pointers to data blocks.

### Inode Structure

```text
             INODE
       +------------------+
       | File Type        |
       | Permissions      |
       | Owner ID         |
       | Group ID         |
       | File Size        |
       | Timestamps       |
       | Link Count       |
       | Direct Pointers  |
       | Indirect Pointer |
       +------------------+
```

### Important: File Name vs Inode

In Unix-like file systems, the file name is generally stored in a directory entry, which maps the name to an inode number.

```text
Directory Entry
+------------------+
| notes.txt -> 105 |
+------------------+
          |
          v
       Inode 105
          |
          v
      Data Blocks
```

The inode does not normally store the file name.

### FCB vs Inode

A File Control Block (FCB) is a general concept for storing file metadata.

An inode is a specific implementation used by Unix-like file systems.

| FCB | Inode |
|---|---|
| General file metadata structure | Unix-like file metadata structure |
| Implementation varies | Common in Unix/Linux file systems |
| May contain file-related information | Stores metadata and block references |
| General OS concept | Specific file-system design concept |

---

## 10. Hard Links vs Symbolic Links

Links allow multiple directory entries to refer to the same file or another path.

### 10.1 Hard Link

A hard link is another directory entry referring to the same inode.

```text
notes.txt ----+
              |
              v
           Inode 105
              |
              v
           Data Blocks
              ^
              |
backup.txt ---+
```

Characteristics:
- Both names refer to the same inode.
- Both names access the same file contents.
- Removing one hard link does not remove the data if another link remains.
- Usually cannot cross file-system boundaries.
- Directories are generally not hard-linked by ordinary users.

### 10.2 Symbolic Link

A symbolic link is a separate file containing a path to another file or directory.

```text
shortcut.txt
      |
      v
Path: /home/user/notes.txt
      |
      v
  Target File
```

Characteristics:
- Has its own inode.
- Stores a pathname to the target.
- Can cross file-system boundaries.
- Can point to directories.
- May become a dangling link if the target is deleted.

### Comparison

| Hard Link | Symbolic Link |
|---|---|
| Refers to same inode | Refers to a pathname |
| Shares file data | Stores target path |
| Usually same file system only | Can cross file systems |
| Target remains accessible through other links | Can become dangling |
| Generally cannot link directories | Can link directories |

---

## 11. File Protection and Permissions

File protection controls who can access or modify files.

The operating system prevents unauthorized access through permissions and access-control mechanisms.

### 11.1 Basic Permissions

In Unix/Linux, common permissions are:

| Permission | Symbol | Meaning |
|---|---|---|
| Read | r | Read file contents |
| Write | w | Modify file contents |
| Execute | x | Execute a file |

For directories:
- Read: List directory entries.
- Write: Create, delete, or rename entries, subject to other permission rules.
- Execute: Search or traverse the directory.

### 11.2 User Categories

Permissions are commonly assigned to:

1. Owner.
2. Group.
3. Others.

Example:

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

### 11.3 Numeric Permission Representation

Permissions are represented using octal values.

| Permission | Value |
|---|---:|
| Read (r) | 4 |
| Write (w) | 2 |
| Execute (x) | 1 |

Examples:

| Permission | Calculation | Value |
|---|---|---:|
| rwx | 4 + 2 + 1 | 7 |
| rw- | 4 + 2 | 6 |
| r-x | 4 + 1 | 5 |
| r-- | 4 | 4 |
| --- | 0 | 0 |

Example:

```bash
chmod 754 notes.txt
```

Meaning:

- Owner: 7 = rwx.
- Group: 5 = r-x.
- Others: 4 = r--.

### 11.4 Access Control List (ACL)

An ACL allows permissions to be assigned to specific users and groups beyond the basic owner/group/others model.

Example:

```text
User A -> Read, Write
User B -> Read
Group C -> Read, Execute
```

### 11.5 Capability-Based Access

A capability is an unforgeable token or reference that grants specific access rights to an object.

For example, a process may hold a capability allowing it to read a file but not modify it.

### Protection Mechanisms Comparison

| Mechanism | Main Idea |
|---|---|
| Permission Bits | Owner, group, others |
| ACL | Detailed user/group permissions |
| Capability | Possession of an authorized access token |

---

## 12. File System Implementation

A file system uses several structures to organize and manage data.

### 12.1 On-Disk Structures

These structures are stored on the storage device.

| Structure | Purpose |
|---|---|
| Boot Control Block | Contains information needed to boot from a volume |
| Superblock / Volume Control Block | Stores file-system information |
| Directory Structure | Maps file names to file metadata |
| FCB / Inode | Stores file metadata |
| Data Blocks | Store actual file contents |
| Free-Space Structure | Tracks unused blocks |

### 12.2 In-Memory Structures

The operating system maintains some information in memory for faster access.

Examples:
- Mount table.
- Directory cache.
- Inode cache.
- System-wide open-file table.
- Per-process file descriptor table.
- Buffer and page caches.

### 12.3 File Descriptor

A **file descriptor** is a small integer used by a process to identify an opened file or another I/O resource in Unix-like systems.

Common standard file descriptors:

| File Descriptor | Meaning |
|---:|---|
| 0 | Standard Input |
| 1 | Standard Output |
| 2 | Standard Error |

Example:

```c
int fd = open("notes.txt", O_RDONLY);

if (fd >= 0) {
    // Perform file operations
    close(fd);
}
```

### 12.4 Open File Tables

The operating system uses open-file information to manage active file access.

A simplified model:

```text
Process A
+----------------------+
| FD 0 -> stdin        |
| FD 1 -> stdout       |
| FD 3 -> Open Entry 1 |
+----------------------+
             |
             v
      System Open File Table
             |
             v
         Inode / FCB
             |
             v
          Data Blocks
```

Different operating systems implement these structures differently, but the general purpose is to track open files and their state.

### 12.5 Virtual File System (VFS)

VFS provides a common interface for applications to access different file-system types.

For example, an operating system may support:
- ext4
- FAT32
- NTFS
- XFS
- Network file systems

Applications can use standard operations such as `open()`, `read()`, and `write()` without needing to know the internal details of each file system.

---

## 13. Caching and Buffering

### 13.1 Caching

Caching stores frequently accessed data in faster memory so future requests can be served more quickly.

Example:

If a file block is accessed repeatedly, the operating system may keep a cached copy in RAM.

Benefits:
- Reduces disk access.
- Improves performance.
- Reduces repeated I/O operations.

### 13.2 Buffering

Buffering temporarily stores data while it is transferred between devices or processes.

Example:

When writing data to a disk, the operating system may collect data in a buffer before writing it.

### Caching vs Buffering

| Caching | Buffering |
|---|---|
| Keeps copies of frequently used data | Temporarily holds data during transfer |
| Main goal is faster repeated access | Main goal is smooth data transfer |
| Avoids repeated retrieval | Handles speed differences between components |

---

## 14. Journaling File Systems

A journaling file system maintains a log, called a journal, of intended file-system changes.

The journal helps the file system recover after unexpected shutdowns or crashes.

### Why Is Journaling Needed?

Suppose a file operation requires multiple metadata updates.

A crash between those updates can leave the file system inconsistent.

Journaling records changes so that recovery can determine which operations were committed.

### Basic Working

```text
       File System Operation
                |
                v
        Write to Journal
                |
                v
       Commit Journal Entry
                |
                v
       Update File System
                |
                v
       Mark Transaction Done
```

The exact sequence depends on the journaling mode and implementation.

### Types of Journaling

1. **Metadata Journaling:** Logs metadata changes.
2. **Full Data Journaling:** Logs both file data and metadata.
3. **Ordered Mode:** Writes file data before committing the related metadata transaction, helping avoid metadata pointing to unwritten data.

### Advantages

- Faster recovery after crashes.
- Better file-system consistency.
- Reduces the need for a full file-system check after every crash.

### Disadvantages

- Additional write overhead.
- Journal consumes storage.
- Performance depends on journaling mode and workload.

**Important:** Journaling improves consistency and crash recovery, but it is not a substitute for backups.

---

## 15. Disk Scheduling

Disk scheduling decides the order in which pending disk I/O requests are serviced.

The goal is to reduce seek time and improve disk performance.

This topic is especially important for traditional magnetic hard disks (HDDs).

### Important Terms

**Seek Time:** Time required for the disk head to move to the required track.

**Rotational Latency:** Time waiting for the desired sector to rotate under the disk head.

**Transfer Time:** Time required to transfer data.

**Disk Access Time:**

```text
Disk Access Time =
Seek Time + Rotational Latency + Transfer Time
```

### 15.1 FCFS (First Come First Serve)

Requests are serviced in their arrival order.

Example:

```text
Request Queue: 98, 183, 37, 122
Initial Head: 53

Movement:
53 -> 98 -> 183 -> 37 -> 122
```

Total head movement:

```text
|98 - 53|  = 45
|183 - 98| = 85
|37 - 183| = 146
|122 - 37| = 85

Total = 45 + 85 + 146 + 85
      = 361 cylinders
```

Advantages:
- Simple.
- Fair in arrival order.
- No starvation caused by request reordering.

Disadvantages:
- Can cause large head movement.
- Poor average seek performance.

### 15.2 SSTF (Shortest Seek Time First)

Services the request closest to the current head position.

Example:

```text
Initial Head: 53
Requests: 98, 183, 37, 122

Order:
53 -> 37 -> 98 -> 122 -> 183
```

Total movement:

```text
|53 - 37|  = 16
|37 - 98|  = 61
|98 - 122| = 24
|122 - 183| = 61

Total = 16 + 61 + 24 + 61
      = 162 cylinders
```

Advantages:
- Often reduces average seek time compared with FCFS.
- Prioritizes nearby requests.

Disadvantages:
- Far-away requests may suffer starvation.
- Does not guarantee the globally minimum total movement.

### 15.3 SCAN (Elevator Algorithm)

The disk head moves in one direction, servicing requests along the way. After reaching the end, it reverses direction.

```text
Direction: Towards higher cylinders

0 ------------------------------ 199
             Head
              |
              v
       Services requests
       while moving right
```

Advantages:
- More uniform waiting time than SSTF in many workloads.
- Reduces unnecessary back-and-forth movement.

Disadvantages:
- Requests near the ends may wait longer.
- The head travels to the disk end before reversing.

### 15.4 C-SCAN (Circular SCAN)

The head services requests in one direction only.

After reaching one end, it returns to the opposite end without servicing requests during the return.

```text
0 ------------------------------ 199
^                                |
|                                |
+---------- Return --------------+
```

Advantages:
- Provides more uniform waiting time across cylinders.
- Services requests in a single direction.

Disadvantages:
- Return movement adds overhead.

### 15.5 LOOK

LOOK is similar to SCAN, but the head reverses direction at the last pending request rather than travelling to the physical disk boundary.

### 15.6 C-LOOK

C-LOOK is similar to C-SCAN, but the head moves only as far as the last pending request before jumping to the first pending request at the other end.

### Disk Scheduling Comparison

| Algorithm | Working | Main Disadvantage |
|---|---|---|
| FCFS | Arrival order | High head movement |
| SSTF | Nearest request first | Starvation possible |
| SCAN | Moves like an elevator | May travel to disk end |
| C-SCAN | Circular one-way scan | Return movement overhead |
| LOOK | Reverses at last request | Waiting time can vary |
| C-LOOK | Circular movement between requests | Jump movement overhead |

### Solved Numerical Example

Given:

```text
Request Queue = 98, 183, 37, 122, 14, 124, 65, 67
Initial Head = 53
Disk Range = 0 to 199
```

#### FCFS

```text
53 -> 98 -> 183 -> 37 -> 122
   -> 14 -> 124 -> 65 -> 67
```

Total head movement:

```text
45 + 85 + 146 + 85 + 108 + 110 + 59 + 2
= 640 cylinders
```

#### SSTF

Choose the nearest pending request each time.

```text
53 -> 65 -> 67 -> 37 -> 14
   -> 98 -> 122 -> 124 -> 183
```

Total movement:

```text
12 + 2 + 30 + 23 + 84 + 24 + 2 + 59
= 236 cylinders
```

**Important:** SCAN, C-SCAN, LOOK, and C-LOOK numerical answers depend on the initial head direction and the precise definition of the return movement. Always state these assumptions before calculating.

**Note:** Modern SSDs do not have a mechanical disk head, so traditional seek-based disk scheduling is less directly applicable to them.

---

## 16. File System Performance and Reliability

File-system performance depends on several factors:

- Storage device speed.
- Block size.
- Allocation method.
- Caching.
- Directory organization.
- Fragmentation.
- Metadata operations.
- I/O scheduling.

### Fragmentation

There are two important types:

**External Fragmentation:** Free space is divided into non-contiguous pieces.

**Internal Fragmentation:** Allocated storage contains unused space inside an allocated block.

Example:

If a block size is 4 KB and a file uses only 3 KB, the remaining 1 KB may be wasted inside that allocated block.

### Defragmentation

Defragmentation rearranges file blocks to improve locality and reduce fragmentation.

It is more relevant to traditional HDDs. SSDs generally do not benefit from traditional defragmentation in the same way.

### File System Consistency

Consistency means that file-system metadata and data structures remain in a valid state.

Methods that help maintain consistency:
- Journaling.
- Metadata checks.
- Write ordering.
- File-system checking utilities.
- Backups.

---

## 17. Important Linux File System Commands

| Command | Purpose |
|---|---|
| `pwd` | Displays current directory |
| `ls` | Lists directory contents |
| `cd` | Changes directory |
| `mkdir` | Creates directory |
| `rmdir` | Removes an empty directory |
| `touch` | Creates an empty file or updates timestamps |
| `cat` | Displays file contents |
| `cp` | Copies files |
| `mv` | Moves or renames files |
| `rm` | Removes files |
| `chmod` | Changes permissions |
| `chown` | Changes ownership |
| `ln` | Creates hard or symbolic links |
| `df` | Displays file-system disk space |
| `du` | Displays file/directory space usage |
| `mount` | Mounts a file system |
| `umount` | Unmounts a file system |

---

# 18. Important Interview Questions and Answers

### Q1. What is a file system?

A file system is an OS mechanism that organizes and manages files on storage devices. It handles file naming, storage allocation, directories, permissions, and retrieval.

### Q2. What is the difference between a file and a directory?

A file stores data, while a directory organizes files and subdirectories by maintaining their names and references.

### Q3. What are the three main file allocation methods?

1. Contiguous allocation.
2. Linked allocation.
3. Indexed allocation.

### Q4. Which allocation method supports direct access efficiently?

Indexed allocation supports direct access efficiently because an index block stores the addresses of the file's data blocks. Contiguous allocation also supports direct access.

### Q5. What is external fragmentation?

External fragmentation occurs when free disk space is divided into small non-contiguous regions, making it difficult to allocate a large contiguous file.

### Q6. What is internal fragmentation?

Internal fragmentation is wasted space inside an allocated block because the requested data does not completely fill the allocated block.

### Q7. What is an inode?

An inode is a Unix-like file-system data structure that stores file metadata and pointers or references to the file's data blocks.

### Q8. Does an inode store the file name?

Normally, no. The directory entry maps a file name to an inode number.

### Q9. What is the difference between a hard link and a symbolic link?

A hard link refers to the same inode as the original file. A symbolic link is a separate file containing a pathname to the target.

### Q10. What is a superblock?

A superblock stores important information about a file system, such as its size, block size, and file-system state.

### Q11. What is a file descriptor?

A file descriptor is a small integer used by a process to refer to an opened file or I/O resource.

### Q12. What is mounting?

Mounting attaches a file system to a directory in the existing directory hierarchy.

### Q13. What is journaling?

Journaling records intended file-system changes in a log to help restore consistency after a crash.

### Q14. What is the difference between SCAN and C-SCAN?

SCAN services requests while moving in both directions. C-SCAN services requests in one direction and returns to the opposite end without servicing requests on the return.

### Q15. What is the difference between SCAN and LOOK?

SCAN moves to the disk boundary before reversing. LOOK reverses at the last pending request in the current direction.

### Q16. What is VFS?

Virtual File System provides a common interface for accessing different file-system implementations.

### Q17. What is free-space management?

It is the method used by the operating system to track unused disk blocks for future allocation.

### Q18. What is the purpose of a bitmap?

A bitmap uses one bit per block to indicate whether that block is free or allocated.

### Q19. What is the difference between caching and buffering?

Caching keeps copies of frequently accessed data to speed up repeated access. Buffering temporarily stores data during transfer between components.

### Q20. Why is disk scheduling required?

Disk scheduling determines the order of pending disk requests to improve performance, particularly by reducing mechanical seek movement in HDDs.

---

# 19. Quick Revision Notes

| Topic | Key Point |
|---|---|
| File System | Organizes and manages stored data |
| File Attribute | Metadata describing a file |
| Sequential Access | Reads data in order |
| Direct Access | Accesses a specific location |
| Indexed Access | Uses an index to locate data |
| Single-Level Directory | One directory for all files |
| Tree Directory | Hierarchical structure |
| Absolute Path | Starts from root |
| Relative Path | Starts from current directory |
| Contiguous Allocation | Consecutive blocks |
| Linked Allocation | Blocks connected by pointers |
| Indexed Allocation | Index block stores addresses |
| Bitmap | One bit per disk block |
| Inode | Metadata and data-block references |
| Hard Link | Another name for the same inode |
| Symbolic Link | Stores a path to a target |
| Mounting | Attaches a file system |
| File Descriptor | Integer representing an open resource |
| VFS | Common interface for file systems |
| Journaling | Log-based crash recovery |
| FCFS | Services requests in arrival order |
| SSTF | Services nearest request |
| SCAN | Elevator algorithm |
| C-SCAN | Circular one-direction scanning |
| LOOK | Reverses at last pending request |
| C-LOOK | Circular LOOK scheduling |

---

# 20. Most Important Topics for Placements

Focus on these topics for technical interviews and OS examinations:

1. File allocation methods and their comparison.
2. Contiguous vs linked vs indexed allocation.
3. Internal and external fragmentation.
4. Directory structures.
5. Inode and FCB.
6. Hard links vs symbolic links.
7. Free-space management techniques.
8. File permissions and `chmod`.
9. File descriptors and open-file tables.
10. Virtual File System (VFS).
11. Journaling and crash recovery.
12. Disk scheduling algorithms.
13. SCAN vs C-SCAN vs LOOK vs C-LOOK.
14. Solving disk head movement numericals.

