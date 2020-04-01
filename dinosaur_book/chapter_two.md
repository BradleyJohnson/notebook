# Chapter One :: Operating-Systen Structures
#### Chapter Objectives

  - Identify services provided by an operating system
    - File management
      - create, delete, copy, rename, print, list, access and manipulate files
    - Status information
      - get date/time, amount of available memory/disk space, number of users
      - detailed performance, logging, debugging information
    - File modification
      - text editors
    - Programming-language support
      - compilers, assemblers, debuggers, interpreters
    - Program loading and execution
      - absolute loaders, relocatable loaders, linkage editors, overlay loaders
    - Communications
      - send/receive messages between processes, users, computer systems
    - Background services
  
  - Illustrate how system calls are used to provide operating system services
    - NEEDS ANSWER
  
  - Compare and contrast monolithic, layered, microkernel, modular, and hybrid strategies for designing operating systems
    - NEEDS ANSWER
  
  - Illustrate the process for booting an operating system
    - NEEDS ANSWER
  
  - Apply tools for monitoring operating system performance
    - NEEDS ANSWER
  
  - Design and implement kernel modules for interacting with a Linux kernel
    - NEEDS ANSWER

---

###### Section 2.1 - Operating-System Services

  - As mentioned numerous times, the OS provides an environment in which to execute program. 
    - it does this, partly, by making services available to programs and users of those programs
    - these services vary widely amongst operating systems, but a few archetypes exist
      - User Interface. Can be graphical window system or just a CLI for example
      - Program Execution. The system will load and execute a program
      - I/O Operations. A running program might require io, many reasons not to let the user do io on their own
      - File-system manipulation. Of course users and programs need to interact with files. OS does this and more (permissions, etc)
      - Communications. Interprocess locally, over network.
      - Error detection. Crucial that the OS detects erorrs and ideally can recover from them
    - there's another class of services that are less obvious but still important
      - resource allocation
      - logging
      - protection and security

###### Section 2.2 - User and Operating-System Interface.

  - **2.2.1 Command Interpreters**
  
  - Interpreters or shells live to get and execute the next user command
    - Commands are generally implemented in one of two ways.
      - the interpreter contains the code to execute the command
      - the interpreter executes commands through system programs

  - **2.2.2 Graphical User Interface**

  - classic windowing system
  - first GUI appeared on the Xerox Alto in 1973

  - **2.2.3 Touch-Screen Interface**

  - Not much to add, a sub-type of the GUI

  - **2.2.4 Choice of Interface**

  - It's like, personal preference or a matter of what work one is doing.... or something.

###### Section 2.3 - System Calls

  - the simplest description is that `system calls` provide an interface to the services the OS provides
    - some sys calls are made available as a C or C++ function
    - sys calls interacting directly with hardware might be written in assembly

  - **2.3.2 Application Programming Interface**

  - most common APIs for application programmers
    - Windows API
    - POSIX API (nearly all UNIX, Linux, macOS)
    - Java API for JVM
  - programmers access the the api via a library
    - in UNIX/Linux C programs, this is `libc`
  
  - another factor in handling system calls is the run-time environment (RTE)
    - composed of all the software needed to execute an application
      - compilers/interpreters, libraries, etc
  - the RTE provides a system-call interface which intercepts function calls in the API and invokes the correct system call

  - **2.3.3 Types of System Calls**

  - system calls can be grouped into six major categories
    - process control
      - create/terminate processes
      - load/execute processes
      - get/set process attrs
      - wait/signal event
      - allocate and clear memory
    - file management
      - create/delete files
      - open/close file
      - read/write/reposition file
      - get/set file attrs
    - device management
      - request/release device
      - read/write/reposition device
      - get/set device attrs
      - logically attach/detach device
    - information maintenance
      - get/set time or date
      - get/set system data
      - get process, file, or device attrs
      - set process, file, or device attrs
    - communications
      - create/delete communication connection
      - send/receive messages
      - transfer status information
      - attach/detach remote devices
    - protection
      - get/set file permissions

  - **2.3.3.1 Process Control**

  - does some obvious things like start and terminate processes but here's a little more interesting example
    - two processes are both accessing the same block of memory and it would be problematic for one process to change it while the other is reading it. therefore, the system call functions `acquire_lock()` and `release_lock()` are provided to make this concurrent access safe.

  - **2.3.3.2 File Management**

  - create() and delete() files of course
    - open(), read(), read(), write(), reposition(), close()
    - most of these need to work on directories too
    - get and set file attrs
    - move() and copy() offered in some OSes

  - **2.3.3.3 Device Management**

  - The various resources of a computer system can also be thought of as devices
    - memory, disk, etc
  - a multi-user system might require a request() system call to a device to ensure that the user has exclusive access
    - after we're done we can release() it
    - not all systems require managed access like this but it opens the possibility of device contention and deadlock
    - in UNIX io devices and files are so similar they use much of the same system calls

  - **2.3.3.4 Information Maintenance**

  - giving access to system information and meta data
    - time, date, verions of OS
    - amount of free memory or disk space
    - this is also where memory dumps fall
    - information about existing processes

  - **2.3.3.5 Communication**

  - two main methods for inter-process communication
    - message-passing
      - two or more processes send messages amongst themselves
        - done by either sending direct messages
          - open_connection(), close_connection(), accept_connection()
            - then, read_message() or write_message()
        - or a sort of dead-drop/mailbox system in the form of a file
          - messages are read through file syscalls like open() and read()
    - shared-memory
      - shared_memory_create(), shared_memory_attach()
      - the rest is mostly self explanatory, messages are left in shared memory that both processes have access to

  - **2.3.3.6 Protection**

  - set_permission(), get_permission, etc are used to manage access to resources (disks, memory, files)
  - allow_user(), deny_user() set similar permissions for individual users

