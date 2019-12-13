---
title: 'Java array has length field?'
toc: true
date: "2016-08-22T21:36:25+00:00"
---

Today, a coworker asked me in which part of object header the length of an array object is stored. Since in my impression, the object header is of two machine words. But a book said for array, there must be length information stored in the object header. But where?

# array is special

There is no "class definition" of an array (you can't find it in any .class file), they're a part of the language itself.

This is quoted from JLS:

- - -

> 10.7. Array Members

> The members of an array type are all of the following:

> The public final field length, which contains the number of components of the array. length may be positive or zero.
> The public method clone, which overrides the method of the same name in class Object and throws no checked exceptions. The return type of the clone method of an array type T[] is T[].

> A clone of a multidimensional array is shallow, which is to say that it creates only a single new array. Subarrays are shared.
> All the members inherited from class Object; the only method of Object that is not inherited is its clone method.


# length is a field of array class?
No. It is not a field of an array class. You cannot reflect it from its class.



```java
String[] arr = {"a", "b"};
Field field = arr.getClass().getField("length");
System.out.println(field.get(arr));
```


To be disappointed, you cannot get this `length` field.
<!-- more -->
# specify length when array creation


```java
	public void foo(int i) {
		String[] arr = new String[i];
		int l = arr.length;
	}
```

	
The following is the decompiled bytecode:
	


```bytecode
	public void foo(int);
    flags: ACC_PUBLIC
    Code:
      stack=1, locals=4, args_size=2
         0: iload_1
         1: anewarray     #16                 // class java/lang/String
         4: astore_2
         5: aload_2
         6: arraylength
         7: istore_3
         8: return
      LineNumberTable:
        line 6: 0
        line 7: 5
        line 8: 8
      LocalVariableTable:
        Start  Length  Slot  Name   Signature
               0       9     0  this   Lplayground/ArrayLength;
               0       9     1     i   I
               5       4     2   arr   [Ljava/lang/String;
               8       1     3     l   I
   
```


# get length using specilized opcode

`arraylength` is a specialized opcode to get the array length.


# where to store this length

This is really JVM implementation related. From the [The Java HotSpot Performance Engine Architecture](http://www.oracle.com/technetwork/java/whitepaper-135217.html#memory)

- - -
> Two-Word Object Headers
> The Java HotSpot VM uses a two machine-word object header, as opposed to three words in the Classic VM. Since the average Java object size is small, this has a significant impact on space consumption -- saving approximately eight percent in heap size for typical applications. The first header word contains information such as the identity hash code and GC status information. The second is a reference to the object's class. **Only arrays have a third header field, for the array size**.

_NOTE: Hotspot uses an extra word in the object header to store the array length._

To verfiy this statement, we write some code to view the object header



```java
import org.openjdk.jol.info.ClassLayout;
import org.openjdk.jol.vm.VM;

public class ObjectHeaderView {

	public static void main(String[] args) throws Exception {
        System.out.println(VM.current().details());
        
        A a = new A();
        A[] arr = new A[100];
        System.out.println(ClassLayout.parseInstance(a).toPrintable());
        System.out.println(ClassLayout.parseInstance(arr).toPrintable());
    }
}

class A {}
```


This is the output:


```text

# Running 64-bit HotSpot VM.
# Using compressed oop with 3-bit shift.
# Using compressed klass with 3-bit shift.
# WARNING | Compressed references base/shifts are guessed by the experiment!
# WARNING | Therefore, computed addresses are just guesses, and ARE NOT RELIABLE.
# WARNING | Make sure to attach Serviceability Agent to get the reliable addresses.
# Objects are 8 bytes aligned.
# Field sizes by type: 4, 1, 1, 2, 2, 4, 4, 8, 8 [bytes]
# Array element sizes: 4, 1, 1, 2, 2, 4, 4, 8, 8 [bytes]

playground.A object internals:
 OFFSET  SIZE  TYPE DESCRIPTION                    VALUE
      0     4       (object header)                01 00 00 00 (00000001 00000000 00000000 00000000) (1)
      4     4       (object header)                00 00 00 00 (00000000 00000000 00000000 00000000) (0)
      8     4       (object header)                33 de 0c f8 (00110011 11011110 00001100 11111000) (-133374413)
     12     4       (loss due to the next object alignment)
Instance size: 16 bytes
Space losses: 0 bytes internal + 4 bytes external = 4 bytes total

[Lplayground.A; object internals:
 OFFSET  SIZE         TYPE DESCRIPTION                    VALUE
      0     4              (object header)                01 00 00 00 (00000001 00000000 00000000 00000000) (1)
      4     4              (object header)                00 00 00 00 (00000000 00000000 00000000 00000000) (0)
      8     4              (object header)                85 de 0c f8 (10000101 11011110 00001100 11111000) (-133374331)
     12     4              (object header)                64 00 00 00 (01100100 00000000 00000000 00000000) (100)
     16   400 playground.A [Lplayground.A;.<elements>     N/A
Instance size: 416 bytes
Space losses: 0 bytes internal + 0 bytes external = 0 bytes total
```


You can see, for JDK 7 on 64-bit MacOS:
- normal object has one-word (64-bit) mark word and half-word (32-bit) kclass pointer(due to use of compressed oop). 
- array object has one-word mark word, half-word kclass pointer and extra half-word arrary size. (in the example, its value is 100).

More implementation details, please refer to:
- [markOop.hpp](http://hg.openjdk.java.net/jdk8/jdk8/hotspot/file/87ee5ee27509/src/share/vm/oops/markOop.hpp)
- [oop.hpp](http://hg.openjdk.java.net/jdk8/jdk8/hotspot/file/87ee5ee27509/src/share/vm/oops/oop.hpp)

