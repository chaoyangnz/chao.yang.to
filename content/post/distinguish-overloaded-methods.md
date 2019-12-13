---
title: Distinguish overloaded methods
toc: true
id: 23
categories:
  - OOP
date: "2013-01-12T02:36:16+00:00"
---

Each overloaded method must have **a unique list of argument types.**

This means that there are the following possible cases:

1. argument count are different



```java
void foo(int a) {}
void foo(int a, String b) {}
```


2. argument types are different



```java
void foo(int a) {}
void foo(String b) {}
```


3. argument types are different, even if inheritance



```java
void foo(List a) {}
void foo(ArrayList a) {}
```


4. return types are different



```java
void foo(int a) {}
int foo(int b) { return 0; }
```


**NO!! This will have a compiler error.**

When overloaded methods are invoked, compiler choose the best matched method by using the declared types of passing arguments.

So:



```java
public static void main(String[] args) {
    List a = new ArrayList();
    o.foo(a) // This will invoke the &quot;void foo(List a) {}&quot; method
}

```


because overloading is a **static binding** behavior and only occurs in **compile time**.

When considering which overloaded method will be invoked, you should always regard yourself as the compiler.

When it comes to the primitives, please see the following example:



```java

class Demotion {
    void f1(char x) {
        System.out.println(&quot;f1(ch&lt;wbr /&gt;ar)&quot;);
    }

    void f1(byte x) {
        System.out.println(&quot;f1(byte)&quot;);
    }

    void f1(short x) {
        System.out.println(&quot;f1(short)&quot;);
    }

    void f1(int x) {
        System.out.println(&quot;f1(int)&quot;);
    }

    void f1(long x) {
        System.out.println(&quot;f1(long)&quot;);
    }

    void f1(float x) {
        System.out.println(&quot;f1(float)&quot;);
    }

    void f1(double x) {
        System.out.println(&quot;f1(double)&quot;);
    }

    void f2(char x) {
        System.out.println(&quot;f2(char)&quot;);
    }

    void f2(byte x) {
        System.out.println(&quot;f2(byte)&quot;);
    }

    void f2(short x) {
        System.out.println(&quot;f2(short)&quot;);
    }

    void f2(int x) {
        System.out.println(&quot;f2(int)&quot;);
    }

    void f2(long x) {
        System.out.println(&quot;f2(long)&quot;);
    }

    void f2(float x) {
        System.out.println(&quot;f2(float)&quot;);
    }

    void f3(char x) {
        System.out.println(&quot;f3(char)&quot;);
    }

    void f3(byte x) {
        System.out.println(&quot;f3(byte)&quot;);
    }

    void f3(short x) {
        System.out.println(&quot;f3(short)&quot;);
    }

    void f3(int x) {
        System.out.println(&quot;f3(int)&quot;);
    }

    void f3(long x) {
       System.out.println(&quot;f3(long)&quot;);
    }

    void f4(char x) {
        System.out.println(&quot;f4(char)&quot;);
    }

    void f4(byte x) {
        System.out.println(&quot;f4(byte)&quot;);
    }

    void f4(short x) {
       System.out.println(&quot;f4(short)&quot;);
    }

    void f4(int x) {
        System.out.println(&quot;f4(int)&quot;);
    }

    void f5(char x) {
        System.out.println(&quot;f5(char)&quot;);
    }

    void f5(byte x) {
        System.out.println(&quot;f5(byte)&quot;);
    }

    void f5(short x) {
        System.out.println(&quot;f5(short)&quot;);
    }

    void f6(char x) {
        System.out.println(&quot;f6(char)&quot;);
    }

    void f6(byte x) {
        System.out.println(&quot;f6(byte)&quot;);
    }

    void f7(char x) {
        System.out.println(&quot;f7(char)&quot;);
    }

    void testDouble() {
        double x = 0;
        System.out.println(&quot;double argument:&quot;);
        f1(x);
        f2((float) x);
        f3((long) x);
        f4((int) x);
        f5((short) x);
        f6((byte) x);
        f7((char) x);
    }

    public static void main(String[] args) {
        Demotion p = new Demotion();
        p.testDouble();
    }
}

```


Output is:
> double argument:
> 
> f1(double)
> 
> f2(float)
> 
> f3(long)
> 
> f4(int)
> 
> f5(short)
> 
> f6(byte)
> 
> f7(char)

From the above example, you can see the same rule: the compiler decide method binding in compile time and it always chooses the best and most possible method. When it is possible, it need casting a type to another. So when some wider types like double, float cast to some narrower types like int, short etc, the precision and width of number will be narrowed. If it cannot do this, an error message will be issued.

