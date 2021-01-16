---
title: Java Object Initialization Order
toc: true
id: 10
categories:
  - OOP
date: "2013-01-12T02:22:46+00:00"
---

See the following program, and you guess the running result:


```java
public class Dog {
    public static Dog dog = new Dog();
    static final int val1 = -5;
    static int val2 = 3;
    public int val3;

    public Dog() {
        val3 = val1 + val2;
    }

    public static void main(String[] args) {
       System.out.println(Dog.dog.val3);
    }
}
```

Now, I tell you the result is -5. But why?

To clarify this confusing initialization order, you must know a little about the memory layout and what the JVM do (imagine yourself as the compiler and VM).

Because static fields belong to the Class which located in the method area of JVM. The class initialization occurs in the class loading process by the class loader. The process is more or less like the one of plain object initialization other than the member fields of classes are static and class initialization doesn't invoke any user-defined constructor.

One thing I must mention is that -5 is just CONSTANT, so assigning it to val1 means it is hard-corded into class file at compile-time. Therefore, **final class variables and fields of interfaces whose values are compile-time constants are initialized first**.
具有编译器常量的final类变量首先初始化！！
[<span class="qlink_container">http://docs.oracle.com/ja<wbr />vase/sp...</span>](http://docs.oracle.com/javase/specs/jls/se6/html/execution.html#12.4.2)

[<span style="background-color: #ffff00;">Next, execute either the class variable initializers and static initializers of the class, or the field initializers of the interface, in textual order, <span style="background-color: #ff6600;">as though they were a single block</span>, except that `final` class variables and fields of interfaces whose values are compile-time constants are initialized first (§8.3.2.1, §9.3.1, §13.4.9).</span>](http://docs.oracle.com/javase/specs/jls/se6/html/execution.html#12.4.2)

To verify your thought, please see the decompiled bytecodes:
```

Classfile /playground/bin/playground/Dog.class
  Last modified Aug 25, 2016; size 725 bytes
  MD5 checksum 73b977e92851a8923319ee5a1032171a
  Compiled from "Dog.java"
public class playground.Dog
  SourceFile: "Dog.java"
  minor version: 0
  major version: 51
  flags: ACC_PUBLIC, ACC_SUPER
Constant pool:
   #1 = Class              #2             //  playground/Dog
   #2 = Utf8               playground/Dog
   #3 = Class              #4             //  java/lang/Object
   #4 = Utf8               java/lang/Object
   #5 = Utf8               dog
   #6 = Utf8               Lplayground/Dog;
   #7 = Utf8               val1
   #8 = Utf8               I
   #9 = Utf8               ConstantValue
  #10 = Integer            -5
  #11 = Utf8               val2
  #12 = Utf8               val3
  #13 = Utf8               <clinit>
  #14 = Utf8               ()V
  #15 = Utf8               Code
  #16 = Methodref          #1.#17         //  playground/Dog."<init>":()V
  #17 = NameAndType        #18:#14        //  "<init>":()V
  #18 = Utf8               <init>
  #19 = Fieldref           #1.#20         //  playground/Dog.dog:Lplayground/Dog;
  #20 = NameAndType        #5:#6          //  dog:Lplayground/Dog;
  #21 = Fieldref           #1.#22         //  playground/Dog.val2:I
  #22 = NameAndType        #11:#8         //  val2:I
  #23 = Utf8               LineNumberTable
  #24 = Utf8               LocalVariableTable
  #25 = Methodref          #3.#17         //  java/lang/Object."<init>":()V
  #26 = Fieldref           #1.#27         //  playground/Dog.val3:I
  #27 = NameAndType        #12:#8         //  val3:I
  #28 = Utf8               this
  #29 = Utf8               main
  #30 = Utf8               ([Ljava/lang/String;)V
  #31 = Fieldref           #32.#34        //  java/lang/System.out:Ljava/io/PrintStream;
  #32 = Class              #33            //  java/lang/System
  #33 = Utf8               java/lang/System
  #34 = NameAndType        #35:#36        //  out:Ljava/io/PrintStream;
  #35 = Utf8               out
  #36 = Utf8               Ljava/io/PrintStream;
  #37 = Methodref          #38.#40        //  java/io/PrintStream.println:(I)V
  #38 = Class              #39            //  java/io/PrintStream
  #39 = Utf8               java/io/PrintStream
  #40 = NameAndType        #41:#42        //  println:(I)V
  #41 = Utf8               println
  #42 = Utf8               (I)V
  #43 = Utf8               args
  #44 = Utf8               [Ljava/lang/String;
  #45 = Utf8               SourceFile
  #46 = Utf8               Dog.java
{
  public static playground.Dog dog;
    flags: ACC_PUBLIC, ACC_STATIC


  static final int val1;
    flags: ACC_STATIC, ACC_FINAL
    ConstantValue: int -5


  static int val2;
    flags: ACC_STATIC


  public int val3;
    flags: ACC_PUBLIC


  static {};
    flags: ACC_STATIC
    Code:
      stack=2, locals=0, args_size=0
         0: new           #1                  // class playground/Dog
         3: dup           
         4: invokespecial #16                 // Method "<init>":()V
         7: putstatic     #19                 // Field dog:Lplayground/Dog;
        10: iconst_3      
        11: putstatic     #21                 // Field val2:I
        14: return        
      LineNumberTable:
        line 4: 0
        line 6: 10
      LocalVariableTable:
        Start  Length  Slot  Name   Signature

  public playground.Dog();
    flags: ACC_PUBLIC
    Code:
      stack=3, locals=1, args_size=1
         0: aload_0       
         1: invokespecial #25                 // Method java/lang/Object."<init>":()V
         4: aload_0       
         5: bipush        -5
         7: getstatic     #21                 // Field val2:I
        10: iadd          
        11: putfield      #26                 // Field val3:I
        14: return        
      LineNumberTable:
        line 8: 0
        line 9: 4
        line 10: 14
      LocalVariableTable:
        Start  Length  Slot  Name   Signature
               0      15     0  this   Lplayground/Dog;

  public static void main(java.lang.String[]);
    flags: ACC_PUBLIC, ACC_STATIC
    Code:
      stack=2, locals=1, args_size=1
         0: getstatic     #31                 // Field java/lang/System.out:Ljava/io/PrintStream;
         3: getstatic     #19                 // Field dog:Lplayground/Dog;
         6: getfield      #26                 // Field val3:I
         9: invokevirtual #37                 // Method java/io/PrintStream.println:(I)V
        12: return        
      LineNumberTable:
        line 12: 0
        line 13: 12
      LocalVariableTable:
        Start  Length  Slot  Name   Signature
               0      13     0  args   [Ljava/lang/String;
}
```


Let's illustrate the whole process using pictures:
<div>![](/media/main-qimg-ba927672afe463e722864c4843bf4e3e.webp)</div>
To summarize the process of creating an object, consider a class called Dog:
1). Even though it doesn’t explicitly use the static keyword, the constructor is actually a static method. So the first time an object of type Dog is created, _or_the first time a static method or static field of class Dog is accessed, the Java interpreter must locate Dog.class, which it does by searching through the classpath.
2). As Dog.class is loaded (creating a Class object, which you’ll learn about later), all of its static initializers are run. Thus, static initialization takes place only once, as the Class object is loaded for the first time.
3). When you create a new Dog( ), the construction process for a Dog object first allocates enough storage for a Dog object on the heap.
4). This storage is wiped to zero, automatically setting all the primitives in that Dog object to their default values (zero for numbers and the equivalent for boolean and char) and the references to null.
5). Any initializations that occur at the point of field definition are executed.
6). Constructors are executed. This might actually involve a fair amount of activity, especially when inheritance is involved.

&nbsp;

The following is a complicated example:


```java
public class Test {

    private static int k = 1;
    private static Test t1 = new Test("t1");
    private static Test t2 = new Test("t2");
    private static int n = 99;
    private static int j = print("j");

    private int i = print("i");

    {
        print("构造块");
    }

    static {
        print("静态构造块");
    }

    public Test(String str) {
        System.out.println((++k) + ":" + str +"  j="+j+" n="+n);
        ++j;
        ++n;
    }

    public static int print(String str) {
        System.out.println((++k) + ":" + str +"  j="+j+" n="+n);

        ++n;
        return ++j;
    }

    public static void main(String[] args) {

    }
}
```

the result is:

2:i j=0 n=0
3:构造块 j=1 n=1
4:t1 j=2 n=2
5:i j=3 n=3
6:构造块 j=4 n=4
7:t2 j=5 n=5
8:j j=6 n=99
9:静态构造块 j=7 n=100

&nbsp;

