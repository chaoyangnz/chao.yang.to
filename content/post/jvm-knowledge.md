---
title: JVM knowledge
toc: true
id: 936
categories:
  - JVM
date: "2015-04-18T22:28:39+00:00"
---

references:

*   &lt;[Inside the Java Virtual Machine](https://www.artima.com/insidejvm/ed2/index.html)&gt;
*   &lt;[Java Virtual Machine specification](https://docs.oracle.com/javase/specs/jvms/se7/html/) 7&gt;

### run-time data areas

![jvm](/media/jvm.png)

#### pc registers

Every JVM thread has its own pc(program counter) register.

The pc register is one word in size, so it can hold both a native pointer and a `returnAddress`.

If a thread is executing a native method, the value of the pc register is undefined.

#### JVM stacks

Every thread has a private JVM stacks. It stores frames.

It can be of fixed size or dynamically expanded and contracted as required.

The JVM only performs two operations directly on Java Stacks: it pushes and pops frames.

![jvm-stacks-per-thread](/media/jvm-stacks-per-thread.png)

#### heap <script src="file:///c:\program files (x86)\eudic\dat\res\main.js" type="text/javascript"></script>

> <div id="ID679732753" class="explain_wrap">
> 
> <div id="ID679732753Child" class="expDiv"><span style="color: #0000cd;">hiːp</span></div>
> 
> </div>
The JVM has a heap. It stores all class instances and arrays.

Heap storage for objects is reclaimed by an automatic storage management system (aka. GC or garbage collector).

It can be of fixed size or dynamically expanded as required by the computation and may be contracted if larger heap becomes unnecessary.

JVM provides options for **initial size**, and  if the heap can be dynamically expanded or contracted, users can control over the **maximum and minimum size** of the heap.

#### method area

It's logically part of the heap. It stores per-class structures, such as the run-time constant pool, field and method data and the code for methods and constructors (including special methods like &lt;init&gt; and &lt;clinit&gt;).

&nbsp;

per-class structure(class data) includes:

##### Type Information

*   The fully qualified name of the type
*   The fully qualified name of the type's direct superclass (unless the type is an interface or class `java.lang.Object`, neither of which have a superclass)
*   Whether or not the type is a class or an interface
*   The type's modifiers ( some subset of` `public`, `abstract`, `final`)
*   An ordered list of the fully qualified names of any direct superinterfaces

##### run-time Constant Pool

A constant pool is an ordered set of constants used by the type, including literals (string, integer, and floating point constants) and symbolic references to types, fields, and methods.

These symbolic references to all types, fields, and methods used by a type, the constant pool plays a central role in the **_dynamic linking_** of Java programs.

##### Field Information

*   The field's name
*   The field's type
*   The field's modifiers (some subset of `public`, `private`, `protected`, `static`, `final`, `volatile`, `transient`)

##### Method Information

*   The method's name
*   The method's return type (or `void`)
*   The number and types (in order) of the method's parameters
*   The method's modifiers (some subset of `public`, `private`, `protected`, `static`, `final`, `synchronized`, `native`, `abstract`)
for non-abstract method:

*   The method's bytecodes
*   The sizes of the operand stack and local variables sections of the method's stack frame (these are described in a later section of this chapter)
*   An exception table

##### Class Variables

JVM must allocate memory from the method area for each non-final class variable declared in the class.

final class variable (constant) is not here, they are in constant pool instead.

##### A Reference to Class `ClassLoader`

The virtual machine must store a reference to the user-defined class loader that loaded the type. (if the type is loaded by bootstrap class loader, the reference is not required)
> NOTICE: method area just stores the reference to ClassLoader. The ClassLoader instance itself is allocated in the heap.

##### A Reference to Class `Class`

> NOTICE: method area just stores the reference to Class. The Class instance itself is allocated in the heap.

##### Method Tables

A method table is an array of direct references to all the instance methods that may be invoked on a class instance, including instance methodsinherited from superclasses.

A method table allows a virtual machine to quickly locate an instance method invoked on an object.

![method-area-and-heap](/media/method-area-and-heap.gif)

### run-time data structures

#### run-time constant pool

It's a per-class or per-interface representation of the <span style="font-family: 'courier new', courier;">constant_pool</span> table in a <span style="font-family: 'courier new', courier;">class</span> file.

It's constructed when the class or the interface created by JVM.

#### frames

A frame is used to store data and partial result, as well as to perform dynamic linking, return values for methods and dispatch exceptions.

It's created each time a method is invoked.

Frames could be allocated on a contiguous stack, or they could be allocated on a heap, or some combination of both.

In a given thread of control, only the frame for the executing method is active. This active frame is calls **current frame**, and the executing method is called **current method**. The class in which the current method is defined is called** current class**.

![16fig03](/media/16fig03.gif)

Now we will go to the components of a frame:

##### local variables

Each frame contains an array of local variables known as _local variables_.

The length of the local variable array can be determined at compile-time and supplied in a binary class file.

*   A single variable can hold a value of type <span style="font-family: 'courier new', courier;">boolean, byte, char, short, int, float, reference, or returnAddress</span>.
*   A pair of variables can hold a value of type<span style="font-family: 'courier new', courier;"> long, double</span>.
On class method invocation, any parameters are passed in from local variable 0.

On instance method invocation, local variable 0 is always the reference to the instance object. (aka, <span style="font-family: 'courier new', courier;">this</span>)


```java
class Example3a {

    public static int runClassMethod(int i, long l, float f,
        double d, Object o, byte b) {

        return 0;
    }

    public int runInstanceMethod(char c, double d, short s,
        boolean b) {

        return 0;
    }
}
```

![method-parameters-on-local-variables](/media/method-parameters-on-local-variables.png)

##### operand stack

Each frame contains a LIFO stack known as _operand stack_. It's mainly for operations on operands and partial/intermediate result storage.

The maximum depth of the operand stack can be determined at compile-time and supplied in a binary class file.

operations on operand stack:

*   load constants or values from local variables onto the operand stack
*   tack operands from the operand stack, operate on them, and push the result onto the operand stack.


```java
int c = a + b
```

The above source code has the instructions:


```java
iload_0    // push the int in local variable 0
iload_1    // push the int in local variable 1
iadd       // pop two ints, add them, push result
istore_2   // pop int, store into local variable 2
```

![add-two-local-variables](/media/add-two-local-variables.png)

##### Frame Data

The Java stack frame includes data to support constant pool resolution, normal method return, and exception dispatch, also some implementation dependent information, such as data to support debugging.

###### normal method invocation completion

When normal completion, the frame of current method is popped and the return value (if any) is pushed onto the operand stack of the invoker's frame.

###### abrupt method invocation completion

Every method is associated with zero or more _exception handlers_.

When an exception occurs, the JVM searches for a matching exception handler in the current method.

When no such exception handler is found to handle the exception, the current method invocation completes abruptly. At this time, the operand stack and local variables of the current method invocation are discarded, and its frame is popped. Then the exception is rethrowed in the context of the invoker's frame and so on.

If no suitable exception handler is found before the top of method invocation chain is reached, the execution of the thread is terminated.

The following is an possible implementation which allocates frames from the heap.

![frames-in-heap](/media/frames-in-heap.png)

### class file format

This is about binary representation of a class.

### loading, linking and initializing

This is for run-time representation of a class.

### 

&nbsp;

&nbsp;
