---
title: Inner classes synthetic
toc: true
id: 1454
comment: false
categories:
- Uncategorized
date: "2015-11-04T12:40:59+00:00"
---



```java
import java.util.ArrayList;
import java.util.List;

public class Enclosing {
    private List instanceList = new ArrayList<>();
    { instanceList.add(1);}

    public static void main(String[] args) {
        Enclosing outer = new Enclosing();
        outer.instanceList.add(2);
        outer.foo();

        InnerClass inner = outer.new InnerClass();
        outer.instanceList.add(3);
        inner.bar();
    }

    public void foo() {
        final List localList = new ArrayList<>();
        localList.add(1);

        class LocalClass {
            public void bar() {
                System.out.println(localList);
                System.out.println(instanceList);
            }
        }

        localList.add(2);

        new LocalClass().bar();
    }

    private class InnerClass {
        public void bar() {
            System.out.println(instanceList);
        }
    }
}
```




一旦编译，会生成3个class文件：

* Enclosing.class
* Enclosing$1.class
* Enclosing$1LocalClass.class
* Enclosing$InnerClass.class
  
  为什么会生成一个空的Enclosing$1.class？

下面我们来看看编译器替我们做了哪些事情：

首先看`Enclosing$InnerClass.class`



```java
class corejava/Enclosing$InnerClass {
 synthetic final corejava.Enclosing this$0;

 private Enclosing$InnerClass(corejava.Enclosing arg0) { // <init> //(Lcorejava/Enclosing;)V
     <localVar:index=0 , name=this , desc=Lcorejava/Enclosing$InnerClass;, sig=null, start=L1, end=L2>

     L1 {
         aload0 // reference to self
         aload1
         putfield corejava/Enclosing$InnerClass.this$0:corejava.Enclosing
         aload0 // reference to self
         invokespecial java/lang/Object <init>(()V);
         return
     }
     L2 {
     }
 }

 public bar() { //()V
     <localVar:index=0 , name=this , desc=Lcorejava/Enclosing$InnerClass;, sig=null, start=L1, end=L2>

     L1 {
         getstatic java/lang/System.out:java.io.PrintStream
         aload0 // reference to self
         getfield corejava/Enclosing$InnerClass.this$0:corejava.Enclosing
         invokestatic corejava/Enclosing access$100((Lcorejava/Enclosing;)Ljava/util/List;);
         invokevirtual java/io/PrintStream println((Ljava/lang/Object;)V);
     }
     L3 {
         return
     }
     L2 {
     }
 }

 Enclosing$InnerClass(corejava.Enclosing arg0, corejava.Enclosing$1 arg1) { // <init> //(Lcorejava/Enclosing;Lcorejava/Enclosing$1;)V
     <localVar:index=0 , name=this , desc=Lcorejava/Enclosing$InnerClass;, sig=null, start=L1, end=L2>
     <localVar:index=1 , name=x0 , desc=Lcorejava/Enclosing;, sig=null, start=L1, end=L2>
     <localVar:index=2 , name=x1 , desc=Lcorejava/Enclosing$1;, sig=null, start=L1, end=L2>

     L1 {
         aload0 // reference to self
         aload1 // reference to arg0
         invokespecial corejava/Enclosing$InnerClass <init>((Lcorejava/Enclosing;)V);
         return
     }
     L2 {
     }
 }

 synthetic class corejava/Enclosing$1 {
 }
 }
```



很显然，编译器替我们合成了一个构造函数，传入的参数正是外部的实例对象。
再看看`Enclosing$1LocalClass.class`:



```java
class corejava/Enclosing$1LocalClass {
 synthetic final java.util.List val$localList;
 synthetic final corejava.Enclosing this$0;

 Enclosing$1LocalClass(corejava.Enclosing arg0, java.util.List arg1) { // <init> //(Lcorejava/Enclosing;Ljava/util/List;)V
     <sig:()V>         <localVar:index=0 , name=this , desc=Lcorejava/Enclosing$1LocalClass;, sig=null, start=L1, end=L2>
     <localVar:index=1 , name=this$0 , desc=Lcorejava/Enclosing;, sig=null, start=L1, end=L2>

     L1 {
         aload0 // reference to self
         aload1 // reference to arg0
         putfield corejava/Enclosing$1LocalClass.this$0:corejava.Enclosing
         aload0 // reference to self
         aload2
         putfield corejava/Enclosing$1LocalClass.val$localList:java.util.List
         aload0 // reference to self
         invokespecial java/lang/Object <init>(()V);
         return
     }
     L2 {
     }
 }

 public bar() { //()V
     <localVar:index=0 , name=this , desc=Lcorejava/Enclosing$1LocalClass;, sig=null, start=L1, end=L2>

     L1 {
         getstatic java/lang/System.out:java.io.PrintStream
         aload0 // reference to self
         getfield corejava/Enclosing$1LocalClass.val$localList:java.util.List
         invokevirtual java/io/PrintStream println((Ljava/lang/Object;)V);
     }
     L3 {
         getstatic java/lang/System.out:java.io.PrintStream
         aload0 // reference to self
         getfield corejava/Enclosing$1LocalClass.this$0:corejava.Enclosing
         invokestatic corejava/Enclosing access$100((Lcorejava/Enclosing;)Ljava/util/List;);
         invokevirtual java/io/PrintStream println((Ljava/lang/Object;)V);
     }
     L4 {
         return
     }
     L2 {
     }
 }
 }
```



很明显，编译器也帮我们合成了构造器，并且除了传入外部实例对象，还有额外的参数是改本地类中引用到的本地变量的引用。
那我们现在来解释为什么对于本地类中引用的变量需要加final：
language designers wanted value of copied local variable to be "consistent" every time such a copy is created
