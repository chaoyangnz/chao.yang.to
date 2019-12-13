---
title: Garbage Collection
toc: true
id: 975
categories:
  - JVM
date: "2015-04-20T14:13:28+00:00"
---

References:

*   &lt;[Inside the Java Virtual Machine](https://www.artima.com/insidejvm/ed2/index.html)&gt;
*   &lt;Java Performance - The Definitive Guide&gt;

### GC overview

#### Heap Layout

![Hotspot-VM-heap-layout](/media/Hotspot-VM-heap-layout.png)

#### Geneation spaces

Nearly every JVM uses generational garbage collectors. They work by splitting the heap into different generations:

*   Old generation (tenured generation)
*   Young generation, which is further divided into sections: Eden and Survivor spaces.
![young-generation](/media/young-generation.png)

#### minor GC

GC for the young generation is called **minor GC**. All GC algorithms have _**stop-the-world**_ pauses during collection of the young generation.

*   Objects that are no longer in use are discarded, and objects that are still in use are moved elsewhere.
*   all objects in eden are either moved to the unused survivor space(To survivor space) or discarded:
*   all objects in From survivor space are either discarded or moved to the unused survivor space(To survivor space) or moved to the old generation (when still liveable after a few minor GC).
*   Since all objects are moved, the young generation is automatically compacted when it is collected.
*   After every minor GC, the From survivor space and To survivor space are swapped.
![minor-gc](/media/minor-gc.png)

#### full GC

GC for the old generation collection is called **full GC**. Concurrent collectors scan for unused objects can occur without stopping application threads, such as CMS and G1.

### Garbage collectors

Early GC uses **reference counting** strategy. But now nearly all the GCs use **Tracing** garbage strategies, which trace out the graph of object references starting with the root nodes.

The basic tracing algorithm is called "mark and sweep" :

*   In the mark phase, the garbage collector traverses the tree of references and marks each object it encounters.
*   In the sweep phase, unmarked objects are freed, and the resulting memory is made available to the executing program.
*   In the JVM, the sweep phase must include finalization of objects.
Two strategies for defragmentation: **compacting** and **copying**

*   **compacting **algorithm** **slides live objects over free memory space toward one end of the heap
*   **copying **algorithm moves all live objects to a new area, placed side by side with current area.
<table border="1" width="1207">
<tbody>
<tr>
<td></td>
<td>**serial GC**</td>
<td>**throughput / parallel GC**</td>
<td>**CMS GC**</td>
<td>**G1**</td>
</tr>
<tr>
<td>designed for</td>
<td>client machine: 32bit JVM on Windows or single processor machine (default)</td>
<td>server machine: multi-CPU Unix machine or 64-bit JVM (default)</td>
<td></td>
<td>process large heaps (greater than
about 4 GB) with minimal pauses  divides the heap into a number of regions</td>
</tr>
<tr>
<td>minor GC</td>
<td>performs with single thread  stop all application threads</td>
<td>performs with multiple threads  stop all application threads</td>
<td>performs with multiple threads  stops all application threads</td>
<td>performs with multiple threads  stops all application threads</td>
</tr>
<tr>
<td>full GC</td>
<td>performs with single threads  stop all application threads</td>
<td>performs with multiple threads  stop all application threads</td>
<td>uses one or more
background threads to periodically scan through the old generation  don't stop application threads</td>
<td>processed by background threads  don’t need to stop the application threads</td>
</tr>
<tr>
<td>flag</td>
<td>-XX:+UseSerialGC</td>
<td>-XX:+UseParallelGC  -XX:+UseParallelOldGC</td>
<td>-XX:+UseConcMarkSweepGC  -XX:+UseParNewGC</td>
<td>-XX:+UseG1GC</td>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>when too fragmented -&gt; serial colletor</td>
<td>G1 can clean up objects from the old generation
by copying from one region into another,it (at least partially) compacts
the heap during normal processing  less likely to be fragmented</td>
</tr>
</tbody>
</table>
In GC algorithms, there are several terms:

Parallelism / Serialism: there is a single thread or multiple threads for the GC?

Concurrency: the GC threads are running exclusively or along with the application threads? If exclusively, that's when the "**stop-the-world**" happens.

&nbsp;

![gc-comparison](/media/gc-comparison.png)

You can see, all GC algorithms use "stop-the-world" when doing minor GC.

Triggered when:

*   a minor GC will be triggered when the new generation is full
*   a full GC will be triggered when the old generation is full, or a concurrent GC (if applicable) will be triggered when the heap starts to fill up.

### Serial collector

![serial-gc](/media/serial-gc.png)

### Parallel collector

Default GC for server class machine. The throughput matters!

**Two variations**

*   Multi-threaded young generation (minor) GC with single threaded old generation (full) GC
-XX:+UseParallelGC   default GC in Java 6 through Java 7 update 3.

*   Multiple-threaded young generation (minor) GC with multi-threaded old generation (full) GC
-XX:+UseParallelOldGC   default GC in Java 7 update 4 or over.

Both young generation and old generation GC(both minor and full GC) are "stop-the-world" events.

![parallelGC](/media/parallelGC.png)

![parallelOldGC](/media/parallelOldGC.png)

#### Minor GC

![throughout-minor-gc](/media/throughout-minor-gc.png)

#### Full GC

![throughout-full-gc](/media/throughout-full-gc.png)

### CMS collector

CMS is the Concurrent Mark-and-Sweep collector.

The minor GC has no difference with Parallel collector, but the full GC cycle is _<u>mostly</u>_ concurrent and includes several phases: initial mark phase, concurrent mark &amp; pre-cleaning phase, remark phase, concurrent sweep phase.

There are still "stop-the-world" events in the initial mark and remark phases.

![cms-gc](/media/cms-gc.png)

#### Minor GC

Trigger when young generation exhausted

![cms-minor-gc](/media/cms-minor-gc.png)

#### full GC (Concurrent Cycle)

*   mostly concurrent old generation GC, some phases run concurrently with the application, some are "stop-the-world", some phases are single threaded.
*   does not compact old generation, but the "stop-the-world", single threaded compaction will occur if: 1) concurrent cycle not keeping up 2) too fragment.
Triggered at old generation space occupancy threshold -- default is around 70%. This can be adjusted by` -XX:CMSInitiatingOccupancyFraction=n`
<table style="height: 254px;" border="1" width="748">
<tbody>
<tr>
<td>Phases</td>
<td></td>
<td></td>
</tr>
<tr>
<td>initial-mark phase</td>
<td>"stop-the-world"</td>
<td>single threaded</td>
</tr>
<tr>
<td>concurrent marking phase</td>
<td>run concurrently with the application</td>
<td>multi-threaded</td>
</tr>
<tr>
<td>pre-cleaning phase</td>
<td>run concurrently with the application</td>
<td>multi-threaded</td>
</tr>
<tr>
<td>remark phase</td>
<td>"stop-the-world"</td>
<td>multi-threaded</td>
</tr>
<tr>
<td>concurrent sweeping phase</td>
<td>run concurrently with the application</td>
<td>multi-threaded</td>
</tr>
</tbody>
</table>
![cms-full-gc](/media/cms-full-gc.png)

#### PerGen collection enable

Permanent generation space can also be collected concurrently by set:

`-XX:+CMSClassUnloadingEnable`

`-XX:CMSInitiatingPermOccupancyFraction=n`

`-XX:+CMSInitiatingPermOccupancyOnly`

### G1 collector

G1 is Garbage First.

G1 uses a drastically different Java heap layout to the other garbage collectors in the HotSpot VM. It splits the Java heap into equal-sized chunks called regions.
Even though G1 is generational, it does not have **physically** separate spaces for the young and old generations. Instead, each generation is a set of (maybe noncontiguous) regions.

![g1-layout](/media/g1-layout.png)

![cms-vs-g1](/media/cms-vs-g1.png)

PerGen(JDK 7) / metaspace (JDK 8)

&nbsp;
