---
title: auto-boxing and valueOf(..)
toc: true
id: 485
categories:
  - Basics
date: "2015-03-17T19:20:41+00:00"
---

Autoboxing uses `Integer.valueOf(int)`,which internally caches Integer objects for small integers (by default -128 to 127, but the max value can be configured with the "java.lang.Integer.IntegerCache.high" property.

Double is the same by using Double.valueOf(..)

So the following two lines are equivalent.



```java

Integer i = 12;
Integer i = Integer.valueOf(12);
```


If you don't believe it, you can decompile the .class file to see what the compiler does.
