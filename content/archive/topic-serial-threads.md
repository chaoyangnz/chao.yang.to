---
title: Java Thread Management
toc: true
id: 153
categories:
  - Multithreading
date: "2015-01-19T07:30:46+00:00"
---

About thread:

### 1\. Single-thread and multi-thread

A single-thread program has a single entry point (the main() method) and a single exit point. A multi-thread program has an initial entry point (the main() method), followed by many entry and exit points, which are run concurrently with the main().
![Multithread](/media/Multithread.gif)

### 2\. Lifecycle

![Multithread_ThreadLifeCycle](/media/Multithread_ThreadLifeCycle.png)

In Java, there are only 6 states when invoking_ **getState()**_:
NEW
RUNNABLE
BLOCKED
WAITING
TIMED_WAITING
TERMINATED

In reality, we cannot differentiate the running or runnable but not running.

a. A thread is _alive_ when it is started but has not died. We can check it by **_isAlive()_**
b. A daemon thread or a user thread mark is useful when JVM determine if it should exit. JVM exits when the only threads running are all daemon threads.

### 3\. Control through the lifecycle

In the following writing, I will use "control thread" as the source thread which instantiate, operate the new produced thread, and the latter is "target thread".

#### 1) create a thread in control thread (execution context)

We create a thread instance (a real thread representation in Java) by using _new_ keyword.
[![](/media/2129351.png)](/media/2129351.png)

So some properties can be provided in constructors:
a. ThreadGroup
b. Runnable target (define what will be run)
c. thread name
d. stack size

Apart from these, there are the following perperties:
e. daemon thread or user thread?
f. priority
g. id

Other important things:

a. stack trace (TODO)



```java

getStackTrace()
dumpStack()
Thread.getAllStackTraces()
```


b. uncaught exception handler (TODO)



```java

getUncaughtExceptionHandler()
setUncaughtExceptionHandler(UncaughtExceptionHandler)
Thread.getDefaultUncaughtExceptionHandler()
Thread.setDefaultUncaughtExceptionHandler(UncaughtExceptionHandler)
```


c.context class loader (TODO)



```java

getContextClassLoader()
setContextClassLoader(ClassLoader)
```


#### 2) start a thread in control thread



```java

public synchronized void start()
```


Other methods like:



```java

public final void stop();
public final synchronized void stop(Throwable obj);
public void destroy();
public final void suspend();
public final void resume();
```


are all deprecated, as as they are inherently deadlock-prone.

#### 3) interrupt

When we need to interrupt a target thread?
a. the target thread is waiting in an invocation of method which can response the interruption by throwing an InterruptedException; these methods include wait(), notify(), notifyAll(), Thread.sleep()
b. you don't wish the target thread to execute continually (may be a infinite loop or need spending too much time)

When control thread invokes a interrupt in the target thread instance, the target thread has two ways to response, if possible for interruption:
a. catch the InterruptedException
b. check the interrupted status by interrupted() method (once invoked, the interrupted status is cleared)

The following scenarios can produce the InterruptedException:
a. sleep(long)
b. wait(), wait(long), wait(long, int)
c. join(), join(long), join(long, wait)
d. interruption channel and selector
> <span style="color: #ff0000;">NOTICE: current thread cannot interrupt itself.</span>



```java

Thread thread = new Thread(new Runnable() {
    @Override
    public void run() {
        while(true) {
            System.out.println(&amp;quot;I'm running&amp;quot;);
            int a = 1;
            try {
                Thread.sleep(10);
            } catch (InterruptedException e) {
                System.out.println(&amp;quot;Interruption received, now I exit&amp;quot;);
                break;
            }
            a = a + 2;
        }
    }
});

thread.start();
doHardWork();
thread.interrupt();
```


If we don't interrupt it, the thread will be running forever.



```java

Thread thread = new Thread(new Runnable() {
    @Override
    public void run() {

        while(true) {
            if(Thread.interrupted()) {
                System.out.println(&quot;Interruption received, now I exit&quot;);
                break;
            }

            System.out.println(&quot;I'm running&quot;);
            double d = 0.0;
            for (int i = 0; i &lt; 900000; i++) {
                d = d + (Math.PI + Math.E) / d;
            }
        }
    }
});

thread.start();
doHardWork();
System.out.println(&quot;prepare to interrupt&quot;);
thread.interrupt();
```


By using interrupted(), we don't depend on InterruptedException to notify interruption, we check it by my own proactively.

#### 4) join

Sometimes, the control thread does something strictly after the target thread died. If the target thread has not terminated, the control thread must be blocked.
> <span style="color: #ff0000;">NOTICE: current thread can join itself, but it makes no sense because it will wait forever.</span>



```java

Thread thread = new Thread(new Runnable() {
    @Override
    public void run() {
        for(int i=0; i &lt; 10; ++i) {
            System.out.println(&quot;I'm running&quot;);
            double d = 0.0;
            for (int j = 0; j &lt; 900000; j++) {
                d = d + (Math.PI + Math.E) / d;
            }
        }

    }
});

thread.start();
doHardWork();

System.out.println(&quot;do my work&quot;);
System.in.read(); //just make the main thread not exit
```


In this example, the "do my work" happens in uninsured point, like this in one run:
I'm running
I'm running
I'm running
I'm running
I'm running
I'm running
I'm running
I'm running
do my work
I'm running
I'm running

Now, we use join() and see the result:



```java

Thread thread = new Thread(new Runnable() {
    @Override
    public void run() {
        for(int i=0; i &lt; 10; ++i) {
            System.out.println(&quot;I'm running&quot;);
            double d = 0.0;
            for (int j = 0; j &lt; 900000; j++) {
                d = d + (Math.PI + Math.E) / d;
            }
        }

    }
});

thread.start();
doHardWork();
thread.join();
System.out.println(&quot;do my work&quot;);
System.in.read(); //just make the main thread not exit
```


Now, the output is definitely:

I'm running
I'm running
I'm running
I'm running
I'm running
I'm running
I'm running
I'm running
I'm running
I'm running
do my work

It can ensure that the code behind the join() must be executed after the target thread died.
> <span style="color: #ff00ff;">WARNING: All the above examples, don't use JUnit for testing, becaust JUnit terminate the program once the main thread terminate, regardless of the running/waiting spawn threads.</span>

##### 5) Operations on current thread

When code is executed in a thread context, it always knows the current thread object which represents the current execution thread context.



```java

Thread.currentThread();
Thread.sleep(long);  Thread.sleep(long, int);
Thread.yield();
Thread.interrupted();  // Please differentiate Thread.interrupted() and thread.isInterrupted()
Thread.holdsLock()
```


Some convenient methods from ThreadGroup:



```java

Thread.activeCount()
Thread.enumurate()
```

