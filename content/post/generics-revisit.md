---
title: Generics revisit
toc: true
tags:
  - TODO
id: 688
categories:
  - Generics
date: "2015-03-24T14:15:50+00:00"
---

Generics is an important feature since JDK 1.5

### subtyping and wildcards

<table style="height: 152px;" width="393">
<tbody>
<tr>
<td>Integer</td>
<td>is a subtype of</td>
<td>Number</td>
</tr>
<tr>
<td>Double</td>
<td>is a subtype of</td>
<td>Number</td>
</tr>
<tr>
<td>ArrayList<E></td>
<td>is a subtype of</td>
<td>List<E></td>
</tr>
<tr>
<td>List<E></td>
<td>is a subtype of</td>
<td>Collection<E></td>
</tr>
<tr>
<td>Collection<E></td>
<td>is a subtype of</td>
<td>Iterable<E></td>
</tr>
<tr>
<td>Long[]</td>
<td>is a subtype of</td>
<td>Number[]</td>
</tr>
<tr>
<td>List<Integer></td>
<td>is **NOT** a subtype of</td>
<td>List<Number></td>
</tr>
</tbody>
</table>
In Java, array subtyping is **_covariant_**, meaning that type S[] is considered to be a subtype of T[] whenever S is a subtype of T.

In contrast, the subtyping relation for generics is **_invariant_**, meaning that type List<S> is _not _considered to be a subtype of List<T>, except in the trivial case where S and T are identical.

So now it's what the wildcards for.

*   Wildcards reintroduce **covariant** subtyping for generics, in that type List<S> _is _considered to be a subtype of List<? extends T> when S is a subtype of T.
*   Wildcards also introduce _**contravariant** _subtyping for generics, in that type List<S> is considered to be a _subtype _of List<? super T> when S is a _supertype _of T.


```java
List<? extends Number> list = new ArrayList<Integer>;
```

The question mark is called a _wildcard_, since it stands for some type that is a subtype of E.

&nbsp;

You can always consider the generic class with wildcard as a family of generic classes.

For example,

Collection<? extends Fruit> is a family of generic classes: Collection<Fruit>, Collection<Apple>, Collection<Orange>...

Collection<? super Apple> is a family of generic classes: Collection<Apple>, Collection<Fruit>, Collection<Plant>...

#### Get and Put principal

Some tutorals said:
> _The Get and Put Principle_: use an extends wildcard when you only _get _values out of a structure, use a super wildcard when you only _put _values into a structure, and don’t use a wildcard when you _both _get and put.
I think this has a little flaw. Actually, get and put is just a too vague expression. For instance, the remove() method of List interface is get or set?

So more general means is by using reasoning.


```java
public class GenericClass<T> {

    public T foo() {
        return null;
    }

    public void bar(T a) {}

    public static void main() {

        GenericClass<? extends Fruit> g1 = new GenericClass<Fruit>();
        Fruit fruit = g1.foo();
        Plant plant = g1.foo();
        Creature creature = g1.foo();

        GenericClass<? super Fruit> g2 = new GenericClass<Fruit>();
        g2.bar(new Fruit());
        g2.bar(new Apple());
        g2.bar(new RoyalGala());
    }
}

class Creature {}
class Plant extends Creature {}
class Fruit extends Plant {}
class Apple extends Fruit {}
class RoyalGala extends Apple {}
```

In the above code, we have a deep hierarchy. But all the rule is based on this:

*   <? extend Fruit> means it is some fruit sub type, maybe Fruit, Apple or RoyalGala.
*   <? super Fruit> means it is some fruit super type, maybe Fruit, Plant or Creature.


```java
<? extends Fruit> a = ...;
Apple aa = a; // wrong, if a is really a fruit? downcast failed
RoyalGala aaa = a; // wrong, if a is really a apple? downcast failed
Fruit aa = a; // ok, whatever a is fruit, apple or royal gala.
Plant aaa = a; // ok, whatever a is fruit, apple or royal gala.
Creature aaaa = a; // ok, whatever a is fruit, apple or royal gala.

<? super Fruit> b = ...;
b = new Plant(); // wrong, if b is really a fruit? downcast failed
b = new Creature(); // wrong, if b is really a plant? downcast failed
b = new Fruit(); // ok, whatever b is fruit, plant or royal gala.
b = new Apple(); // ok, whatever b is fruit, plant or royal gala.
b = new RoyalGala(); // ok, whatever b is fruit, apple or royal gala.
```

From the above pseudo code, we know how the compiler do reasoning. As per uncertain things the compiler cannot ensure, the compilation will fail naturally.

So for g1.foo(), compiler infer to return <? extends Fruit> type, but this is just a parameterize type, which cannot exist standalone. But the compiler can ensure the assignment to Fruit/Plant/Creature variable is safe.

That's it!

#### wildcard capture

//todo

#### constraints on wildcards

*   instance creation


```java
List<?> list = new ArrayList<?>(); // compile-time error
Map<String, ? extends Number> map = new HashMap<String, ? extends Number>(); // compile-time error
```

&nbsp;

*   generic method calls


```java
List<?> list = Lists.<?>factory(); // compile-time error
```


###  bounds

&nbsp;

###  bridge method
