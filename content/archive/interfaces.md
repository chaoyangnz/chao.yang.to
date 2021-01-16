---
title: interfaces
toc: true
id: 62
categories:
  - OOP
date: "2015-01-14T10:48:18+00:00"
---

Before Java 8, interfaces are not allowed to have static method.



```java

interface SS {
    static void bar(); // Cannot be compiled
    static void foo() {} // Cannot be compiled
}
```


The compiler complains: **Extension methods** are not supported at this language level.

But what are Extension methods? That's a term defined in Java 8.

&nbsp;

As for interfaces, all elements in its definition body are public:

1) all fields are <span style="text-decoration: underline;">public</span>, <span style="text-decoration: underline;">final</span> and <span style="text-decoration: underline;">static</span> implicitly.

2) all methods are <span style="text-decoration: underline;">public</span> and <span style="text-decoration: underline;">abstract</span> implicitly.

3) all nested classes, interfaces are <span style="text-decoration: underline;">public</span> and static implicitly, cannot be private!


```java

interface Intfa{
    // this is public and static nested class implicitly
    class nestedClass {}
    // this is public and static nested interface implicitly
    interface nestedInterface {}
}
```


4) static methods are not allowed. member classes or interfaces are not allowed (and has no way to declare).

An appropriate guideline is to **_prefer classes to interfaces_**. Start with classes, and if it becomes clear that interfaces are necessary, then refactor.
