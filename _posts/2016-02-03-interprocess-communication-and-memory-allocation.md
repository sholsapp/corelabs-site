---
title: ipc + malloc
author: Stephen Holsapple
subtitle: Experiment 30.1
layout: post
date: 2016-03-02
img: startup-framework.png
thumbnail: startup-framework-thumbnail.png
alt: image-alt
category: engineering
description: An experiment for gallocy/issues/30 that implements a basic IPC scenario with a custom memory allocator.

---

--------

This experiment verifies that it is possible to implement a simple application
that uses Unix domain sockets for interprocess communication that does not
implicitly use `malloc`. The primary motivation behind this experiment is to
answer questions raised in
[gallocy/issues/30](https://github.com/sholsapp/gallocy/issues/30) - which
discuss a way to cleanly design a complex memory allocator..

## about

There are various ways to acheive interprocess communication using files,
signals, sockets, pipes, shared memory, and more. The first question we'd like
to answer is: why Unix domain sockets?

> The API for Unix domain sockets is similar to that of an Internet socket, but
rather than using an underlying network protocol, all communication occurs
entirely within the operating system kernel.

We chose to conduct the experiment with Unix domain sockets because they are
efficient, simple to reason about, and fit nicely into the client/server
paradigm. Although we've chosen to move forward with Unix domain sockets, we
can implement a similar API using shared memory and memory barriers if we find
this is a performance bottleneck in the future.

## control

We started with [a simple socket
example](https://www.cs.cf.ac.uk/Dave/C/node28.html) that implements a socket
server and a socket client. The two programs are hard wired to send and receive
three strings. The two programs specify their desire to use Unix domain sockets
during their call to `socket` by specyfing the `AF_UNIX` family.

We compile the socket server and client as stand alone applications and run
them in separate terminals. *Note, you will not see the output from either
application until both applications are started.*

First, compile and run the server:

```
gcc socket_server.c -o server
./server
This is the first string from the client.
This is the second string from the client.
This is the third string from the client.
```

Second, compile and run the client:

```
gcc socket_client.c -o client
./client
This is the first string from the server.
This is the second string from the server.
This is the third string from the server.
```

We know that both applications compile and execute as expected and therefore
establish a reasonable baseline for behavior.

## experiment

We include a custom `malloc` definition using function interposition so that
we can notify ourselves via standard output if memory is implicitly allocated
by these programs. This custom definition simply prints to standard output
before calling the standard library's original version of `malloc`.

```cpp
#define _GNU_SOURCE
#include <dlfcn.h>
#include <stdio.h>
#include <stdlib.h>
typedef void *(*malloc_function)(size_t);
void *malloc(size_t sz) {
    printf("custom malloc(%d)\n", sz);
    malloc_function __libc_malloc = (malloc_function) ((int *) dlsym(RTLD_NEXT, "malloc"));
    return __libc_malloc(sz);
}
```

We compile our custom `malloc` code into a shared object.

```
gcc -fPIC -shared my_malloc.c -o libmymalloc.so
```

Using the `LD_PRELOAD` environment variable we intercept calls to `malloc`
within an application of our choice. We choose to *client* arbitrarily.

```
LD_PRELOAD=`pwd`/libmymalloc.so ./client
custom malloc(568)
This is the first string from the server.
This is the second string from the server.
This is the third string from the server.
```

We see that *client* is implicitly using memory since our custom `malloc`
implementation reports on standard output that 568 bytes have been allocated.
We can use tools like *gdb* with breakpoints on `malloc` to learn that it is
the standard library (i.e., libc) that is the caller of `malloc`, in
particular, calls to *fdopen*.

We verified this was the case by rewriting socket_client.c so that it does not
use the standard library. This involved replacing libc functions `fdopen` and
`fgetc` with equivalent code that only uses system calls. In the examples
disucssed in this experiment, those functions were `socket`, `read`, and
`write`.  After these adjustments, we see the output revert back to the control
output, indicating that `malloc` is not implicitly used when using only system
calls.

## conclusion

We *can* implement a simple IPC system using Unix domain socks that *does not
implicitly use `malloc`*. This is important for the discussion in
[gallocy/issues/30](https://github.com/sholsapp/gallocy/issues/30) since it
implies that a multiprocess design can be written in a way that makes problems
discussed in [gallocy/issues/20](https://github.com/sholsapp/gallocy/issues/20)
and [gallocy/issues/25](https://github.com/sholsapp/gallocy/issues/25)
unncessary to solve.
