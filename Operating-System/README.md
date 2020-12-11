## Operating System Course Notes

> Vanderbilt University Fall 2020, taught by Professor *Daniel Balasubramanian*
> 
> Transcribed by *Xue Zou*

[Textbook: Operating Systems: Three Easy Pieces](http://pages.cs.wisc.edu/~remzi/OSTEP/)

#### Contents
- [Lecture 2: background and review](#lecture-2-background-and-review)
- [Lecture 3: Virtualization and System Calls](#lecture-3-virtualization-and-system-calls)
- [Lecture 4: Process Creation](#lecture-4-process-creation)
- [Lecture 5: process termination](#pre-attentive-processing)
- [Lecture 6: Memory Management Basis](#lecture-6-memory-management-basis)
- [Lecture 7: Virtual Memory](#lecture-7-virtual-memory)
- [Lecture 8: Advanced memory topics](#lecture-8-advanced-memory-topics)
- [Lecture 9: introduction to the Linux kernel](#lecture-9-introduction-to-the-linux-kernel)
- [Lecture 10: introduction to threads](#lecture-10-introduction-to-threads)
- [Lecture 11: synchronization and thread](#lecture-11-synchronization-and-thread)
- [Lecture 12: Mutexes](#lecture-12-mutexes)
- [Lecture 13: condition variables](#lecture-13-condition-variables)
- [Lecture 14: Semaphores](#lecture-14-semaphores)
- [Lecture 15: Scheduling Overview](#lecture-15-scheduling-overview)
- [Lecture 16: MLFQ scheduling](#lecture-16-mlfq-scheduling)
- [Lecture 17: scheduling in Linux](#lecture-17-scheduling-in-linux)
- [Real-time scheduling](#real-time-scheduling)
- [Lecture 18: Interprocess communication and pipes](#lecture-18-interprocess-communication-and-pipes)
- [Lecture 21: Sockets and Networks](#lecture-21-sockets-and-networks)
- [Lecture 22: sockets and web servers](#lecture-22-sockets-and-web-servers)
- [Lecture 24: filesystems](#lecture-24-filesystems)
- [Proc file system](#proc-file-system)
- [Lecture 27: Virtual Machine Monitors](#lecture-27-virtual-machine-monitors)


---

### Lecture 2: background and review

- Layers of a modern computing system
    + the operating system **kernel**
    + system Libraries - the core services of OS are encapsulated by these libraries 
    + Application framework
        + helpful libraries for providing modularity and reuse
        + use interface and not part of the core
    + Application

- How do we interact with the kernel? 
    + Applications only see them via system calls (system calls are the API of the kernel)
    + `strace ls` yields system call for `ls` cmd

- Background Review
    - **Shell**: a command line interpreter. (bash, zsh etc.)
    - **Make**: a program that builds program for you (specify dependencies, and figures out when/how to build it)
    - **CMake**: a program that generates build dependencies (generate makefiles)
    - command `file file_name` yields file information
        + ELF executable - extensible file format in Linux
    - `ls -l` gives more info than plain ls
    - "Hello World" in Assembly 
        + step 1: move 4 into `eax` (4 is the # of the write system call)
        + `mov ebx 1` 1 means write to "standard output"
        * Write needs 3 things: where to write, address of first byte, how many bytes
        - `int 80h` (last line) invoke the system call
            + example of ABI (Application Binary Interface)
        + without success exiting call, it would fetch other memory (segmentation fault)
    - From Source code to running program
        + we begin with a source file saved in a text file
        + before it can run, must be translated into a sequence of machine-language instructions
            * "executable object files"
            - translation of Unix is done by a compiler driver like `gcc`
            - Flow
                * source program (hello.c) - *pre-processor* (cpp) -> 
                * Modified source program (text) (hello.i) - *compiler* ->
                * Assembly program (text) (hello.s) - *Assembler* -> 
                * Relocatable object programs (hello.o) -> *Linker* (ld)
                * Executable object program (binary) hello 
       - Running hello World
           + Reading the hello command from the keyboard
           + loads the executable file by DMA
                - DMA - Direct Memory Access allows the data to go from disk to main memory directly 
                    + take the loads off the CPU
            - `int 80h`
                + These instructions copy the data bytes "hello, world\n" from main memory to a register. From there they go to the display device where they are displayed on the screen

- vim
    + `:wq` = `zz`
    + `:q!` quit without saving the file
    + `i` insert mode
    + `esc` to cmd mode
    + capital O up a line, o down a line
- Textbook Chapter 3 A Dialogue on Virtualization
    - one physical CPU - make it look like many virtual CPUs to the applications running on the system and each application thinks they have a their own CPU to use
    - **Time sharing** is a basic technique used by an OS to share a resource

- assignment 1
    - [Linux Device Driver](https://lwn.net/Kernel/LDD3/)

    - `ls` `lsmod` which command locate a program file in the user's path
    - `ls -l` shows more info then `ls`
    - `ls -lh \boot` - inspect the kernel (`l` option shows the B in bytes)
        - linux has a relative small kernel size (12M) `vmlinuxz-6.5.0-42-generic`
        - kernel is a *program*, which is executable

### Lecture 3: Virtualization and System Calls
- Operating Systems
    + `file vmlinu` gives info about the file
    + a special executable program, shares a computer among multiple programs and provides a more useful set of services than the hardware alone
    + Accomplishes this by "visualizing" the hardware
        * The OS makes it appear as though every process has the hardware all to itself
    * Provides services through a *system call* interface
    * The core part of the OS is called the *OS kernel* / *kernel*
    * Layers: hardware, OS kernel, libraries (provide access to the above services), applications: (gcc (compiler), bash(shell), vi (editor) etc.)
        - `ls` is also a user space application
        - kernel module is not a *user space* program
            + eg `hello.ko` not executable file, relocatable (not run on own)
            + object code hello.o linked togerth with other things then could be executed eg
            + `insmod hellp.ko` insert the kernel module and run the initialization function
    * Key point: the os is itself a program!
        - unlike others, it should have full access to all resources. How?
- Processes
    + A process is a running instance of a program
    + It's the primary unit of **isolation/abstraction** provided by the OS
    + Serves several purposes:
        - Prevents a process X from wrecking or spying on a process Y
        - Prevents a process from wrecking the os itself
    + the abstraction that gives the illusion to a program that it has the machine to itself
        * Each process thinks it has the whole CPU
        * Each process thinks if has all memory to itself
    - `ps` - report a snapshot of the current processes, `ps -ef` - gives every process
- System calls
    + The system call API is the interface that makes OS services available to user-level programs.
    + examples:
        * create a process
        * wait for a process to terminate
        * request memory 
        * tell me what my unique process identifier is
        * read/write from/to a file
        * send data over the network
    * A process request these service from the OS by making a *system call*
    * Why does the OS provided these services? (why not user space)
        - User-level processes cannot be trusted to be non-malicious
        - eg One malicious process could erase your entire hard drive!
- Kernel mode vs user mode (Review about Rings)
    + Recall: the kernel needs full access to all hardware and CPU instructions
    + User-level processes (like Chrome or Firefox) should not be allowed full access to all hardware nor should they be able to execute all CPU instructions
    + How should we enforce this? With hardware support
        * A flag in a CPU register determines whether privileged instructions are allowed
        * On the x86 it's called the CPL (current privilege level)
            - The bottom two bits of the cs register
            - CPL = 0 means kernel mode: privileged
            - CPL = 3 means user mode: no privilege
        - The CPL enforces isolation in several ways:
            + It guards access to the CS register
            + It checks every memory read/write
            + I/O port accesses
            + Control register access (like the EFLAGS register)
    + In summary:
        * The CPL, represented by the bottom two bits of the cs register, determine whether instructions can access privileged hardware
        * Only the kernel should be allowed to operate in the privileged mode
* Invoking a system call
    - So how and when should the CPL change?
    - On the x86 it happens when a system call is invoked
        + The instruction used to invoke the system call sets CPL = 0
        + Execution jumps to a specific entry point in the kernel of the OS, which can then do further validation
        + The system call returns sets CPL=3 before returning to user code

- System Call Control FLow example
    + From User space to Kernel space
    + User application - getpid -> C-library -load arguments...(int 80) *this is an interrupt, transition to kernel* -> Kernel -> call system call ... 
    + This is an interrupt. The instruction used to invoke the system call sets CPL = 0. Execution jumps to a specific point in the kernel of the OS, The systm call return sets PL = 3 before returning to user code ...

- Extending the Kernel in C
    - Linux is open source; you can modify a kernel
        - Adding your own system call prototypes (eg multiplexing)
        - Adding your own system call definitions
        - Connecting them to the system call dispatch table
        - Adding the new definition files to the Makefile
- Tracing system calls
    + `strace ls` (lives in "bin/ls", bin is binary)
        * might see calls like `execve` - loads a new program and starts running it, `open`, `read`, `close`, `fstat` - get info about a file

- Most system calls are accessed through a wrapper function in the C library, 
    - which takes care of putting arguments in the appropriate registers and invoking a trapping instruction, like int 80h, to trap into the operating system. 
    - This is an example of exceptional control flow: when the int 80h instruction is executed, execution jumps into the kernel.

- `cat /var/log` dmesg 
- resource learning makefile - GNU Make book
- out-of-tree kernel
    + kernel.org to download the kernel sources
        * most code in the `driver` directories
- `uptime` cmd gives how long the system is there

- (can move the unnecessary iles in .gitignore file)


### Lecture 4: Process Creation
- System calls are how user-level process request services from the kernel
    + Many kinds of system calls: connect to a network host, read bytes, open a file, close a file, etc.
+ System calls are supported by special machine-code instructions
    * On x86: int80h syscall
    * these instructions cause a lot of "hidden" work to happen!
- **fork():** the system call for process creation
    - fork() allows one process, the parent, to create a new process, the child.
        + fork() creates an (almost identical) copy of the the calling process
            * Things like the PID are different
    * After a program calls fork(), there is a new process that begins executing the next line of the original program right after the call to fork()!
    * fork() is called once, but returns twice!
        + This is very different from most functions!
        + The biggest difference is the return value from fork()
    - After fork(), the parent and child are separate processes that run concurrently!
        + Their instructions might be interleaved by the kernel in any order
    + The child inherits all of the open files of the parent
    + The parent and child have *duplicate* but *separate* address space
- **Address spaces**
    + 'virtual' address space
    + Every process has its own address space that looks (roughly) like the following
    + Different area include:
        * text: the process's instructions
        * data: initialized (global) data
            - eg `int arr[1000] = {0};`
        * bss: uninitialized (global) data
            - eg `int arr[1000];` takes less space on disk, map as uninitialized data 
        * heap: dynamically allocated memory
        * stack: organizes procedure calls with *stack frames*
    * Use the `size` command to find the sizes of these segments on an executable
* After fork()
    - eg `lsmod | grep hello` they can talk to each other through file descriptors
        + calls fork() for grep
    - The new process inherits:
        + process group ID
        + Resource limits
        + working directory
        + open file descriptors
            * `find .| grep '.java' | wc -l` // find all .java files and tell me how many there are
* `exec()`: loading a new program
    - This exec() function loads a new program
        + The existing address space is blown away and loaded with the data and instructions of the new program
        + however, things like the PID and file descriptors remain the same
    + exec() causes the OS to: 
        * destroy the address space of the calling process
        * load the new program in memory, creating a new stack and heap
        * run the new program from its entry points
* Why are fork() and exec() separate?
    - the separation allows the parent to "fix-up" file descriptors after fork() but before exec()
    - allow the parent to redirect the input and output of the new process
- Process state
    + the kernel has a *processor descriptor* of type struct task_struct for each process
        * defined in `<linux/sched.h>`s
    * process descriptor contains all the info about a process
    * the kernel stores the list of processes in a circular doubly linked list called the task list
    * what does the state of a process include?
        - state: running, ready, terminated, waiting
        - priority, parent, PID, address space, pending signals, open files
- **Examples**
    + *fork is a copy of the parent program!*
    - dir /kernel/linux-5.4.1/kernel/sched 
    - book 1 linux kernel development, 2 understanding the linux kernel
    - `gcc main.c` produces `a.out`
        +  a very simple program to start a new process and run a program
        +  `man exec` `man man` exec calls the underlying system call
        +  `man 2 execve`
            * The Linux kernel has one corresponding system call named "execve", whereas all aforementioned functions are user-space wrappers around it.
            *  `execve` transforms the calling process into a new process.
            *  in linux, ` int execve(const char *filename, char *const argv[ ], char *const envp[ ]);`
            *  the file name
                -  env variable
                -  `echo $PATH`, where to link for executable program 
    - example in class
    ```
    fork();
    fork();
    printf(...)
    ```
        - two forks won't be able to print out all 4 statements, when the original `a.out` exits, the control goes back to the shell
        - `wait()` - wait for process to change state
        - when to use thread and when to use processes?
            + thread - need to talk a lot
            + processes - disjoint
            - web server, heavy image processing will use
        - parent process wait for the child processes
        ```
        char *args[] = {"ls", "-l", (char *)0}; // last one of the array is NULL
        execvp(args[0], args);
        printf("will only print of err with execvp.");
        exit(1)
        ```
- difference fork() and exec()
    + fork is forking and creating copies, with the same executable
    + runs an executable file in the context of an already existing process, replacing the previous executable. (overlay)

### Lecture 5: process termination
- Review
    + Two main system calls for process creation and program loading:
        * `fork()` creates an (almost) identical copy of the calling process
        - `exec()` loads a new program into the address space of the calling process
    - Typical sequence: fork() followed by exec()
        + fork() makes a new process
        + exec() blows away the address space of the process
- Process termination
    + when a process terminates, the kernel does not remove it from the system immediately
        * minimal information is kept (in a terminated state) until the process is *reaped* by its parent
    * A process can terminate in two ways
        ```
        1 set up args in registers for write
        2 int 80h
        3 Set up args for exit
        4 int 80h
        (x86) "Dense" take random sequence of bytes as addresses ROP Return-oriented programming
        ```
            - we will get seg fault if we do not set up exit
            - `exec` in C library loads the *dynamic linker* (ld.so), which will *loads* shared libs and later *jumps* to C-library start up
        - Normal termination
            + this happens when you do something like exit(0) or return from main (success - 0, failure other)
        + Abnormal termination: the process was terminated by a signal
            * example: a process dereferenced a null pointer, which cause the OS to send it the SIGSEGV signal, whose default action is to terminate the process
            * "normal" termination does not mean a program ran "successfully", it means that it didn't get killed y a signal
- Typical flow
    + a parent process creates a child process via fork()
    + the child process runs a new program via a exec()
    + the parent calls wait() to wait for the child program to terminate
    + the parent uses the return information from wait() to determine whether the process exited normally or abnormally
        * if it exited normally, the exit status can e obtained
        * if it exited abnormally, the signal that killed it can be obtained
+ The `wait()` system call
    * the wait() system call is used by a parent to wait for its children to have a change in state
        - eg child terminated, child was stopped by a signal, child was resumed by a signal
        - `SIGSEGV` - something bad with the memory, `SIGSTOP`  (eg: `kill -STOP pid` ) 
    - If the child was terminated, performing wait() allows the system to release all resources associated with the child
        + otherwise the child remains in the "zombie" state
            * minimal information is kept around
    - example
        - macro `WIFEXITED` checks the least significant byte (lsb) of a multi-byte integer
        - macro `WIFSIGNALED` checks maybe the second lsb
        - different macro inspect different parts of bytes
* Signals
    - *signals* are a technique used to notify a process that some condition has occurred
        + (kill process sends a signal)
        + also called *software interrupts*
            * E.g. is a process divides by zero, the signal whose name is SIGFPE (floating-point exception) is sent to the process.
    * Process can deals with signals in three ways:
        - ignore the signal
        - let the default action occur, the default - terminate
        - catch the signal by telling the kernel to call a function of ours whenever the signal occurs. do this by registering a *signal handler*
    - how? (async) eg page fault handler sends a signal, array of pending signals is updated, os will later respond 
        + os gains control and reacts the pending signals 1 timer interrupt 2 system call
            * 10ms elapses
            * timer interrupt occurs
            * os processes the timer interrupt
            * os switches to process b which is responding to pending signals
- Transferring signals
    + The transfer of a signal to a destination process in two distinct steps:
    1. sending a signal
        - the kernel delivers a signal to a destination process by updating some state in the context of the destination process. The signal is delivered for one of two reasons:
            + the kernel detected a system event (such as divide y zero)
            + a process invoked the *kill* function to send a signal to the destination process
    2. receiving a signal
        - The destination process *receives* a signal when it is forced by the kernel to react in some way to the delivery of the signal
        - the *reaction* is one of the three choices described above(ignore, handle, or let default action occur)
    - for example trying to read from 1st virtual address space
        - page handler (virtual memory divides into chunk/pages) says we don't have access and will send back a signal
- Core dumps
    + default action for signals like SIGABRT or SIGSEGV, is to terminate the process and generate a *code dump*
    + A *core dump* file contains an image of the virtual memory of the process, which can be loaded into a debugger
- example
    + `gcc segfault.c`
    + `ulimit -c unlimited` would enable core file, ulimit: user limit, -c: core file, unlimited: generate core file as as we want
    - `date` gives time
    - `gdb -q -c core ./a.out` loads the core file into debugger, -q: quiet(don't show starter information), ./a.out: the executable program
        + we want line number `bt` back trace
        + `set disassembly-flavor intel`
        + `disass f` disassemble f
        + `quit` quit the gdb
    + `gcc -g segfault.c` would generates with debug info, includes line number information
        * `ls -rlt` r is reverse order while t is ordered by time
    * in cmake `set(DEBUG_INFO..)`
    * wait vs waitpid:
        - wait is sync (block the caller until a child process terminates) while waitpid have options that prevents it from blocking
        - waitpid can specify a pid, wait would wait on all children

### Lecture 6: Memory Management Basis

- `printenv` prints environment vars
- `echo $?` gives the return value of last shell command
- sleep 4, `ctrl + z` sends sigstp signal, `fg` brings it forward and continue
    + `bg` 1 send it to background

- Background: physical memory and addressing
    + physical memory is the RAM installed in a computer
        * example: there might be 4GB or 8GB or DRAM installed
    * Physical addressing: used in simple systems like embedded micro-controllers in devices like cars, elevators, and digital picture frames
        - also called single memory or flat address space
* why don't we use a single physical address space?
    - question: why don't modern OS'es have a single shared address space?
    - many reasons: 
        + safety: if all process share the address space, one process could crash another process (or crash the kernel)
        + management: process would need to know where they reside in physical RAM, they'd need to know or detect how much physical RAM there is
    + virtual memory solves these issues
+ Systems using virtual addressing
    * used in all modern laptops, phones, servers, desktops
    * one of the great ideas in computer science
    * CPU Chip: CPU -- Virtual Address (vA) --> MMU -- Physical address (PA) --> Main memory -- Data word --> CPU
        * MMU - a combination of software and hardware
    * ASLR - *address space layout randomization*
* virtual memory
    - virtual memory is a system that uses an address mapping
        + maps virtual address space to the physical address space
        + eg segfault, debug mode gives virtual address
    + every process has its own virtual address space
        + These are the address spaces we've looked so far
    + when a process references an address, it is using a *virtual* address
    + virtual addresses are translated into physical addresses by a comination of software + hardware
        * the software is the OS kernel
        * the hardware is called the MMU
+ Virtual memory advantages
    * each process can have a different(but similar) memory mapping
        - similar in that they may have the same general layout: text, data, bss, heap, stack, etc
    - memory can be moved and swapped
        + OS can do this without a user-space process knowing or caring
    * physical RAM can e mapped into multiple processes at once
        - Facilitates reuse, reduces overhead
        - for instance, the C-library: the read-only portions can be share between may processes.
    - Memory regions can have access permissions
        + read, write, execute
        + in 4kb page, (to 2 MB to 1GB, 2^12 to 2^21, 2^30, goes up by 9 bits) 
            - W exclusion X, either writable or executable (RX, readable and executable)
            * page size (most architecture has page size as 4KB)
            - cannot rewrite the code that we want to execute for safety reason
- Common misconceptions about memory
    + if you take the address of a variable in a program, you get a physical address
        * not true: you get a virtual address; the OS translates this into a physical address
    + memory leaks mean memory is lost when the process terminates
        * not true: the OS reclaims a process's resources, including the memory it used, upon termination (so the memory is never lost)
    + a computer with 2GB of physical RAM can't simultaneously run process that consume more than 2GB total
        * not true: the OS can "swap" the contents of memory to disk when it runs low
    + when you launch a program (like a video game) whose size is 1GB, it immediately consumes 1GB of physical RAM
        * Not true: the OS brings in data it needs *on-demand*
    * Virtually contiguous means physically contiguous
        - not necessarily true: two adjacent virtual pages may not be mapped to adjacent physical pages
        - however, bytes in the same page will be bytes adjacent in mapped physical space
        - page table matches virtual address to physical address
        - 4k size is a balance (large - less flexibility especially when we have to move the page)
- exec family
    + execv - we have to specify path such as `/bin/ls` as the first argument
    + execvp - we could leave that as `ls`, it will find it form PATH for us
    + execlp - list version of above

- stack (grows downward), heap (grow upward), bss, data, text (from high to low)
    + bss eg `int arr[25]` data `int arr[3] = {0}` (compile time world)
    + extra - over Provisioning
    + segment.h file declaration extern, main define these pointers, segment.c initialize the pointers
    + %p tells what is the address, %s, interpret it as string value
    + (should work under mac!)
    + data section
    ```
    data_ptr_1 = "data 1";
    // no need to free data section, only free things living in the heap'
    // malloc
    ```
+ Heap
    * there's a brk in the heap (part of which is occupied)
    * ask for 4 mega bytes, malloc would ask for adjusting `program brk`, then program break increases and free for malloc to use
    * hence heap is dynamic
    * os generally won't make break decreases
    - why there is a brk? simplifies the os, doesn't have to set up the translation table for the other parts
    - syscalls `brk()` `sbrk()`
- for testing purpose disable ASLR
    + `echo 0/2 | sudo tree / ...proc/...`
+ where are symbols etext defined?
    * linter script where text initialize data section's address

### Lecture 7: Virtual Memory
- Review
    + simple systems (digital frames, elevators) might use physical addressing
        * the address used by a program correspond to real, physical addresses
        * also called single memory, or flat address space
    * all modern OS are virtual addressing
        - the address used by a program are translated to physical addresses
        - the translation is done mostly in hardware; the OS plays a big role in setting up data structures used by the hardware
* Definition: **Context Switch**
    - when OS decides that the current process should stop running and a different process should be run instead
    - the os: 
        + saves the state of the currently running process
        + restores the context of the process that will begin running
    + the state of a process includes:
        * register values
        * memory mappings (the translations from virtual to physical addresses)
        * the parent process
        * list of open files
        * its priority, PID, etc
* Why is virtual memory good?
    - helps use main memory efficiently
    - simplifies memory management
        + each process gets the "same" linear space layout
    + easy to share code and data between processes
    + isolates address spaces
        * one process can't access the address space of another process
        * user space can't access the kernel's address space
        - eg in a bad system `insmod` small driver might wreck the whole system
- address space layout of a process
    + typical address space layout of a user-level process is shown on the right
        * stack (grows downwards),  unallocated memory, heap (grows upward)
        * uninitialized data (bss), initialized data, text (program code)
        * kernel part is outdated
            - mapped into process virtual memory, but not accessible to program
    + these address are virtual
        * job of the MMU + OS to translate these to physical addresses
- Paging introduction
    + a process's virtual memory is divided into equal sized units
        * these are called *pages*
    * physical memory is also divided into units of this size
        - these are called *page frames* or just *frames*
    - the hardware + OS maps from virtual memory pages to physical page frames
        + when a process makes a memory access it uses a virtual address
        + the hardware + OS translates this into physical address
- Paging
    + paging is good for many reasons:
        * a virtual address can be mapped anywhere in physical memory that the OS wants
        - pages can be marked with different permissions, such as read-only. if a process tries to access a page in a way that doesn't match the page's permissions, the OS can detect this.
        - it helps to minimize the amount of wasted space
        - physical pages can be *swapped out to disk* when physical memory is low. When it is swapped back in, it can go somewhere else in physical memory -- the OS only needs to adjust the virtual to physical mapping
        - 4GB address space in 32 bits
        - ELF exposes the sections information
- **demand paging**
    + example
        * malloc(100000) and looked at the process using the *top* command
        * only after we wrote to that memory was physical memory allocated
        * sizeof(char) sill always be 1
        - different pages can have different permissions but map to same physical page frames
        - observability tool `pmap`
        - `ps -ef | grep allocation` gives us the PID
        - then `pmap -x PID` 
            + `[anon]` anonymous mapping things like heap
            + `libc-3.31.so` such as malloc in memory
        - RSS - Resident Set Byte (in Mega byte), how much is being back by physical memory (less care about)
        - before we use (after allocation) the 1000000 we go up by 1000KB (program break adjusted)
        - after using it then the RSS also increases by 1000KB
        - illustrates demand paging
    + pages only brought into memory when a reference is made to a location on that page
        * in other words, the page isn't *resident* until i's accessed (read from or written to)
        * ..
    + `cd proc` `cd ...` `cat maps`

- why the entire heap wasn't automatically mapped into a process's address space when it begins executing. 
    - the heap (which is the part of your address space usually managed by library functions, like malloc) can also be used load libraries dynamically. 
        - e.g. if your process dynamically loaded a shared library after running for a while, that shared library would be mapped into the heap area. 
    - Different parts of the shared library need to be mapped in different ways (for instance, the executable code would be marked as executable, but not marked as writable), and those permissions can be specified when the library is loaded into the space. 
        - e.g. If you mapped the "whole heap" into your address space at process startup, you'd have to go back and change the permissions of individual pages throughout the execution of the process (using something like the mprotect() system call).
        - `mprotect` - set protection on a region of memory

### Lecture 8: Advanced memory topics
- Basic virtual address translation
    + The os + hardware divides a virtual address into two parts:
        * an index into a page table
        * an offset into the address provided by the tale
    * The page tale maps a virtual page to a physical page
    * the offset tells which byte on the physical page to access
* Simplified Address translation
    - page table map from (virtual page number -> physical page number)
        + is it valid?
        - valid bit = 0; page not in memory (page fault)
    -  Virtual page offset = physical page offset (12 bits bc page size is 4KB = 2^12 bytes)
    -  The PTBR (Page table base register) points to the current page table.
-  address translation: page hit (valid bit = 1)
    +  processor sends virtual address to MMU
    +  MMU fetches PTE from page table in memory
    +  MMU sends physical address to cache/memory
    +  cache/memory sends data word to processor
- address translation: page fault
    + MMU triggers page fault exception
    + handler identifier victim (and, if dirty, pages it out to disk)
    + handler pages in new pages and updates PTE in memory
    + Handler returns to original process, restarting the faulting instruction
- `mmap` system call map or unmap files, anonymously(heap)
- What about cache?
    - TLB
        + page table entries (PTEs) are cached in L1 like any other memory word
        + PTEs may be evicted by other data references
        + PTE hit still requires a small L1 delay
        + Solution - TLB
            * small set-associative hardware cache in MMU
            * maps virtual page numbers to physical page frames
            * contains complete page table entries for small number of pages
    * Some architectures have multiple TLBs: Instruction TLB, Data TLB
    * can even have multiple levels of TLBs (smaller faster)

### Lecture 8: advanced memory topics (continue)
- Thrashing and page replacement
    + when physical memory gets low, physical frames are swapped out
        * they are swapped to a swap file; often a separate petition on your hard drive
    * The OS has to select a "victim" frame to swap out
        - different algorithms: FIFO, LRU, Clock replacement
        - can't predict witch will be the best choice
    - Thrashing is when you have many page faults and the OS spends most of the time loading in pages
        + happens when you have a lot of processes running simultaneously and they use a lot of memory
+ TLB in more detail
    * VPN (Virtual Page Number) + VPO (offset)
    * TLB tag + TLB index (t bits)
        - index finds bucket
        - T = 2^t sets 
        - TLB tag matches tag of line within set
- THE TLB and context switches
    + when a context switch happens, the TLB entries are often invalid
        * diff for every process
    * On the x86, the kernel can flush the entire TLB or individual entries
        - which is better depends; the kernel can't always know ahead of time
    - eg you witch back to the original process quickly
        + Flushing only a few entries is better
+ Page tables revisited
    * A single level page table usually takes up too many space
    * eg
        - 4kb page size, 32-bit address space, 4 byte PTE
            + need 2^20 PTEs (32-12); each is 4 byte
            + 2^22 = 4MBB - not too bad, but still quite a bit
        + 4kb bpage size, 48-bit address space, 8-byte PTE
            * need 2^36 PTEs; each is 8 bytes
            * 2^39 bytes = 512 GB - way too big!
    * Common solution: multiple level of page tables
* Multi-level page tales
    - a four level page table is usually used on modern systems
    - the number of bits used for each level depends
    - X86 linux uses 48-bit virtual address and 4kb pages (normally)
        + 12 bits for offset, 9 bits for each lookup level (36/9)
        - 2^9 = 512 entries at each level
- Intel Core i7 Memory System
    + L3 unified cache is share among 4 cores
        * L1/L2 cache is not
    * CPU use 48 bits (enough) - 52 bit physical address space
        - The ISA supports full 64-bit instructions, but (at least for now) 48-bits is large enough to address the practical amount of memory a system supports. In other words, the CPU uses 48-bits.
* End-to-end core i7 address translation
    - 48 -> 54 bits physical address
    - 54 bits = Index = tag
        - virtually indexed and physically tag
        - b.c. the tag remains the same!
        + hence the tag and index can go parallel 
+ page - spatial locality - sequential memory - cache make it easier


- Typing it together
    + how can understanding memory management help you?
        * understand how to write efficient programs
        - the deeper you understand something, the easier it becomes to understand trade-offs
    - example: what are the trade-offs between an array and a linked list?
        + arrays: elements are stored in virtually contiguous pages
        + linked list: the pointers might point all over the place in virtual memory
            * which is better if you need to iterate over every element and you have a lot?
                - array might be a better choice (iterate and fast access)
                - some case we use both
                    + have a tree over the linked list
    * example: how large should a data structure be?
        - in the linux kernel, there is a `struct page` data structure to describe every page of physical memory
            + this data structure is *intentionally* 64 bytes because that makes it "cache friendly": it fits entirely in one cache line
                * any bigger, and a struct page requires two cache lines
- Locality
    + well-written programs exploit locality when possible
        * *spatial locality*: when memory is referenced, reference nearly memory in near future
        * Temporal locality: when variable is used, use it again in near future
        * eg 2D array first j and then j is good for spatial locality
* Core i7 level 4 page tale entries
- trick for speeding up L1 access
    + bits that determine CI (cache index) identical in virtual and physical address
    + can index into cache while address translation taking place
    + generally we hit in TLB, so PPN bits (CT (cache tag) bits) available next
    + "virtually indexed, physically tagged"
    + cache carefully sized to make this possible
+ Linux representation of memory
    * the linux kernel organizes VM as a collection of "areas"
        - vm_start and vm_end: point to the begging and end of the area
        - vm_prot: describes read/write permissions
        - vm_flags: tells whether pages in this area are private
        - vm_next: points to next area vm_area_struct in list
- Implementation of malloc
    + recall: malloc and free allocate and free memory on the heap
        * internally they use `brk()` or `sbrk()` system calls to ask the OS to adjust the size of the process's heap
        * brk() and sbrk() gros or shrink the heap, but malloc is responsible for managing free memory
    * when you ask malloc() to give you memory, it allocates more than you ask
        - why so free() will know how much was allocated
        - there's an integer right before the address you get
    - when passing an incorrect address to free, we may have seg fault or give back wrong things
- code cannot both W X xor writable and executable (prevent code injection)


### Lecture 9: introduction to the Linux kernel
- the Linux os is considered the most successful open source project
    + supports many architecture
    + hundreds of contributors
    + it's the OS used y Netflix to stream TV to everyone!
+ learn
    * build a kernel
    * adding system call, building kernels, using netfilter
* History
* Monolithic vs micro-kernels
    - Linux is a *monolithic* kernel
        + Translation: it’s one big process running in one big address space
        + Good things: runs fast, simplifies implementation
        + Bad things: if one bad thing happens, the whole OS can crash
        + Including drivers: if a driver does something bad, the whole OS can crash
    - This is a contrast to a *microkernel*
        - Functionality broken down into separate processes
        - These processes communicate with each other
        - Good things: perhaps a “cleaner” design, isolation between subsystems
        - Bad things: substantial overhead with the communication between processes
- Configuring the kernel 
    + need to configure what you want in your kernel before building it
    + options are either booleans, tristates, strings, or integers
* Building the kernel
    + `make -j 4`
    + building modules
        * make modules_install
        * modules are installed to /lib/modules
    * installing
        - make install
        - updates your grub2 file for you, copies kernel to /boot
    - Note: building a new kernel does not delete your old files
- overview of the source directories
    + where's the majority of the code? driver
+ Kernel subsystems and their interaction
    * Diagram on the right shows kernel subsystems, for example:
        - User level processes interact with the kernel through system calls and virtual memory
        - Signals are one way to “talk” to userspace from the kernel
- Other notes about the kernel
    + The kernel isn’t a user-space application
        - Thus no libc! For example, no printf or malloc
    - There are often kernel-specific equivalents
        - kprint or kmalloc
    - Linux is heavily tied to gcc
        - Many gcc specific extensions needed to build kernel
        - There are efforts to make it build with clang/llvm
    - Fixed size stacks
        - Architecture specific, but in the kernel, your stack is usually 8kB (32-bit) or 16kB (64-bit)
    - No memory protection like userspace
        - Example: userspace processes get a SIGSEGV signal when dereferencing a null pointer
    - Portability is important 
        - [link](https://en.wikipedia.org/wiki/List_of_Linux-supported_computer_architectures) 
- Modules
    + Linux is *monolithic*
        * the whole kernel runs in a single address space
    - But code can be dynamically inserted and removed at runtime with modules
        - Module: a loadable kernel object
    - Allows the base kernel image to be relatively small
        - Insert modules for extra functionality on-demand
        - In-tree: modules (drivers) that come with the linux kernel source
        - Out-of-tree: modules that don’t ship with the linux kernel source
        - Maintainer has to make sure it works with each kernel version
    - `lsmod` and `modinfo` get info about loaded modules
        + `sudo modinfo vboxvideo` `uname -r`
+ A short look: processes
    * each process is represented by a `struct task struct` defined in <linux/sched.h>
        - kernel keeps a doubly linked list of these called the task list
        - relatively large: ~1.7KB


### Lecture 10: introduction to threads
- concurrency and synchronization
    + concurrency: doing things at the same time
    + synchronization: keep things consistent
    + look at the use first, then the implementation
    + eg `fork()` based on `clone2()` create a new vm area
+ Threads
    * threads let an application perform multiple tasks concurrently
    * common use of threads:
        - web servers: a web server "listens" for requests. When a request arrives, a thread to handle that request is created so the server can quickly go back to listening for more requests
        - GUIs: progress bars that indicate amount of progress run in separate threads so that they can be updated while the actual "work" is being done simultaneously
        - anytime you want to parallelize a task
- Processes?
    - why not use processes to parallelize a task?
        + example, call `fork()` to create a new process, then call a different function
    * this approach works
        - but recall after `fork()`, the parent and child have totally different address spaces
    - parent and child no longer share data!
- multiple threads vs processes
    + threads: if sub-tasks need to access shared data; otherwise processes
+ threads
    * Linux implements threads as processes that happen to *share* certain resources with other processes
    * what are the "shared" resources?
        - everything except their stacks
            + recall: stacks are used to organize procedure calls
    - in other words:
        + threads all have their own flow of control. Two threads can execute different functions or the same function
        + threads share the same text data bss and heap
        + each thread has its own stack (in heap)
+ easy?
    * creating multi-thread program is straightforward
    * ensure that they don't contain the following is very difficult
        - race conditions (result depends on the order of execution)
        - deadlocks (wait for sth never ends)
        - both stem from access to shared data
- Pthreads
    + Facilities used to create/manage threads are usually in a separate library
        - most popular: the pthreads library
        - the "p" in pthreads comes from the fact that the library's interface is defined by a POSIX standard
    - provides data structures and operations needed to:
        + create threads
        - Coordinate their execution (e.g., synchronize)
        - Terminate threads that are no longer needed
- `gcc simple.c -l pthread` link to pthread `gcc simple.c -pthread`

- assignment 5
```
void * func(void *arg) {
    void * res; 
    pthread_join(thread, &res); // pass the address of the pointer which is a pointer to a pointer

    // eg
    int *p = malloc(sizeof(int));
    pthread_create(... p); // can't free p here, free it in join_and_get_result
}
```

### Lecture 11: synchronization and thread
- Review
    + Threads enable concurrency in an application
        * example: update a progress bar while simultaneously doing background work
        * 'stacks' for each stack
    + processes also enable concurrency
    + Linux implements threads as process that share certain resources
        * threads of the same process share almost everything except stacks
- POSIX thread API
    + Linux exposes treads via the POSIX thread, or pthread, API
    + `pthread_create()` - create a thread
    - `pthread_join()` - join ("wait for") a terminated thread
    - `pthread_exit()` - terminate the calling thread (does not cause the whole program to terminate)
        + e.g. in main(), last line `thread_exit(NULL);`
- Items not shared between threads
    + threads do not share their stacks
    * they also do not share:
        - thread ID
        - signal mask: set of signals whose delivery is currently blocked
        - thread-specific data: allows function to have separate data for each thread
        - ...
- Race conditions
    + Race condition: a situation where the result produced by multiple threads (or processes) operating on shared resources depends in an unexpected way on the relative order in which the processes gain access to the CPU(s).
    + What causes race conditions?
        - In a nutshell: non-atomic operations
- example race conditions
    - the race condition is caused by the fact that sum++ is implemented as three non-atomic instructions (divided into multiple steps eg three following steps for sum++)
        + mov eax, DWORD PTR [rip+0x200663] #fetch the current value of sum
        + add eax, 01 #increment it
        + mov DWORD PTR [rip+0x20065a], eax #write it back to the sum
    - key point: the OS can switch to a different process after any of these instructions
    ```
    +1
      Get vlaue sum 0
      increment 1
    +2
      Get value sum 0
      increment 1 
      store val =1
    +1
      store val = 1
    ...
    ```
    - larger sum, more often it goes wrong 
- Locks
    - The typical way to prevent race conditions is to use locks. The idea is:
        - Obtain lock
        - Perform critical section
        - Release lock
    - Question: how do we build locks?
        - We need at least two operations:
            - Obtain lock
            - Release lock
    - How do we implement these operations?
- Implementing locks
    + Simple implementation: use an integer variable to represent the lock
        - 0: lock is free
        - 1: lock is taken
    - Releasing the lock is simple
        - Set the value to 0! Guaranteed to be atomic (by the architecture)
    - But what about obtaining the lock?
        - Check if the current value is 0
        - If current value is 0, then set it to 1
        - But that’s at least two non-atomic operations!
- xchg does two things atomatically: (Atomically swap the value of a register and memory)
    + set the value of *lock to 1
    + return the previous value of *lock
+ spin-lock
    * A spin-lock is a lock that just keeps trying to obtain the lock until it succeeds
    ```c
    void init(int *lock) {
        // 0 = free, 1 = taken
        *lock = 0;
    }
    void lock(int *lock) {
        int curr = xchg(lock, 1); // atomic, read from documentation
        while(curr == 1) {
            curr = xchg(lock, 1);
        }
        // while(xchg(lock, 1) == 1) ;
    }
    void unlock(int *lock) {
        *lock = 0;
    }
    ```
- Critical Sections
    + critical section: piece of code that accesses a shared resources and whose execution should be atomic
    + Must be careful that multiple threads don’t perform simultaneous updates
- Reentrancy
    + a function is *reentrant* if it can safely be simultaneously executed by multiple threads of execution in the same process
    + what does 'safely' mean?
        * the function achieves its expected result, regardless of the state of execution of any other thread of execution.
        * A function that uses only local variables: guaranteed to be reentrant
        * Functions that update global variables: may be nonreentrant
        * Functions that use static data: may be nonreentrant


### Lecture 12: Mutexes 
- Thread scheduling in more detail
    + The functions of the scheduler
        * *time sharing*: every runnable thread gets a chance to run; highest priority goes first
        * *preemption*: the scheduler can preempt the currently running thread and run another
        * *load balancing*: the scheduler can move runnable threads to other CPUs
        - oths
            + might wait on mutex, I/O, thread sleep(2000) on the wait queue
            + auto load balancing move thread into other cpu (increase cache miss)
* Time sharing is driven by the timer interrupt
    - calls scheduler_tick(), which invokes the scheduler to see if another thread should run
- Mutexes
    - Spin-locks are usually not the right solution for locking
        - They waste CPU cycles when the lock is contended
    - Better solution is to **sleep** if the lock is not available
        - Have the OS wake you up when the lock is available
        - (like cpu will put the threads to waiting queue)
    - Linux provides mutexes for just this purpose
        - Part of the POSIX library
- Pthread mutexes
    - Data type: `pthread_mutex_t`
    - Operations
        + `int pthread_mutex_lock(pthread_mutex_t *mutex);`
        + `int pthread_mutex_unlock(pthread_mutex_t *mutex);`
        ```c
        static pthread_mutex_t mtx = PTHREAD_MUTEX_INITIALIZER;

        void *thread_func(void *arg)
        {
          pthread_mutex_lock(&mtx); // get the lock
          // access shared data
          pthread_mutex_unlock(&mtx); // release the lock
        }

        ```
- Linux implementation of mutexes
    + locking:
        * bit 31: indicates if lock is taken (1 is taken and 0 is free)
        * remaining bits: number of waiters
        - Line 7: don’t return from here until we get the lock
        - Lines 8-10: check lock is free and decrement # of waiters if so
            - Decrement because we incremented
        - Line 15: check if lock is taken
        - Line 17: futex system call
            - Put calling process on queue
    - Unlocking 
        - Lines 24-25: if *mutex == 0 after adding 0x80000000, then nobody was waiting
        - Line 29: invoke the futex system call
            - Argument of FUTEX_WAKE
    - futex() is a multiplexed system call
        - Different arguments change the behavior
        - FUTEX_WAIT: go to sleep until mutex is available
        - FUTEX_WAKE: wake up someone waiting on this mutex
    - Described in paper “Futexes are Tricky” by Ulrich Drepper

### Lecture 13: condition variables
- Review
    + Mutexes provide mutual exclusion
        + Support from the OS: go to sleep if the lock is contended, wake up when available
+ Producer-consumer problem
    * canonical example for condition variables: the **producer-consumer problem**
        - producer threads produce elements
        - consumer threads consume the elements produced by the producer threads
    - A lock alone isn't a good solution:
        + it only ensures mutual exclusion
        + consider the case where a consumer wants to run but there are no elements available
            * contain lock
            * check for elements
            * release lock
            * sleep
        * condition variables to the rescue!
    - Condition variables
        + A condition variable allows one thread to inform other threads about changes in the state of a shared variable (or other shared resource) and allows the other threads to wait (block) for such notification.
    ```c
    static pthread_mutex_t mtx = PTHREAD_MUTEX_INITIALIZER;
    pthread_cond_t cond_full = PTHREAD_COND_INITIALIZER;
    pthread_cond_t cond_empty = PTHREAD_COND_INITIALIZER;

    void* producer_func(void * arg) 
    {
        pthread_mutex_lock(&mtx);
        while (num_avail >= MAX_SIZE) 
            // release the lock and put the calling thread to sleep (atomically); when the thread wakes up (after some other thread has signaled it), it must re-acquire the lock before returning to the caller
            pthread_cond_wait(&cond_empty, &mtx);
        num_avail++;
        pthread_mutex_unlock(&mtx);
        pthread_cond_signal(&cond_full);
    }

    void *consumer_func(void *arg)
    {
        pthread_mutex_lock(&mtx);
        while (num_avail <= 0)
            pthread_cond_wait(&cond_full, &mtx);
        // consumer data and process
        num_avail--;
        pthread_mutex_unlock(&mtx);
        pthread_cond_signal(&cond_empty);
    }
    ```
    - The mutex associated with a condition variable is for mutual exclusion
    - The condition variable is for signaling
    - Important: always check the condition in a while loop! From the previous slide
    - the wait function atomically: 1 unlocks the mutex 2 waits on the condition variable
        + it will requires the mutex after wait function (or when it wakes up)
+ condition variable operations
    * Basic operations: signaling and waiting
        - Signaling can be “broadcast” (wake up everyone) or one “signal” (wake up one waiter)
    - Functions return 0 on success, non-zero on error

### Lecture 14: Semaphores
- Review
    + Mutexes provide mutual exclusion
        + Support from the OS: go to sleep if the lock is contended, wake up when available
    + Condition variables provide the ability to signal when conditions are met
        - Canonical example: producer/consumer
    - Today: semaphores and synchronization implementation

- Semaphores
    + Semaphores are another synchronization primitive that can be used for both signaling and mutual exclusion
        + Originally proposed by Dijkstra
    + In real life: semaphore is a system of signals for communicating visually
        - Usually with flags or lights
    - Conceptually: a data type with integer value and two operations:
        - `P()`: decrement value of integer; block if it would go below 0
        - `V()`: increment the value of the integer; wake a waiting thread (if any)
            + P for “prolaag”, a contraction of “probeer” (Dutch for “try”) and “verlaag” (“decrease”);
            + V for  the Dutch word “verhoog” which means “increase”

- Semaphores
    + Like an integer with three differences:
        - When you create it, you can initialize its value to any integer
            - Afterwards you can only increase by one and decrease by one
        - When a thread decrements the semaphore, if the result is negative, the thread is blocked and cannot continue until another thread increments the semaphore
        - When a thread increments the semaphore, if other threads are waiting, one waiting thread gets unblocked
    - This implies:
        - There’s no way to know if a thread will block before it decrements a semaphore
        - After a thread increments a semaphore, you don’t know whether it or waiting thread that woke up will continue running
        - When you increment a semaphore, you don’t know where there are zero or one unblocked threads

- Semaphores
    + Values:
        - Positive: number of threads that can decrement without blocking
        - Negative: number of threads that are blocked and waiting
        - Zero: No thread waiting, but trying to decrement will block
    - A mutex can be implemented as a binary semaphore
        - Initialize the semaphore to 1
    - “Counting” problems can initialize the semaphore to an arbitrary value
    - Linux implementation: can be used across processes
        - Mutexes and condition variables only usable between threads of same process
    - Data type: `sem_t` and associated functions
        - Similar to POSIX mutexes and condition variables

- POSIX “named” semaphore operations
    - Named semaphores can be used across processes (just open using the same name that begins with a “/”)
        - Contrast to an unnamed semaphore: it just resides at an agreed upon location in memory
        - `sem_t *sem_open(const char *, int, …, int value);` // create a semaphore or open an existing semaphore
        - `int sem_close(sem_t *);` // close the semaphore - do not delete it
        - int sem_unlink(const char *);  // delete the named semaphore
        -` int sem_post(sem_t *);` // increment value by 1
        - `int sem_getvalue(sem_t *restrict, int *restrict);` // get value of semaphore
        - `int sem_wait(sem_t *);` // decrement value by 1; block if current value == 0
        - `int sem_timedwait(sem_t *restrict, const struct timespec *restrict);` // timed version of wait
        - `int sem_trywait(sem_t *);` // non-blocking version of wait

- Rendezvous
    - Common synchronization pattern
    - Two threads (A and B)
        - A has to wait for B
        - B has to wait for A
    - In other words:
        - a1 happens before b2
        - b1 happens before a2

- Rendezvous solution
    - Common synchronization pattern
    - Two threads (A and B)
        - A has to wait for B
        - B has to wait for A
    - In other words:
        - a1 happens before b2
        - b1 happens before a2
    - Solution on the right
        - Tell the other thread you’ve arrived
        - Then wait on the other thread

- Barriers
    - Another common synchronization pattern
        - “Generalizes” the Rendezvous to arbitrary number of threads
    - In other words, no thread reaches the critical point until everyone has executed the rendezvous
    - We’ll look at the solution next time
        - Also in “The Little Book of Semaphores” (https://greenteapress.com/wp/semaphores/)

- Synchronization implementation
    + How are all of these synchronization primitives implemented?
    + If no sleeping involved: with atomic machine instructions
        + Example: spin-locks use the atomic compare and exchange instruction
    + If sleeping involved: with the futex system call
    + With pthreads, the mutex, condition variable, and semaphore operations are all defined in the C library
        + “Fast” path (i.e., the lock is free): no need to transition to kernel-space (i.e., no system call)
        + “Slow” path (i.e., lock is contended): the C library invokes the futex system call
    - How is the futex system call implemented?
        - Quickly check again if the resource is free
        - If not, the kernel puts the calling process on a queue associated with the lock/semaphore/condition variable
        - The kernel then switches to another process
            - Putting the process on the queue and switching to another needs to happen atomically so a wake-up isn’t missed; 
                - Corner cases often complicate the implementation
    - When another thread/process releases the lock:
        - Check if there’s anyone on the queue associated with that lock
        - If so, wake them up



### Lecture 15: Scheduling Overview
- [the infinite space between words](https://blog.codinghorror.com/the-infinite-space-between-words/)
- [The Little Book of Semaphores](http://greenteapress.com/semaphores/LittleBookOfSemaphores.pdf0)
    + chapter 3.3 (Rendezvous)
    + Semaphores
        * integer value variable with 2 operations:
            - P (**decrement** the value and block if less than 0)
            - V (**increment** value; release waiter if needed)
* Overview
    - The scheduler is the part of the OS kernel responsible for deciding which process runs and for how long
    - There are many types of scheduling algorithms
        - Which one is “best” depends on many things, such as the expected workload and metrics
    - These slides discuss scheduling basics
        - Subsequent slides discuss particular types of schedulers, such as those in Linux
- Scheduling
    + A scheduling algorithm is responsible for deciding which process to run
    + Some simple (impractical) scheduling algorithms (chapter 7 in your book):
        - *First In First Out* (FIFO): run each process to completion in the order they arrive
            - Problems: what if a process runs “indefinitely”? What if a long running process happens to arrive first?
        - *Shortest Job First* (SJF): select the process that will run for the shortest time
            - Problem: we don’t know how long a process will run ahead of time! 
        - *Shortest Time-to-Completion First* (STCF): preempt the currently running process if another process has a shorter time to completion
            - Problem: long running processes will be *starved* if short running processes keep arriving

* Real-time Systems
    - A *real-time* system is a time-bound system which has well-defined, fixed time constraints. i.e., response should be guaranteed within a specified timing constraint or system should meet the specified deadline.
    - example Vx Works

- Scheduling metrics
    + metrics can be used to measure how good a scheduler is
    + Examples:
        * *Turnaround time*: Time of completion - Time of arrival
            - a scheduler with poor turnaround time might be bad for CPU bound processes
        * *response time*: time of *first* run - time of arrival
            - a scheduler with a poor response time will feel "laggy" to interactive users
            - eg timer interrupt (check if we should witch processes) 1000Hz if better for interaction, 100Hz better for throughput
        - *throughput*: number of processes completed per unit time
- More realistic scheduling: **round robin**
    + Round-robin scheduling: instead of running a process to completion, run a process for a *time slice*( or time *quantum*), *preempt* the process, and context switch to the next process in the run queue; do this repeatedly
        * in other words: everybody gets a turn eventually!
        * RR is used in many real-world schedulers
    * What triggers the preemption of the currently running process?
        - Simple case: the periodic timer interrupt (whose rate is configurable)
            + Timer interrupt occurs
            + OS handles this interrupt by executing the timer interrupt handler
            + timer handler checks how long current process has been running
            + If current process has run longer than its allotted time slice, the scheduler is invoked
            + The scheduler saves the context of the current process and selects a new process to run
            + (eg scheduler use red-black tree looking up next process to run - fast) 
+ What about I/O?
    * all "interesting" programs do some I/O
    * what should the scheduler do when a process is waiting for I/O?
        - run another process instead! It's a bad idea to have a process spin while waiting for I/O
    - overlap allows better use of resources
        + DMA (don't need CPU to access memory and transfer memory to hard drive/disk)
- CPU bound vs I/O bound
    - Processes are often CPU bound or I/O bound
    - CPU bound: when they run, they use the CPU continuously
        - Examples: MATLAB simulations, training a machine learning model, video encoding
    - I/O bound: when they run, they spend most of their time waiting for I/O
        - Examples: text editors, powerpoint
    - Processes can switch between being I/O bound and CPU bound (and vice versa)
        - Example: when you do a “spell check” on a document, the editor goes from being I/O bound to CPU bound
    - Question: how can a scheduler let CPU bound processes use the CPU but also quickly switch to an I/O bound process when input or output arrives?
        - Something the Linux *CFS* (covered later) aims to address


### Lecture 16: MLFQ scheduling
- Multi-level Feedback Queues
    + MLFQ is a scheduling algorithm that tries to do two things
        - Optimize turnaround time
            - This can be done by running shorter processes first, but we don’t know how  long a process will run!
        - Minimize response time
            - This makes the system feel responsive to interactive users
            - Round-robin is good for response time, but is bad for turnaround time
    * MLFQ is cool because it address these two goals without knowing anything about the running time
* MLFQ basic rules
    - MLFQ has:
        + a number of distinct queues, each at a different priority level
        + at any given time, a process that is ready to run is on a single queue
        + processes with the higher priority are run first, processes with equal priority are run RR
    + Rule 1: priority(A) > priority(B) => A runs
    + RUle 2: priority(A) == priority(B), A and B run in RR
+ Changing priority
    * Rule 3: When process enters system, it is placed at highest priority (top queue)
    * Rule 4a: if a process uses up an entire time slice while running, its priority is reduced (it moves down on queue)
    * RU;e 4b: if a process gives up the CPU before its time slice is up, it stays stay the same priority level
* Example of moving through queues
    - over time a long running will move to the bottom queue
    - a long running process and short-running interactive job
    - a long running process and an I/O bound process
- Problems
    + There are two problems with the design so far:
        - Processes have no way to move back up in priority
            + priority boost (after fixed time reconsider priority)
        - Processes can “game” the scheduler by giving up the CPU right before using the quantum
            + gaming tolerance (use cumulative time slice)
+ Patches
    * [*muqss* scheduler](http://ck.kolivas.org/patches/muqss/sched-MuQSS.txt)
        - based on BFS scheduler
+ Addressing the problem
    * We can keep processes from “gaming” the scheduler by moving them down a queue after they exhaust their *cumulative* time slice at a given level
    - We can move processes up in priority periodically to keep them from starving
        - As an extension: we could *“weight”* processes so that some get to move up faster (but not too fast!)
- Sum of MLFQ rules
    + Rule 1: priority(A) > priority(B) => A runs
    + Rule 2: priority(A) == priority(B), A and B run in RR
    + Rule 3: when process enters system, it is placed at highest priority (top queue)
    + Rule 4: once a process uses its allotment at a given level, its priority is reduced (it moves down a queue)
    + Rule 5: after some time period S, move all the jobs in the system to the topmost queue (*priority boost*)
- [*The Rotating Staircase Deadline Scheduler* RSDL](https://lwn.net/Articles/224865/)
- rendezvous pattern
    + Thread 1, A RV, don't get here until T2 reaches A
        * announce my arrival wait for T2
            - sem_post(B) // increment the val
            - sem_wait(A)
    + Thread 2, B RV, don't get here until T1 reaches A
        announce my arrival wait for T1 (easy to implement as semaphore)
            - sem_post(A)
            - sem_wait(B)
            - then A = 0, B = 0
    - any number of threads? (*barrier*)
        + semaphore could be shared between processes

- Implementation of Barrier
    - use **futex** 
        - note lock = 0 corner case
            + when last thread reaching the barrier the thread set lock to 0 (assignment is a atomic operation, os guarantee this)
            + in the FUTEX_WAIT we expect the lock to be 1
                + for another thread before the last thread, it might call futex wake after last thread already set lock to 0
                + if we don't check value of lock, it might never get woken up
                + since lock might have to changed to 0, then it doesn't have to wait
        ```c
        #define NUM_THREADS 5
        static int lock = 1;
        static int count = 0;

        void *thread_func(void *args)
        {
            int t = *((int *)args);
            printf("Thread %d about to sleep for %d seconds\n", t, t);
            sleep(t);
            printf("Thread %d done sleeping\n", t);

            // Implement barrier here!

            if (__sync_add_and_fetch(&count, 1) == NUM_THREADS) {
                lock = 0;
                syscall(SYS_futex, &lock, FUTEX_WAKE, INT_MAX, NULL, NULL, NULL);
            } else {
                syscall(SYS_futex, &lock, FUTEX_WAIT, 1, NULL, NULL, NULL);
            }

            printf("These should all print at once!\n");
            pthread_exit(NULL);
        }

        int main(int argc, char *argv[])
        {
            pthread_t threads[NUM_THREADS];
            for (int i = 0; i < NUM_THREADS; i++) {
                int *val = malloc(sizeof(int));
                *val = i;
                pthread_create(&threads[i], NULL, thread_func, (void *)val);
            }

            pthread_exit(NULL);
        }
        ```
    - another using semaphores in page 21 little book of semaphores
        + one wait another, the waken up one then wait another etc...
        ```c
        rendezvous
        mutex.wait()
            count = count + 1
        mutex.signal()

        if count == n: barrier.signal()
        
        barrier.wait()
        barrier.signal()

        critical point
        ```
        - Now as each thread passes, it signals the semaphore so that the next thread can pass.
        - This pattern, a wait and a signal in rapid succession, occurs often enough that it has a name; it’s called a **turnstile**, because it allows one thread to pass at a time, and it can be locked to bar all threads.
        - In its initial state (zero), the turnstile is locked. The nth thread unlocks it and then all n threads go through.
        - It might seem dangerous to read the value of count outside the mutex. In this case it is not a problem, but in general it is probably not a good idea. 

### Lecture 17: scheduling in Linux
- Some terminology
    + **processor**: 
        + the physical chip that plugs into a socket on a system or processor board and contains one or more CPUs implemented as cores or hardware threads
    + **Core**: 
        + an independent CPU instance on a multi-core processor. The use of cores is a way to scale processors, called CMP *chip-level  multiprocessing*
    + **hardware thread**: 
        + a CPU architecture that supports executing multiple threads in parallel on a single core (including Intel's Hyper-Threading Technology), where each thread is an independent CPU instance. One name for this scaling approach is *multi threading*.
        - physical hardware: 1 processor and 4 cores, each core has 2 HThread
        - as seen by os: 8 CPUs
        * core shares different things e.g. they have their own L1 cache but share l2 l3 cache
    * **CPU instruction**: 
        * a single CPU operation from its *instruction set*. There are instructions for arithmetic operations, memory I/O, and control logic
    * **Logical CPU**: 
        * also called a *virtual processor*, an operating system CPU instance (a schedulable CPU entity). This may be implemented by the processor as a hardware thread (in which case it may also be called a *virtual core*), a core or a single-core processor
    * **Scheduler**: 
        * the kernel subsystem that assigns threads to run on CPUs. 
    * **Run queue**:
        - a queue of runnable threads that are waiting to be serviced by the CPUs. For Solaris, it is often called a *dispatcher queue*. 

- CPU run queues
    + figure on the right shows 5 software threads
        * 4 queued (Ready to run), 1 running on-CPU
    * Time spent waiting on a CPU run queue is called *run-queue latency*
        - for multi-processor systems: kernel has a run queue for each CPU
            + Tries to keep threads on same CPU
                * To keep caches hot (spatial locality for memory)
    
- User-time and kernel-time
    + **user-time**: the CPU time spent executing user-level application code
    + **kernel-time**: the CPU time spent executing kernel-level code
        * includes system calls, kernel threads, interrupts
    * **computation intensive tasks**: may have a high user/kernel ratio
        - eg. **image processing** may be 99/1
        - eg mandel is an user space task / user-level code (not ask os to do anything, it only crunches number in userspace)
        - even run mandel on Linux on virtual box, computing image is as performant as on host os
            - virtual machine running on user-space nearly performant as running on host os user-space
    * **I/O intensive tasks**: may have lower user/kernel ratio
        - most of time in kernel performing I/O, input/output operation is kernel task
        - eg. **web server doing network I/O** may be 70/30
        - most of time in kernel performing the I/O operations (kernel-level task)
        
- **Processes vs threads**
    + processes have entirely **separate address space**
    + threads **share** address space and can communicate
    + threads are usually more work to implement, but give better performance
    + comparison
        * development 
            - multiprocess: can be easier. use of fork()
                - less possible to run into deadlock in multi-threading
            - multi-threading: use of threads API
        - memory overhead
            + multiprocess: separate address space per process consumes some memory resources
            + multi-threading: small requires only extra stack and register space
        - CPU overhead
            + multiprocess: cost of fork/exit(), which includes MMU work to manage address spaces
            + multi-threading: Small. API calls
                * separate address space cache harder 
        - communication
            + via IPC. This incurs CPU cost including context switching for moving data between address spaces, unless shared memory regions are used.
            + Fastest. Direct access to share memory. Integrity via synchronization primitives (e.g. mutex locks)
        - memory usage
            + while some memory may be duplicated, septate process can exit() and return all memory back to the system
            + via system allocator. This may incur some CPU contention from multiple threads, and fragmentation before memory is used
                * less likely to return memory to system (eg when 3 threads are still running)

- Multitasking, preemptive operating systems
    + Linux is preemptive, multitasking OS
        * **preemptive**: the scheduler decides when a process should stop and another should start
        * **multitasking**: it can simultaneously interleave execution of more than one process
        * Windows, macOS, iOS, Android are also all multitasking, preemptive operating systems
    * On a single processor machine, this makes it seem like multiple processes are running concurrently
        - instruction streams are interleaved
    * on a multiprocessor (multi-core) machine this allows multiple processes to run in parallel on different processors (cores)
    * the *time-slice* (or quantum) is the amount of time a process runs before being preempted

- Review: **preemption**
    + (f temporarily interrupting a task being carried out by a computer system, without requiring its cooperation, and with the intention of resuming the task at a later time. Such changes of the executed task are known as context switches. They are normally carried out by a privileged task or part of the system known as a preemptive scheduler, which has the power to preempt, or interrupt, and later resume, other tasks in the system.)
    + what triggers the preemption of the currently running process? Two things
    - case 1: **returning from interrupt handler**
        - Example: the timer interrupt occurs
        - OS handles this interrupt by executing the timer interrupt handler
        - Time interrupt handler checks how long current process has been running
        - if current process has run longer than its allotted time slice, the scheduler is invoked
        - the scheduler saves the context of the current process and selects a new process to run
    + case 2: **process makes a system call**
        * recall: process generates an intentional 'exception' called a trap (e.g., int 80h)
        * OS executes a trap handler (to handle the system call)
        * When exiting the system call, the OS checks if it should invoke the scheduler
        * the scheduler, if run, checks for higher priority processes

- **Brief history** of scheduler in Linux
    + 1.2 kernel: circular queue for runnable tasks with RR policy
        * simple and fast! but not scalable, no SMP(Symmetric Multi-processing) (i.e., multiple processors)
    * 2.2 kernel: introduced idea of scheduling classes
        * real-time tasks, non-preemptible tasks, non-real-time tasks; support for SMP
    * 2.4 kernel: (early 2000) O(N) scheduler 
        * (time to find next task to run is proportional to number of tasks) (iterated over every process during scheduling)
        - lacked scalability, weak for real-time systems, inefficient (O(N))
    - 2.6 kernel: O(1) scheduler (constant time)
        + don't iterate over every task; two run queues for every priority level: active and expired
        + more scalable but *heuristics* for determining whether a task is interactive became large and difficult to reason about
            * nit: [What is the difference between a heuristic and an algorithm?](https://stackoverflow.com/questions/2334225/what-is-the-difference-between-a-heuristic-and-an-algorithm)
                - A heuristic is still a kind of an algorithm, but one that will not explore all possible states of the problem, or will begin by exploring the most likely ones.

    + 2.6.23 kernel: **completely fair scheduler**
        * incorporated some ideas form **[RSDL](https://lwn.net/Articles/224865/)** (Rotating Staircase Deadline Scheduler)
            - RSDL
                + good interactive response
                + priority array, each process has a quota of time it is allowed to execute at that priority. The processes at the highest priority are given time slices, and the scheduler rotates through them using a RR
                + when a process uses its quota at a given level, it is dropped down to the next priority and given a new quota
                + each priority has a quota of its own. Once the highest priority level has used its quota, all processes running at that level are pushed down *"minor rotation*
                + processes are moved to "expired" array if they used up their time. They are placed back until no more processes remain in t he active array *"major rotation*
        * current default scheduler in Linux
    * ~2.6.21 kernel: Rotating Staircase Deadline Scheduler
        - simple design, popular, but never made it into mainline kernel; written by Con Kolivas
    - ~2.6 series kernel: BFS: another scheduler from author of RSDL
        + simple scheduler, mainly for desktop workloads; don't scale well for many CPUs
        + not integrated into mainline kernel; also written by Con Kolivas
    + ~4 series kernel: MuQSS (pronounced "mux"): Multiple Queue Skip-list Schedule;er
        * addresses scalability issues with BFS; focuses on responsiveness/interactivity
        * no plans for mainline kernel

* Scheduler classes
    - Linux has different algorithms for scheduling different types of processes
        + called scheduler classes
    + each class implements a different but "pluggable" algorithm for scheduling 
        * within a class, you can set the **policy**
    * RT class: (real time processes - has strict deadline) SCHED_DEADLINE, SCHED_FIFO, SCHED_RR
        - real-time: timing guarantee 
        - Deadline (EDF, earliest deadline first; hard to implement as its dynamic) > FIFO > RR
    * CFS class: SCHED_NORTAL and SCHED_BATH
        - the default class is called **SCHED_NORMAL**; this is the CFS


- High-level idea: **lottery scheduling**
    + simple idea: every so often, hold a lottery to determine which process should run next
        * processes that should run more often get more *tickets*, and  thus more chances to win the lottery
        * Described in chapter 9 in the book
    - Example:
        - Two processes, A and B
        - 100 tickets total. A has 75, B has 25
        - Pick a random winning ticket to see who gets to run next
        - A should get the CPU ~75% of the time, B should get it ~25% of the time
    - Need a good random number generator, a data structure for tickets, and the total number of tickets!
- **Completely Fair Scheduler** scenario
    + The CFS in Linux is similar to lottery scheduling
    + Consider the following scenario
        * two tasks: a text editor (I/O bound) and a MATLAB simulation (CPU bound)
        * the text editor needs to respond key presses quickly
        * MATLAB needs CPU time to perform a simulation
    * In the ideal case: 
        - give a larger proportion of CPU time to text editor
            + But not because it needs it! Because we want it to have time the moment it needs it.
        - Allow the text editor to preempt MATLAB as soon as input is available (i.e., when a key is pressed)
            - This will make it responsive and give good interactive performance
    - Suppose the text editor and MATLAB are the only two processes running
    - Linux gives both processes a 50% “share” of the CPU
        - The text editor doesn’t use anywhere close to its 50% share
        - MATLAB is free to use more than its 50% share
    - When the text editor *does* need the CPU (i.e., when a key is pressed):
        - The *interrupt handler* (to handle the key press) notices that the text editor (1) needs to run, and (2) has used far less than its “fair-share”, and thus schedules the text editor
- CFS
    - Simple concept: model process scheduling as if the system *had an ideal, perfectly multitasking processor*
        - Each process gets 1/n of the processor’s time (n is number of runnable processes)
    - Instead of a “fixed” time-slice, CFS calculates how long each process should run as a function of the total number of runnable processes
        - Use the nice value to weight this proportion of processor a process receives (not nice - higher priority)
        - If all nice values are equal: all processes get an equal proportion of processor time
    - Uses a simple counting technique known as virtual runtime (**vruntime**)
        - Lower vruntime => a process hasn’t had its “fair share”

- Virtual runtime
    + as a process runs, it accumulates vruntime
    + when scheduler needs to pick a new process, it picks the process with the lowest vruntime
        * use a red black tree, easier to find the next lowest vruntime process
    * so how is the timeslice calculated?
        - too low: increases "fairness" but also increases overhead (due t o context switching)
        - too high: decreases overhead but also decreases short-term fairness
    - Timeslice uses a "magic value" (empirically determined) called `sched_latency` (typical value = 48ms) to calculate the timeslice
        + `timeslice = sched_latency / # of processes`
        + we can tune this value for different workloads
    - But what if there are too many processes?
        - Define a minimum timeslice value; never use timeslice lower than this
    - But how do we give processes “priority”?
        - In CFS: give them a larger share of the CPU
    + Nice value: parameter of a process controllable by user
        * goes from -20 to +19; default of 0;
        * negative implies higher priority (you are less nice)

- Timeslice
    - Use the nice value to "**weight**" the timeslice
        - time_slice_k = weight_k / total weight * sched_latency 
    + also scale the vruntime:
        * vruntime_i += + weight_0 / weight_i * runtime_i
    + table of weights...

- Rotating Staircase Deadline Scheduler (RSDL)
    + Simple scheduler that never made it into mainline kernel
        - But inspired the CFS
    - Goals:
        - Fairness with good interactivity; starvation free
    - Design:
        - No interactivity estimator
        - No sleep/run measurements
        - Only simple fixed accounting
    - Maintains a priority array; each level has list of processes, each of which has a “quota” of time it is allowed to execute at that level; cycles through processes at highest level RR
        - Similar to MLFQ
    - Process moves down a level and is given a new quota when it uses its whole quota
    - Different from MLFQ: each priority level also has a quota!
        - When a level reaches its quota, all processes in that level move down to the next level; called a “minor rotation”
        - Maximum latency is bounded and there is no starvation
    - When a process uses up its time, it is moved to the “expired” array
        - Eventually a “major rotation” happens: the active and expired arrays are swapped and the whole process begins again


### Real-time scheduling
- Overview of real-time scheduling
    + real-time scheduling is about *determinism*
        * you want to have guarantees that task swill execute when you need them to correctness means execution at the correct time
        * missing a ddl leads to a (catastrophic) error
    * real time scheduling is *not* about doing things as fast as possible!
    * Used in systems like aerospace or automation
- Basic real-time scheduling concepts
    + periodic vs aperiodic tasks
        * periodic: happens once per period P or exactly T time units apart
            - example: temperature measurement that happens every min
        - aperiodic: might have a ddl by which it must start/finish, or might have a constrict on start/finish time
            + example: braking task must run whenever brakes are pressed
            + TSN (temporal segment network) Ethernet, TCP IP etc.

- Simple real-time scheduling policies
    - earliest deadline first (EDF): when a scheduling decision needs to be made, pick the task with the earliest deadline - dynamic (search for process closest to its deadline)
        + usually happens at a preemption point: timer interrupt, system call invocation, new task launched, current task finishes or sleeps
        + schedulability criterion: sum(execution time / period) of all tasks <= 1
        + advantage: this is the optimal real-time scheduling policy
        + disadvantage: *runtime* overhead
    + Rate monotonic scheduling: priority is a function of the rate
        * (a shorter cycle duration results in a higher job priority)
        * higher rate = higher priority: priorities assigned "**statically**" (i.e. before runtime)
        * schedulability criterion: sum(execution time / period) of all tasks <= ln(2)
            - example: A has 1 sec every 2 sec, and B has 1 sec every 10 sec
            - then 1/2 + 1/10 < ln(2), such alg is schdulable, won't miss any deadline
                + greater than ln(2) might still be schedulable but need some other ways to check
        * advantage: easy to assign priorities, low runtime overhead
- Priority Inversion
    - Issue that can occur with any priority-based preemptive scheduling scheme
        - Particularly relevant to realtime scheduling
    - Example *Unbounded priority inversion*
        + lower level process share resource with T1
        + T3 lowest priority lock a semaphore and T1 highest priority tries to grab the lock, but it couldn't
        + then control  gets back to T3
        - however, then T2 comes (middle priority) and interrupt, and it runs a long time and block T3 and T1
- Ways to deal with priority inversion
    + Two popular ways to deal with priority inversion:
        - Priority inheritance protocol and the priority ceiling protocol
    - **Priority inheritance**: when a lower priority task holds a lock that a higher priority tasks tries to obtain, temporarily (and immediately) raise the priority of the lower priority task
        - In other words: the lower priority task inherits the priority of the higher priority task
    - **Priority ceiling**: assign each resource (e.g., lock) a “priority ceiling” and raise the priority of any process obtaining that resource under certain situations
        - Requires you to assign priorities to resources ahead of time!

- Priority Inversion: example
    - Interesting example of priority inversion in a realtime system: Mars PathFinder
    - Low priority weather task grabbed a mutual exclusion semaphore and was then preempted by medium level tasks; a high priority data distribution task then blocked trying to get the semaphore; several medium level tasks then ran and the low priority weather task couldn’t run and release the semaphore
        - Because the high priority data distribution task didn’t complete, the higher priority scheduling task thought an error had occurred, so it initiated a reset!
    - See [here](http://www.cse.chalmers.se/~risat/Report_MarsPathFinder.pdf) for a detailed explanation!


### Lecture 18: Interprocess communication and pipes
- Overview of interprocess communication
    + **interprocess communication** about ways to make processes "talk" to one another or "synchronize" with one another
        * recall that processes have separate virtual address spaces, so they can't just share variables
    * Three big categories of IPC
        - **communication**: how do processes exchange data
            + eg send a list of files form one process to another
        + **synchronization**: synchronize the actions of processes of threads
            * think of synchronization as how to coordinate actions
                - eg allow processes to avid updating the same part of a file simultaneously
        - **Signals**: can be used for synchronization (but are primarily for other purposes)
- Taxonomy of IPC
    + the figure on the right shows a taxonomy of IPC mechanisms
        * communication: 
            - data transfer(pipe, message queues), shared memory (memory mapping)
        * signal: standard signal, realtime signal
        * synchronization: semaphore, file lock, mutex/threads, condition variables/threads
        - We’ve looked at signals, mutexes, condition variables, and semaphores
        - We’ll be looking at pipes, message queues, shared memory, and memory mapping

+ fundamental concept: **file descriptors**
    * file descriptor: normal small, non negative **integers** that the kernel uses to identify the files accesses by a process
        - [Stack overflow: What are file descriptors, explained in simple terms?](https://stackoverflow.com/questions/5256599/what-are-file-descriptors-explained-in-simple-terms)
        - eg when a process opens an existing file or creates a new file, the kernel returns a file descriptor that can be used to *read* or *write* the file
    - all shells open three descriptors when a new program is run:
        + 0 *standard input*
        + 1 *standard output*
        + 2 *standard error*
    + if nothing special done: all of them are connected to **terminal**
        * In other words, input comes from the terminal, and output (including errors) are written to the terminal
    * How does the kernel view and use file descriptors?

- some terminal commands
    - `ps -ef | grep i -virtual` grab process form linux
    - `kill -9 ...`
    - `ls -l > filename.txt` `ls -l 1> filename.txt` send the content to a file rather than the default terminal
        - how does ls -l works? run `ls` program (from an early assignment)
        - what system call make them to point to other places? `pipe`
        ```c
        // shell
        loop: 
            c = read input();
            pid = fork(); 
            if(pid == 0) {
                exec(c);
            }
        ```

- Kernel data structures for I/O
    - (a) Per-*process* file descriptor table contains a pointer to a **file table entry**
    - (b) **Kernel** maintains **file table** for all open files
        - **Flags** are read, write, append, sync, etc
        - current file offset (eg where to read from the file next time)
        - **V-node pointer** is a pointer to v-node table entry
    - (c) Kernel maintains V-node table
        - Each entry contains information about the type of file and pointers to functions that operate on the file
            - Usually also contains the file’s **i-node**, which is its metadata
            - Note: Linux uses two i-nodes instead of a v-node: one generic and one specific
    - Figure on the right shows two processes with the same open file
    - Two fds from the same process can also point to the same file table entry
        - The `dup()` system call
    - Two fds from different processes can also point to the same file table entry
        - For instance, after a `fork()`
    - Let’s use this knowledge to do something interesting

- Example of file descriptor
    + [`man2 open`](https://man7.org/linux/man-pages/man2/open.2.html)
        * return vlaue is a file descriptor, a small, nonnegative interger that is used in subsequent system calls to refer to the open file. 
    + [`man2 read`](https://man7.org/linux/man-pages//man2/read.2.html)
        * read up to count bytes from file descriptor fd
       into the buffer starting at buf.
    ```c
        int fd = open("./data.txt", O_APPEND);
        printf("Value of returned fd is %d\n", fd); //eg give 3
        char buf[100] = {0};
        while(read(fd, buf, 100)) {
            buf[n] = '\0';
            prinf("We read: %s \n", buf);
        }
    ```
    - [lseek - reposition read/write file offset](https://man7.org/linux/man-pages/man2/lseek.2.html)

- Pipes: motivation
    + Big “real-world” use: connecting programs
        - How can the shell send the output of one program to the input of another program?
    - Example
        - The `ls` program will show the contents of a directory
        - The `wc` program will count the number of lines in its input
        - How can we use these together to count the number of files in a directory?
        - One (poor) solution:
            - Run ls and send its output to a temporary file (temp.txt)
                + `ls -ld /*`
                - `ls -ld /* > num_dirs.txt`
            - Run wc using temp.txt as the input
                + `wc -l num_dirs.txt`
            - Delete temp.txt

- Pipes: 
    - Better solution: use a pipe!
        + `ls -ld /* | wc -l`
            * ls -ld /* d stands for directories
            * The -d flag changes ls not to list the files within the specified directory, but to list the information about the directory itself.
        + `find . | grep -i main\.c| wc -l`
            `-i` case insensitive
        how many files contain "int main"
            - `grep -rin "int main" * | wc -l`
        - Think of it as a piece of “plumbing” that lets data flow from one process to another
            + stdout (fd1) ls -> pipe -> stdin (fd0) wc
    - More formally: a pipe is a **byte-stream** **IPC** mechanism that provides a one-way flow of data between processes
        - All data written to the pipe is routed by the kernel to another process, which can then read it
        - Think of them as open *files* that have no corresponding image on your filesystem
            + pipes are treated as file, fd of a pipe also points to a file table entry
        + can use pipe to talk among different processes (other ways: eg sockets, shared memory)

- Using pipes
    + a process can create a pipe using the pipe system call
    + [`man2 pipe`](https://man7.org/linux/man-pages/man2/pipe.2.html)
        - `int pipe(int filedes[2]);`
        ```c
        int fds[2];
        pipe(fds); // fds[0], fds[1] gives 3 - read end of pipe, 4 - write end of pipe
        ```
        - That is, the pipe system call takes an integer array of size 2 (returns 0 in success)
            - filedes[0] can be used to read from the pipe
            - filedes[1] can be used to write to the pipe

- example code in class
    + [Why should you close a pipe in linux?](https://stackoverflow.com/questions/19265191/why-should-you-close-a-pipe-in-linux)
        * why we close read end before write in child process?
            - if child suddenly terminates, since the read end is closed, and when the parent get the write error (pipe error), parent will know. Otherwise, parent will wait endlessly.
        - why we close write end before read in parent process?
            + in parent process, we close the write end of the pipe b.c. (child and parent are both writer of the pipe.) we want to know when the read process ends (or there's no more data coming or detect EOF condition)


### Lecture 19 continue
- `cat input.txt` and `cat < input.txt` gives same output
    + `man cat` read files, writing them to standard output, if file is absent then read from standard input
    + difference?
        * second one: standard input is redirected to input.txt
        * can chain inputs
        ```c
        ...
        while((num = read(STDIN_FIFENO, buf, sizeof(buf) - 1))) { // use fd instead of stdin to read from file instead
            buf[num]= '\0';
            prinf("%s", buf);
        }
        ```
- implement `tail`, use lseek, seeks to the end and go backward
- The shell and pipes
    - Back to our motivation: how can pipes help the shell connect the output of one program to the input of another program?
        - Recall how the shell works:
            - Read a command
            - Do a fork() to create a new process
            - Do an exec() in the new process to run the program
            - Repeat
    - We need the *“standard output”* of one process to go to the *“standard input”* of another process
        - Solution: have the shell “fix-up” the two processes’ file descriptors!

- **The shell and pipes**
    - *Step 0*: the shell has the three “standard” file descriptors open
    - *Step 1*: the shell process calls pipe() to create the pipes (the shell get read end and write end of pipe)
    - *Step 2*: the shell process calls fork() twice to create the two child processes (inherit the same file descriptors)
    - *Step 3*: in the first child process, the **write end of the pipe** is *dup’ed* onto the file descriptor for standard output
        + Child process closes both pipe fds and calls exec
        + eg `cat input.txt | wc` first child calls exec first, standard output of first child process is write end, standard input of second child is read end
        + doesn't matter which program runs first. b.c. closes write end of pipe

        + The dup system call:
            + int dup2(int oldfd, int newfd);
            + Duplicates the descriptor in oldfd to the descriptor in newfd
    - *Step 4*: in the second child process, the **read end of the pipe** is *dup’ed* onto the file descriptor for standard input
        - Child process closes both pipe fds and calls exec
    - Step 5: **shell process** closes both pipe fds

- Msg queue
    + byte stream oriented e.g. pipe
        * byte stream oriented IPC (each read operation might read an arbitrary number of bytes regardless of how many were written by the writer)
        - no concept of message boundary
    - message queues has distinct boundary, when being read, we have all or nothing
        + message oriented IPC (each read operation reads a whole message as written by the writer)


### Lecture 21: Sockets and Networks
- Pipe - chpt 44 of TLPI - *The Linux Programing Interface*

- Sockets Intro
    + foundation of abstraction of Networks
    + **Sockets**: method for IPC between applications
        - Can be on the same host
        - Can be on a different host connected by a network
    - Typical organization: **client-server**
        - The client makes requests
            - Example: a web browser
        - The server responds to requests
            - Example: an Apache web server
    - Communication involves a network protocol
        - Usually multiple layers of network protocols
    - We’ll cover **TCP/IP**
        - Also called the Internet protocol suite

- Big Picture: Internet
    - Began in 1960s: as network that could connect computers that were far away
        - Funding came from DARPA, and first ARPANET message was sent from UCLA to Stanford (350 miles) in 1969
    - Originally linked research operations and CS departments
        - Spread to the commercial world in the 1990s and become “the Internet”
    - Today: the Internet links millions of loosely-connected, independent networks
    - Data is through the networks in **“packets”** called IP (Internet Protocol) packets
        - Transported in one or more physical packets, like Ethernet or WiFi
        - Each IP packet passes through **multiple gateways** 
            - Each gateway passes the packet to a gateway closer to the ultimate destination
    - An internet (**lowercase i**) connects different computer networks
        - The Internet (**capital I**) refers to the TCP/IP internet that connects millions of computers

- The Internet (Cont)
    - The core protocol is the **Internet Protocol**
        - Defines a uniform transport mechanism and a common format for information in transit
        - IP packets are carried by different kinds of hardware using their own protocols
    - The **Transmission Control Protocol (TCP)** sits on top of IP
        - TCP provides a **reliable** mechanism for sending arbitrarily long sequences of bytes
    - Above TCP, higher-level protocols use TCP to provide services that we think of as “the Internet”
        - Examples: browsing, e-mail, file sharing
    - All of these protocols taken together define the Internet
    - Why IP is not reliable
        + Open Systems Interconnection model (OSI model) 
            + Application, 
            + Presentation, 
            + Session - open a connection, pass info back and forth and then close the connection when finished, 
            + Transport (TCP) - point to point data transfer error and recovery, 
            + Network(IP) - switching and routing, 
            + Data-link (Ethernet) - collision detection, exponential back off and similar low level protocols that mediate to the physical layers, 
            + physical
        + reliable TCP on top of unreliable IP 
        + in contrast UDP protocol - unreliable bc *message oriented*
        - TCP checks to see if everything that was transmitted was delivered at the receiving end (it may not have been due to packet loss). TCP allows for the retransmission of lost packets, thereby making sure that all data transmitted is (eventually) received.

- Protocol layers
    - Example on the right: 
        - Web servers and web clients communicate using TCP
        - TCP uses the Internet Protocol (IP)
        - IP uses a data link protocol (like Ethernet)
    - The client and server use an *application protocol*
        - The transport layers use the *TCP protocol*
    - Information flows down the **protocol stack** on one side, back up on the other
    - Client and server are in **user** space
        - TCP, IP, data link in **kernel** space (usually)
    - On the same LAN (local area network) v.s. different LANs (through WAN - Wide Area Network)

- Sockets and client/server communication
    - Each application creates a socket
    - The server binds its socket to a well-known address so clients can locate it `fd = socket(domain, type, protocol);`
    - Domain determines:
        - Format of address, and range of communication (same or different hosts)
            - AF_UNIX (on same machine), 
            - AF_INET, (use IPv4)
            - AF_INET6, (use IPv6)
    - Socket Type: **stream** or **datagram**
        + stream has *reliable delivery*
        + Datagram has *message boundaries preserved*
        + stream is *connection-oriented*
    - Protocol: generally 0
        - Nonzero for some types like raw sockets (passes directly from data link to application)

- Stream sockets
    + **Stream** sockets provide **reliable**, **bidirectional**, **byte-stream** communication
        - **Reliable**: Either the transmitted data arrives intact at the receiving end, or we receive notification of a probable failure in transmission
        - Bidirectional: data may be transmitted in either direction
        - **Byte-stream**: no message boundaries
            - Example: receiver doesn’t know if the sender originally sent two 1-byte messages or one 2-byte message
    - Operate in connected pairs (aka **connection oriented**)
        - Peer socket: socket at the other end of a connection
        - Peer address: address of that socket
        - Peer application: application using the peer socket
        - Peer is equivalent to *remote* or *foreign*

- Datagram Sockets
    + Allow data to be exchanged in the form of **messages** called **datagrams**
        + **Message boundaries** are preserved
        + Data transmission is **not** reliable
            + Data may arrive out of order, but duplicated, or not arrive at all
        + Example of a **connectionless** socket
            - Doesn’t need to be connected to another socket in order to be used
    - In the Internet domain:
        - Datagram sockets use **UDP**
        - Stream sockets use **TCP** (almost always)

- Key socket calls
    + socket() creates a new socket
    + bind() binds a socket to an address
    + listen() lets a TCP socket to accept incoming connections from other sockets
    + accept() accepts a connection from a peer application 
    + connect() establishes a connection with another socket
    + Socket I/O can be done using
        + read() and write(), or 
        + send(), recv(), sendto(), recvfrom()

- Protocols and communication
    + like layers of onion (add more thing through each layer from application-defined content to TCP segment(TCP header + TCP data/Application Data) to IP datagram (IP header + IP Data/TCP segment) etc.)
    + peel off the onions back to application


### Lecture 22: sockets and web servers
- Overview of sockets and web servers
    - Network socket:
        - Wikipedia: “an internal endpoint for sending or receiving data within a node on a computer network”
        - man 2 socket: socket() creates an endpoint for communication and returns a descriptor
    - The socket() function gives you back a file descriptor
        - You can use that file descriptor in subsequent functions to communicate
            - You can communicate locally (between processes on the same machine) or between processes on different machines (across the world, for instance!)
    - Later we’ll build the world’s dumbest web server

+ System calls with stream Sockets
    - Install a telephone
        - socket() creates a socket
    - Have a phone number and turn it on
        - One application calls bind() (bind to well-known address) and then listen() (notify kernel that to accept incoming connections)
    - Dial someone’s telephone number
        - Other application calls connect()
    - Pick up the phone when it rings
        - Call accept()
    - Talk to each other
        - Use things like read()/write() (or send()/recv())

- Web Servers
    + A web server serves content to web clients using the hypertext transfer protocol, or HTTP
        + A server is a program that has a well known name and port (e.g., google.com and port 80)
        + A client (e.g., a web browser like Firefox) opens a connection to a web server and requests content
        + The server reads the request, sends the content, and closes the connection
        - The client (browser) displays the content
    - Web content is written in the hypertext markup language (HTML)

- Web Content 
    - Content is a sequence of bytes with a multipurpose internet mail extensions (MIME) type
        - text/html: HTML page
        - text/plain: unformatted text
        - image/gif: binary image encoded using the GIF format
        - image/jpeg: binary image encoded using the JPEG format
    - All content returned by a web server is associated with a file it manages
        - Each file has a unique name known as a URL (universal resource locator)
    - Example: http://www.google.com:80/index.html
        - Tells the browser to use the the http protocol to contact www.google.com on port 80 and ask for the index.html page

- HTTP Requests
    - An HTTP request includes:
        - A request line (e.g., GET / HTTP/1.1); this has the form `method URI version`
        - Zero or more request headers (e.g., Host: balasub.com)
        - An empty text line to terminate the list of headers
    - Example: the GET method tells the server to generate and return the content identified by the URI; the version states which HTTP version the request conforms to
        - HTTP/1.1: latest version; defines additional headers
    - Headers provide additional information to the server (e.g., name of browser)
        - Form is: `header-name`: `header-data`

- HTTP Response
    - Responses are similar to requests; they consist of:
        - A response line; format is: version status-code status-message
        - Example: HTTP/1.1 200 OK
        - Zero or more response headers
        - Empty line terminating the headers
        - Response body
    - Let’s put this all together to build a web server

- A small web server
    + The code on the right implements a functioning server:
        - socket() gives us a file descriptor
        - We bind() and listen() on that socket
        - We enter an infinite loop where we:
            - accept() connections
            - write() back a response
            - close() the connection
            - sleep() for 1 second

- What would a real web server do differently?
    + Parse the request headers
        - For example, determine if it’s a GET or POST
    - Handle multiple requests efficiently; typical options are:
        - Fork to handle each connection
            - “Pre-forking” can speed this up by creating a pool of reusable client-handling processes
        - Use multiple threads
        - Use multiple processes and multiple threads
            - [The Apache approach](https://httpd.apache.org/docs/2.4/mod/worker.html#:~:text=Apache%20HTTP%20Server%20always%20tries,their%20requests%20can%20be%20served.)
        - Polling
            - The Node/Express way. Spin up more node instances to scale
                - Some argue this puts the complexity where it should be
        - [Three Ways to Web Server Concurrency](https://www.linuxjournal.com/content/three-ways-web-server-concurrency)

- stack frame vulnerability 
    + [Stack Buffer Overflow](https://en.wikipedia.org/wiki/Stack_buffer_overflow)
        * malicious client could overwrite return address and inject malicious code
    - one solution: check **stack canary*** (right under stack return address)
        + wrong stack canary - process terminates
    - forking web servers - still vulnerable 
        + [return-to-csu](https://www.youtube.com/watch?v=mPbHroMVepM&ab_channel=BlackHat)

### Lecture 24: filesystems
- Reading: 36 37 39 of textbook and first part of chpt 3 char drivers (important data structure for file system)
- Overview of filesystems
    + A filesystem is an organized collection of files and directories
    + The Linux kernel maintains a single hierarchical directory structure to organize all files in the system
        + Not like Windows where each drive (C, D, E, etc) has its own hierarchy
    + Root directory is named /
        + Pronounced “slash”
- File Types
    - Every file has a type
        - This is the character in the first column when you do ls -l
    - Regular files: ordinary data files, like text files, executables, libraries
    - Special files: files other than ordinary data files
        - Devices: represents a device (virtual or physical)
            - Block device
            - Character device
        - Named pipes (also called fifos)
        - *Directories*
        - Symbolic links
    - Example on right: block device files `ls -l sda*`
- Filenames and pathnames
    + Linux filesystems allow filenames:
        + Up to 255 characters
        + To contain any characters except slashes (/) and null characters (\0)
        + Recommended to only use letters, digits, . (period), _ (underscore), and - (hyphen)
    + A pathname is a string with an optional / followed by a series of filenames separated by slashes
        + Absolute pathname: starts with a /
            + Examples: /home/daniel/ (my home directory), /home/daniel/hw.txt (my homework file)
        + Relative pathname: relative to the current directory
            + Examples: assignment-8/build (assignment-8 is a subdirectory of my current directory)
- File permissions
    + Each file has a user ID and group ID that defines the owner and group
        - Ownership determines file access rights to users of the file
    - Three categories of users:
        - Owner
        - Group: users who are members of the group
        - Other: everyone else 
    - There are three permission bits for each category: read, write, execute
        - Examples: 
            - 111 110 000 (760): owner can read, write, and execute; group can read and write; everyone else cannot access the file
            - 110000000 (600): owner can read and write; everyone else cannot access
            - Look at the permission bits on ~/.ssh/id_rsa -- what is the reason for this? -rw-------
- Back to filesystems
    - A disk drive is divided into circles called *tracks*
        - Tracks are divided into *sectors*
            - Sectors are a series of physical blocks 
                - Physical block: the smallest unit a disk can read or write
                    - Usually 512 bytes (older disks) or 4096 bytes (newer disks)
    - Each disk is divided into partitions
        - Each is a separate device under /dev
        - A partition holds either
            - Filesystem (Boot, Super, I-node table, data blocks)
            - Data area (raw-mode device)
            - Swap (for virtual memory)
- Types of filesystems
    + [Many types of filesystems](https://en.wikipedia.org/wiki/List_of_file_systems) -- too many to list!
    + A few common ones:
        + ext4: the most recent ext filesystem; common on Linux (ext3, ext2 are older versions)
        + ZFS: supports very large files, journaling
        + ReiserFS, Reiser4: journaling filesystem
        + NTFS: Windows filesystem (Linux driver has read and write support)
        + NFS: network filesystem; allows you to access files over a network
        + Ceph, GFS: high-performance distributed filesystems
    + How do (disk-based) filesystems differ?
        + Different filesystems may be better/optimized for different things, like file sizes, speed of file access, integrity, etc.

- Quick recap: reading and writing files
    + why does assignment 11 use the `Rio_readlineb` and `Rio_readnb` routines?
    - TODO about short count

- Filesystem structure 
    + **Boot block**: always the first block in a FS
        - Not used by FS; contains info to boot the OS
        - Only one needed by OS
    - **Super block**: contains parameter info about the filesystem
        - Size of the i-node table, size of logical blocks, size of the filesystem (in logical blocks)
    - **I-node table**: contains one (unique) entry for every file in file system
        - Contains most of the “metadata” about individual file
        - (no file name)
    - **Data blocks**: the (logical) blocks that contain the data for files and directories
        - This is the vast majority of a FS

- I-nodes
    + `ls -li` lists inode number
    + `ln file1 flile2` link two files together and they point to same i-node (reference count number will be 2)
    + file won't be removed until all **hard links** are removed
    + filename is not a intrinsic property of file
    + **soft/symbolic link**
        * `ln -s`
        * `ls -lai`
        * dangerous when delete the file the soft link points to
    + Index nodes (i-nodes) contains the following metadata about a file
        - File type (for example, regular, char device, block device, directory, symbolic link)
        - Owner of the file
        - Group of the file
        - File access permissions (user, group, other)
        - Three timestamps:
            - Time of last access (ls -lu)
            - Time of last modification (default timestamp in ls -l)
            - Time of last status change (change to i-node info) (ls -lc)
        - Number of hard links to file
        - Size of the file (in bytes)
        - Number of blocks allocated to file 
        - Pointers to the data blocks

- Mounting 
    + Recall: all files from all filesystems reside under a single directory tree
    - You can attach new filesystems into the hierarchy using the mount command
        - Just typing `mount` shows you all the mounted file systems
    - For example
        + `mount | grep ext4`
        - My root filesystem is of type `ext4`; the device is `/dev/sda1`, meaning the first partition of the (first) hard drive
            - `rw`: mount it read-write
            - `relatime`: access time will not be written to disk during every access
            - `errors=remount-ro`: when an error is encountered, remount the filesystem read-only
            - `data=ordered`: force data out to filesystem prior to metadata being committed to the journal

- Data Blocks
    - How can files of very different sizes be supported?
        - One method: store **pointers** to the data blocks!
    - Figure on the right shows how *ext2* does this
        - Small files might fit entirely in direct pointers
    - Bigger files use:
        - Indirect pointers
            + The number of pointers in the indirect blocks is dependent on the block size and size of block pointers. 
                - Example: with a 512-byte block size, and 4-byte block pointers, each indirect block can consist of 128 (512 / 4) pointers.
        - Double-indirect pointers
        - Triple-indirect pointers
    - Advantages of pointers
        - Fixed-size i-node
            - But arbitrary size files
        - Store blocks non-contiguously

- Directories
    + `ls -ld /*`
    - A directory is stored in a filesystem in a similar way as a regular file, but
        - It is marked as a directory in its i-node
        - It’s a file with a special organization: it’s a table consisting of filenames and i-node numbers (in data block pointers)
    - Example is on the right
    - Note: the i-node doesn’t have a filename!
        - Implication: you can have multiple links to the same file!


- Developing Embedded Linux Device Drivers [LFD435](https://training.linuxfoundation.org/training/developing-embedded-linux-device-drivers/)

### Proc file system
- Overview of `/proc`
    - The `/proc` filesystem is a *virtual* filesystem
        - This means the entries you see there aren’t backed by “real” files on a disk
    - The files there can be read (and sometimes written to)
        - Reading a file returns information about the OS
        - Writing usually configures something about the OS
    - Think of the files in `/proc` as “windows” into the kernel

- examples of `/proc`
    - If you do the following:
        - `cd /proc` `ls`
    - You’ll see many entires; entries with a number correspond to a PID
        - Go into one of the numbered directories, do an ls, and you’ll see information about a process that you can inspect, like its current working directory  
        - e.g. `cat maps`
            + `ls -l map` gives 0
            + but cat maps some give more than 0 bytes
                * where does these come from 
                ```
                read_proc()
                ```

- Using `/proc` in the assignment
    + You can use the `/proc` filesystem to export information to the user
        - Example: implementing a simple firewall with Netfilter
    - This can work as follows:
        - Implement a module that creates a proc file
        - Register callback functions that are invoked when your `/proc` entry is (1) read and (2) written
        - In the read callback, return information to the user
    - In the write callback, you can “update” your module’s functionality
        - For netfilter: add an ip address to block
        - For instrumentation: reset counts
    - In the read callback, you can get information back to the user
        - For netfilter: stats about traffic filtering
        - For instrumentation: stats about how many times the process functions have been invoked
- Example
```c
#include <linux/init.h>
#include <linux/module.h>
#include <linux/sched.h>
#include <linux/proc_fs.h>
#include <linux/slab.h>
#include <linux/uaccess.h>

MODULE_LICENSE("Dual BSD/GPL");

#define MSG_SIZE 100

static char *message;
static int message_length = 0;

static ssize_t read_proc (struct file *filp,
              char __user * buf,
              size_t count,
              loff_t *f_pos)
{
  ssize_t retval = 0;
  if (*f_pos >= message_length) {
    printk(KERN_ALERT "f_pos >= message_length\n");
    goto out;
  }

  if (*f_pos + count > message_length) {
    count = message_length - *f_pos;
    printk(KERN_ALERT "count is: %d, f_pos is: %ld\n", count, *f_pos);
  }

  // use copy_to_user to validate user space pointer!
  // we are in kernel space! so we can't blindly write
  // to a pointer (address) the user gives us
  if (copy_to_user(buf, message + *f_pos, count)) {
    retval = -EFAULT;
    goto out;
  }

  printk(KERN_ALERT "Incrementing f_pos\n");
  *f_pos += count;
  retval = count;

  out:
    return retval;
  //return simple_read_from_buffer(buf, count, f_pos, message, message_length);
}

static ssize_t write_proc (struct file *filp,
               const char __user * buffer,
               size_t length,
               loff_t * off)
{
  message_length = simple_write_to_buffer(message, MSG_SIZE, off, buffer, length);
  return message_length;
}

static const struct file_operations proc_fops = {
  .owner = THIS_MODULE,
  .read = read_proc,
  .write = write_proc,
};

static int __init proc_init(void)
{
  printk(KERN_ALERT "Initializing example proc file\n");

  // create a file in the /proc file system named "example"
  // when you read from it, call the read_proc function
  // when you write to it, call the write_proc function
  proc_create ("example", 0666, NULL, &proc_fops);
  message = kmalloc(100 * sizeof(char), GFP_KERNEL);
  if (!message)
    return -ENOMEM;

  strcpy(message, "Initial content\n");
  message_length = strlen("Initial content\n");
  return 0;
}

static void __exit proc_exit(void)
{
  printk(KERN_ALERT "Removing example proc file\n");

  remove_proc_entry ("example", NULL);
}

// call these functions when module is loaded/unloaded
module_init(proc_init);
module_exit(proc_exit);
```

### Lecture 27: Virtual Machine Monitors
- Overview
    - We’ve discussed how operating systems abstract hardware
        - For example, the OS isolates processes from one another so that each thinks it has the whole CPU (via context switching) and all of memory (via virtual memory) to itself
    - But what if we want to run different operating systems on the same hardware simultaneously?
        - Solution: a virtual machine monitor (VMM); also called a hypervisor
    - The VMM needs to provide similar illusions
        - Each “guest” OS must think it’s interacting with the physical hardware when the VMM is actually in control
- Why use VMMs?
    + Cost
        + Let multiple OS instances exist on the same hardware
            + Example: spinning up an EC2 instance
    + Compatibility
        + Example: this class! You need to run Linux, but might not want to dual-boot your machine
        + Example: when Apple moved from PowerPC to x86
            + Rosetta did real-time binary translation from PPC to x86
        + Example: when Apple moved from x86 to Apple Silicon a few weeks ago
            + Rosetta 2 does both JIT and AOT translation
    - Debugging
        - Test software on different operating systems
- Types of VMMs
    + traditional uniprocessor system
        * the os in privileged mode
        * example the Xen hypervisor (called bare-metal hypervisors)
    + native VM system
        * the VMM operates in the privileged mode
    + user-mode hosted VM system
    + dual-mode hosted VM system
        + a trusted host operating system executed in privileged mode in a hosted VM system 
        * the guest os resides in the virtual machine and operates at a less privileged level. The VMM of a hosted system may work entirely in the user mode or in a dual mode when parts of the VMM operate in the privileged mode
        * example virtual box
- Comparison to OS-level virtualization
    - OS-level virtualization is enabled inside an individual OS
        - Examples: Docker containers, Solaris Zones, or “jails”
        - Many people refer to all of these as “containers”
        - [Everything You Need to Know about Linux Containers, Part I: Linux Control Groups and Process Isolation](https://www.linuxjournal.com/content/everything-you-need-know-about-linux-containers-part-i-linux-control-groups-and-process)
    - Programs inside a container can only access that container’s contents 
    - Under the hood: many container systems use the Linux `cgroup` facility
    - The `cgroup` framework provides:
        - Limits on resources (e.g., a certain amount of memory)
        - Prioritization
        - Accounting (keep track of a group’s useage)
        - Control: a group of processes can be frozen or stopped and restarted
- How does a VMM virtualize the CPU?
    - Recall how a context switch works with regular processes
        - The OS saves the state of one process and restores the state of another
    - With a VMM switching between OSes, a *machine switch* is performed 
        - Save the entire state of one OS and restore the entire state of another
    - Recall some terminology:
        - A **trap** is an intentional exception that occurs as the result of executing an instruction
    - System call implementation
        - Normally: an `int 80h` instruction results in the OS’s trap handler being invoked
            - And this changes the processor’s mode from user mode to kernel mode
    - But what should happen when an `int 80h` is invoked by a guest OS?
        - We can’t let it change the processor’s mode! Otherwise the guest is in control of everything

- Handling System calls
    + the guest OS makes a system call as usual
        * but the VMM will first get control instead of the guest OS'e kernel
        * it then jumps to the actual system call handler in the guest OS

- Memory virtualization
    - Recall: the OS provides virtual address spaces to each process
        - A virtual page is mapped to a physical page
    - With VMMs, we need another level of indirection
        - Guest OS: maps virtual to “physical”
        - VMM: maps “physical” to actual machine memory

- Memory translation with the IA-32
    + recall that the page table is architected
        * The TLB is maintained by hardware; when a TLB miss happens, the hardware walks the page table and inserts the correct entry in the TLB
            - If there’s no entry in the page table, the OS’s page fault handler takes over and either (a) installs an entry in the page table, or (b) gives a seg fault back to the offending process
    - On the IA-32 architecture, the VMM can use a shadow page table for each process’s page table
        + The VMM monitors for changes the OS makes to each page table and updates the shadow page table accordingly
    - Guest OS: page table maps from virtual to “physical”
    - VMM: shadow page table maps from virtual to actual machine memory

- Shadow page tables
    + The page table pointer for a guest process is “virtualized”
        + In other words, the CR3 register doesn’t hold what the guest OS thinks it holds; it holds the base address of the current shadow page table
    + Whenever the guest OS accesses its CR3 register, the VMM is notified
        + If the guest was reading: the VMM returns the virtual CR3 value
        + If the guest was writing: the VMM updates the virtual version and then updates the real CR3 to point to the corresponding shadow table
    + Newer hardware extensions enable “nested paging”
        + This implements memory management for guests in hardware

- paravirtualization
    + "paravirtualization" it means that the guest OS is modified to run on a VMM
    + the big reason to do this is *efficiency*
        * if the OS knows it's running under a VMM, it can optimize certain things
        * for example: 
            - it can know that physical pages have already been zeroed
            - it can know that when it's idle, rather than spinning, it can give control back to the VMM
    - A paravirtualization interface can be exposed by a VMM to allow the guest OS to communicate with the VMM
        - This allows guest software to be executed more efficiently
        - But it also requires that the guest OS be aware of the paravirtualization interface
    - Many OSes (including Windows and Linux) support paravirtualization
        - In other words: when you run Windows or Linux as a guest, they “know” they’re being virtualized to some degree -- this is good!
    - Different types of paravirtualization interfaces
        - Minimal: says that the environment is virtualized; reports TSC and APIC frequency to guest
        - KVM: support paravirtualized clocks and SMP spinlocks
        - Hyper-V: recognized by Windows 7 and newer; supports virtualized clocks, guest debugging


- Hardware vs software visualization
    + VMMs, like VirtualBox, try their best to let software in a VM run directly om the host processor
        * But, the VMM needs to intercept operations that would interfere with the host
    * examples: 
        - when the guest OS(1) invokes a system call, (2) modifies its page tables, or (3) accesses a hard disks
    - intercepting these operations is difficult on the x86
        + the x86 was never meant to be virtualized!
    + you can detect and intercept these operations in two ways:
        * hardware virtualization: Intel calls this VT-x; AMD calls it AMD-V (orAMD SVM)
* Software visualization of the x86
    - brief history: original x86 (1970s), big changes in 1980s (protected mode with 286), 32-bit in 1980s (386)
        + take-away: x86 is an old architecture that didn't originally include virtualization support 
    + recall: the x86 has 4 privilege levels or rings
        * most OSes (like Windows and Linux) only use two levels: 0(privileged) and 3(unprivileged)
    * virtualization also needs to distinguish between host and guest context 
    * host context : no VMM; can be in either ring 0 or ring 3
    * guest context: a VM is active, usual easy in ring 3
        - problems arises in intercepting guest context in ring 0 (kernel mode)

- Intercepting ring-0 code
    - One solution: use binary translation; analyze and transform all guest code so that it neither sees or modifies true state of the CPU
        - This is usually the QEMU approach
        - Downside: expensive
    - VirtualBox uses a ring-0 kernel driver so that it can take over when needed
        - In other words: part of VirtualBox is running in privileged mode
    - VB is in one of three states
        - Guest code in ring 3: runs unmodified at full speed as much as possible
        - Guest code in ring 0: VB reconfigures guest so that it’s actually running in ring 1
            - VB takes over when guest does something privileged
        - VB (the VMM) is active
    - There are several low-level issues with the approach above
    - So, VB uses a Code Scanning and Analysis Manager (CSAM) and Patch manager (PATM)
    - Before executing ring-0 guest code
        - CSAM scans it to discover problematic instructions
        - PATM performs in-situ (in-place) patching of instructions
            - Jumps to VMM memory where a code generator has placed a “suitable” implementation
    - This is complex!

- Hardware virtualization
    - Intel’s VT-x and AMD’s AMD-V introduce new CPU operation modes:
        - VMX root mode: four privilege levels (rings), same instruction set plus new virtualization specific instructions. Used by host OS without virtualization, and also used by VMM when virtualization is active
        - VMX non-root mode: still four rings and same instruction set, but new *Virtual Machine Control Structure* (VMCS) controls the CPU operation and determines how certain instructions behave. Guest OSes run in non-root mode
    - In a nutshell: VT-x lets guest code run in in the ring it expects!
        - The VMM uses the VMCS to control how certain situations are handled
    - Example: the VMM can set up the VMCS so that the guest can write certain control registers but not others

- Software vs hardware virtualization recap
    - VT-x and AMD-V avoid some problems faced by pure software virtualization
        - Avoid the problem of having the guest OS and the VMM share an address space
        - Guest OS kernel code runs at ring-0 (where it expects to run)
    - Disadvantage of hardware virtualization vs software:
        - The overhead of VM exits (i.e., having the VM emulate an instruction) is relatively high

- Nested paging
    - Intel and AMD provide hardware extensions for virtual memory with VMMs
        - These are called nested paging or EPT
    - Big idea: do memory management for VMMs in hardware
        - The guest can handle paging without intervention from the VMM
        - As opposed to shadow page tables which require the VMM to maintain the real page tables

- References
    + [Introduction](https://www.anandtech.com/show/2653)
    + [VirtualBox documentation](https://www.virtualbox.org/manual/ch10.html)
    + [What exactly do shadow page tables (for VMMs) do?](https://stackoverflow.com/questions/9832140/what-exactly-do-shadow-page-tables-for-vmms-do)
    + [AnandTech article: Hardware Virtualization: the Nuts and Bolts](https://www.anandtech.com/show/2480/10)
    + [AnandTech article: Virtualization - Ask the Experts #1](https://www.anandtech.com/show/3827/virtualization-ask-the-experts-1)
    + [Control groups](https://www.linuxjournal.com/content/everything-you-need-know-about-linux-containers-part-i-linux-control-groups-and-process)
    + Intel architecture manual, Volume 3B, chapter 23


### [Final Review Notes](./final-review.md)
### [Site Channel Attack](./site-channel-attack)