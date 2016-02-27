---
title: A single, huge computer
author: Stephen Holsapple
subtitle: How to make a super computer
layout: post
date: 2016-01-01
img: startup-framework.png
thumbnail: startup-framework-thumbnail.png
alt: image-alt
category: engineering
description: A discussion of the implementation of distributed shared memory infrastructures.

---

-----------

The first question that everyone asks when we say we're building a distributed
shared memory infrastructure is: what's that? The answer is always: the same
thing that you have on your desk, just bigger, maybe. This simple answer lends
itself to a deeper discussion, and that is the topic of this essay.

Before diving further, you should know a few things about computers. Namely,
you should have a cursory understanding of how operating systems work and the
hardware that they manage. You should have a casual understanding of major systems
programming concepts such as processes, threading, signal handling, memory
management, and maybe more. There is a smattering of C/C++ programming concepts
throughout the essay but we'll explain them as we go.

## a single computer

A single computer is a machine that manages hardware to provide a rudimentary
software abstraction that people like you and I can use to build useful things
on top of. We build these things with programming languages that the computer
knows how to interpret into actions against the hardware that it manages, be it
a computer monitor that displays pixels, a network card that sends bits over a
wire, a memory chip that stores bits, or a processor that adds things together.
Usually, a single action isn't especially useful by itself, so we group
together actions into programs and run them as processes.

The process is a key abstraction provided by the operating system. A process is
an *instance* of a program. Each instance has state, like memory, that the
operating system manages that instance, the process, can read and write to.
This management comes for free and is referred to as *the process abstraction*.
The process abstraction is the reason why we as programmers do not need to
think about managing the physical hardware ourselves: the operating system
abstracts this from us. Curious readers can read [The Abstraction: The
Process](http://pages.cs.wisc.edu/~remzi/OSTEP/cpu-intro.pdf) for a deeper
discussion of the process abstraction.

One of the most interesting resources that the process uses is *memory*. The
process's program, its data, its stack, everything, exists in the memory that
the operating system leased to it.

A process interacts with the operating system using *system calls*. These are
special functions that the operating system provides in order to allow the
process to request resources from it. For example, if a process wants to get
memory from the operating system, it asks for memory using `sbrk(2)` or
`mmap(2)`.  These special functions are clunky to work with directly so
standard libraries like libc provide functions like `malloc(3)` to make
interacting with the operating system more convenient.

Two processes that ask for memory from the operating system will notice that
the addresses of each of the memory blocks returned to the processes *may* be
the same. This is because the operating system *virtualizes* the physical
resources to make them easier to manage. We say that a process runs in *virtual
memory* because that memory is isolated to just the process. Therefore, each
process has the capacity to address *all* of its memory, called the *address
space*. It is the job of the operating system to map each process's virtual
memory into physical memory.

On a 64-bit architecture, a process can use 2^48 bits to address memory, which
corresponds to 256 Tebibytes (Terabytes, in binary units) of memory. This is
typically more memory than can fit into any single machine, so this address
space is never fully used.

When an operating systems wants to tell a process something, it generates a
signal for that process. A signal is a primitive operation that interrupts what
a process might be doing so that the signal can be serviced by the process.
Some signals are informative, while others require an action be taken. For
example, an operating system might send a `SIGCHLD` signal is generated in a
process if and when one of its children processes terminates, which may or may
not be interesting to the parent process. Other signals, like `SIGKILL` or
`SIGABRT`, notify the process that it is being terminated.

One of the most famous signals is sent when a process makes an invalid virtual
memory reference: `SIGSEGV`. A segmentation fault generates a signal to the
process in order to see if the process can recover somehow from the
segmentation violation. In typically program, a segmentation fault indicates a
logic or state error. However, as we'll see, this signal can be leveraged to
take corrective action.

## a group of computers

When we talk about groups of computers, we necessarily talk about networks. A
network is made possible by network interfaces: hardware components that know
how to send bits over a wire. Computer networks have becoming increasingly fast
over the past decades, but they don't match the speeds of memory or processors.
Still, as some describe, we're in a golden age of networking, and we're seeing
network speeds increasing at impressive rates.

> “We are in the Golden Age of networking, driven by Moore’s Law,”
>
> *Andy Bechtolsheim, 2012*

In addition to just the physical components, there is a plethora of software
that makes networking possible, ranging from router algorithms, protocols, to
domain name services. We will not talk about these software components of
networking in this essay.

What we will talk about is writing software that makes use of a group of
computers. As we know now, a process runs on a single computer managed by a
single operating system, so managing shared state among a group of computers is
more challenging. This area of computing is known as distributed computing and
is well studied but still evolving.

One major camp of distributed systems suggest modeling distributed state as a
log based data structure.

**TODO(sholsapp):** talk about networking (with a focus on network speeds,
protocols), distributed computing (with a focus on log data structures),
coherence models, and more.

## a single, huge computer

**TODO(sholsapp):** talk about modeling a virtual memory manager as a
distributed log, coherence models applied as a threading interface, and fault
handling. Talk about applications that can be built atop this platform.
Introduce distributed shared memory infrastructure.


# resources

https://en.wikipedia.org/wiki/SPMD
https://en.wikipedia.org/wiki/MIMD
https://en.wikipedia.org/wiki/Non-uniform_memory_access
https://en.wikipedia.org/wiki/Locality_of_reference
https://en.wikipedia.org/wiki/Computer_cluster
https://en.wikipedia.org/wiki/Single_system_image
https://en.wikipedia.org/wiki/MOSIX
https://en.wikipedia.org/wiki/LinuxPMI
https://en.wikipedia.org/wiki/Plan_9_from_Bell_Labs
