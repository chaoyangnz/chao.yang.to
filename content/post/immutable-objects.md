---
title: Immutable objects
toc: true
tags:
  - Immutable
id: 533
categories:
  - Basics
  - Best Pricatice
date: "2015-03-19T16:54:45+00:00"
---

An [immutable class](http://en.wikipedia.org/wiki/Immutable_object "immutable class") is one whose state can not be changed once created. There are certain guidelines to create an class immutable. In this post, we will revisit these guidelines.

### Benefits of making a class immutable

Lets first identify benefits of making a class immutable. Immutable classes are

1.  are simple to construct, test, and use
2.  are automatically thread-safe and have no synchronization issues
3.  do not need a copy constructor
4.  do not need an implementation of clone
5.  allow [hashCode](http://howtodoinjava.com/2012/10/09/working-with-hashcode-and-equals-methods-in-java/ "Working with hashCode and equals methods in java") to use lazy initialization, and to cache its return value
6.  do not need to be copied defensively when used as a field
7.  make good [Map keys and Set elements](http://howtodoinjava.com/2012/10/09/how-hashmap-works-in-java/ "How hashmap works in java") (these objects must not change state while in the collection)
8.  have their class invariant established once upon construction, and it never needs to be checked again
9.  always have “**failure atomicity**” (a term used by Joshua Bloch) : if an immutable object throws an exception, it’s never left in an undesirable or indeterminate state

### Guidelines to make a class immutable

Java documentation itself has some guidelines identified [in this link](http://docs.oracle.com/javase/tutorial/essential/concurrency/imstrat.html "immutable classes"). We will understand what they mean actually:

**1) Don’t provide “setter” methods — methods that modify fields or objects referred to by fields.**

This principle says that for all mutable properties in your class, do not provide setter methods. Setter methods are meant to change the state of object and this is what we want to prevent here.

**2) Make all fields final and private**

This is another way to increase immutability. Fields declared private will not be accessible outside the class and making them final will ensure the even accidentally you can not change them.

**3) Don’t allow subclasses to override methods**

The simplest way to do this is to declare the class as **_final_**. Final classes in java can not be overridden.

**4) Special attention when having mutable instance variables**

Always remember that your instance variables will be either **mutable or immutable**. Identify them and **return new objects with copied content for all mutable objects**. Immutable variables can be returned safely without extra effort.

A more sophisticated approach is to make the constructor **_private_** and construct instances in **factory methods**.

A central idea of these rules is that you must ensure all the** accessible objects in the object graph** are immutable.

&nbsp;

Built-in library has many immutable classes:

*   String
*   Primitive wraper classes: Byte, Character, Short, Integer, Long, Float, Double

### Collections.unmodifiableList(..) like ISN'T immutable

```java
public class ImmutableTest {
    @Test
    public void testUnmodifiedCollectionImmutable(){                                                                                                                                                                                                                                    
        List<String> list=new ArrayList<String>();                                                                               
        list.add("a");                                                                                                           
        list.add("b");                                                                                                           
        list.add("c");

        System.out.println(list);

        List<String> unmodifiableList=Collections.unmodifiableList(list); 

        System.out.println(unmodifiableList);

        List<String> unmodifiableList1=Collections.unmodifiableList(Arrays.asList("a","b","c")); 
        System.out.println(unmodifiableList1);

        String temp=unmodifiableList.get(1);
        System.out.println("unmodifiableList [0]："+temp);

        list.add("baby");
        System.out.println("list add a item after list:"+list);
        System.out.println("list add a item after unmodifiableList:"+unmodifiableList);

        unmodifiableList1.add("bb");
        System.out.println("unmodifiableList add a item after list:"+unmodifiableList1);

        unmodifiableList.add("cc");
        System.out.println("unmodifiableList add a item after list:"+unmodifiableList);        
    }
}
```

Output:
[a, b, c]
[a, b, c]
[a, b, c]
unmodifiableList [0]：b
list add a item after list:[a, b, c, baby]
list add a item after unmodifiableList1:[a, b, c, baby]

From the above example, you can see unmodifiableList is not immutable. It will change if the underlying List is changed.

So the correct usage of unmodified List is like:


```java
List list = Collections.unmodifiableList(Arrays.asList('1','2','3');
```

or


```java
List list = new ArrayList();
list.add("1");
list.add(2);
list.add("333");
list = Collections.unmodifiableList(list);
```

Never expose the underlying list reference to anything, so the best way is passing an argument and initialization in place.


