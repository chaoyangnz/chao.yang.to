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
<td>ArrayList&lt;E&gt;</td>
<td>is a subtype of</td>
<td>List&lt;E&gt;</td>
</tr>
<tr>
<td>List&lt;E&gt;</td>
<td>is a subtype of</td>
<td>Collection&lt;E&gt;</td>
</tr>
<tr>
<td>Collection&lt;E&gt;</td>
<td>is a subtype of</td>
<td>Iterable&lt;E&gt;</td>
</tr>
<tr>
<td>Long[]</td>
<td>is a subtype of</td>
<td>Number[]</td>
</tr>
<tr>
<td>List&lt;Integer&gt;</td>
<td>is **NOT** a subtype of</td>
<td>List&lt;Number&gt;</td>
</tr>
</tbody>
</table>
In Java, array subtyping is **_covariant_**, meaning that type S[] is considered to be a subtype of T[] whenever S is a subtype of T.

In contrast, the subtyping relation for generics is **_invariant_**, meaning that type List&lt;S&gt; is _not _considered to be a subtype of List&lt;T&gt;, except in the trivial case where S and T are identical.

So now it's what the wildcards for.

*   Wildcards reintroduce **covariant** subtyping for generics, in that type List&lt;S&gt; _is _considered to be a subtype of List&lt;? extends T&gt; when S is a subtype of T.
*   Wildcards also introduce _**contravariant** _subtyping for generics, in that type List&lt;S&gt; is considered to be a _subtype _of List&lt;? super T&gt; when S is a _supertype _of T.


```java
List&lt;? extends Number&gt; list = new ArrayList&lt;Integer&gt;;
```

The question mark is called a _wildcard_, since it stands for some type that is a subtype of E.

&nbsp;

You can always consider the generic class with wildcard as a family of generic classes.

For example,

Collection&lt;? extends Fruit&gt; is a family of generic classes: Collection&lt;Fruit&gt;, Collection&lt;Apple&gt;, Collection&lt;Orange&gt;...

Collection&lt;? super Apple&gt; is a family of generic classes: Collection&lt;Apple&gt;, Collection&lt;Fruit&gt;, Collection&lt;Plant&gt;...

#### Get and Put principal

Some tutorals said:
> _The Get and Put Principle_: use an extends wildcard when you only _get _values out of a structure, use a super wildcard when you only _put _values into a structure, and don’t use a wildcard when you _both _get and put.
I think this has a little flaw. Actually, get and put is just a too vague expression. For instance, the remove() method of List interface is get or set?

So more general means is by using reasoning.


```java
public class GenericClass&lt;T&gt; {

    public T foo() {
        return null;
    }

    public void bar(T a) {}

    public static void main() {

        GenericClass&lt;? extends Fruit&gt; g1 = new GenericClass&lt;Fruit&gt;();
        Fruit fruit = g1.foo();
        Plant plant = g1.foo();
        Creature creature = g1.foo();

        GenericClass&lt;? super Fruit&gt; g2 = new GenericClass&lt;Fruit&gt;();
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

*   &lt;? extend Fruit&gt; means it is some fruit sub type, maybe Fruit, Apple or RoyalGala.
*   &lt;? super Fruit&gt; means it is some fruit super type, maybe Fruit, Plant or Creature.


```java
&lt;? extends Fruit&gt; a = ...;
Apple aa = a; // wrong, if a is really a fruit? downcast failed
RoyalGala aaa = a; // wrong, if a is really a apple? downcast failed
Fruit aa = a; // ok, whatever a is fruit, apple or royal gala.
Plant aaa = a; // ok, whatever a is fruit, apple or royal gala.
Creature aaaa = a; // ok, whatever a is fruit, apple or royal gala.

&lt;? super Fruit&gt; b = ...;
b = new Plant(); // wrong, if b is really a fruit? downcast failed
b = new Creature(); // wrong, if b is really a plant? downcast failed
b = new Fruit(); // ok, whatever b is fruit, plant or royal gala.
b = new Apple(); // ok, whatever b is fruit, plant or royal gala.
b = new RoyalGala(); // ok, whatever b is fruit, apple or royal gala.
```

From the above pseudo code, we know how the compiler do reasoning. As per uncertain things the compiler cannot ensure, the compilation will fail naturally.

So for g1.foo(), compiler infer to return &lt;? extends Fruit&gt; type, but this is just a parameterize type, which cannot exist standalone. But the compiler can ensure the assignment to Fruit/Plant/Creature variable is safe.

That's it!

#### wildcard capture

//todo

#### constraints on wildcards

*   instance creation


```java
List&lt;?&gt; list = new ArrayList&lt;?&gt;(); // compile-time error
Map&lt;String, ? extends Number&gt; map = new HashMap&lt;String, ? extends Number&gt;(); // compile-time error
```

&nbsp;

*   generic method calls


```java
List&lt;?&gt; list = Lists.&lt;?&gt;factory(); // compile-time error
```


###  bounds

&nbsp;

###  bridge method
