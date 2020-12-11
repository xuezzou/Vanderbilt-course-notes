## Final Review


### Address spaces
- Every process gets its own private (*virtual*) address space
- Different areas include:
    - **text**: the process’s instructions
    - **data**: initialized (global) data
    - **bss**: uninitialized (global) data
    - **heap**: dynamically allocated memory (related syscalls: `brk()`, `sbrk()`)
    - **stack**: organizes procedure calls with ​*stack frames*
- Accessing an address in an invalid way usually results in a process receiving a `SIGSEGV` signal which is a *segmentation fault*
    - Example: dereferencing a null pointer
    - Writing to an area of memory that’s marked as read-only
- The OS deals with chunks of memory called **​pages**
    - Usually **4kB**; Linux supports ​transparent huge pages​ -- usually 2MB 
- OS translates **virtual addresses** into **physical addresses**
    - Main data structure: **page table**
        - Other (Linux) data structure `vma_struct`
    - Big idea:
        - Part of the virtual address is used to find the physical page
            - The **page table entry (PTE)** maps from the virtual page to the physical page
        - The other part forms the **offset** on the physical page
    - Currently, x86-64 Linux uses 4-levels of page tables (and 48-bit addresses)
    - Can also use 5-level page tables (and 57-bits of the 64-bit address)
- Made possible by a special cache called the **translation lookaside buffer (TLB)**
    - Built into the CPU and runs at full CPU speed
    - Caches recently used page table entries
    - Once a PTE is present in the TLB, it is often used repeatedly
        - Why? Things like loops reference addresses that are *close* to each other

### Processes
- Motivation: *instance of a running program*
- The kernel keeps many accounting structures around
- Creation (in Linux): `fork()` (often followed by `exec()`)
    - `fork()` creates an (almost identical) copy of the calling process 
        - Things like the PID are different
    - `exec()` loads a new program into the address space of the calling process
        - The existing address space is blown away and loaded with the data and instructions of the new program
        - However, things like the PID and file descriptors remain the same
- The separation allows the parent process to “fix-up” file descriptors after `fork()` but before `exec()`
- A process can terminate in two ways
    - **Normal termination**: things like exit(0) or return 1 from main
        - Successful: usually means the process returned 0
        - Unsuccessful: usually means the process didn’t return 0
    - **Abnormal termination**: the process was termination by a **signal**
        - Example: a process dereferenced a null pointer, which cause the OS to send it the SIGSEGV signal, whose default action is to terminate the process
- A process can obtain exit status information on another process by using the `wait()` system call
    - Special macros, such as `WIFEXITED` and `WEXITSTATUS` are then used to determine if the process exited normally, abnormally, successfully or unsuccessfully

### Threads
- Motivation: do things in **parallel**, improve responsiveness
- Used in things like UIs, networking
- Provided via a special library
    - The `pthread` library in Linux
- Usage: pass the `pthread_t` a pointer to a function
    - `void *(*function)(void *arg);`
    - function is a pointer to a function that takes a void pointer and returns a void pointer
- On Linux: very similar to processes
    - Difference: threads are processes that **share certain parts of their address space**

### Synchronization
- Motivation: **race conditions** otherwise
- Implemented on modern OSes with hardware supported **atomic instructions**
    - `xchg`: atomically sets a variable to a given value and returns the old value 
- **Spin-locks**: just continuously check the condition
    - Bad choice if you don’t know how long you’ll wait
    - Useful when you know the condition will **change quickly**
- **Mutex**:
    - The OS puts you to sleep if the lock is already taken
    - `pthread_mutex_t` in Linux
    - Built on top of the ​`futex()​` system call in Linux
        - F​ast ​u​serspace mu​tex
        - Idea: if the lock is not contended, take it without making a system call
        - If the lock is contended (i.e., already taken) spin for a little bit and try to avoid going to sleep
- **Condition variable**: if you need a lock ​and​ a condition to hold
    - Example: You want the value of a shared variable to be a certain value before proceeding also provides mutual exclusion
        - You need the lock to obtain exclusive access
        - But what if the variable isn’t the right value?
            - A ​condition variable​ lets you sleep until the condition ​might​ hold
    - Pattern:
        - Obtain the lock
        - Check the condition
            - Condition true: make your updates; release lock
            - Condition false: ​wait​ on the condition variable
                - The OS takes care of automatically release the lock for you
                - When you return from waiting, you automatically have the lock; return to the “Check the condition” step above
- **Semaphore**: an object with an integer value
    - Think of its value as the “amount” of a resource that’s available
    - Usage: increment and decrement with two functions
        - Increment the value with `sem_post()`
            - If there are one or more waiting threads, wake one
        - Decrement with `sem_wait()`
            - Wait if the value of the semaphore is negative
    - Binary semaphore is like a lock
    - Can also be used for ordering
        - Example: initialize the semaphore to 0
        - A *“producer”* thread appends to a buffer and increments semaphore
        - The *“consumer”* thread is woken and knows the buffer isn’t empty
    - Can be implemented with a mutex and condition variable, for instance
    - In Linux: semaphores can be used **across threads or processes**; pthread mutexes, on the other hand, can only be used with threads

### Scheduling
- Scheduler decides which process runs and for how long
- Linux is a preemptive, multitasking OS
    - **Preemptive**: scheduler (via external interrupts) decides when a process should stop and another should start
    - **Multitasking**: it can interleave execution of more than one process simultaneously
        - On a single processor machine, this gives the *illusion multiple processes are running concurrently*
        - On a multi-processor (multi-core) machine, this allows multiple processes to truly run in parallel
