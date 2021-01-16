---
title: 'JavaScript prototype: __proto__ vs. prototype'
toc: true
id: 1220
categories:
  - Javascript
date: "2015-05-12T15:35:15+00:00"
---

Javascript is a prototype-based language. It resolve property using prototype chains.

### Prototype chains (aka prototype inheritance)

![prototype-chains](/media/prototype-chains.png)

### The __proto__ property

__proto__ is the internal property of an object, it points to the prototype object of the object and is used for property look-up. Every object has a __proto__ perperty and its relevant prototype object. But it maybe null, which means the object has no prototype object.

### function as constructor

In JavaScript you create an instance of a function like this:


```java
function Foo() {
  this.kind = ‘foo’
}

var foo = new Foo(); 
foo.kind //=&gt; ‘foo’
```

Behind this scenes it is like doing something like this:


```java
function Foo() {
  var this = {}; // this is not valid, just for illustration
  this.__proto__ = Foo.prototype;

  this.kind = ‘foo’

  return this;
}
```

But keep in mind that the implicit ‘**this**’ is only assigned to a new object when using ‘**new**’. If you forget ‘**new**’ keyword then ‘**this**’ will be the global object.

### the ‘function prototype’

Every function in JavaScript has a special property called ‘**prototype**’. To avoid confusion, we call it "function prototype".

Basically, this object is provided at run-time by Javascript engine. This object has a property called "constructor" which points to the function itself.

It is used to** build `__proto__`** when you create an object with `new` keyword.

### Javascript object model

Now we illustrate the complete relations for prototype:

![js-prototype](/media/js-prototype.png)
