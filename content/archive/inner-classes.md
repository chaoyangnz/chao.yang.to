---
title: Inner Classes
toc: true
id: 64
categories:
  - Basics
date: "2015-01-14T10:53:54+00:00"
---

There are 4 kinds of inner classes:

*   Member classes (non-static inner classes)
*   Local classes
*   Anonymous classes
*   Nested top-level classes and interfaces (static inner classes)
The former three kinds are called _**inner classes**_.

The last kind is called _**nested class**_.

&nbsp;

Member interface is implicitly static, so it's never considered as inner class.

#### Member classes

<span style="color: #ff0000;">**What is a member class?**</span>

A _member class_ is a class that is defined inside the definition of another class, _(without the use of the static modifier as is the case with a nested top-level class).
_

An object of the member class must be internally linked to an object of the enclosing class, _(which is not the case with a nested top-level class)._

Thus, a member class is truly an inner class.  _(An object of the member class cannot exist in the absence of an object of the enclosing class.)_

<span style="color: #ff0000;">**What about a member interface?**</span>

Interfaces defined within classes are implicitly static.  This means that they are always _top-level._  There is no such thing as a member interface, a local interface, or an anonymous interface.

不存在member interface！！

<span style="color: #ff0000;">**Why use member classes?**</span>

Probably the most important benefit of member classes has to do with accessing the other members of enclosing classes.  The methods of a member class have direct access to all the members of the enclosing classes, including private members.  Thus the use of member classes can eliminate the requirement to connect objects together via constructor parameters.

This is particularly useful in those cases where there is no reason for an object of a member class to exist in the absence of an object of the enclosing class, and where the methods of the object of the member class need access to members of the object of the enclosing class.

* * *

> _
> 
> "A class defined as a member (non-static) of another.  Each instance has an enclosing instance, and can use its members.  New syntax for **this**, **new**, and **super**.  Cannot have **static **members.  Cannot have same name as containing class."_
The main features of member classes are:

*   Every instance of a member class is internally associated with an instance of the class that defines or contains the member class.
*   The methods of a member class can implicitly refer to the fields defined within the member class, as well as those defined by any enclosing class, including **private **fields of the enclosing class.
1) Because the object of that inner class has a reference to the object of the enclosing object, so it can access the members of the enclosing object.


```java
public class Sequence {
    private Object[] items;
    private int next = 0;
    public Sequence(int size) { items = new Object[size]; }
    public void add(Object x) {
        if(next &lt; items.length)
            items[next++] = x;
    }
    private class SequenceSelector implements Selector {
        private int i = 0;
        public boolean end() { return i == items.length; }
        public Object current() { return items[i]; }
        public void next() { if(i &lt; items.length) i++; }
    }
    public Selector selector() {
        return new SequenceSelector();
    }
    public static void main(String[] args) {
        Sequence sequence = new Sequence(10);
        for(int i = 0; i &lt; 10; i++)
            sequence.add(Integer.toString(i));
        Selector selector = sequence.selector();
        while(!selector.end()) {
            System.out.print(selector.current() + " ");
            selector.next();
        }
    }
}
```

2) To construct a object of the inner class, you should use the <span style="text-decoration: underline;">**.new**</span> syntax


```java
class AAA {
    class BBB {}
}

class CCC {
    public static void main(String[] args) {
        AAA aaa = new AAA();
        AAA.BBB bbb = aaa.new BBB();
    }
}
```

3) To get the reference of the out-class object, you should use the **<span style="text-decoration: underline;">.this</span>** syntax


```java
public class DotThis {
    void f() { System.out.println("DotThis.f()"); }
    public class Inner {
        public DotThis outer() {
            return DotThis.this;
            // A plain "this" would be Inner’s "this"
        }
    }
    public Inner inner() { return new Inner(); }
    public static void main(String[] args) {
        DotThis dt = new DotThis();
        DotThis.Inner dti = dt.inner();
        dti.outer().f();
    }
}
```

4) Member classes can be private or protected (also public, package), but non-inner (top level) classes can be only public or package. So if you expose some inner class types to client programmer, they may have no access to these type.


```java
class Dog {
    private class DogTail {}

    public DogTail showYourTail() {
        return new DogTail();
    }
}

class ClientProgrammer {
    public void main(String[] args) {
        Dog dog = new Dog();
        // compiler error: Dog.DogTail has private access in "Dog"
        // Dog.DogTail dogTail = dog.showYourTail();
    }
}
```

Although the definition of Dog class can be compiled normally, the user of this class cannot be compiled when they want to get the return type of showYourTail() method.

In this case, a good fixture is to upcast the inner class to a public base class/interface like the following code:


```java
class Tail {}

class Dog {
    private class DogTail extends Tail {}

    public Tail showYourTail() {
        return new DogTail();
    }
}

class ClientProgrammer {
    public void main(String[] args) {
        Dog dog = new Dog();
        Tail dogTail = dog.showYourTail();
    }
}
```

