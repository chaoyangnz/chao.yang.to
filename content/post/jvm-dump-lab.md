---
title: JVM dump lab
toc: true
id: 1311
categories:
  - Performance
date: "2015-06-05T15:16:23+00:00"
---

### IBM dump files

[pdf-embedder url="/media/WSTE-02092010-DeepDiveJVMJavacoresJavaDumps-Grigorenko.pdf"]

#### **What is java core (or javadump or thread dump)?**

A **java core** is a snapshot of the threads at work in a JVM. It also details a cross section of information from the major components in the JVM like XM,XE, LK, etc (IBM Javacore only, Sun does not – it just shows threads)

A **Java dump**, also known as a **Java core**, Java **thread dump**, or a thread dump is a file that contains the following sections:

*   All of the threads that run on a Java Virtual Machine (JVM).
*   All of the monitors on a JVM.
*   Some useful information about the system that the JVM runs under.


```java
# kill list

1) SIGHUP 2) SIGINT 3) SIGQUIT 4) SIGILL 5) SIGTRAP
6) SIGABRT 7) SIGBUS 8) SIGFPE 9) SIGKILL 10) SIGUSR1
11) SIGSEGV 12) SIGUSR2 13) SIGPIPE 14) SIGALRM 15) SIGTERM
16) SIGSTKFLT 17) SIGCHLD 18) SIGCONT 19) SIGSTOP 20) SIGTSTP
21) SIGTTIN 22) SIGTTOU 23) SIGURG 24) SIGXCPU 25) SIGXFSZ
26) SIGVTALRM 27) SIGPROF 28) SIGWINCH 29) SIGIO 30) SIGPWR
31) SIGSYS 34) SIGRTMIN 35) SIGRTMIN+1 36) SIGRTMIN+2 37) SIGRTMIN+3
38) SIGRTMIN+4 39) SIGRTMIN+5 40) SIGRTMIN+6 41) SIGRTMIN+7 42) SIGRTMIN+8
43) SIGRTMIN+9 44) SIGRTMIN+10 45) SIGRTMIN+11 46) SIGRTMIN+12 47) SIGRTMIN+13
48) SIGRTMIN+14 49) SIGRTMIN+15 50) SIGRTMAX-14 51) SIGRTMAX-13 52) SIGRTMAX-12
53) SIGRTMAX-11 54) SIGRTMAX-10 55) SIGRTMAX-9 56) SIGRTMAX-8 57) SIGRTMAX-7
58) SIGRTMAX-6 59) SIGRTMAX-5 60) SIGRTMAX-4 61) SIGRTMAX-3 62) SIGRTMAX-2
63) SIGRTMAX-1 64) SIGRTMAX
```

&nbsp;

#### **What is a heap dump?**

A **heapdump** is a snapshot of JVM memory – it shows the live objects on the heap along with references between objects. It is used to determine memory usage patterns and memory leak suspects.

A heap dump is a snapshot of memory at a given point in time. It contains information on the Java objects and classes in memory at the time the snapshot was taken. In the same way that thread dumps are an instant in time a single heap dump cannot provide much in the way of temporal information to answer questions like “where did an object come from?” or “what has it been doing?”

Heap dumps come in various formats:

*   the Sun VM generates HPROF
*   the IBM VM provides portable heap dump (PHD) files.
At Rally we run Sun’s VM so usually work with HPROF dumps. The internals vary a little but the basic principals are still the same.

- See more at: https://www.rallydev.com/blog/engineering/outofmemoryerror-fun-heap-dump-analysis#sthash.We3ozSN9.dpuf

**How to get a heap dump?**

Executing the Java VM with the appropriate parameters:

*   <span class="geshifilter">`-XX:+HeapDumpOnOutOfMemoryError`</span> writes heap dump on <span class="geshifilter">`OutOfMemoryError`</span> (recommended)
*   <span class="geshifilter">`-XX:+HeapDumpOnCtrlBreak`</span> writes heap dump together with thread dump on <span class="geshifilter">`CTRL+BREAK`</span>
*   by default enabled on WebSphere


```java
#java -Xdump:what

Registered dump agents
----------------------
dumpFn=doSystemDump
events=gpf+abort
filter=
label=//core.%Y%m%d.%H%M%S.%pid.%seq.dmp
range=1..0
priority=999
request=serial
opts=
----------------------
dumpFn=doSnapDump
events=gpf+abort
filter=
label=//Snap.%Y%m%d.%H%M%S.%pid.%seq.trc
range=1..0
priority=500
request=serial
opts=
----------------------
dumpFn=doSnapDump
events=systhrow
filter=java/lang/OutOfMemoryError
label=//Snap.%Y%m%d.%H%M%S.%pid.%seq.trc
range=1..4
priority=500
request=serial
opts=
----------------------
dumpFn=doHeapDump
events=systhrow
filter=java/lang/OutOfMemoryError
label=//heapdump.%Y%m%d.%H%M%S.%pid.%seq.phd
range=1..4
priority=40
request=exclusive+compact+prepwalk
opts=PHD
----------------------
dumpFn=doJavaDump
events=gpf+user+abort
filter=
label=//javacore.%Y%m%d.%H%M%S.%pid.%seq.txt
range=1..0
priority=10
request=exclusive
opts=
----------------------
dumpFn=doJavaDump
events=systhrow
filter=java/lang/OutOfMemoryError
label=//javacore.%Y%m%d.%H%M%S.%pid.%seq.txt
range=1..4
priority=10
request=exclusive
opts=
----------------------
```

Or you can run the jmap tool:

