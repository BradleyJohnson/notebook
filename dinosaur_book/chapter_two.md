# Chapter One :: Operating-Systen Structures
#### Chapter Objectives

  - Identify services provided by an operating system
    - NEEDS ANSWER
  
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




