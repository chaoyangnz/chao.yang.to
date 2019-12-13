---
title: 'JMM, Atomicity,  Visibility and Reordering'
toc: true
id: 255
categories:
  - Concurrency
  - Multithreading
date: "2015-01-21T09:36:18+00:00"
---

Memory that can be shared between threads is called _shared memory_ or _heap memory_. The term _variable_ as used in this section refers to both fields and array elements. Variables that are shared between threads are referred to as shared variables. All instance fields, `static` fields, and array elements are shared variables and are stored in heap memory. Local variables, formal method parameters, and exception handler parameters are never shared between threads and are unaffected by the memory model.

In modern shared-memory multiprocessor architectures, each processor has one or more levels of cache that are periodically reconciled with main memory as shown in the following figure:

![](https://www.securecoding.cert.org/confluence/download/attachments/65340568/cache.jpg?version=1&amp;modificationDate=1305747075000&amp;api=v2)

#### Visibility

The visibility of writes to shared variables can be problematic because the value of a shared variable may be cached; writing its value to main memory may be delayed. Consequently, another thread may read a stale value of the variable.

Cause:

*   memory caching
*   compiler optimizations
&nbsp;
