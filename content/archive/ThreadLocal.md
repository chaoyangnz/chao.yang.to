---
title: 'ThreadLocal'
toc: true
date: "2016-09-05T19:52:25+00:00"
---


# how to use?

Basically, `ThreadLocal` is used for these scenarios: **whenever you set a value/reference within this thread, you can get it from this thread. But it cannot guarantee you can ONLY get it from this thread.**

When wrongly used, it may cause something counter-intuitive. For example, when you set a value/reference to a ThreadLocal, you also share it with other threads (say other threads can access the reference and mutable it.



```java
public class ThreadLocalHolder {

    private ThreadLocal<SomeObject> threadLocal = new ThreadLocal<SomeObject>() {
        protected SomeObject initialValue() { // set the initial value when first getting or after removing
            return SomeObject.DEFAULT;
        }
    };

    public void foo() {
        getAndPrint();
        threadLocal.set(new SomeObject(Thread.currentThread().getId()));
        getAndPrint();
        threadLocal.remove();
        getAndPrint();
    }

    private void getAndPrint() {
        SomeObject someObject = threadLocal.get();
        System.out.println(String.format("Thread %d: %d", Thread.currentThread().getId(), someObject.getValue()));
    }
}

class SomeObject {
    public static SomeObject DEFAULT = new SomeObject(0L);
    private long value;
    public SomeObject(long value) {
        this.value = value;
    }

    public long getValue() {
        return this.value;
    }

    public void setValue(long value) {
        this.value = value;
    }
}
```




```java
public class Main {
    public static void main(String[] args) throws InterruptedException {
        final ThreadLocalHolder threadLocalHolder = new ThreadLocalHolder();

        final int threadCount = 200;
        final CountDownLatch countDownLatch = new CountDownLatch(threadCount);
        ExecutorService executorService = Executors.newFixedThreadPool(threadCount);
        for(int i=0; i< threadCount; ++i) {
            executorService.submit(new Runnable() {
                public void run() {
                    threadLocalHolder.foo();
                    countDownLatch.countDown();
                }
            });
        }

        countDownLatch.await();
        executorService.shutdown();
    }
}
```


Possible "incorrect" usage: Here we share the reference by setting it to the a field of ThreadLocalHolder, so another thread can mutate its value. From the respective of the first thread, this thread sets it but its state can still be visible and modified by other threads. Sometime it seems to voilate the original purpose of `ThreadLocal`.
<!-- more -->



```java
public class ThreadLocalHolder {

    private ThreadLocal<SomeObject> threadLocal = new ThreadLocal<SomeObject>() {
        protected SomeObject initialValue() { // set the initial value when first getting or after removing
            return SomeObject.DEFAULT;
        }
    };

    public volatile SomeObject shared;

    public void foo() throws InterruptedException {
        getAndPrint();
        this.shared = new SomeObject(Thread.currentThread().getId());
        threadLocal.set(shared);
        Thread.sleep(3000);
        getAndPrint();
        threadLocal.remove();
        getAndPrint();
    }

    private void getAndPrint() {
        SomeObject someObject = threadLocal.get();
        System.out.println(String.format("Thread %d: %d at %d", Thread.currentThread().getId(), someObject.getValue(), System.nanoTime()));
    }
}

class SomeObject {
    public static SomeObject DEFAULT = new SomeObject(0L);
    private long value;
    public SomeObject(long value) {
        this.value = value;
    }

    public long getValue() {
        return this.value;
    }

    public void setValue(long value) {
        this.value = value;
    }
}
```

Simply test it:


```java
public class Main {
    public static void main(String[] args) throws InterruptedException {
        final ThreadLocalHolder threadLocalHolder = new ThreadLocalHolder();

        final int threadCount = 3;
        final CyclicBarrier cyclicBarrier = new CyclicBarrier(threadCount);

        ExecutorService executorService = Executors.newFixedThreadPool(threadCount);
        Runnable runnable = new Runnable() {
            public void run() {
                try {
                    cyclicBarrier.await();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                } catch (BrokenBarrierException e) {
                    e.printStackTrace();
                }
                try {
                    threadLocalHolder.foo();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        };
        executorService.submit(runnable);
        executorService.submit(runnable);
        executorService.submit(new Runnable() {
            public void run() {
                try {
                    cyclicBarrier.await();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                } catch (BrokenBarrierException e) {
                    e.printStackTrace();
                }
                while(threadLocalHolder.shared == null) {

                }
                threadLocalHolder.shared.setValue(9999);
            }
        });
        executorService.shutdown();
    }
}
```


Another hack is we reflection another `Thread`'s `threadlocals`.


```java

```


# implementation details
In the first place, we don't dive into the source code. We can think of how to implement it on our own.

Circumstance: 
- Whenever a native thread is created, a `Thread` object is instantiated in the heap. You can simply think it as the "handle" or "mirror" of the native thread.
- we should be able to define multiple `ThreadLocal`.
- Every `ThreadLocal` is associated with ONLY one value/reference within a thread.

As per the above requirement, it's easy to think of this solution:
- we use `Thread` object to maintain a threadlocal map: [ThreadLocal, Value]
- when `set` is invoked, firstly get the `Thread` object of current thread and get its threadlocal map and put the value into it.
- when `get` is invoked, firstly get the `Thread` object of current thread and get its threadlocal map and get the value from it.

So the most important class here is the `ThreadLocalMap`. Every `Thread` object has a `ThreadLocalMap`: `threadlocals`.

## ThreadLocal.ThreadLocalMap.Entry
The Entry is a `WeakReference` to its key `ThreadLocal`, so it doesn't prevent the `ThreadLocal` from being reclaimed by GC. If all the references to the ThreadLocal object are weak of soft, GC can reclaim it even entry reference to it. At this time, entry.get() will return null and therefore this entry can be expunged from table. Such entries are referred to "stale entries".
![](/media/threadlocalmap-entry.png)

When to clean these "stale entries"?
- Threadlocal#get(..)
- Threadlocal#set(..)
- Threadlocal#remove(..)

![](/media/threadlocalmap-expunge.png)



## ThreadLocalMap: hash table

### open addressing
It don't use ‘LinkList' to resolve conflicts, instead use "Open addressing".

find next slot until the slot is empty


```java
private void set(ThreadLocal<?> key, Object value) {

  // We don't use a fast path as with get() because it is at
  // least as common to use set() to create new entries as
  // it is to replace existing ones, in which case, a fast
  // path would fail more often than not.

  Entry[] tab = table;
  int len = tab.length;
  int i = key.threadLocalHashCode & (len-1);

  for (Entry e = tab[i];
       e != null;
       e = tab[i = nextIndex(i, len)]) {
      ThreadLocal<?> k = e.get();

      if (k == key) {
          e.value = value;
          return;
      }

      if (k == null) {
          replaceStaleEntry(key, value, i);
          return;
      }
  }

  tab[i] = new Entry(key, value);
  int sz = ++size;
  if (!cleanSomeSlots(i, sz) && sz >= threshold)
      rehash();
}
```


How to find next index? Simple, just increment i modulo len
`((i + 1 < len) ? i + 1 : 0);`

### from hashcode to slot index
Like HashMap does:
`hashcode & (length - 1)`

### ThreadLocal hashcode
//TODO



# Is ThreadLocal thread safe?
Refer to:
- http://stackoverflow.com/a/27188314/4344443
- http://stackoverflow.com/a/15653015/4344443

Basically, the ThreadLocalMap itself is not thread-safe, but it is associated with a Thread object, which makes it can be only accessed by one thread. So if you don't use abnormal access ways (like reflection), it can ensure thread-safe.