5) Member classes cannot have **static** members


```java
&lt;pre&gt;class AAAAA {
    class BBBBB {
        //static int a =1; // Cannot compile
        //static void foo() {} // Cannot compile
    }
}
```

5) Member classes cannot have the same name as its enclosing class


```java
class AAAAA {
 class AAAAA { // Cannot compile: Duplicate class

 }
}
```

<span style="color: #ff0000;">**What does compiler do?**</span>

Every class definition in a Java program, including nested top-level classes, member classes, local classes, and anonymous classes, produces a class file when the program is compiled.  According to Flanagan,
> _"The Java Virtual Machine knows nothing about nested top-level classes and interfaces or the various types of inner classes.  Therefore, the Java compiler must convert these new types into standard non-nested class files that the Java interpreter can understand.  This is done through source code transformations that insert <span style="color: #0000ff;">$</span> characters into nested class names.   These source code transformations may also insert hidden fields, methods, and constructor arguments into the affected classes."_
<span style="color: #000000;">**A reference to the containing object**</span>

For example, the compiler automatically inserts a <span style="text-decoration: underline;">private instance variable</span> in the member class to hold a reference to the containing object.  It also inserts a hidden argument in all constructors for the member class, and passes the containing object's reference to the constructor for the member class.  The modified constructor saves that reference in the private instance variable of the object of the member class.  Thus each object instantiated from the member class contains a private reference to the containing object.

<span style="color: #000000;">**Accessing private members**</span>

In those cases where it is necessary for an object of the member class to access private members of the containing object, the compiler automatically creates and uses accessor methods that make such access possible.

http://www.developer.com/java/other/article.php/3085271/The-Essence-of-OOP-using-Java-Member-Classes.htm#Complete_program_listing

#### Local classes

<span style="color: #ff0000;">**What is a local class?**</span>

A _local class_ is a class that is defined within a block of Java code.  While local classes are probably most frequently defined within methods and constructors, they can also be defined inside static initializer blocks and instance initializers.

As is the case for an object of a member class _(discussed in the previous lesson),_ an object of a local class must be internally linked to an object of the enclosing class.  I will often refer to that object as the containing object, and make comments about the containment hierarchy.

Thus, a local class is truly an inner class, because an object of the local class cannot exist in the absence of an object of the enclosing class.

<span style="color: #ff0000;">**What about a local interface?**</span>

Interfaces defined within classes are implicitly static.  This means that they are always _top-level._  <span style="text-decoration: underline; color: #800000;">There is no such thing as a member interface, a local interface, or an anonymous interface.</span>

<span style="color: #ff0000;">**Why use local classes?**</span>

Objects instantiated from local classes share many of the characteristics of objects instantiated from member classes.  However, in some cases, a local class can be defined closer to its point of use than would be possible with a member class, leading to improved code readability.

Probably the most important benefit of local classes has to do with accessing the members of enclosing classes.  Just like with member classes, methods of a local class have direct access to all the members of the enclosing classes, including private members.  Thus the use of local classes can sometimes eliminate the requirement to connect objects together via constructor parameters.

<span style="color: #000000;">**Can be particularly useful when ...**</span>

A local class can be particularly useful in those cases where

*   There is no reason for an object of the local class to exist in the absence of an object of the enclosing class
*   There is no reason for an object of the local class to exist outside a method of the enclosing class
*   Methods of the object of the local class need access to members of the object of the enclosing class
*   Methods of the object of the local class need access to final local variables and method parameters belonging to the method in which the local class is defined
**<span style="color: #ff0000;">Local classes versus member classes</span>**

A local class has approximately the same relationship to a member class that a local variable in a method has to an instance variable of the class containing the method.

<span style="color: #ff0000;">**The scope of a local class**</span>

As is the case with local variables, the name of a local class is visible and usable only within the block of code in which it is defined _(and blocks nested within that block)._  Further, the name of the local class is visible and usable only to code following the class definition within that block.

The methods of a local class can use any _**final**_ local variables or method parameters that are visible from the scope in which the local class is defined.

<span style="color: #ff0000;">**Similar to member classes**</span>

As mentioned earlier, local classes have many characteristics in common with member classes.  This includes access to private fields and methods in the containing class.  The thing that separates local classes from member classes is the fact that local classes have access to local variables in the scope in which the local class is defined.

<span style="color: #ff0000;">**A big restriction**</span>

There is a big restriction, however, on the ability of methods of the local class to access local variables and method parameters.  The methods in a local class can access local variables and method parameters only if they are declared **final**.

* * *

