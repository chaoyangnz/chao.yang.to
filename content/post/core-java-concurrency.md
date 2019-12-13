---
title: Core Java Concurrency
toc: true
id: 402
categories:
  - Multithreading
date: "2015-03-13T04:59:28+00:00"
---

### A birdview of core java concurrency

![core-java-concurrency](/media/core-java-concurrency.png)

### Concepts

<div>![table1](/media/table1.png)</div>

### Procting shared data

<div>Java provides Lock-based concurrency control machanism. Locking **<span style="text-decoration: underline;">establishes the orderings</span>** needed to satisfy the Java Memory Model and **<span style="text-decoration: underline;">guarantee the visibility</span>** of changes to other threads.</div>
<div>

#### Synchronized

<div>

Every object instance has a monitor that can be locked by one thread at a time. The _synchronized _keyword can be specifid on a method or in block form to lock the monitor. Modifying a field while synchronized on an object guarantees that subsequent reads from any other thread synchronized on the same object will see the updated value. It is important to note that writes outside synchronization or synchronized on a different object than the read are not necessarily ever visible to other threads.
<div>The _synchronized_ keyword can be specified on a method or in block form on a particular object instance. If specified on a non-static method, the _this_ reference is used as the instance. In a synchronized static method, the Class defining the method is used as the instance.</div>

#### Lock

<div>The java.util.concurrent.locks package has a standard Lock interface. The ReentrantLock implementation duplicates the functionality of the synchronized keyword but also provides additional functionality such as obtaining information about the state of the lock, non-blocking tryLock(), and interruptible locking.</div>
<div></div>
<div>Example of using a explicit ReentrantLock instance:</div>
<div>


```java
public class Counter {
    private final Lock lock = new ReentrantLock();
    private int value = 0;
    public int increment() {
        lock.lock();
        try {
            return ++value;
        } finally {
            lock.unlock();
        }
    }
}
```

</div>

#### ReadWriteLock

<div>The java.util.concurrent.locks package also contains a ReadWriteLock interface (and ReentrantReadWriteLock implementation) which is defined by a pair of locks for reading and writing, typically allowing multiple concurrent readers but only one writer. Example of using an explicit ReentrantReadWriteLock to allow multiple concurrent readers:</div>
<div>


```java
public class statistic {
    private final ReadWriteLock lock = new ReentrantReadWriteLock();
    private int value;
    public void increment() {
        lock.writeLock().lock();
        try {
            value++;
        } finally {
            lock.writeLock().unlock();
        }
    }
    public int current() {
        lock.readLock().lock();
        try {
            return value;
        } finally {
            lock.readLock().unlock();
        }
    }
}
```

</div>

#### volatile

<div>The volatile modifier can be used to mark a field and indicate that changes to that field must be seen by all subsequent reads by other threads, regardless of synchronization. Thus, volatile provides visibility just like synchronization but scoped only to each read or write of the field. Before Java SE 5, the implementation of volatile was inconsitent between JVM implementations and architectures and could not be relied upon. The Java Memory Model now explicitly defines volatile's behavior.</div>
<div></div>
<div>An example of using volatile as a signaling flag:</div>
<div>


```java
public class Processor implments Runnable {
    private volatile boolean stop;
    public void stopProcessing() {
        stop = true;
    }
    public void run() {
        while(!stop) {
            //.. do processing
        }
    }
}
```

</div>
<div>Hot Tip: Marking an array as volatile does not make entries in the array volatile! In this case volatile applies only to the array reference itself. Instead, use a class like AtomicIntegerArray to create an array with volatile-like entries.</div>

#### Atomic classes

<div>One shorcoming of volatile is that while it provides visibility guarantees, you cannot both check and update a volatile field in a single atomic call. The java.util.concurrent.atomic package contains a set of classes that support atomic compound actions on a single value in a lock-free manner similar to volatile.</div>
<div>


```java
public class Counter {
    private AtomicInteger value = new AtomicInteger();
    public int next() {
        return value.incrementAndGet();
    }
}
```

Atomic classes are provided for booleans, integers, longs and object references as well as arrays of integers, longs, and object references.

</div>
<div></div>

#### ThreadLocal

<div>One way to contain data within a thread and make locking unnecessary is to use ThreadLocal storage. Conceptually a ThreadLocal acts as if there is a variable with its own version in every Thread. ThreadLocals are commonly used for stashing per-Thread values like the "current transaction" or other resources. Also, they are used to maintain per-thread counters, statistics, or ID generators.</div>
<div>


