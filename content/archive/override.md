---
title: Overriding in Java
toc: true
id: 127
categories:
  - OOP
date: "2015-01-16T06:35:35+00:00"
---

#### 1\. Override methods must have the identical method signature as the methods overridden

Return type doesn't belong to the method signature, but Java supports covariant return type for overriding.

Identical signature means:
1) the number of formal arguments must be equal.
2) the type of relevant argument must be the same. The subtype of the methond argument cannot be considered as identical.

#### 2\. Override methods can less restricted in access control



```java

class AA {
    protected void foo() {}
}

class BB extends AA {

    @Override
    public void foo()  {
    }
}
```


We make the foo() method accessible from weaker privilege "protected" to less restricted specifier "public".

#### 3\. Override methods and throw clause

More precisely, suppose that B is a class or interface, and A is a superclass or superinterface of B, and a method declaration n in B overrides or hides a method declaration m in A. Then:

If n has a throws clause that mentions any checked exception types, then m must have a throws clause, or a compile-time error occurs.

For every checked exception type listed in the throws clause of n, that same exception class or one of its supertypes must occur in the erasure (§4.6) of the throws clause of m; otherwise, a compile-time error occurs.

![IMG_2690](/media/IMG_2690.png)



```java

class AA {
    protected void foo() throws IOException {}
}

class BB extends AA {

    //This is naturally OK
//    @Override
//    public void foo() throws IOException {
//    }

    //This is also OK, since EOFException is a subtype of IOException
//    @Override
//    public void foo() throws EOFException {
//    }

    //This is also OK, since we don't increase new checked Exception
//    @Override
//    public void foo() {
//    }

    //This is OK, since RuntimeException is unchecked exception, which is not requested
//    @Override
//    public void foo() throws RuntimeException {
//    }

    //This is OK, since IllegalArgumentException is a subtype of RuntimeException
//    @Override
//    public void foo() throws IllegalArgumentException {
//    }

    //This is OK, since Error is unchecked exception, which is not requested
//    @Override
//    public void foo() throws Error {
//    }

    //This is OK, since AssertionError is a subtype of Error
//    @Override
//    public void foo() throws AssertionError {
//    }

    // Compile error: class AA don't have AccessControlException declaration
//    public void foo() throws AccessControlException {
//    }

    // Compile error: Throwable is also a checked exception class AA don't have Throwable declaration
//    public void foo() throws Throwable {
//    }
}
```

# a pitfall of overriding

Let's see the flowing program and guess the run result:



```java

class Animal {

    protected void drink() {
        System.out.println(&quot;animal drinks before eating&quot;);
    }

    public void eat() {
        this.drink();
    }
}

public class Dog extends Animal {
    protected void drink() {
        System.out.println(&quot;dog drinks before eating&quot;);
    }

    public void eat() {
        super.eat();
    }

    public static void main(String[] args) {
        Animal animal = new Dog();
        animal.eat();
    }
}
```


Yes, the result it: dog drinks before eating. And why?

Overridden just occurs in run time. So it is also called dynamic binding.

In run time, you must <span style="text-decoration: underline;">always trace the real object reference</span>.

In the aforementioned example, there is only one object both _super_ and _this _refer.



```java


Animal animal = new Dog();
animal.eat();

```


// because the real type of animal is Dog, so the eat() method uses the overridden version of eat().



```java


super.eat();

```


It specifically invokes the methods of its parent.



```java


this.drink();

```


OK, now _this _refers dog object, so polymorphism occurs once again. So it just use the overridden version of drink().

Anyway, that's it.

# Overriding vs Shadowing / Hiding

Simply speaking, overriding is for member methods, while hiding is for fields and static methods.

### Override

Override is the feature of dynamic binding or run-time polymorphism.

It's necessary for overriding that the method of subclasses has the same method signature as that of its super class.

"Same" means the number and types of the method argument list must be the same. But the return type is not necessary the same. You can use the more concrete type as the return type, like its sub types.

As per the access modifier, you can use the less restrictive access (stronger) modifier --- (increase visibility).
> **stronger-&gt; weaker**:  public &gt; protected &gt; package-default &gt; private
For more access modifier details, refer to [java modifiers review](http://richdyang.com/java-modifiers-review)

For instance:

*   if the to-be-overridden method is **package**, you can make it package/**protected/public** accessible in the sub classes.
*   if the to-be-overridden method is **protected**, you can make it **protected/public** accessible in the sub classes.
*   if the to-be-overridden method is **public**, you can only make it **public** accessible in the sub classes.
<table style="height: 160px;" border="1" width="564">
<tbody>
<tr>
<td width="295">Access modifier in super class method</td>
<td width="295">Allowed access modifier for overriding</td>
</tr>
<tr>
<td width="295">private</td>
<td width="295">private, package, protected, public</td>
</tr>
<tr>
<td width="295">package</td>
<td width="295">package, protected, public</td>
</tr>
<tr>
<td width="295">protected</td>
<td width="295">protected, public</td>
</tr>
<tr>
<td width="295">public</td>
<td width="295">public</td>
</tr>
</tbody>
</table>
As per the exception throwing, you can **reduce the checked exceptions throwings**, but no matter the runtime exceptions.

All the rules concerning the access modifier and exception throwing, it's easy to understand because you must maintain the contract given by the parent class.


```java
class Animal {

    void drink() {}

    protected Number eat(Food food) {
        return 1;
    }
}

class Horse extends Animal {

    @Override
    protected void drink() {}

    @Override
    protected Integer eat(Food food) {
        return 2;
    }

    protected Number eat(Grass food) { // override failed
        return 2;
    }
}

class Food {}

class Grass extends Food {}
```

For more complicated overriding example, refer to [override - a pitfall](http://richdyang.com/override-a-pitfall)

### Hiding

Hiding/shadowing is another type of static binding or compile-time polymophism.

#### variable shadowing

When both a parent class and its sub class have a field with the same name, this technique is called variable shadowing.


```java
public class Hiding {
    @Test
    public void testVariableShadowing() {
        Animal animal = new Cat();
        Cat cat = new Cat();

        System.out.println(animal.name);
        System.out.println(cat.name);
        System.out.println(((Cat)animal).name);
        System.out.println(((Animal)cat).name);

        System.out.println(animal.getName());
        System.out.println(cat.getName());
    }
}

abstract class Animal {
    String name = "animal";

    public String getName() {
        return this.name;
    }
}

class Cat extends Animal {
    String name = "Cat";

    public String getName() {
        return this.name;
    }
}
```

Output:
animal
Cat
Cat
animal
Cat
Cat

Static variable is similar as per hiding.


```java
public class OverridingHiding {

    @Test
    public void testStaticVariableHiding() {
        Animal animal = new Cat();
        Cat cat = new Cat();

        System.out.println(animal.name);
        System.out.println(cat.name);
    }

}

class Animal {
    static String name = "Animal";
}

class Cat extends Animal {
    static String name = "Cat";
}
```

Output:
Animal
Cat

#### static method hiding

When both a parent class and its sub class have a static method with the same signature, this technique is called static method hiding.


```java
public class Hiding {
    @Test
    public void testStaticMethodHiding() {
        Animal animal = new Cat();
        Cat cat = new Cat();

        System.out.println(animal.who());
        System.out.println(cat.who());
    }
}

abstract class Animal {

    static String who() {
        return "Animal";
    }

}

class Cat extends Animal {
    static String who() {
        return "Cat";
    }
}
```

Output:
Animal
Cat
