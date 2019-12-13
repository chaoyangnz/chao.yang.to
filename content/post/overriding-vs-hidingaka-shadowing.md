---
title: Overriding vs. hiding(aka. shadowing)
toc: true
id: 597
categories:
  - OOP
date: "2015-03-22T18:40:28+00:00"
---

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
