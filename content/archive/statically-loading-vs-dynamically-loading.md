---
title: statically loading vs. dynamically loading
toc: true
id: 1423
comment: false
categories:
  - Basics
date: "2015-09-05T22:20:18+00:00"
---

### statically loading

when we need to use the class definition, like new operator, invoking static method, accessing static variables etc, the JVM will load the class statically.

But just declare variable of that type, class loading actually don't happen.


```java
class StaticallyLoading {
    public static void main(String[] args) {
        C c = null;
        System.out.println("run here");
    }
}
```

After you compile this class successfully with class C definition, you run it as deleting the class C dependency at runtime.

This program can run normally.

But if you do some operations, like

*   `C c = new C();`
*   `C.foo()`
Once the C class doesn't exist at runtime, a **`NoClassDefFoundException`** will be thrown.

### dynamically loading

*   Class.forName(..)
*   ClassLoader.findSystemClass(..)
*   ClassLoader.findClass(..)
**`ClassNoFoundException`** will be thrown if the class cannot be found at runtime.