> " the methods in a local class don't really have access to local variables and method parameters.  Rather, when an object of the local class is instantiated, copies of the **final** local variables and method parameters referred to by the object's methods are stored as <span style="text-decoration: underline;">instance variables</span> in the object.  The methods in the object of the local class really access those hidden instance variables. "
Thus, the local variables and method parameters accessed by the methods of the local class must be declared **final** to prevent their values from changing after the object is instantiated.

<span style="color: #000000;">**Restrictions on local classes**</span>

As with member classes, local classes cannot contain **static** members.

As with local variables, local classes cannot be declared **public**,** protected**,** private**,** **or **static**.

A local class cannot have the same name as any of its enclosing classes.

<span style="color: #ff0000;">**What does compiler do?**</span>
> _"The Java Virtual Machine knows nothing about nested top-level classes and interfaces or the various types of inner classes.  Therefore, the Java compiler must convert these new types into standard non-nested class files that the Java interpreter can understand.  This is done through source code transformations that insert $ characters into nested class names.  These source code transformations may also insert hidden fields, methods, and constructor arguments into the affected classes."_
**<span style="color: #000000;">A reference to the containing object</span>**

For example, the compiler automatically inserts a private instance variable in the local class to hold a reference to the containing object.  It also inserts a hidden argument in all constructors for the local class, and passes the containing object's reference to the constructor for the local class.  The modified constructor saves that reference in the private instance variable of the object of the local class.  Thus each object instantiated from the local class contains a private reference to the containing object.

<span style="color: #000000;">**Accessing private members**</span>

In those cases where it is necessary for an object of the local class to access private members of the containing object, the compiler automatically creates and uses accessor methods that make such access possible.

1) A class defined within a method


```java
class Tail {}

class Dog {

    public Tail showYourTail() {
        class DogTail extends Tail {}
        return new DogTail();
    }
}
```

Because DogTail is defined in method showYourTail(), it cannot be accessed outside of showYourTail(). Any specifier is not allowed, like public/private/protected, static etc.

2) A class defined within a scope inside a method


```java
class Tail {}

class Dog {

    public Tail showYourTail(boolean flag) {
        Tail tail = null;
        if(flag) {
            class DogTail extends Tail {}
            tail = new DogTail();
        }
        // Can’t use it here! Out of scope:
        // DogTail dt = new DogTail();
        return tail;
    }
}

```

http://www.developer.com/java/other/article.php/3107181/The-Essence-of-OOP-using-Java-Local-Classes.htm

&nbsp;

#### Anonymous classes

<span style="color: #ff0000;">**What is an anonymous class?**</span>

I'm going to begin my discussion with a quotation from one of my favorite authors, David Flanagan, author of <span style="text-decoration: underline;">Java in a Nutshell</span>.
> _"An anonymous class is essentially a local class without a name."_
If you have read the previous lesson, you should know quite a lot about local classes at this point in time.  Continuing with Flanagan's words,
> _"Instead of defining a local class and then instantiating it, you can often use an anonymous class to combine these two steps...  an anonymous class is defined by a Java expression, not a Java statement.  This means that an anonymous class definition can be included within a larger Java expression..."_
As you will see from the sample program in this lesson, anonymous class definitions are often included as arguments to method calls.

As is the case for an object of a member class or a local class _(discussed in previous lessons),_ an object of an anonymous class must be internally linked to an object of the enclosing class.

Thus, an anonymous class is truly an inner class, because an object of the anonymous class cannot exist in the absence of an object of the enclosing class.

<span style="color: #ff0000;">**What about an anonymous interface?**</span>

Interfaces defined within classes are implicitly static.  This means that they are always _top-level._  There is no such thing as a member interface, a local interface, or an anonymous interface.

<span style="color: #ff0000;">**Why use anonymous classes?**</span>

As with local classes, objects instantiated from anonymous classes share many of the characteristics of objects instantiated from member classes.  However, in some cases, an anonymous class can be defined closer to its point of use than would be possible with a member class or a local class.  Once you become accustomed to the somewhat cryptic syntax used with anonymous classes, this can often lead to improved code readability.

Probably the most important benefit of anonymous classes has to do with accessing the members of enclosing classes.  Just like with member classes and local classes, methods of an anonymous class have direct access to all the members of the enclosing classes, including private members.  Thus the use of anonymous classes can often eliminate the requirement to connect objects together via constructor parameters.

In addition, although not demonstrated in this lesson, as with local classes, objects of anonymous classes have access to **final** local variables that are declared within the scope of the anonymous class.

<span style="color: #000000;">**Can be particularly useful when ...**</span>

An anonymous class can be particularly useful in those cases where

