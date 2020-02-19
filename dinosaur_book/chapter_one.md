# Chapter One :: Introduction
#### Chapter Objectives
  - Describe the general organization of a computer system and the role of interrupts.
    - A generalized computer system is typically composed of four primary components. Hardware (CPU, io devices, memory, storage), Application programs (such as those a user finds helpful, word processesors, compilers, browsers), a user, and the operating system which allocates and orchestrates the hardware for application programs and, ultimately, the user.
  - Describe the components in a modern multiprocessor computer system.
    - Exluding the portions of the system that are universal eg bus, devices, storage
    - There are several contemporary architectures of multiprocessor systems. The simplest would be symmetric multiprocessing in which a system is host to 2+ CPUs. Each CPU has its own local resources but shares other parts of the system like the bus and memory
      - Increasing the number of processors by *n* does not mean *n* times speedup. Coordinating execution and contending for access to resources comes with overhear. At a large enough number, performance degrades. NUMA solves some of these problems.
    - NUMA is a more complicated version of SMP in which a dedicated bus, called the interconnect, links the peer CPUs and each peer has its own local memory. This allows for faster inter CPU communication as the data doesn't have to travel an already busy system bus and memory contention is limited because each CPU has their own. The interconnect means that even though a CPU has a very fast bus to its own memory, all CPUs share an address space so they can work with each others memory.
    - To some extent, multicore processors are now considering multi-processor systems.
    
  - Illustrate the transition from user mode to kernel mode.
      +-----------------------------------------------------------------------+
      |                                                                       |
      |  user process executing -> system call       return from system call  | USER MODE
      |                               \                 ▲                     | mode bit = 1
      +--------------------------------|----------------|---------------------+
      +--------------------------------|---------------/----------------------+
      |                              `trap`        `return`                   |
      |                                ▼             /                        | KERNEL MODE
      |                            execute system call                        | mode bit = 0
      +-----------------------------------------------------------------------+
  - Discuss how operating systems are used in various computing environments.
    - *NEEDS ANSWER*
  - Provide examples of free and open-source operating systems.
    - GNU/Linux
    - BSD Unix
    - OpenSolaris

---

