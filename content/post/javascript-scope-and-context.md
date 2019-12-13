---
title: JavaScript scopes and context
toc: true
id: 1230
categories:
- Javascript
date: "2015-05-12T17:06:39+00:00"
---

### scope vs. context

Both two terms are related to function.

Every function invocation has both a scope and a context associated with it. Functions can be adopted for various contexts and scope can be encapsulated and preserved.

Fundamentally, scope is **function-based** while context is **object-based**.

* Scope pertains to the variable access of a function when it is invoked and is unique to each invocation.
* Context is always the value of the `this` keyword which is a reference to the object that “owns” the currently executing code.
<iframe width="560" height="315" src="https://www.youtube.com/embed/hDT3IbvH-9I" frameborder="0" allowfullscreen="allowfullscreen"></iframe>

### scope

A variable can be defined in either local or global scope, which establishes the variables’ accessibility from different scopes during runtime.

Javascript has no <del>**block scope**</del>.

All scopes in JavaScript are created with `function Scope` *only*, they aren't created by `for` or `while` loops or expression statements like `if` or `switch`.

#### global scope

This is the top level scope. Any variable declared outside of a function body will live throughout runtime and can be accessed and altered in any scope.

#### local scope

New functions = new local scope - that's the rule.



```javascript
// Scope A - global
var myFunction = function () {
  // Scope B - local
  var myOtherFunction = function () {
    // Scope C - local
  };
};
```


#### scope chain

Each function defined has its own nested scope, and any function defined within another function has a local scope which is linked to the outer function.



```javascript
function first(){
    second();
    function second(){
        third();
        function third(){
            fourth();
            function fourth(){
                // do something
            }
        }
    }   
}
first();
```


### `"this"` context

Context is most often determined by how a function is invoked. When a function is called as a method of an object, `this` is set to the object the method is called on.

invoke no-bound function

* bind `this` to `window` (by default) by invoking unbound function



```javascript
function Dog() {
  console.log(this);
  var that = this;
  var bar = function () {
    console.log(this);
    console.log(this == that);
  };
  bar();

  this.bar = bar;
  this.bar();
}

var dog = new Dog();
```


* bind `this` by `new`



```javascript
function Cat() {
    this.drink = function() {
       console.log(this);
    }
}

Cat(); // this -> window

var cat = new Cat();
cat.drink(); // this -> cat
```


* bind `this` by specifying function to object property



```javascript
var obj = {
    foo: function(){
        alert(this === obj);    
    }
};

obj.foo(); // true
```


* Changing scope with `.call()`, `.apply()` and `.bind()`



```javascript
function foo(name, age) {
  this.name = name;
  this.age = age;
}

var john = {
  dept: 'dev'
};

foo.apply(john, ['John', 32]);
//foo.call(obj, 'Richard', 32);
console.log(john); // -&gt; Object {dept: "dev", name: "John", age: 32}

var tom = {
  dept: 'test'
};

var bar = foo.bind(tom, 'Tom', 28); // just bind, not call
console.log(tom); // Object {dept: "test"}
// do something else
// ...
bar();
console.log(tom); // Object {dept: "test", name: "Tom", age: 28}
```


### Execution Context

This terminology is defined by ECMA specification. Don't confuse it with the "`this`" context.

JavaScript is a single threaded language, meaning only one task can be executed at a time. When the JavaScript interpreter initially executes code, it first enters into a global execution context by default. Each invocation of a function from this point on will result in the creation of a new execution context.

Each time a new execution context is created it is appended to the top of the **_execution stack_**.

An execution context can be divided into a creation and execution phase. In the creation phase, the interpreter will first create a *variable object* (also called an *activation object*) that is composed of all the variables, function declarations, and arguments defined inside the execution context. From there the *scope chain* is initialized next, and the value of `this` is determined last. Then in the execution phase, code is interpreted and executed.

### closure

Accessing variables outside of the immediate lexical scope creates a closure.

It says, a closure is formed when a nested function is defined inside of another function, allowing access to the outer functions variables.

A simple illustration:



```javascript
function aa() {
    var bb = 'hello ';
    var cc = function() {
        return bb + "world";
    }
}
```

`cc` as a function accesses its immediate outer scope. That creates a closure.

* * *

Returning the nested function allows you to maintain access to the local variables, arguments, and inner function declarations of its outer function. This encapsulation allows us to hide and preserve the execution context from outside scopes while exposing a public interface and thus is subject to further manipulation.

A more practical example:



```javascript
var sayHello = function (name) {
  var text = 'Hello, ' + name;
  return function () {
    console.log(text);
  };
};

sayHello("Richard")();
```

One of the most popular types of closures is what is widely known as the *module pattern*; it allows you to emulate public, private, and privileged members:



```javascript
var Module = (function(){
    var privateProperty = 'foo';

    function privateMethod(args){
        // do something
    }

    return {

        publicProperty: '',

        publicMethod: function(args){
            // do something
        },

        privilegedMethod: function(args){
            return privateMethod(args);
        }
    };
})();
```


References:

1. http://ryanmorr.com/understanding-scope-and-context-in-javascript/
2. http://toddmotto.com/everything-you-wanted-to-know-about-javascript-scope/

