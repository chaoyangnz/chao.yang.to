---
title: 'Double Checked Locking in lazy-initialization Singleton'
toc: true
date: "2016-09-02T19:36:25+00:00"
---

# double-checked locking idiom (aka. DCL)

## broken versions

Here we follow the evolution process of how to create a lazy-initialization singleton.

```java 
// naive version
public class Singleton {
    
    private static Singleton DEFAULT;
    
    public static Singleton getDefault() {
        if(DEFAULT == null) {
            DEFAULT = new Singleton();
        }
        return DEFAULT;
    }
    
    private Singleton() {}

    // other instance methods

}
```


Race condition:
- when thread 1 has executed `if(DEFAULT == null)` and then is preempted by thread 2.
- Thread 2 checks DEFAULT is null, it instantiates the DEFAULT.
- Thread 1 starts executing again, then it instantiates the DEFAULT once again and overwrites the DEFAULT reference.

Actually, more than one instances are created in the heap.

Now we fix it to synchronize the instantiation.

```java 
// synchronized version
public class Singleton {
    
    private static Singleton DEFAULT;
    
    public static synchronized Singleton getDefault() {
        if(DEFAULT == null) {
            DEFAULT = new Singleton();
        }
        return DEFAULT;
    }
    
    private Singleton() {}

    // other instance methods

}
```


whenever we need to fetch the singleton, we need to synchronize no matter whether the singleton is null.

This version is inherently thread safe but not efficient.

Now we continue to fix it by reducing the locking range.

```java 
// reducing locking range
public class Singleton {
    
    private static Singleton DEFAULT;
    
    public static Singleton getDefault() {
        if(DEFAULT == null) {
            synchronized(Singleton.class) {
                DEFAULT = new Singleton();
            }
        }
        return DEFAULT;
    }
    
    private Singleton() {}

    // other instance methods

}
```

 
Race condition:
- when thread 1 has executed `if(DEFAULT == null) {` and then fail to acquire lock which is occupied by thread 2.
- Thread 2 instantiates the DEFAULT and releases the lock.
- Thread 1 acquire the lock and continue to execute, then it instantiates the DEFAULT once again and overwrites the DEFAULT reference.

<!-- more -->
We continue to fix the problem.

```java 
// double-checked locking
public class Singleton {
    
    private static Singleton DEFAULT;
    
    public static Singleton getDefault() {
        if(DEFAULT == null) {
            synchronized(Singleton.class) {
                if(DEFAULT == null) {
                    DEFAULT = new Singleton();
                }
            }
        }
        return DEFAULT;
    }
    
    private Singleton() {}

    // other instance methods

}
```


But this still has problems.
`DEFAULT = new Singleton()` can be reordered by compiler or cpu. 
It may be compiled to multiple instructions:

```

1 new #1 <playground/singleton/naive/Singleton>
2 dup
3 invokespecial #19 <playground/singleton/naive/Singleton.<init>>
4 putstatic #17 <playground/singleton/naive/Singleton.DEFAULT>
```


The 4th instruction can even be executed before the 3rd instruction.
*NOTE: Usually, compiler reordering happens in native instruction level, so JIT compilier, not AoT compiler does that. Here we use bytecode instructions just for demonstrations.*

That means another thread can see a non-null reference to a Singleton object, but see the default values for fields of the Singleton object, rather than the values set in the constructor.
Using the partially constructed object is of some risks.

> synchronized and reordering
> Does synchronized prevent reordering?
> It prevents some re-ordering. You can **still have re-ordering outside the synchronized block and inside the synchronized block, but not from inside a synchronized block, to outside it**.

The above cases can be found with details here: [The "Double-Checked Locking is Broken" Declaration](http://www.cs.umd.edu/~pugh/java/memoryModel/DoubleCheckedLocking.html)

## workable version

However, after JDK 5.0, the fixed JMM make `volatile` have clear semantics. So we can make the above version workable.

```java 
// double-checked locking enhanced by volatile; this version is workable.
public class Singleton {
    
    private static volatile Singleton DEFAULT;
    
    public static Singleton getDefault() {
        if(DEFAULT == null) {
            synchronized(Singleton.class) {
                if(DEFAULT == null) {
                    DEFAULT = new Singleton();
                }
            }
        }
        return DEFAULT;
    }
    
    private Singleton() {}

    // other instance methods

}
```


Here, the `volatile` is the key. From the semantics of `volatile`, it makes the actions that happen before the write to helper in the code must, when the program executes, actually happen before the write to helper — no sneaky reordering is allowed.

```

1 new #1 <playground/singleton/naive/Singleton>
2 dup
3 invokespecial #19 <playground/singleton/naive/Singleton.<init>>
|__ store barrier __|
4 putstatic #17 <playground/singleton/naive/Singleton.DEFAULT>
```


You can see there should be a store barrier for pubstatic, which make the instructions before it cannot be reordered to after it.

Please refer to http://jeremymanson.blogspot.jp/2008/05/double-checked-locking.html

# Better ways

I don't know how the lazy-initialization makes sense.
Some more concise and thread-safe eager-initialization ways:

```java 
// final
public class Singleton {
    
    public static Singleton DEFAULT = new Singleton();
    
    private Singleton() {}

    // other instance methods

}
```


or static factory way:



```java
// final / static factory
public class Singleton {
    
    private static Singleton DEFAULT = new Singleton();
    
    public static Singleton getDefault() {
        return DEFAULT;
    }
    
    private Singleton() {}

    // other instance methods

}
```


`<clinint>` is invoked by JVM, it can guarantee the thread safe and the write visible to threads. For better reasoning, we can add extra `final` to the static field.

Refer to:
https://docs.oracle.com/javase/specs/jls/se8/html/jls-12.html#jls-12.4.2
https://docs.oracle.com/javase/specs/jls/se8/html/jls-17.html#jls-17.5

"Java Concurrency in Practice" provides a way of lazy-initialization placeholder pattern.



```java
// lazy-initialization placeholder
public class Singleton {
    private static class SingletonHolder {
        public static Singleton DEFAULT = new Singleton();
    }
    
    public static Singleton getDefault() {
        return SingletonHolder.DEFAULT;
    }
    
    private Singleton() {}

    // other instance methods

}
```


Another way is to use `enum`.



```java
// enum
public enum Singleton {
    
    DEFAULT;
    
    private Singleton() {}

    // other instance methods

}
```


This way is from the book "Effective Java".


