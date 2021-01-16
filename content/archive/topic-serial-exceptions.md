---
title: Java Exceptions
toc: true
id: 145
categories:
  - 'Coding &amp; Debuging'
  - Exception
  - Exception Handling
date: "2015-01-19T02:00:33+00:00"
---

An exception is said to be <span class="emphasis">_thrown_</span> from the point where it occurred and is said to be <span class="emphasis">_caught_</span> at the point to which control is transferred.

### exception family tree

[![IMG_2690](/media/IMG_2690.png)](/media/IMG_2690.png)

All exceptions are inherited from Throwable.

All run-time exception classes and error classes are unchecked exception classes, and the rest are checked exception classes.

That is, the checked exception classes are all subclasses of `Throwable` other than `RuntimeException` and its subclasses and `Error` and its subclasses.

### play with finally block

If you put a finally block after a try and its associated catch blocks, then once execution enters the try block, the code in that finally block will definitely be executed except in the following circumstances:

1.  An exception arising in the finally block itself.
2.  The death of the thread.
3.  The use of _System.exit()_
4.  Turning off the power to the CPU.
finally block is always executed _before_ the try-catch blocks exit, including:

1) return


```java
void foo() {
    try {
        return;
    } finally {
        System.out.println("finally executed"); // execute before return
    }
}
```

2) no exception, execute sequentially


```java
void foo() {
    try {
        // some code
    } finally {
        System.out.println("finally executed"); // execute before try block ends
    }
}
```

3) has exception, but no catch


```java
void foo() {
    try {
        throw new Exception("11");
        // some code
    } finally {
        System.out.println("finally executed"); // execute before throwing the exception
    }
}
```

4) has exception, but caught successfully


```java
void foo() {
    try {
        throw new Exception("11");
        // some code
    } catch(Exception e) {
        // some code
    } finally {
        System.out.println("finally executed"); // execute before the catch block ends
    }
}
```

5) has exception, try to catch but failed


```java
void foo() {
    try {
        throw new Exception("11");
        // some code
    } catch(RuntimeException e) {
        // some code
    } finally {
        System.out.println("finally executed"); // execute before throwing the exception
    }
}
```

For more details about finally behavior, please refer to [finally execution in try-catch-finnally blocks](http://richdyang.com/finally-execution-in-try-catch-finnally-blocks)

### exception catch order

If class BaseException is the super class of class DerivedException, and you write the catch clause of BaseException ahead of DerivedException, then a compilation error will show.


```java
try
{
    int x = 0;
    int y = 5 / x;
}
catch (Exception e)
{
    System.out.println("Exception");
}
catch (ArithmeticException ae) // compiler error: Exception "java.lang.ArithmeticException" has already been caught
{
    System.out.println(" Arithmetic Exception");
}
System.out.println("finished");
```

