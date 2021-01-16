---
title: Thread Synchronization
toc: true
id: 180
categories:
  - Concurrency
  - Multithreading
date: "2015-01-20T02:16:57+00:00"
---

#### Two questions about synchronized

1.  mutual exclusion - shared data access among multiple threads -
2.  coordinating activities
Some of the Java virtual machine's runtime data areas are shared by all threads, others are private to individual threads.

*   The heap and method area are shared by all threads, Java programs need to coordinate multi-threaded access to two kinds of data:

*   b. class variables, which are stored in the method area
*   a. instance variables, which are stored on the heap

*   Programs never need to coordinate access to local variables, which reside on Java stacks, because data on the Java stack is private to the thread to which the Java stack belongs.

#### Monitors

The mechanism that Java uses to support synchronization is the monitor, which supports two kinds of thread synchronization: mutual exclusion and cooperation.

Mutual exclusion, which is supported in the Java virtual machine via object locks, enables multiple threads to independently work on shared data without interfering with each other.
Cooperation, which is supported in the Java virtual machine via the wait and notify methods of class Object, enables threads to work together towards a common goal.

A monitor is like a building that contains one special room that can be occupied by only one thread at a time. The room usually contains some data. From the time a thread enters this room to the time it leaves, it has exclusive access to any data in the room. Entering the monitor building is called "entering the monitor." Entering the special room inside the building is called "acquiring the monitor." Occupying the room is called "owning the monitor," and leaving the room is called "releasing the monitor." Leaving the entire building is called "exiting the monitor."

In addition to being associated with a bit of data, a monitor is associated with one or more bits of code, which in this book will be called **_monitor regions_**.

