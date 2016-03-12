---
title: A single, huge computer
author: Stephen Holsapple
subtitle: How we're building a super computer
layout: post
date: 2016-03-12
img: startup-framework.png
thumbnail: startup-framework-thumbnail.png
alt: image-alt
category: engineering
description: Super computers in the real world.
tags:
- distributed shared memory
- dsm
- software distributed shared memory
- sdsm
- single system image
- ssi
- super computer
- multicomputer

---

-----------

We're building a single, huge computer with a simple recipe composed of old
ideas mixed up in a new way. These systems have been built before a number of
times, but none of these systems have survived the test of time. We're doing it
again partially out of stupidity, but mostly because it's a fascinating.

## two different kinds

There are a number of approaches to build a single, huge computer out of many
small computers. _This is not a new idea._ Like other technologies, the idea of
a single, huge computer has been implemented before, but has not survived the
test of time for two reasons: _cost of entry_ and _performance_.

Two approaches to building a single, huge computer out of many range from
single system images to distributed shared memory machines, with lots of
hybrids in between.

A single system image is an approach to computing that provides the illusion of
a single computer at the operating system level, and are typically implemented
in or below the kernel. They're extremely powerful but have a rather high _cost
of entry_ since it involves complete adoption.

> A single system image (SSI) is the property of a system that hides the
> heterogeneous and distributed nature of the available resources and presents
> them to users and applications as a single unified computing resource.

A distributed shared memory machine takes a different approach by targeting the
process and providing operating system like services, like virtual memory
management. These approaches can be implemented in kernel or user space.
Despite what your intuition might tell you, these systems _can_ be implemented
efficiently in user space. Distributed shared memory systems like Treadmarks,
Munin, Shasta, and Grappa, are all implemented in user space.

> A distributed-memory system (often called a multicomputer) consist of
> multiple independent processing nodes with local memory modules which is
> connected by a general interconnection network. Software DSM systems can be
> implemented in an operating system, or as a programming library and can be
> thought of as extensions of the underlying virtual memory architecture.

Each approach has advantages and disadvantages. For example, an SSI approach
makes capturing certain types of kernel state such as socket connections or
process state possible, which would be hard or impossible to do with a software
distributed shared memory state. On the contrary, a software distributed shared
memory system tends to be portable and expose more to the application
developer.

The commonality in both of these types of system is that they maintain _the
process abstraction_. We feel that maintaining the process abstraction is
critical in keeping systems simple, which in turn makes them easy to develop,
deploy, and maintain.

Yet other systems exist in a hybrid space that employ many of the same
practices that SSI or distributed shared memory systems. These systems like
those like RAMCloud, which has been shown to be extremely performant! Systems
like these typically employ shared memory or global address spaces but do not
maintain the process abstraction.

## the new contender

We're deciding to ease cost of entry by implementing our single, huge computer
as a software distributed shared memory system in user space. We bet that
trends in computing and improvements in hardware will make these systems not
only efficient, but advantageous. We look at the fact that exposing more to the
developer is a feature: by exposing an interface to the develop that solves
distributed computing problems in a library, we make their lives easier, and
give them the opportunity to optimize their use of the library for their given
application.

In doing this, we feel like we address _both_ of the challenges for new
multicomputer technologies: _cost of entry_ and _performance_.

We're planning on using our software distributed shared memory system as a
C/C++ library to implement several technologies that exploit the interface
provided by our library. Stay tuned for additional essays that discuss our
approaches for implementing the software distributed shared memory system as a
C/C++ library, a distributed in-memory filesystem, a distributed in-memory
cache, and last but not least, a distributed java virtual machine.

> The greatest performance improvement of all is when a system goes from
> not-working to working

Right now, we're focused on learning and having fun. We're embracing one of
John Ousterhout's favorite sayings, and striving for a proof-of-concept in
2016. We hope that the work that we do can help simplify the programming model
at the same time as leveraging trends in computing.

If you're interested, follow our project on
[github](https://github.com/sholsapp/gallocy). We're looking for developers to
help out in both the implementation of the core library as well as target
applications we mentioned above. If you have ideas for other applications that
may benefit from using our core library, reach out to us!