```java
public class TransactionManager {
    private static final ThreadLocal&lt;Transaction&gt; currentTransaction = new ThreadLocal&lt;Transaction&gt;() {
        @Override
        protected Transaction initialValue() {
            return new NullTransactin();
        }
    }
    public Transaction currentTransaction() {
        Transaction current = currentTransaction.get();
        if(current.isNull()) {
            current = new TransactionImpl();
            currentTransaction.put(current);
        }
        return current;
    }
}
```

</div>

### Concurrent collections

<div>A key technique for properly protecting shared data is to encapsulate the synchronization mechanism with the calss holding the data.</div>
<div>You can regard all plain objects as containers of its fields(data), but to get them thread-safe, you have to implement them one by one cautiously and seriously.</div>
<div>Collections are the container facilities Java provides for holding data and we use nearly everywhere.</div>
<div>This technique makes it impossible to improperly access the data as all usage must conform to the synchronization protocol. The java.util.concurrent package holds many data structures designed for concurrent use. Generally, the use of these data structures yields far better performance than using a synchronized wrapper around an unsynchronized collection.</div>
<div></div>

#### Concurrent lists and sets

<div> ![table-concurrent-lists-sets](/media/table-concurrent-lists-sets.png)![](file:///C:/Users/Administrator/Documents/My%20Knowledge/temp/5a59455f-283a-4866-8f6b-3dc0c08f7c6a.png)</div>

#### Concurrent maps

<div>The java.util.concurrent package contains an extension to the Map interface called ConcurrentMap, which provides some extra methods described in following table. All of these methods perform a set of actions in the scope of a single atomic action. Performing this set of actions outside the map would introduce race conditions due to making multiple (non-atomic) calls on the map.</div>
<div></div>
<div>![table-concurrent-map-methods](/media/table-concurrent-map-methods.png)![](file:///C:/Users/Administrator/Documents/My%20Knowledge/temp/289ebe3f-9f50-4c78-bff7-2d9f1fbc287a.png)</div>
<div>There are two ConcurrentMap implementations available as shown in following table.</div>
<div>![table-concurrent-map](/media/table-concurrent-map.png)![](file:///C:/Users/Administrator/Documents/My%20Knowledge/temp/5a4f4218-f143-409f-a1c0-98c707b9dcaa.png)</div>
</div>

#### Queue

<div>Queues act as pipes between “producers” and “consumers”.Items are put in one end of the pipe and emerge from the other end of the pipe in the same “fist-in fist-out” (FIFO) order.
The Queue interface was added to java.util in Java SE 5 and while it can be used in single-threaded scenarios, it is primarily used with multiple producers or one or more consumers, all writing and reading from the same queue.
The BlockingQueue interface is in java.util.concurrent and extends Queue to provide additional choices of how to handle the scenario where a queue may be full (when a producer adds an item) or empty (when a consumer reads or removes an item).
In these cases, BlockingQueue provides methods that either block forever or block for a specifid time period, waiting for the condition to change due to the actions of another thread.
The following table demonstrates the Queue and BlockingQueue methods in terms of key operations and the strategy for dealing with these special conditions.</div>
<div>![table-concurrent-queue-methods](/media/table-concurrent-queue-methods.png)![](file:///C:/Users/Administrator/Documents/My%20Knowledge/temp/b374c6dd-f4ae-4fe3-96fb-80233b3e1299.png)</div>
<div>Several Queue implementations are provided by the JDK and their relationships are discribed in the following table.</div>
![table-concurrent-queue](/media/table-concurrent-queue.png)

#### Deques

<div>A double-ended queue or Deque (pronounced “deck”) was added in Java SE 6\. Deques support not just adding from one end and removing from the other but adding and removing items from both ends. Similarly to BlockingQueue, there is a
BlockingDeque interface that provides methods for blocking and timeout in the case of special conditions. Table 7 shows the Deque and BlockingDeque methods. Because Deque extends Queue and BlockingDeque extends lockingQueue, all of those methods are also available for use.
![](file:///C:/Users/Administrator/Documents/My%20Knowledge/temp/307ab1f8-64da-4916-a6a7-ef6b431af845.png)![table-concurrent-deu-methods](/media/table-concurrent-deu-methods.png)</div>
<div>One special use case for a Deque is when add, remove, and examine operations all take place on only one end of the pipe. This special case is just a stack (fist-in-last-out retrieval order). The Deque interface actually provides methods that use the terminology of a stack: push() , pop() , and peek() . These methods map to addFirst() , removeFirst() , and peekFirst() methods in the Deque interface and allow you to use any Deque implementation as a stack. Table 8 describes the Deque and BlockingDeque implementations in the JDK. Note that Deque extends Queue and BlockingDeque extends BlockingQueue.</div>
<div>
<div>![table-concurrent-deque](/media/table-concurrent-deque.png)![](file:///C:/Users/Administrator/Documents/My%20Knowledge/temp/31906fc7-031c-4660-89d5-406daac22202.png)</div>

### Threads

</div>
<div>In Java, the java.lang.Thread class is used to represent an application or JVM thread. Code is always being executed in the context of some Thread class (use Thread.currentThread() to obtain your own Thread).</div>

#### Thread interference

<div>The most obvious way to communicate between threads is for one thread to directly call a method on another Thread object. Table 9 shows methods on Thread that can be used for direct interaction across threads.</div>
<div>![table-concurrent-thread](/media/table-concurrent-thread.png)</div>
<div></div>
<div>![thread-inference](/media/thread-inference.png)</div>
> <div>NOTE: In current Thread implementation, suspend(), resume(), stop() are all deprecated. I guess there is a principle here: _**once started until destroyed, thread SHOULD determine its own life, except that it's willing proactively to response the external interference: interruption, or some flag-base mechanics.**_</div>
> 
> <div>So sleep(), yield(), join() are all for the current thread itself instead of controlling others.</div>
<div></div>
<div>Interruptible points are some points that can be interrupted or intend to accept interruption signal.</div>
<div>There are two ways to response interruption:</div>
1)  some methods are interruptible. These method can throw InterruptedException, such as wait(), await() of CyclicBarrier and CountdownLatch, acquire() of Semophore

2)  some plain codes intend to handle interruption signal by using Thread.interrupted() to test interruption mark.
<div></div>
<div>In current thread, the running code can do some actions to influence the current thread by sleep() or yield().</div>
<div>![thread-current-context](/media/thread-current-context.png)![](file:///C:/Users/Administrator/Documents/My%20Knowledge/temp/159e3222-f1a3-4ba4-806e-a79d29896d45.png)</div>

#### Uncaught exception handling

<div>Threads can specify an UncaughtExceptionHandler that will receive notifiation of any uncaught exception that cause a thread to abruptly terminate.</div>
<div>


```java
Thread t = new Thread(runnable);
t.setUncaughtExceptionHandler(new Therad.UncaughtExceptionHandler() {
    void uncaughtException(Thread t, Throwable e) {
        // get Logger and log uncaught exception
    }
});
t.start();
```

</div>

#### Deadlock

<div>A deadlock occurs when there is more than one thread, each waiting for a resource held by another, such that a cycle of resources and acquiring threads is formed. The most obvious kind of resource is an object monitor but any resource that causes blocking (such as wait / notify) can qualify.</div>
<div>Many recent JVMs can detect monitor deadlocks and will print deadlock information in thread dumps produced from a signal, jstack, or other thread dump tool.</div>
<div>In addition to deadlock, some other threading situations are starvation and livelock. Starvation occurs when threads hold a lock for long periods such that some threads “starve” without making progress. Livelock occurs when threads spend all of their time negotiating access to a resource or detecting and avoiding deadlock such that no thread actually makes
progress.

### Thread coordination

</div>

#### wait / notify

<div>The wait / notify idiom is appropriate whenever one thread needs to signal to another that a condition has been met, especially as an alternative to sleeping in a loop and polling the condition. For example, one thread might wait for a queue to contain an item to process. Another thread can signal the waiting threads when an item is added to the queue.
The canonical usage pattern for wait and notify is as follows:</div>
<div>
<div></div>
<div>


```java
public class Latch {
    private final Object lock = new Object();
    private volatile boolean flag = false;
    public void waitTillChange() {
        synchronized(lock) {
            while(! flag) {
                try {
                    lock.wait();
                } catch (InterruptedException e) {
                }
            }
        }
    }
    public void change(0 {
        synchronized(lock) {
            flag = true;
            lock.notifyAll();
        }
    }
}
```

Some important things to note about this code:

</div>
<div>• Always call wait, notify, and notifyAll inside a synchronized lock or an IllegalMonitorStateException will be thrown.
• Always wait inside a loop that checks the condition being waited on – this addresses the timing issue if another thread satisfis the condition before the wait begins. Also, it protects your code from spurious wake-ups that can (and do) occur.
• Always ensure that you satisfy the waiting condition before calling notify or notifyAll. Failing to do so will cause a notifiation but no thread will ever be able to escape its wait loop.</div>

#### Condition

</div>
<div>In Java SE 5, a new java.util.concurrent.locks.Condition class was added. Condition implements the wait/notify semantics in an API but with several additional features such as the ability to create multiple Conditions per Lock, interruptible waiting, access to statistics, etc. Conditions are obtained from a Lock instance as follows:</div>
<div>


```java
public class LatchCondition {
    private final Lock lock = new ReentrantLock();
    private final Condition condition = lock.newCondition();
    private volatile boolean flag = false;
    public void waitTillChange() {
        lock.lock();
        try {
            while(! flag) {
                condition.await();
            }
        } finally {
            lock.unlock();
        }
    }
    public void change() {
        lock.lock();
        try {
            flag = true;
            condition.signalAll();
        } finally {
            lock.unlock();
        }
    }
}
```

</div>

#### Coordination classes

<div>The java.util.concurrent package contains several classes pre-built for common forms of multi-thread communication. These coordination classes cover most common scenarios where wait/notify and Condition might be used and are strongly perferred for their safety and ease of use.
**CyclicBarrier**
The CyclicBarrier is initialized with a participant count. Participants call await() and block until the count is reached, at which point an optional barrier task is executed by the last arriving thread, and all threads are released. The barrier can be reused indefiitely. Used to coordinate the start and stop of groups of threads.</div>
<div>字面意思循环挡板，通过它可以实现让一组线程等待至某个状态之后再全部同时执行。称为循环是因为当所有等待线程都被释放以后，CyclicBarrier可以被重用
Allows N-1 treads to wait on await() method until N-th thread calls await() method and then they resume their execution.
<div class="separator">![CyclicBarrier](/media/CyclicBarrier.png)</div>
As this illustration shows there is a barrier for 3 parties. When T1 and T2 come they wait. When T3 comes an optional TA handler is called and (after it completes?) T1,2,3 resume their work. You can reuse CyclicBarrier many times by calling reset() method.

And the code example:
<div class="dp-highlighter">
<div class="bar">
<div class="tools">


```java
final CyclicBarrier cb = new CyclicBarrier(N);
for (int i = 0; i &lt; N; i++) {
    final int idx = i;
    new Thread(new Runnable() {
        public void run() {
            System.out.println("T" + idx + ": await");
            try {
                cb.await();
            } catch (InterruptedException ex) {
                System.out.println("T" + idx + ": interrupted");
                return;
            } catch (BrokenBarrierException ex) {
                System.out.println("T" + idx + ": broken");
                return;
            }
            System.out.println("T" + idx + ": continue");
        }
    }).start();
}

```

</div>
</div>
</div>
Console output:


```java
T0: await
T1: await
T2: await
T2: continue
T1: continue
T0: continue
```

Lines can "jump" a little bit because threads in these examples print out information to console concurrently. So you may have slightly different results.
**CountDownLatch**
The CountDownLatch is initialized with a count. Threads may call await() to wait for the count to reach 0\. Other threads (or same) may call countDown() to reduce count. Not reusable once the count has reached 0\. Used to trigger an unknown set of threads once some number of actions has occurred.

</div>
<div>

倒计时闩, 利用它可以实现类似计数器的功能。比如有一个任务A，它要等待其他4个任务执行完毕之后才能执行，此时就可以利用CountDownLatch来实现这种功能了

Works like a counter – allows one or more threads to wait on await() method for another N threads to callcountDown() method N times (total number of calls should be N).
<div class="separator">![CountdownLatch](/media/CountdownLatch.png)</div>
As the illustration shows only T<span class="Apple-style-span">A</span> is awaiting. T<span class="Apple-style-span">1,2,3</span> call <span class="Apple-style-span">countDown()</span> and proceed their execution. When T<span class="Apple-style-span">3</span> calls<span class="Apple-style-span">countDown(),</span> T<span class="Apple-style-span">A</span> gets unlocked and can resume its work.

“HelloWorld app” for CountdownLatch:


```java
final CountDownLatch cdl = new CountDownLatch(N);
        new Thread(new Runnable() {
            public void run() {
                System.out.println("awaiting...");
                try {
                    cdl.await();
                } catch (InterruptedException ex) {
                    System.out.println("await has been iterrupted");
                    return;
                }
                System.out.println("ready");
            }
        }).start();

        for (int i = 0; i &lt; N; i++) {
            final int idx = i;
            new Thread(new Runnable() {
                public void run() {
                    System.out.println("T" + idx + ":countDown");
                    cdl.countDown();
                    System.out.println("T" + idx + ":continue");
                }
            }).start();
        }

```

Console output:
<pre>awaiting...
T1:countDown
T0:countDown
T0:continue
T1:continue
T2:countDown
T2:continue
ready
```

It is different from CyclicBarrier:
1) it cannot be reused, used only one time
2) it does not block these N threads when they call countDown() method. Only thread on <span class="Apple-style-span">await()</span> method waits.

**Semaphore**
A Semaphore manages a set of “permits” that can be checked out with acquire() which will block until one is available. Threads call release() to return the permit. A semaphore with one permit is equivalent to a mutual exclusion block.

</div>
<div>

Maintains N permits which is shared between M threads. Each thread can acquire or release permits. If there are not enough permits thread blocks until other threads release necessary amount of permits.
<div class="separator">![Semaphore](/media/Semaphore.png)</div>
Let's see what is happening on the illustration above. There are 2 permits and 4 threads. Each thread acquires/releases only 1 permit. T<span class="Apple-style-span">1</span> and T<span class="Apple-style-span">2</span> do this and proceed their execution without being blocked. T<span class="Apple-style-span">3</span> and T<span class="Apple-style-span">4</span>block and wait for permits because there are no any permits available. T<span class="Apple-style-span">2</span> releases one permit and T<span class="Apple-style-span">4</span> proceeds its calculations<span class="Apple-style-span"> (T<span class="Apple-style-span">3</span> is also might be chosen?)</span>. Next T<span class="Apple-style-span">4</span> releases its permit and T<span class="Apple-style-span">3</span> resumes. An finally T<span class="Apple-style-span">1</span> and T<span class="Apple-style-span">3</span> release their permits.

Simple example:


```java
public static void main(String args[]) {
    final Semaphore sem = new Semaphore(N);
    acquire("T1", sem);
    acquire("T2", sem);

    release("Ta", sem);
    release("Tb", sem);
}

private static void acquire(final String id, final Semaphore s){
    new Thread(new Runnable() {
        public void run() {
            System.out.println(id + ": acquire");
            try {
                s.acquire();
            } catch (InterruptedException ex) {
                System.out.println(id + ": acquire|interrupted");
                return;
            }
            System.out.println(id + ": acquire|ready");
        }
    }).start();
}

private static void release(final String id, final Semaphore s){
    new Thread(new Runnable() {
        public void run() {
            System.out.println(id + ": release");
            s.release();
            System.out.println(id + ": release|ready");
        }
    }).start();
}

```

Console output:
<pre>T2: acquire
T2: acquire|ready
Tb: release
T1: acquire
Ta: release
Ta: release|ready
Tb: release|ready
T1: acquire|ready
```

**Exchanger**
An Exchanger waits for threads to meet at the exchange() method and swap values atomically. This is similar to using a SynchronousQueue but data values pass in both directions.

</div>
<div></div>
<div>

下面对上面说的三个辅助类进行一个总结：

1）CountDownLatch和CyclicBarrier都能够实现线程之间的等待，只不过它们侧重点不同：
CountDownLatch一般用于某个线程A等待若干个其他线程执行完任务之后，它才执行；
而CyclicBarrier一般用于一组线程互相等待至某个状态，然后这一组线程再同时执行；
另外，CountDownLatch是不能够重用的，而CyclicBarrier是可以重用的。
2）Semaphore其实和锁有点类似，它一般用于控制对某组资源的访问权限。

</div>

### Task execution

<div>Many concurrent Java programs need a pool of workers executing tasks from a queue. The java.util.concurrent
package provides a solid foundation for this style of work management.

#### ExecutorService

The Executor and more expansive ExecutorService interfaces defie the contract for a component that can execute
tasks. Users of these interfaces can get a wide variety of implementation behaviors behind a common interface.
The most generic Executor interface accepts jobs only in the form of Runnables:
• void execute(Runnable command)
The ExecutorService extends Executor to add methods that take both Runnable and Callable task and collections of tasks:
• Future&lt;?&gt; submit(Runnable task)
• Future&lt;T&gt; submit(Callable&lt;T&gt; task)
• Future&lt;T&gt; submit(Runnable task, T result)
• List&lt;Future&lt;T&gt; invokeAll(Collection&lt;? extends Callable&lt;T&gt;&gt; tasks)
• List&lt;Future&lt;T&gt; invokeAll(Collection&lt;? extends Callable&lt;T&gt;&gt; tasks, long timeout, TimeUnit unit)

</div>
<div>• T invokeAny(Collection&lt;? extends Callable&lt;T&gt;&gt; tasks)
•T invokeAny(Collection&lt;? extends Callable&lt;T&gt;&gt; tasks, long timeout, TimeUnit unit)

#### Callable and Future

A Callable is like the familiar Runnable but can return a result and throw an exception:
• V call() throws Exception;
It is common in the executor framework to submit a Callable and receive a Future. A Future is a marker representing a result that will be available at some point in the future. The Future has methods that allow you to either poll or block while waiting for the result to be ready. You can also cancel the task before or while it’s executing through methods on Future.
If you need the functionality of a Future where only Runnables are supported (as in Executor), you can use FutureTask as a bridge. FutureTask implements both Future and Runnable so that you can submit the task as a Runnable and use the task itself as a Future in the caller.

#### ExecutorService implementations

The primary implementation of ExecutorService is ThreadPoolExecutor. This implementation class provides a
wide variety of confiurable features:
• Thread pool – specify “core” thread count (optionally pre-started), and max thread count
• Thread factory – generate threads with custom characteristics such as a custom name
• Work queue – specify the queue implementation, which must be blocking, but can be bounded or unbounded
• Rejected tasks – specify the policy for tasks that cannot be accepted due to a full input queue or unavailable worker
• Lifecycle hooks – overridden to extend to override key points in the lifecycle like before or after task execution
• Shutdown – stop incoming tasks and wait for executing tasks to complete ScheduledThreadPoolExecutor is an extension of ThreadPoolExecutor that provides the ability to schedule tasks for completion rather than using FIFO semantics. For cases where java.util.Timer is not sophisticated enough, the ScheduledThreadPoolExecutor often provides suffiient flxibility.
The Executors class contains many static methods (see Table 10) for creating prepackaged ExecutorService and
ScheduledExecutorService instances that will cover a wide variety of common use cases.
![](file:///C:/Users/Administrator/Documents/My%20Knowledge/temp/98fb8f57-8cfd-441a-b0be-850e7533aebe.png)[![table-concurrent-executor](/media/table-concurrent-executor.png)](/media/table-concurrent-executor.png)

</div>
<div>The following example creates a fied thread pool and submits a long-running task to it:</div>
<div>


```java
int processors = Runtime.getRuntime().availableProcessor();
ExecutorService excutor = Executors.newFixedThreadPool(processors);
Future&lt;Integer&gt; futureResult = executor.submit(new Callable&lt;Integer&gt;() {
    public Integer call() {
        // long running computation that returns an integer
    }
});
Integer result = futureResult.get(); //block for result
```

In this example the call that submits the task to the executor will not block but return immediately. The last line will block on the get() call until the result is available.

</div>
<div>

ExecutorService covers almost all situations where you would previously create Thread objects or thread pools. Any time your code is constructing a Thread directly, consider whether you could accomplish the same goal with an ExecutorService  produced by one of the Executor factory methods; often this will be simpler and more flxible.

#### CompletionService

Beyond the common pattern of a pool of workers and an input queue, it is common for each task to produce a result that must be accumulated for further processing. The CompletionService interface allows a user to submit Callable and Runnable tasks but also to take or poll for results from the results queue:
• Future&lt;V&gt; take() – take if available
• Future&lt;V&gt; poll() – block until available
• Future&lt;V&gt; poll(long timeout, TimeUnit unit) – block until timeout ends
The ExecutorCompletionService is the standard implementation of CompletionService. It is constructed with an Executor that provides the input queue and worker thread pool.

</div>
<div>

&nbsp;

* * *

</div>
<div>Some thread-safe questions:</div>
<div>String  -&gt; immutable</div>
<div>StringBuffer -&gt; mutable, synchronized version</div>
<div>StringBuilder -&gt; mutable, unsynchronized version</div>
<div></div>
<div>HashMap -&gt;  unsynchronized</div>
<div>HashTable -&gt;  synchronized</div>
<div></div>
<div>ArrayList -&gt; unsynchronized</div>
<div>Vector -&gt; sysnchronized</div>
<div></div>
<div></div>
<div></div>
</div>
