---
title: 'How JVM handle method invocation'
toc: true
date: "2016-08-22T14:39:25+00:00"
---

# Introduction
Five forms of method invocation in Java:
- `invokevirtual`
- `invokeinterface`
- `invokestatic`
- `invokespecial`
- `invokedynamic`

# invokestatic and invokevirtual
Of these, `invokestatic` and `invokevirtual` are easy to understand. 
`invokevirtual` is the default behaviour when we invoke an instance method.
`invokestatic` is used to invoke the class method based on the type of the reference, not the class of the object when we invoke the static method through the instance rather than the class name.



```java
class Superclass {
    public static void interestingMethod() {
        System.out.println("Superclass's interesting method.");
    }
}
class Subclass extends Superclass {
    public static void interestingMethod() {
        System.out.println("Subclass's interesting method.");
    }
    public static void main(String args[]) {
        Subclass sub = new Subclass();
        sub.interestingMethod();

        Superclass sub1 = new Subclass();
        sub1.interestingMethod();
    }
}
```


It will print:
Subclass's interesting method.
Superclass's interesting method.
<!-- more -->
# invokespecial
Now let's see where `invokespecial` is used.
- invocation of instance initialization (`<init>`) methods
- invocation of methods using the `super` keyword
- invocation of `private` methods



```java
class Superclass {
    private void interestingMethod() {
        System.out.println("Superclass's interesting method.");
    }
    void exampleMethod() {
        interestingMethod(); // invokespecial
    }
}
class Subclass extends Superclass {
    void interestingMethod() {
        System.out.println("Subclass's interesting method.");
    }
    public static void main(String args[]) {
        Subclass me = new Subclass();
        me.exampleMethod();
    }
}
```

This code snippet will print: Superclass's interesting method.

Now we just change `Superclass#interestingMethod()` to `package` or `protected` modifier. Then, it will print: Subclass's interesting method.

# invokeinterface vs. invokevirtual
`invokeinterface` opcode performs the same function as `invokevirtual`. The only difference is that `invokeinterface` is used when the reference is of an interface type.

## difference explanation
But why we need a different opcode: `invokevirtual` vs. `invokeinterface`?
The essential idea is that `invokevirtual` makes it possible for JVM to cache methods resolution results.

This is an explanation from Stackoverflow.

-------

> Each Java class is associated with a virtual method table that contains "links" to the bytecode of each method of a class. That table is inherited from the superclass of a particular class and extended with regard to the new methods of a subclass. E.g.,

> class BaseClass {
>     public void method1() { }
>     public void method2() { }
>     public void method3() { }
> }

> class NextClass extends BaseClass {
>     public void method2() { } // overridden from BaseClass
>     public void method4() { }
> }
> results in the tables

> BaseClass
> 1. BaseClass/method1()
> 2. BaseClass/method2()
> 3. BaseClass/method3()

> NextClass
> 1. BaseClass/method1()
> 2. NextClass/method2()
> 3. BaseClass/method3()
> 4. NextClass/method4()
> Note, how the virtual method table of NextClass retains the order of entries of the table of BaseClass and just overwrites the "link" of method2() which it overrides.

> An implementation of the JVM can thus optimize a call to invokevirtual by remembering that BaseClass/method3() will always be the third entry in the virtual method table of any object this method will ever be invoked on.

> With invokeinterface this optimization is not possible. E.g.,

> interface MyInterface {
>     void ifaceMethod();
> }

> class AnotherClass extends NextClass implements MyInterface {
>     public void method4() { } // overridden from NextClass
>     public void ifaceMethod() { }
> }

> class MyClass implements MyInterface {
>     public void method5() { }
>     public void ifaceMethod() { }
> }
> This class hierarchy results in the virtual method tables

> AnotherClass
> 1. BaseClass/method1()
> 2. NextClass/method2()
> 3. BaseClass/method3()
> 4. AnotherClass/method4()
> 5. MyInterface/ifaceMethod()

> MyClass
> 1. MyClass/method5()
> 2. MyInterface/ifaceMethod()
> As you can see, AnotherClass contains the interface's method in its fifth entry and MyClass contains it in its second entry. To actually find the correct entry in the virtual method table, a call to a method with invokeinterface will always have to search the complete table without a chance for the style of optimization that invokevirtual does.

> There are additional differences like the fact, that invokeinterface can be used together with object references that do not actually implement the interface. Therefore, invokeinterface will have to check at runtime whether a method exists in the table and potentially throw an exception. If you want to dive deeper into the topic, I suggest, e.g., "Efficient Implementation of Java Interfaces: Invokeinterface Considered Harmless".

## vitual / interface dispatching performance benchmark

A benchmark code to compare invokevirtual with invokeinterface performance. invokeinterface is 38% slower. If volatile modifier is removed from `aand i` (and it becomes possible for JVM to cache methods resolution results), the performance becomes comparable for both cases and the code runs ~10x faster than with volatile variables. 
A background in essence is "interfaces do not exist in runtime" so virtual dispatching can't be applied to interface methods. The idea is very well explained above. 
Below is a benchmark to compare `invokevirtual` with `invokeinterface` performance. Prevents JVM from optimizing out `invokeinterface` by declaring target object volatile.



```java
public class InvokevirtualVsInvokeinterface {
  private static interface I {
    public int getInteger ();
  }

  private static class A implements I {
    public int getInteger () { return 0; }
  }

  private static class B extends A { }

  static volatile I i = new B();
  static volatile A a = new B();

  public static void main(String[] args) {
    {
      long tm1 = System.nanoTime();
      for (int k = 0; k < 100000000; ++k) {
        a.getInteger();
      }
      long tm2 = System.nanoTime();
      System.out.println("invokevirtual took " + (Math.abs(tm2 - tm1) / 1000) + " us");
    }

    {
      long tm1 = System.nanoTime();
      for (int k = 0; k < 100000000; ++k) {
        i.getInteger();
      }
      long tm2 = System.nanoTime();
      System.out.println("invokeinterface took " + (Math.abs(tm2 - tm1) / 1000) + " us");
    }

    // Output on Intel Xeon X5570 @ 2.93GHz:
    // invokevirtual took 41170 us
    // invokeinterface took 66305 us
  }
}
```

