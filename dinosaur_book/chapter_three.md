# Chapter Three :: Processes
#### Chapter Objectives

  - Identify the separate components of a process and illustrate how they are represented and scheduled in an Operating System
  - Describe how processes are created and terminated in an operating system, including developing programs using the appropriate systems calls that perform these operations
  - Describe and contrast interprocess communication using shared memory and message passing
  - Design programs that use pipes and POSIX shared memory to perform interprocess communication
  - Describe client-server communication using sockers and remote procedure calls
  - Design kernel modules that interact with the Linux operating system.

---

###### Section 3.1 - Process Concept

  - **3.1.1 The Process**
    - as stated several times before, a process is a program in execution
    - the status of a process is represented by the value of the program counter and the contents of the registers
    - the memory layout of a process is typically divided into sections, usually including:
      - Text section -- the executable code
        - memory size is fixed
      - Data section -- global variables
        - memory size is fixed
      - Heap section -- memory that is dynamically allocated during program run time
        - can shrink and grown dynamically during run time
        - the heap will grow dynamically when needed and shrink as memory is returned to the system
      - Stack section -- temporary data storage when invoking functions (such as function parameters, return addresses, local variables)
        - can shrink and grown dynamically during run time
        - each time a function is called, an activation record containing function parameters, local variables, and the return address is pushed onto the stack
          - when control is returned from the function, the activation record is popped off the stack 

       max  +------------+
            |   stack    |
            +-----|------+
            |     ▼      |
            |            |
            |     ▲      |
            +-----|------+
            |   heap     |
            +------------+
            |   data     |
            +------------+
            |   text     |
         0  +------------+

      - the stack and heap grow towards each other but must not overlap

  - **3.1.2 Process State**
    - as a process executes, it changes state
    - states include:
      - New - process is being created
      - Running - instructions are being executed
      - Waiting - process is waiting for an event
      - Ready - process is waiting to be assigned to a processor
      - Terminated - process is finished

  - **3.1.3 Process Control Block**
    - each process is represented by a process control block (PCB)
       
       PCB  +------------------+
            |   process state  |
            +------------------+
            |  process number  |
            +------------------+
            |  program counter |
            +------------------+
            |                  |
            |    registers     |
            |                  |
            +------------------+
            |   memory limits  |
            +------------------+
            |list of open files|
            +------------------+
            |       etc        |
            +------------------+

    - Process State: current state
    - Program Counter: address of the next instruction to be executed for the process
    - CPU Registers: information about the registers being used
    - CPU-Scheduling info: includes process priority, pointers to scheduling queues, and other scheduling parameters
    - Memory-management info: value of base and limit registers and page/segment tables
    - Accounting info: includes the amount of CPU and real time used, time limits, account numbers, etc
    - I/O status info: includes list of io devices allocated to the process, a lit of open files, etc

    - Ultimately, the PCB contains some metadata and everything needed to start, stop, or resume the process

  - **3.1.4 Threads**
    - Most operating systems have extended the process concept to allow a process have multiple threads and execute multiple instructions
    - in OSes that support threading, the PCB includes information about threads

###### Section 3.2 - Process Scheduling

  - the objective of multitasking is to have some process runing at all times to maximize CPU utilization
  - the objective of time sharing is to switch a CPU among competing processes
  - a cpu scheduler is how to mesh these objectives which can be at odds in some cases
    - cpu scheduler selects an available process to run on a processor
      - in single core systems there will only ever be a single process executing
      - multicore processes can run one process per core

  -**3.2.1 Scheduling Queues**
    - a new process that's `ready` will be put in the Ready Queue and the ready queue header will contain a reference to the first PCB and each PCB includes a pointer field that references the next PCB in the queue
    - a process that has request some io or network activity will be put into a Wait Queue while it waits on the event to complete
    - similarly, a process might be interrupted or run out of its alotted time in which case it will be put back into the ready queue

  -**3.2.2 CPU Scheduling**
    - a process lives its life migrating in and out of the ready and wait queues but it's the responsibility of the CPU Scheduler to pick from amongst the processes and apply it to a core
    - some systems support `swapping` which will take a process out of memory and thus contention for CPU and thereby reduce overall load and later load the process back and continue with its execution.

  -**3.2.3 Context Switch**
    - 