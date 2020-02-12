# Chapter One :: Introduction
#### Chapter Objectives
  - Describe the general organization of a computer system and the role of interrupts.
    - A generalized computer system is typically composed of four primary components. Hardware (CPU, io devices, memory, storage), Application programs (such as those a user finds helpful, word processesors, compilers, browsers), a user, and the operating system which allocates and orchestrates the hardware for application programs and, ultimately, the user.
  - Describe the components in a modern multiprocessor computer system.
    - *NEEDS ANSWER*
  - Illustrate the transition from user mode to kernel mode.
    - *NEEDS ANSWER*
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

  - 