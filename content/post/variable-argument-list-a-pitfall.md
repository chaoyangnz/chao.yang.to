---
title: 'variable argument list: a pitfall'
toc: true
id: 32
categories:
  - Basics
date: "2013-01-12T02:56:36+00:00"
---

We usually define a method with a variable argument list like this:



```java


void bar(String...args) {
//blablab
}

```


But there is a pitfall in this place.
Can you ensure args is always an array, even its length is 0 when it's empty?

Yes, most of time.



```java


obj.bar(); // here, args is a zero-length array

```


BUT, things are not always like that.

You can see this:



```java


obj.bar(null);

```


Guess what? Here the args is also null. That's it.

This is a pretty weird behavior!
Naturally, we expect an array with one element the value of which should be null. But in reality, the reference to the array is null.
Instead, we use the following solution for our intention:



```java


obj.bar(new String[]{ null });

```


And what if I pass the null to more than 1 variable argument list?



```java


obj.bar(null, &quot;1&quot;);

```


Yes, it is obvious and expected that args is an array with two elements the values of which are null and "1" respectively.
Alternatively, the following style is also the same:



```java


obj.bar(new String[]{null, &quot;1&quot;});

```


To summarize, variable argument list is really of no secret.

It's a sort of syntax sugar.
1) when only one actual parameter passes,
1.a if it is null, the argument array is null
1.b if it is of the type of element, then the argument array is a one-element array and the only element is the passed parameter
1.c if it is an array with the type of declared argument, then the argument array is just the passed array parameter.

2) when more than one actual parameters pass, then the argument array is a non-empty array and its elements are the passed parameters.