*   There is no reason for an object of the anonymous class to exist in the absence of an object of the enclosing class.
*   There is no reason for an object of the anonymous class to exist outside a method of the enclosing class.
*   Methods of the object of the anonymous class need access to members of the object of the enclosing class.
*   Methods of the object of the anonymous class need access to **final **local variables and method parameters belonging to the method in which the anonymous class is defined.
*   Only one instance of the anonymous class is needed.
*   There is no need for the class to have a name that is accessible elsewhere in the program.
**<span style="color: #ff0000;">Anonymous classes versus local classes</span>**
> _"...an anonymous class behaves just like a local class, and is distinguished from a local class merely in the syntax used to define and instantiate it."_
Unlike a local class, however, an anonymous class cannot define a constructor.  An anonymous class can define an instance initializer, which can provide some of the benefits of a constructor.

<span style="color: #ff0000;">**Restrictions on the use of anonymous classes**</span>

Because an anonymous class has no name, and the definition and instantiation of the class appear in a single expression, only one instance of each anonymous class can be created.  If you need more than one instance of the class, you should probably use a local class, a member class, or a top-level class instead.

As mentioned above, it is not possible to define constructors for anonymous classes.  If you need to use a constructor when you instantiate the class, you should probably use a local class, a member class, or a top-level class instead.

As with member classes and local classes, anonymous classes cannot contain **static** members.

As with local variables and local classes, anonymous classes cannot be declared **public**,** protected**,**private**,** **or **static**.  In fact, no modifiers can be specified in the definition of an anonymous class.

<span style="color: #ff0000;">**What does compiler do?**</span>

The methods in an anonymous class don't really have access to local variables and method parameters.  Rather, when an object of the anonymous class is instantiated, copies of the **final** local variables and method parameters referred to by the object's methods are stored as instance variables in the object.  The methods in the object of the anonymous class really access those hidden instance variables.

Thus, the local variables and method parameters accessed by the methods of the local class must be declared **final** to prevent their values from changing after the object is instantiated.

Generally speaking, this involves the automatic generation of code to cause things to behave as they do.  The good news is that you don't have to write that extra code, and you don't have to maintain it.  The extra code is written for you, and if you modify your class structure, the extra code is automatically modified accordingly.

1) An anonymous class extending a class that has a default constructor


```java
class Tail {}

class Dog {

    public Tail showYourTail() {
        return new Tail() {
            // blabla
        };
    }
}
```

2) An anonymous class extending a class that has a non-default constructor


```java
class Tail {
    Tail(int x) {}
}

class Dog {

    public Tail showYourTail(int x) {
        return new Tail(x) {
            // blabla
        };
    }
}
```

3) An anonymous class that performs field initialization


```java
class Dog {

    public Tail showYourTail() {
        final String dest = "1";
        return new Tail() {
            private String label = dest;
            // blabla
        };
    }
}
```

In this example, the dest must be "final", but why?

// TODO

4) An anonymous class that performs construction using instance initialization (anonymous inner classes cannot have constructors)

If simply assigning a field is not enough, how can you perform some constructor activity?
You can’t have a named constructor in an anonymous class (since there’s no name!), but with instance initialization, you can, in effect, create a constructor for an anonymous inner class, like this:


```java
class Dog {

    public Tail showYourTail() {
        final String dest = "1";
        return new Tail() {
            private String label = dest;
            private boolean flag;
            {
              if("1".equals(label)) flag = false;
              System.out.println("instance initialisation");
            }
            // blabla
        };
    }
}
```


#### Nested top-level classes and interfaces (static inner classes)

<span style="color: #ff0000;">**What is a nested top-level class or interface?**</span>
> _"A nested top-level class or interface is defined as a static member of an enclosing top-level class or interface.  The definition of a nested top-level class uses the static modifier ...  Nested interfaces are implicitly static ...  and so are always top-level.  A nested top-level class or interface behaves just like a 'normal' class or interface ...  The difference is that the name of a nested top-level class or interface includes the name of the class in which it is defined."_
<span style="color: #ff0000;">**Why use nested top-level classes or interfaces?**</span>
> _"Nested top-level classes and interfaces are typically used as a convenient way to group related classes."_
<span style="color: #000000;">**Can be particularly useful when ...**</span>

A particularly useful implementation of top-level classes occurs when the nested classes extend the enclosing class and override methods that are declared or defined in the enclosing class.  This makes it very convenient to construct a hierarchical API, which exhibits very useful polymorphic behavior, and which cannot easily be expanded.

<span style="color: #ff0000;">**What does compiler do?**</span>

_"The Java Virtual Machine knows nothing about nested top-level classes and interfaces or the various types of inner classes.  Therefore, the Java compiler must convert these new types into standard non-nested class files that the Java interpreter can understand.  This is done through source code transformations that insert $ characters into nested class names.  These source code transformations may also insert hidden fields, methods, and constructor arguments into the affected classes."_

http://www.developer.com/java/other/article.php/3358491/The-Essence-of-OOP-using-Java-Nested-Top-Level-Classes.htm

&nbsp;

&nbsp;
