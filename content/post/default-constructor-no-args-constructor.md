---
title: Default constructor (no-args constructor)
toc: true
id: 30
categories:
  - OOP
date: "2013-01-12T02:54:20+00:00"
---

Default constructor is just synthesized by the compiler when you don't define any constructor.

Once you define a constructor, the compiler won't make one for you.



```java

class C {

    public C(int a) { }

    public static void main(String[] args) {
       new C(); // so compiler will issue an error message here
    }
}
```


&nbsp;
