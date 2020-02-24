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