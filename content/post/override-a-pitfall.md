---
title: 'Override: a pitfall'
toc: true
id: 16
categories:
  - OOP
date: "2013-01-12T02:30:14+00:00"
---

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