###### Section 2.4 - System Services

  - System service might also be called system utilities and we've basically already covered these.
    - Some services are just interfaces to system calls and others might be a lot more complex

  - File management
    - create, delete, copy, rename, print, list, access and manipulate files
  - Status information
    - get date/time, amount of available memory/disk space, number of users
    - detailed performance, logging, debugging information
  - File modification
    - text editors
  - Programming-language support
    - compilers, assemblers, debuggers, interpreters
  - Program loading and execution
    - absolute loaders, relocatable loaders, linkage editors, overlay loaders
  - Communications
    - send/receive messages between processes, users, computer systems
  - Background services
    - daemons

###### Section 2.5 - Linkers and Loaders

  - Before it can run on a CPU, a binary executable file must be brought into memory...
    - source files are compiled into object files
    - object files are loaded into any memory location and known as relocatable object file
    - a linker combines (multiple in most cases) relocatable object files into a single binary executable file
      - during this phase, other depedencies might be linked as well
    - a loader is then used to load the binary executable file into memory where it can be run by the CPU
  
  - Some systems allow for dynamically linked library, where dependencies are linked as as the executable is loaded
    - this can allow multiple processes to share linked libraries and save memory
  
  - object files and executable files usually have a standard format that includes
    - the compiled machine code
    - symbol table for metadata for functions and variables
    - in Unix and Linux this format is ELF, Executable and Linkable Format

###### Section 2.6 - Why Applications Are Operating-System Specific

  - Nothing too surprising here. System calls are implemented differently across various OSes.

  - Some ways to make applications available on more than 1 OS
    - Use an interpreted language that has interpreters for multiples OSes
    - Use a language that runs its own virtual machine
    - Use a standard language or API that generates binaries to execute on the OS

  - Other obstacles
    - operating system library APIs that offer functionality to applications vary in their implementation
    - each OS expects differing binary formats for applications
    - different CPUs have different instruction sets (that's not really on the OS though?)
  
  - an Application Binary Interface (ABI)...
    - defines how different components of binary code can interface for a given operating system on a given architecture
    - specifies low-level details like address width, methods for passing params to syscalls, organization of the run-time stack, binary format of system files, size of datatypes, etc
    - is usually specified for an architecture
      - for instance there is an ABI for ARMv8
      - so it's the architectural equivalent of an API

###### Section 2.7 - Operating-System Design and Implementation

  - **2.7.1 Design Goals**

  - Who/what are we designing for is primary design consideration.
    - mobile, desktop, distributed, real-time?
  - Beyond this, things get murky but there's two general guideposts
    - user goals
      - goals that achieve something for the end user eg
        - reliable, convenient, easy to learn/use, safe, fast, etc
        - these aren't terribly useful because they can be achieved in many different ways
    - system goals
      - goals that achieve something for the designers, developers, operators of the OS
        - flexible, reliable, error free, effeicient
        - again these can be interpreted many ways

  - **2.7.2 Mechanisms and Policies**

  - Separating mechanism and policy is an important design principle
    - mechanism describes the 'how' to do something
    - policy describes the 'what' needs doing
    - for example, the timer contrsuct is a mechanism for cpu protection and deciding how long the timer is to be set if the policy decision

    - this separation improves flexibility because
      - policies are likely to change across time and place
        - If the policy and mechanism are intertwined, this means that the mechanism needs to be changed everytime the policy is changed
        - for example, consider a mechanism that gives preference on CPU time to one type of program over another...
          - when properly separated the mechanism can work to give io intensive applications preference over CPU intensive applications, or vice versa.

  - **2.7.3 Implementation**

  - most early OSes were written entirely in assembly
  - now some of the lowest level kernel modules are assembly but much of it is C or C++
  - this helps portability, readiability, debugging, etc

###### Section 2.8 - Operating-System Structure

  - In order to increase flexibility and make it easier to change over time, most OSes are designed as a number of modules that are separate so they can be easily changed.
    - each module has separate, well-defined concerns and APIs
  - Nevertheless, there are a number of common design choices here...
  
  - **2.8.1 Monolithic Structure**
    - No structure at all.
    - Complex and hard to change but some performance benefit is realized from using a single file
    - All kernel functionality in a single binary file that runs from a single memory location
    - Linux is an example of this (kernel is a single bin file) but does a clever thing and allows for the kernel to be modified at run time and add or remove modules.

  - **2.8.2 Layered Approach**
    - system is designed with smaller, loosely coupled components
    - simple to change and debug
    - generally poor performance

  - **2.8.3 Microkernels**
    - removes all non-essentials components from the kernel and implementing them as user-level programs that live in a separate address space
    - obviously, there's disagreement about what is essential
      - generally though there's cpu/process/memory management and communications facility
    - increases extensibility because all new services are added to user-space
    - easier to maintain because of the reduced kernel surface area
    - increased reliability and security because most services are running as user and therefore restricted
    - Darwin/Mach is an example of a microkernel
    - some performance overhead from increased system function calls

  - **2.8.4 Modules**
    - kernel has a set of core components and can link in additional services via modules at boot or run time
      - these are Loadable Kernel Modules (LKMs)
    - similar to but more efficient than microkernels because it does not need to invoke message passing for communication
  
  - **2.8.5 Hybrid Systems**
    - Most operating systems end up as a hybrid, like Linux for example:
      - It's monolithic because the operating system runs from one file in the same address space
      - It's also modular because services can be dynamically added to the kernel

###### Section 2.9 - Building and Booting an Operating System