- Preemption is caused by either
    - Returning from an interrupt handler (like the timer interrupt)
    - A process making a system call
        - When exiting the system call, the kernel checks if scheduler should be invoked
- Many types of scheduling algorithms
    - Different schedulers are good at different types of workloads
- **Multilevel Feedback Queues (MLFQ)** tries to automatically optimize turnaround time and minimizing response time
- The “default” Linux scheduler is called the **completely fair scheduler (CFS)**
    - Based on the idea of “lottery” scheduling
- **Realtime** scheduling is about determinism
    - Realtime does ​not​ mean as fast as possible
    - In realtime scheduling, you want guarantees that tasks will execute them when you need them to
- General purpose Linux is **not** a real-time OS
    - There are patches (`​PREEMPT_RT​`) and scheduling classes (`​SCHED_DEADLINE​`) that try to make Linux real-time

### Interprocess communication (IPC)
- Provides a way to let processes “talk” to one another
- **Byte-stream** vs **message-oriented**
    - Byte-stream IPC means that a read operation may read an arbitrary number of bytes from the IPC facility, regardless of the size of blocks written by the writer.
        - Examples: stream sockets (**TCP sockets**), **pipes**
    - Each read operation reads a whole message, as written by the writer process
        - Examples: datagram sockets (**UDP sockets**), POSIX message queues 
- **Sockets** are also an IPC mechanism
    - Unix domain sockets let processes on the same machine communicate 
        - `AF_UNIX`
    - Internet domain sockets let processes on different machines communicate
        - `AF_INET`: IPv4 addresses
        -` AF_INET6`: IPv6 addresses
    - Another domain, `AF_XDP`, is fairly recent and is for fast packet processing

### File systems
- Organized collection of files and directories
- In Linux file systems every file has a **corresponding i-node**
- I-nodes contain:
    - **Meta-data** (such as permissions, timestamps, file size)
    - **Pointers to the data blocks** (the actual data of the file)
- Older file systems (like `ext2`) use *indirect pointers* to data blocks to represent large files
    - Newer file systems (like `ext4`) use extents, which specify the starting block for a chunk of data and the number of blocks in that chunk
- There is no special “EOF” marker on a file; the i-node contains the file size
- **Journaling file systems** help ensure that the integrity of a file system can be maintained and restored quickly if something unexpected, like a power outage, occurs.
    - **Meta-data updates** are “journaled” (logged) to a journal file before they are performed. The journal is “re-played” if there is a crash or power outage.
    - The alternative is a **“file system check”**, or `fsck`: this does things like check the size of the file system, ensure the free i-node list is correct, ensure the free data-block list is correct. A full file system check often takes a very long time.
- On Linux, almost everything is treated as a file:
    - **Regular file**: things like text files (containing ASCII characters) or binary files (like a compiled executable)
    - **Directories**: a file with a special format; maps file names to i-node numbers
    - **Character devices**: represents a character device (device that reads input character by character). Usually under `/dev`. Things like a mouse, keyboard or terminal driver.
    - **Block devices**: represents a block device (device that reads input in “block” size chunks). Things like hard drive or DVD drivers. Usually under `/dev`.
    - **Sockets**: a method for IPC between processes on the same or different machines
    - **Message queues**: implemented as a special type of file system. Allows IPC through discrete “messages.”
    - **Pipes**: implemented as a special type of file system. Allows *stream-oriented IPC.*

### VMMs
- Virtual Machine Monitors, also called hypervisors, allow more than one OS to run on the same hardware simultaneously
- “Bare-metal” VMMs sit directly on top of the hardware
    - Examples: Xen, Hyper-V 
- *Hosted* VMMs sit on top of an OS
    - User mode hosted run entirely in user mode
    - *Dual-mode* hosted VMMs run partly in user mode, partly in kernel mode
        - Example: VIrtualBox -- it contains a kernel driver
- The VMM has to make the guest think it is in control of the hardware when in fact the VMM is in full control
- VIrtualizing the x86 is difficult
    - It’s an old architecture that didn’t include virtualization support from the beginning
    - Can be done in software with ugly tricks
        - Example: run guest kernel code in *ring-1* instead of *ring-0*
    - Intel and AMD introduced hardware to make this easier
        - Intel calls it *VT-x*; AMD calls it *AMD-V* 
        - The hardware extensions introduce two new CPU operation modes
            - VMX **root** mode: used by host OS without virtualization and VMM
            - VMX **non-root** mode: used by guest OS
                - A hardware *virtual machine control structure* (VMCS) determines how certain instructions behave - reference Intel manual
        - The hardware allows guest code to run in the ring it expects 
    - Additional hardware extensions make virtualizing memory easier
        - Called nested paging or *extended page tables*
        - Idea: use *extra hardware* to do standard page table lookups
            - Alternative: shadow page tables that must be maintained by the VMM; these are relatively expensive

### Books Recommendation
1. The Linux Programming Interface (library has a copy - free) 
    + Socket / network programming 
    + Telephone connection etc. 
2. Unix Network programming: The Sockets Networking API by Richard Stevens 
    + Classic on network programming 
    + (Advanced programming n the UNIX environment also by Richard Stevens) 