###### Section 1.1 - What Operating Systems Do

  - Computer systems can be broken down into four components
    - hardware
      - cpu, memory, i/o devices, etc
    - application programs
      - word processors, compilers, browsers, etc
    - operating system
      - controls hardware and coordinates its use amongst various application programs
    - a user
      - well, you know... a user

  **1.1.1 User View**

  - Various computer systems are designed for their expected use.
    - PCs/laptops are designed with the knowledge that they'll be mostly used by a single user.
      - ease of use is a high priority
      - no care for resource utilization / sharing
    - PCs/laptops were a sea change from the shared system model in the Unix days
    - Mobile devices have repeated this pattern wrt to PCs
  - Some systems have no user view.
    - embedded systems, smart devices, automobiles

  **1.1.2 System View**

  - An operating system could be (reductively) called a 'resource allocator'
  - OS works to split up cpu time, memory, storage, io devices amongst many programs

  **1.1.3 Defining Operating Systems**

  - Very difficult to provide a succinct definition as they vary widely from system to system
    - It goes without saying the the OS for a smart toaster will be markedly different than the OS for the space shuttle
  - A sensible but not-terribly-descriptive definition from the book:
    - "...the operating system is the one program running at all times on the computer -- usually called the kernel"
    - Along with the kernel, there's two other types of programs system programs and application programs
      - System Programs: those associated with the OS but not part of the kernel.
        - Examples? The shell is a good example. Useful to understand that a program can reside in userland and still be part of the OS
        - Really good explanation of distinction between kernel and non-kernel programs [here](https://web.archive.org/web/20161008033553/https://superuser.com/questions/329442/what-is-there-in-an-operating-system-other-than-the-kernel/329479#329479)
      - Application Programs: all programs not associated w/ the OS

###### Section 1.2 - Computer-System Organization

  - "A modern general-purpose computer system consists of one or more CPUs and a number of device controllers connected through a common bus that provides access between component and shared memory"
    - how are device controllers implemented? are they hardware or software?
      - device controllers have their own local buffer storage and special-purpose registers.
        - Bonus Question! How do registers get allocated? For instance, if a new keyboard or microphone is plugged in?
          - Maybe a misunderstanding on my part. The buffer and registers are local to the device controller itself which is a physical piece of hardware separate from the CPU.
      - OSs usually have a device driver for each device controller which provides a consistent interface
      - See 1.2.1 Header for more info

    - is a bus smart? or just a dumb pipe?
      - Not a great question. They can be implemented in various, complex configurations but ultimately just ship data between components or systems.

  **1.2.1 Interrupts**

  - to start an io operation...
    - the device driver loads the appropriate registers in the device controller
    - the device controller examines the content of the registers to determine what action to take
      - For instance, 'read a character from the keyboard'
    - controller starts transfer of data from device to its local buffer
    - controller informs the device driver it's done
    - device driver then gives
    - but how does the controller tell the driver it is done? via interrupt

  **1.2.1.1 Overview**

  - Hardware can interrupt at any time by sending a signal to the CPU on the system bus
    - there are many buses but the system bus is primary for main computer component communication
  - What happens during an interrupt?
    - the cpu stops what it's presently doing
    - immediately transfers execution to a fixed location
    - fixed location usually contains the starting address where the service routine for that interrupt is stored
    - interrupt routine executes
    - on completion, the cpu resumes the inrerrupted work

  **1.2.1.1 Implementation**

  - cpu has a wire called interrupt-request line
    - cpu senses the line after executing every instruction
    - when cpu detects a controller sent an interrupt the interrupt handler...
      - saves any state it needs to
      - determines cause of interrupt
        - reads interrupt number
      - performs the computation
        - jumps to interrupt handler routine by using the interrupt number as an index into the interrupt vector (in memory table of various interrupt routines)
        - starts the routine associated with the interrupt index
      - restores state
      - executes return_from_interrupt

  - device controllers "raise" an interrupt
  - a cpu "catches" the interrupt
  - a cpu "dispatches" the interrupt to the handler
  - an interrupt handler "clears" the interrupt by servicing the interrupt

  - in reality, most CPUs have two interrupt lines
    - a nonmaskable interrupt which is reserved for events like unrecoverable memory errors
    - a maskable interrupt which means it can be turned off by the cpu during critical execution sequences that shouldn't be interrupted. Device controllers use the maskable interrupt to request service.
  - in addition to the maskability of some interrupts, interrupt priority is also implemented so that the cpu doesn't need to mask everything. Also allows for more important interrupts to be handled before the less important.

  **1.2.2 Storage Structure**

  - cpu only loads intructions from memory, thus programs need to be loaded into memory at some point before they can execute
  - general-purpose computers run most of their programs from rewriteable, main memory.
    - most main memory is based on DRAM (dynamic random-access memory)
  - other types of memory include:
    - EEPROM, electrically erasable programmable read only memory
      - might hold the bootstrap program, that is the program that will load the OS into memory
      - can be changed, but not frequently
      - it's slow, mostly used for static, unchanging programs that aren't used frequently
  - who can resist a nice refresher on storage?
    - a bit is the smallest unit of storage
    - a byte is 8 bits, yadda yadda yadda
    - most computers don't have instructions for moving a single bit but can move single bytes
    - all forms of memory is available as an array of bytes and each byte has its own address
  - a typical instruction-execution cycle
    - cpu fetches an instruction from memory and stores that instruction in the instruction register
    - the instruction is decoded and may cause operands to be fetched from memory and stored in another register
    - after any operands are fetched, the result of the execution may be stored back in memory

  - most systems can't store eveything that's needed in main memory so secondary storage is often provided
    - most common kinds of secondary storage are
      - nonvolatile memory (NVM)
        - example: flash drive
      - hard-disk drives (HDD)

  **1.2.3 I/O Structure**

  - I/O is complicated because it needs to be rock solid for a computer to be reliable and there's a huge variety of devices.
  - NVS (non volatile storage) brings a lot of overhead with I/O operations because the amount of data moved is high. To speed this up, `direct memory access` (DMA) is used.
    - once buffers, pointers and counters are set up, the device controller can bypass the CPU and transfer the data it needs to or from the device and main memory.

###### Section 1.3 - Computer-System Architecture

  **1.3.1 Single-Processor Systems**

  - in the days of yore, systems had one CPU with a single core
    - `core` denotes the part of the processor that executes intrsuctions and registers for storing local data
    - there were other special-purpose processors on board too like device-specific processors (disk, keyboard, etc)
      - an example, a disk-controller microprocessor receives a sequence of requests from the main CPU and implements its own disk queue and scheduling algo. This is helpful since it shifts the overhead of disk scheduling off of the CPU.

  **1.3.1 Multi-Processor Systems**

  - these systems will have two (or more) processors each with a single core CPU.
    - in this configuration the processors share the bus, and sometimes the clock, memory and peripheral devices
  - the most common multiprocessor systems use symmetric multiprocessing (SMP) in which...
    - each peer CPU performs all tasks, including OS functions and user processes
  - the main benefit to this is that many processes can simultaneously 
  
  - defition of multiprocessor has changed over time, now includes multicore systems
    - further improves the above situation because core to core communication can be faster that communicating across CPUs

  **1.3.1 Clustered Systems**

  - similar in structure to the previously defined multiprocessor systems except this configuration clusters together a number of separate systems (frequently multiprocessor themselves).
  - systems are similarly connected by a LAN or high throughput interconnect (see: InfiniBand)
  - typicall the goal for this configuration is highly available services
  - two sub-types
    - asymmetric clustering, one or more machines serve has a hot backup which takes over computing tasks if one of the primary systems fail
    - symmetrics clustering, all machines running workload, workloads of failed systems are picked up by operable peers

###### Section 1.4 - Operating-System Operations

  - ultimately, operating systems provide the environment in which programs can be executed
  - some common threads between the highly variable operating systems
    - bootstrap programs to load the operating system, usually stored in firmware
      - bootstrap programs also initialize registers, device controllers, and memory as mentioned above
      - bootstrap program will load the kernel to memory and then the kernel can begin to offer services to the system and users
      - some services are provided outside the kernel by system programs that are loaded into at boot time, those become system daemons
      - in linux, the first system program is `systemd` which starts other daemons. once this is done, the system is fully booted
  
  - we prevously talked about hardware interrupts but there are also `trap` or `exception` interrupts which are generated by software
    - caused by an error like divide by zero or invalid memory access
    - caused by a request from a user program which asks for a os service to perform a system call
  
  **1.4.1 Multiprogramming and Multitasking**

  - Multiprogramming in essence is the ability for the system to manage stop and start different tasks at different points when blocked. This way the cpu is never idle because the current program is waiting on something like io
    - in multiprogramming lexicon, a program is called a `process`

  - Multitasking is an extension of the multiprogramming concept where process hand-off happens frequently, without waiting on a blocking operation.
  - With multitasking it's entirely likely that multiple processes will want to execute at the same time and the cpu will need to choose amongst them. This is `cpu scheduling`

  - Both multitasking and multiprogramming systems must provide
    - a file system and these systems 
    - protection against innappropriate access of resources
    - process synchronization

  **1.4.2 Dual-Mode and Multimode Operation**

  - Many system resources are shared amongst users and programs so it's importnat for a good OS to ensure that incorrect or malicious programs cannot cause issues with other programs (the operating system included!)
  - a critical stepping stone for the above is the ability to distinguish between operating-system code and user-written code
  - a common approach is to provide hardware support for multiple modes of execution.
    - some modes (we need at least two)
      - `user mode` - 
      - `kernel mode` (or supervisor/system/privileged mode) - 
    - a bit, called the `mode bit` is added to harware to distinguish (`kernel(0)` and `user(1)`)
      - from the state of the bit, the system can infer the execution mode
    - when the system is executing on behalf of a user program, unsurprisingly it is in user mode
      - and yet, when the user program asks the operating system for something (via system call), the system transitions to kernel mode
    - that's all well and good but how does this help the os meet the requirements?
      - some instructions that are potentially harmful are marked as `privileges instructions`
        - obviously, privileges instructions can only execute in kernel mode
        - some examples of privileges instructions
          - io control
          - timer management
          - interrupt management
    - not all implementations are that simple, for instance
      - Intel has four separate `protection rings`
        - ring 0 is kernel mode
        - ring 3 is user mode
        - rings 2 and 4 can be used for various other system services
      - ARMv8 systems have seven modes
      - cpus that support virtualization tend to have a mode that indicates the virtual machine manage (VMM) is in control of the system
    
    - when a system call is executed, it is treated as a software interrupt...
      - control passes through the interrupt vector to a service routine in the os
      - and the mode bit is set to kernel mode
      - the kernel examines the interrupting instructions
      - kernel receives any additional parameters via registers
      - kernel determines validity and legality of the isntructions
      - and then executes and returns control to the instruction following the syscall

  **1.4.3 Timer**

  - A timer is used to help the operating system maintain absolute control of the cpu.
    - for instance, a rogue user program could get stuck in an infinite loop and never return control to the os
  - timers are usually set to interrupt the computer after a specified period.
  - before turning over control to the user the os ensures the timer is set to interrupt
    - when the interrupt happens, control is tranferred automatically to the os
    - the os can then determine to treat the interrupt as a fatal error or give the program more time
  
###### Section 1.5 - Resource Management

  **1.5.1 Process Management**

  - a process needs access to resources to accomplish its task, those resources are usually given to the process while it is running
  
  - a single-threaded process has one `program counter` specifying the location of the next instruction
  - a multi-threaded has multiple `program counters`, each pointing to the next instruction for the respective thread

  - the OS is responsible for these things wrt process management:
    - creating and deleting both user and system processes
    - scheduling processes and thread on the cpu
    - suspending and resuming processes
    - providing mechanisms for process synchronication
    - providing mechanisms for process communication

  **1.5.3 Memory Management**

  - memory is just a large array of bytes
    - every byte has its own address
  - for a program to be executed it must map to absolute addresses and loaded into memory

  - the OS is responsible for these things wrt memory management:
    - keeping track of which parts of memory are currently being used and which process is using them
    - allocating and deallocating memory space as needed
    - deciding which processes (or parts of processes) and data to move into and out of memory

  **1.5.3 File-System Management**

  - to make the system more convenient for users, the operating system should provide a uniform, logical view of information storage
  - the operating system abstracts from physical properties of the storage devices to define a logical storage unit, the file

  - the OS is responsible for these things wrt file system management:
    - creating and deleting files
    - creating and deleting directories
    - supporting primitives for manipulating files and directories
    - mapping files onto mass storage
    - backing up files on stable (non-volatile) storage media