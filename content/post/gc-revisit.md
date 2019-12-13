---
title: GC revisit
toc: true
id: 1383
categories:
  - JVM
date: "2015-06-27T15:50:04+00:00"
---

In the another post, I give a short introduction of GC.

Now for the practical purpose, I need to focus on the common used GCs:  Parallel GC, CMS and G1.

#### What is the tracing algorithm and mark-and-sweep method?

**root objects**

Root objects are the starting point of marking phase. Usually, root object references are located in the thread stacks.

![root-objects](/media/root-objects.png)

**object graph**

Every object has its fields, if a field is a object reference, then the reference will be like a pointer to another object.

From the point of whole heap, these objects will form an object graph.

#### Heap layout

##### generational heap

##### G1 heap

#### GC process illustrations

![non-concurrent-gc](/media/non-concurrent-gc.png)

#### tuning options

##### tuning the minor GC

##### tuning the full GC

&nbsp;
