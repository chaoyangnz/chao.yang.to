---
title: Assertion
toc: true
id: 106
categories:
  - Basics
date: "2015-01-16T01:28:54+00:00"
---

Java have an "assert" keyword since java 1.4

#### Usage

There are two kinds of syntax:

1) assert expression1

2) assert expression1 : expression2

expression1 must be evaluated as boolean ( true or false) while expression2 can be primitive, object, or array.

expression2 is passed into the constructor of AssertionError as an argument.

[![44b0b3e9](/media/44b0b3e9.png)](/media/44b0b3e9.png)

So if expression2 is evaluated as void, it's not allowed.


```java

void foo() {}

assert true : foo(); // cannot be compiled

```


#### How to enable or disable assertion

Assert statements will be evaluated only if assertion is enabled.

1) through command options (aka. VM options)
<table id="table3" border="1" width="60%">
<tbody>
<tr>
<td>Options</td>
<td>Example</td>
<td>Description</td>
</tr>
<tr>
<td>-ea</td>
<td>java -ea</td>
<td>open assertion in all user classes</td>
</tr>
<tr>
<td>-da</td>
<td>java -da</td>
<td>close assertion in all user classes</td>
</tr>
<tr>
<td>-ea:&lt;classname&gt;</td>
<td>java -ea:MyClass1</td>
<td>open assertion in class MyClass1</td>
</tr>
<tr>
<td>-da:&lt;classname&gt;</td>
<td>java -da: MyClass1</td>
<td>close assertion in class MyClass1</td>
</tr>
<tr>
<td>-ea:&lt;packagename&gt;</td>
<td>java -ea:pkg1</td>
<td>open assertion in package pkg1</td>
</tr>
<tr>
<td>-da:&lt;packagename&gt;</td>
<td>java -da:pkg1</td>
<td>close assertion in package pkg1</td>
</tr>
<tr>
<td>-ea:...</td>
<td>java -ea:...</td>
<td>open assertion in default(no name) package</td>
</tr>
<tr>
<td>-da:...</td>
<td>java -da:...</td>
<td>close assertion in default(no name) package</td>
</tr>
<tr>
<td>-ea:&lt;packagename&gt;...</td>
<td>java -ea:pkg1...</td>
<td>open assertion in package pkg1 and its subpackage</td>
</tr>
<tr>
<td>-da:&lt;packagename&gt;...</td>
<td>java -da:pkg1...</td>
<td>close assertion in package pkg1 and its subpackage</td>
</tr>
<tr>
<td>-esa</td>
<td>java -esa</td>
<td>open assertion in system classes</td>
</tr>
<tr>
<td>-dsa</td>
<td>java -dsa</td>
<td>close assertion in system classes</td>
</tr>
<tr>
<td>use class and package together</td>
<td>java -da:MyClass1:pkg1</td>
<td>close assertion in class MyClass1 and package pkg1</td>
</tr>
<tr>
<td>use -ea and -da together</td>
<td>java -da:MyClass1 -ea:MyClass</td>
<td>java interprets its options from left to right</td>
</tr>
</tbody>
</table>
2) through ClassLoader at runtime

ClassLoader class has some methods related to assertion setting:
[![f918c7a0](/media/f918c7a0.bmp)](/media/f918c7a0.bmp)

#### Some restrictions and pitfalls

1) assertion and inheritance

The following is an example:


```java
class Derived extends Base {
    public void derivedMethod() {
        assert false : "Assertion failed:This is derive";//assertion always fails
        System.out.println("Derived Method");
    }

    public static void main(String[] args) {
        try {
            Derived derived = new Derived();
            derived.baseMethod();
            derived.derivedMethod();
        } catch (AssertionError ae) {
            System.out.println(ae);
        }
    }
}
```

Now we use all kinds of command options and see the output results:
<table id="table4" style="height: 326px;" border="1" width="745">
<tbody>
<tr>
<td>Command options</td>
<td>Description</td>
<td>Output</td>
</tr>
<tr>
<td>Java Derived</td>
<td>close all assertions(default)</td>
<td>Base Method
Derived Method</td>
</tr>
<tr>
<td>Java -ea Derived</td>
<td>open all assertions</td>
<td>Java.lang.AssertionError:Assertion Failed:This is base</td>
</tr>
<tr>
<td>Java -da Derived</td>
<td>close all assertions</td>
<td>Base Method
Derived Method</td>
</tr>
<tr>
<td>Java -ea:Base Derived</td>
<td>open the assertion in Base class</td>
<td>Java.lang.AssertionError:Assertion Failed:This is base</td>
</tr>
<tr>
<td>Java -ea:Derived Derived</td>
<td>close the assertion in Derived class</td>
<td>Base Method
Java.lang.AssertionError:Assertion Failed:This is derived</td>
</tr>
</tbody>
</table>
<span style="text-decoration: underline;">Conclusion: assertion setting cannot be inherited. Just enabling the assertion of derived class will not necessarily take effect for the assertions in the inherited methods and other methods of its base class.</span>

#### Best/bad usage scenarios

1) it's preferable to assert the arguments of private method.

You should not use assertions to check arguments in public methods because it would otherwise throw runtime exceptions: IllegalArgumentException, NullPointerException, and so forth.

2) it's BAD flavour that assertion has some side effects


```java
private int a = 1;

private String foo() {
    a = 4;
    return "sss";
}

private void assertSomething() {
    assert false : a++; // This has a side effect
    assert false: foo(); // This also has a side effect
}
```

As assertion can be disabled, so there will be something strange to happen if you do something depending on assertion fails.

In the above example, if assertion is disabled, then the variable will be unchanged.

3) in if-then-else and swith-case flow-control statements, you can add "assert false;" in unexpected branch of control.


```java
switch(x) {
    case 1: …;
    case 2: …;
    case 3: …
    default: assert false : "x value is invalid: " + x;
}

if(a==1) {
    // some codes here
} else if {
    // some codes here
} else {
    assert false : "Normally, it cannot be here";
}
```

This is a defensive technology when you do some test. We can generalize this good style into other similar cases, for example, when you think some place in your code will never be reached. In these cases, the assertion is very useful, because once the assertion fails, there will be something critical logic errors or some programming faults.

4) You should not use assertions to validate your business logic and avoid using assertions to check or validate method arguments.