and the corresponding bytecode (the output of "javap -c")



```bytecode
// Compiled from "InvokevirtualVsInvokeinterface.java"
public class InvokevirtualVsInvokeinterface extends java.lang.Object{
static volatile InvokevirtualVsInvokeinterface$I i;

static volatile InvokevirtualVsInvokeinterface$A a;

public InvokevirtualVsInvokeinterface();
  Code:
   0:	aload_0
   1:	invokespecial	#1; //Method java/lang/Object."<init>":()V
   4:	return

public static void main(java.lang.String[]);
  Code:
   0:	invokestatic	#2; //Method java/lang/System.nanoTime:()J
   3:	lstore_1
   4:	iconst_0
   5:	istore_3
   6:	iload_3
   7:	ldc	#3; //int 100000000
   9:	if_icmpge	25
   12:	getstatic	#4; //Field a:LInvokevirtualVsInvokeinterface$A;
   15:	invokevirtual	#5; //Method InvokevirtualVsInvokeinterface$A.getInteger:()I
   18:	pop
   19:	iinc	3, 1
   22:	goto	6
   25:	invokestatic	#2; //Method java/lang/System.nanoTime:()J
   28:	lstore_3
   29:	getstatic	#6; //Field java/lang/System.out:Ljava/io/PrintStream;
   32:	new	#7; //class java/lang/StringBuilder
   35:	dup
   36:	invokespecial	#8; //Method java/lang/StringBuilder."<init>":()V
   39:	ldc	#9; //String invokevirtual took 
   41:	invokevirtual	#10; //Method java/lang/StringBuilder.append:(Ljava/lang/String;)Ljava/lang/StringBuilder;
   44:	lload_3
   45:	lload_1
   46:	lsub
   47:	invokestatic	#11; //Method java/lang/Math.abs:(J)J
   50:	ldc2_w	#12; //long 1000l
   53:	ldiv
   54:	invokevirtual	#14; //Method java/lang/StringBuilder.append:(J)Ljava/lang/StringBuilder;
   57:	ldc	#15; //String  us
   59:	invokevirtual	#10; //Method java/lang/StringBuilder.append:(Ljava/lang/String;)Ljava/lang/StringBuilder;
   62:	invokevirtual	#16; //Method java/lang/StringBuilder.toString:()Ljava/lang/String;
   65:	invokevirtual	#17; //Method java/io/PrintStream.println:(Ljava/lang/String;)V
   68:	invokestatic	#2; //Method java/lang/System.nanoTime:()J
   71:	lstore_1
   72:	iconst_0
   73:	istore_3
   74:	iload_3
   75:	ldc	#3; //int 100000000
   77:	if_icmpge	95
   80:	getstatic	#18; //Field i:LInvokevirtualVsInvokeinterface$I;
   83:	invokeinterface	#19,  1; //InterfaceMethod InvokevirtualVsInvokeinterface$I.getInteger:()I
   88:	pop
   89:	iinc	3, 1
   92:	goto	74
   95:	invokestatic	#2; //Method java/lang/System.nanoTime:()J
   98:	lstore_3
   99:	getstatic	#6; //Field java/lang/System.out:Ljava/io/PrintStream;
   102:	new	#7; //class java/lang/StringBuilder
   105:	dup
   106:	invokespecial	#8; //Method java/lang/StringBuilder."<init>":()V
   109:	ldc	#20; //String invokeinterface took 
   111:	invokevirtual	#10; //Method java/lang/StringBuilder.append:(Ljava/lang/String;)Ljava/lang/StringBuilder;
   114:	lload_3
   115:	lload_1
   116:	lsub
   117:	invokestatic	#11; //Method java/lang/Math.abs:(J)J
   120:	ldc2_w	#12; //long 1000l
   123:	ldiv
   124:	invokevirtual	#14; //Method java/lang/StringBuilder.append:(J)Ljava/lang/StringBuilder;
   127:	ldc	#15; //String  us
   129:	invokevirtual	#10; //Method java/lang/StringBuilder.append:(Ljava/lang/String;)Ljava/lang/StringBuilder;
   132:	invokevirtual	#16; //Method java/lang/StringBuilder.toString:()Ljava/lang/String;
   135:	invokevirtual	#17; //Method java/io/PrintStream.println:(Ljava/lang/String;)V
   138:	return

static {};
  Code:
   0:	new	#21; //class InvokevirtualVsInvokeinterface$B
   3:	dup
   4:	aconst_null
   5:	invokespecial	#22; //Method InvokevirtualVsInvokeinterface$B."<init>":(LInvokevirtualVsInvokeinterface$1;)V
   8:	putstatic	#18; //Field i:LInvokevirtualVsInvokeinterface$I;
   11:	new	#21; //class InvokevirtualVsInvokeinterface$B
   14:	dup
   15:	aconst_null
   16:	invokespecial	#22; //Method InvokevirtualVsInvokeinterface$B."<init>":(LInvokevirtualVsInvokeinterface$1;)V
   19:	putstatic	#4; //Field a:LInvokevirtualVsInvokeinterface$A;
   22:	return
}
```


# vtable and itable in OpenJDK source code
//In Progress

## references

[Efficient Implementation of Java Interfaces: Invokeinterface Considered Harmless](media/oopsla01.pdf)

