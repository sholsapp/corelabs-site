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

**TODO(sholsapp):** talk about a single computer, the process abstraction,
resource management (including memory and cpu), signal handling, and more.

Things to remember:

- We have 2^64 addressable memory (although it's not all actually
addressable). That means we have a lot of burn.
- 

## a group of computers

**TODO(sholsapp):** talk about networking (with a focus on network speeds,
protocols), distributed computing (with a focus on log data structures),
coherence models, and more.

## a single, huge computer

**TODO(sholsapp):** talk about modeling a virtual memory manager as a
distributed log, coherence models applied as a threading interface, and fault
handling. Talk about applications that can be built atop this platform.
Introduce distributed shared memory infrastructure.