*   Sun (Linux, Solaris; not on Windows) <span class="geshifilter">`JMap Java 5: jmap -heap:format=b`</span>
*   Sun (Linux, Solaris; Windows see link) <span class="geshifilter">`JMap Java 6: jmap.exe -dump:format=b,file=HeapDump.hprof`</span>
*   Sun (Linus, Solaris) JMap with Core Dump File: <span class="geshifilter">`jmap -dump:format=b,file=HeapDump.hprof /path/to/bin/java core_dump_file`</span>
*   Sun JConsole: Launch <span class="geshifilter">`jconsole.exe`</span> and invoke operation <span class="geshifilter">`dumpHeap()`</span> on HotSpotDiagnostic MBean
*   SAP JVMMon: Launch <span class="geshifilter">`jvmmon.exe`</span> and call menu for dumping the heap

#### What is system core dump and snap trace?

### Analyzer Tools

#### **ThreadAnalyzer**

*   ThreadAnalyzer is a problem determination tool for WebSphere Application Server thread performance and deadlock detection. You can use it to obtain a Java dump from an application server and use its analysis features for problem determination. For performance problem determination, ThreadAnalyzer provides a top-of-the-stack (TOS) analysis that counts all of the methods at the top of the stack for the listed threads.

*   It then sorts them by ‘weight’ (automatically computed by ThreadAnalyzer), and lists the possible performance bottlenecks in your application. ThreadAnalyzer does automatic deadlock detection while it analyzes the Java dump.
**Downloading ThreadAnalyzer**

To download ThreadAnalyzer go to

http://www.ibm.com/developerworks/websphere/downloads/thread_analyzer.html

#### **IBM Thread and Monitor Dump Analyzer for Java**

*   During the run time of a Java™ process, some Java Virtual Machiness (JVMs) may not respond predictably and oftentimes seem to hang up for a long time or until JVM shutdown occurs. It is not easy to determine the root cause of these sorts of problems.

*   By triggering a _javacore_ when a Java process does not respond, it is possible to collect diagnostic information related to the JVM and a Java application captured at a particular point during execution. For example, the information can be about the operating system, the application environment, threads, native stack, locks, and memory. The exact contents are dependent on the platform on which the application is running.

*   On some platforms, and in some cases, javacore is known as “javadump.” The code that creates javacore is part of the JVM. One can control it by using environment variables and run-time switches. By default, a javacore occurs when the JVM terminates unexpectedly. A javacore can also be triggered by sending specific signals to the JVM. Although javacore or javadump is present in Sun Solaris JVMs, much of the content of the javacore is added by IBM and, therefore, is present only in IBM JVMs.

*   IBM Thread and Monitor Dump Analyzer for Java analyzes javacore and diagnoses monitor locks and thread activities in order to identify the root cause of hangs, deadlocks, and resource contention or monitor bottlenecks.
**How does it work?**

*   This technology analyzes each thread information and provides diagnostic information, such as current thread information, the signal that caused the javacore, Java heap information (maximum Java heap size, initial Java heap size, garbage collector counter, allocation failure counter, free Java heap size, and allocated Java heap size), number of runnable threads, total number of threads, number of monitors locked, and deadlock information.

*   In addition, IBM Thread and Monitor Dump Analyzer for Java provides the recommended size of the Java heap cluster (applicable only to IBM SDK 1.4.2 and 1.3.1 SR7 or above) based on the heuristic analysis engine.

*   IBM Thread and Monitor Dump Analyzer for Java compares each javacore and provides process ID information for threads, time stamp of the first javacore, time stamp of the last javacore, number of garbage collections per minute, number of allocation failures per minute, time between the first javacore and the last javacore, number of hang suspects, and list of hang suspects.

*   This technology also compares all monitor information in javacore and detects deadlock and resource contention or monitor bottlenecks, if there are any.

#### **HeapAnalyzer**

*   HeapAnalyzer allows the finding of a possible Java™ heap leak area through its heuristic search engine and analysis of the Java heap dump in Java applications.

*   Java heap areas define objects, arrays, and classes. When the Garbage Collector allocates areas of storage in the heap, an object continues to be live while a reference to it exists somewhere in the active state of the JVM; therefore the object is reachable. When an object ceases to be referenced from the active state, it becomes garbage and can be reclaimed for reuse.

*   When this reclamation occurs, the Garbage Collector must process a possible finalizer and also ensure that any internal JVM resources that are associated with the object are returned to the pool of such resources. Java heap dumps are snap shots of Java heaps at specific times.
**How does it work?**

HeapAnalyzer analyzes Java heap dumps by parsing the Java heap dump, creating directional graphs, transforming them into directional trees, and executing the heuristic search engine.

The following are examples of features:

*   List of Java heap leak suspects
*   Recommendation of the size of kCluster
*   List of gaps among allocated objects/classes/arrays
*   Java objects/classes/arrays search engine
*   List of objects/classes/arrays by type name
*   List of objects/classes/arrays by object name
*   List of objects/classes/arrays by address
*   List of objects/classes/arrays by size
*   List of objects/classes/arrays by size of child
*   List of objects/classes/arrays by number of child
*   List of objects/classes/arrays by frequency
*   List of available heap spaces by size
*   Tree view of Java heap dump
*   Loading/saving processed Java heap dumps.

#### Memory Analyzer

The Memory Dump Diagnostic for Java (MDD4J) tool has been deprecated and is no longer available.

Memory Analyzer is in the portfolio of IBM Monitoring and Diagnostic Tools for Java, which are IBM's primary troubleshooting tools for problems with Java applications. Memory Analyzer is the recommended heap dump analysis tool.

Memory Analyzer extends Eclipse MAT version 1.4 using the Diagnostic Tool Framework for Java (DTFJ) which enables Java heap analysis using operating system level dumps and IBM Portable Heap Dumps (PHD).
