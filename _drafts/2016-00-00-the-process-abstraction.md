---
title: The process abstraction
author: Stephen Holsapple
subtitle: Where software meets hardware.
layout: post
date: 2016-01-01
img: startup-framework.png
thumbnail: startup-framework-thumbnail.png
alt: image-alt
category: engineering
description: The operating systym abstraction.

---

-----------

The process is arguably *the* key abstraction provided by the operating system.

An operating system is itself a sort of process. It manages hardware to provide
a rudimentary software abstraction that people like you and I can use to build
useful things on top of. We build these things with programming languages that
the computer knows how to interpret into actions against the hardware that it
manages. Usually, a single action isn't especially useful by itself, so we
group together actions into programs and run them as processes.

```
#include <stdio.h>

int main(int argc, char *argv[]) {
  printf("Hello, world.\n");
  return 0;
}
```

We understand that this snippet of ANSI C code can be compiled into a program
`a.out`. `a.out` lives on disk. It's not alive, yet, but has everything that is
needed to run on the operating system and architecture that it was compiled to
live on. We can learn about our program using tools like `nm`, `readelf`, or
`objdump`, to discover, for example, single actions that have been grouped together to formaulate the `main` function..

```
$ objdump -D a.out

0000000000400506 <main>:
  400506:       55                      push   %rbp
  400507:       48 89 e5                mov    %rsp,%rbp
  40050a:       48 83 ec 10             sub    $0x10,%rsp
  40050e:       89 7d fc                mov    %edi,-0x4(%rbp)
  400511:       48 89 75 f0             mov    %rsi,-0x10(%rbp)
  400515:       bf b4 05 40 00          mov    $0x4005b4,%edi
  40051a:       e8 c1 fe ff ff          callq  4003e0 <puts@plt>
  40051f:       b8 00 00 00 00          mov    $0x0,%eax
  400524:       c9                      leaveq
  400525:       c3                      retq
  400526:       66 2e 0f 1f 84 00 00    nopw   %cs:0x0(%rax,%rax,1)
  40052d:       00 00 00
```

This program is nothing more an instruction manual to the operating system to
create a running, living thing: the process. A process is an *instance* of a
program. Each instance has state, like memory, that the operating system
manages that instance, the process, can read and write to. This management
comes for free and is referred to as *the process abstraction*.

A process is composed of many resources. Two of the most interesting resources
that the process uses is *memory* and *cpu*. The process's program, its data,
its stack, everything, exists in the memory that the operating system leased to
it.

> Instructions lie in memory; the data that the running program reads and
> writes sits in memory as well. Thus the memory that the process can address
> (called its address space) is part of the process.

That is, once a program is linked, loaded, and run, it exists entirely in a
region of memory. This is an subtle but important attribute that makes writing
memory allocators and user space threading libraries possible.

A process interacts with the operating system using *system calls*. These are
special functions that the operating system provides in order to allow the
process to request resources from it. For example, if a process wants to get
memory from the operating system, it asks for memory using `sbrk(2)` or
`mmap(2)`.  These special functions are clunky to work with directly so
standard libraries like libc provide functions like `malloc(3)` to make
interacting with the operating system more convenient.

## virtualization

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