![fig20-1](/media/fig20-1.gif)
[http://www.artima.com/insidejvm/ed2/threadsynch.html](http://www.artima.com/insidejvm/ed2/threadsynch.html)

In this figure, number 2 and number 4 acquiring monitor is unpredictable, which is scheduled by JVM and implementation-dependent. Number 3 releasing monitor can be done by invoking wait() method.

Because sleep() have no change with the monitor, so this can explain why sleep is the class method of Thread instead of Object.

How to understand?

An object has a monitor to protect its data (instance variables or class variables).

When applying `sychronized`, it basically declare two things.

*   specify the lock associated with the synchronized object using as privilege
*   mark the code (aka. monitor region) to be monitored by that monitor associated with the synchronized object

#### Object locking by _intrinsic lock _or_ monitor lock_

(The API specification often refers to this lock entity simply as a "monitor.")

In the Java virtual machine, _<span style="background-color: #cccccc;">every object and class is logically associated with a monitor</span>_. For objects, the associated monitor protects the object's instance variables. For classes, the monitor protects the class's class variables. If an object has no instance variables, or a class has no class variables, the associated monitor protects no data.

To implement the mutual exclusion capability of monitors, the Java virtual machine associates a lock (sometimes called a mutex) with each object and class. A lock is like a privilege that only one thread can "own" at any one time. Threads need not obtain a lock to access instance or class variables. If a thread does obtain a lock, however, no other thread can obtain a lock on the same data until the thread that owns the lock releases it. (To "lock an object" is to acquire the monitor associated with that object.)

Note that as a Java programmer, you never explicitly lock an object. Object locks are internal to the Java virtual machine. In your Java programs, you identify the monitor regions of your program by writing synchronized statements and methods. As the Java virtual machine runs your program, it automatically locks an object or class every time it encounters a monitor region.

##### Reentrant

A single thread is allowed to lock the same object multiple times. For each object, the Java virtual machine maintains a count of the number of times the object has been locked. An unlocked object has a count of zero. When a thread acquires the lock for the first time, the count is again incremented to one. Each time the thread acquires a lock on the same object, the count is again incremented. (Only the thread that already owns an object's lock is allowed to lock it again. As mentioned previously, no other thread can lock the object until the owning thread releases the lock.) Each time the thread releases the lock, the count is decremented. When the count reaches zero, the lock is released and made available to other threads.

##### _volatile _

//TODO

#### "Wait and Notify" monitor (aka. "Signal and Continue" monitor)

In this kind of monitor, a thread that currently owns the monitor can suspend itself inside the monitor by executing a _wait command_. When a thread executes a wait, it releases the monitor and enters a _wait set_. The thread will stay suspended in the wait set until some time after another thread executes a _notify command_ inside the monitor. When a thread executes a notify, it continues to own the monitor until it releases the monitor of its own accord, either by executing a wait or by completing the monitor region. After the notifying thread has released the monitor, the waiting thread will be resurrected and will reacquire the monitor.

a. When a thread has acquired the monitor and invoke wait() on this object, this thread release its acquired monitors, and enter the waiting set.
b. When another thread has acquired the monitor and invoke notify() or notifyAll() on the same object, then the waiting state of one thread or all threads in the waiting area ends and the thread starts attempting to regain all the monitors. At one time there may be several threads trying to regain (or maybe gain for the first time) their monitors. If more than one threads attempt to acquire the monitor of a particular object then only one thread (selected by the JVM scheduler) is granted the monitor and all other threads are put into BLOCKED state.

The following pictures demonstrate the lock and wait/notify mechanism:
> Legend: Blue - BLOCKED     Green - RUNNABLE (running)       White - WAITING
> 
> 
> Threads in waiting state can be interrupted, but cannot in blocked state.
[caption id="attachment_209" align="alignleft" width="235"]![11](/media/11.gif) Thread 1 acquires the lock and starts running; other threads cannot acquire the lock, so they are BLOCKED[/caption]

[caption id="attachment_202" align="alignleft" width="235"]![12](/media/12.gif) Thread 1 invokes wait(), releases the lock and gets await; thread 4 acquires the lock and starts running[/caption]

[caption id="attachment_203" align="alignleft" width="235"]![13](/media/13.gif) Thread 4 also invokes wait() ; thread 3 acquire the released lock and starts running[/caption]

[caption id="attachment_204" align="alignleft" width="235"]![14](/media/14.gif) Thread 4 exits; thread 2 acquires the lock and starts running[/caption]

[caption id="attachment_206" align="alignleft" width="235"]![16](/media/16.gif) Thread 2 exits; thread 4 reacquires the lock and gets running[/caption]

[caption id="attachment_207" align="alignleft" width="235"]![17](/media/17.gif) Thread 4 exits; thread 1 reacquires the lock and goes running once again.[/caption]

##### Examples

1\. A typical scenario is that there is a customer account which can be deposited and fetched at the same time.


```java
public class ATM {

    final static CyclicBarrier cyclicBarrier = new CyclicBarrier(4);
    public static void main(String[] args) throws InterruptedException {
        final Account account = new Account();

        ThreadGroup group = new ThreadGroup("group");

        for(int i = 11; i &lt; 13; ++i) {
            new Thread("D #" + i) {
                public void run() {
                    try {
                        cyclicBarrier.await();
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    } catch (BrokenBarrierException e) {
                        e.printStackTrace();
                    }
                    account.deposit(amount());
                }
            }.start();

            new Thread("W #" + i) {
                public void run() {
                    try {
                        cyclicBarrier.await();
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    } catch (BrokenBarrierException e) {
                        e.printStackTrace();
                    }
                    account.withdraw(amount());
                }
            }.start();
        }
    }

    private static int amount() {
        return (int)(Math.random() * 100);
    }
}

class Account {
    private String id;
    private String customerName;
    private int balance = 0;

    public synchronized void deposit(int amount) {
        int oldBalance = balance;
        balance += amount;
        print("Balance: ￥%d + ￥%d =&gt; ￥%d", oldBalance, amount, balance);
        notifyAll();
    }

    public synchronized void withdraw(int amount) {
        while(balance &lt; amount) {
            try {
                print("Balance: ￥%d - ￥%d =&gt; FAIL", balance, amount);
                wait();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
        int oldBalance = balance;
        balance -= amount;
        print("Balance: ￥%d - ￥%d =&gt; ￥%d", oldBalance, amount, balance);
    }

    private void print(String message, Object...args) {
        PrintStream ps = System.out;
        ps.format("%s: ", Thread.currentThread().getName());
        ps.format(message, args);
        ps.println();
    }
}
```

One output is:
W #11: Balance: ￥0 - ￥5 =&gt; FAIL
W #12: Balance: ￥0 - ￥23 =&gt; FAIL
D #12: Balance: ￥0 + ￥30 =&gt; ￥30
D #11: Balance: ￥30 + ￥90 =&gt; ￥120
W #12: Balance: ￥120 - ￥23 =&gt; ￥97
W #11: Balance: ￥97 - ￥5 =&gt; ￥92

Another output which will be await always:
W #11: Balance: ￥0 - ￥54 =&gt; FAIL
W #12: Balance: ￥0 - ￥93 =&gt; FAIL
D #12: Balance: ￥0 + ￥89 =&gt; ￥89
D #11: Balance: ￥89 + ￥28 =&gt; ￥117
W #12: Balance: ￥117 - ￥93 =&gt; ￥24
W #11: Balance: ￥24 - ￥54 =&gt; FAIL
..(waiting here)..
