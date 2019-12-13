---
title: static method and hiding
toc: true
id: 56
categories:
  - OOP
date: "2015-01-13T10:22:22+00:00"
---

Class method can be invoked by Class or by instance.

When it is invoked by instance, it uses the type of the instance, to figure out, **<span style="color: #ff0000;">at compile time</span>**, which class method to invoke.
So the polymorphism would not happen as you think.
For static methods, when the static method of the subclasses has the same signature as their superclass, we call it **Hiding**.

In Java, **private **/ **final **/ **static** methods can do hiding by static binding.

Other methods are virtual, and can do overriding by dynamic binding.

1\. hiding by class methods



```java

class Super {
 static String greeting() { return &quot;Goodnight&quot;; }
 String name() { return &quot;Richard&quot;; }
}
class Sub extends Super {
 static String greeting() { return &quot;Hello&quot;; }
 String name() { return &quot;Dick&quot;; }
}
class Test {
 public static void main(String[] args) {
 Super s = new Sub();
 System.out.println(s.greeting() + &quot;, &quot; + s.name());
 }
}
```


// Output: Goodnight Dick

2\. static final method



```java

class A {
    static final void m1(){}
}

class B extends A {
    static void m1(){}
}
```


A method can be declared final to prevent subclasses from overriding or hiding it.
