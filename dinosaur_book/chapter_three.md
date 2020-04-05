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