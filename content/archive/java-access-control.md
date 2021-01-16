---
title: Java access control
toc: true
id: 14
categories:
  - Basics
date: "2013-01-12T02:28:45+00:00"
---

Java access control is a bit weird, especially for the ones with C++ background.

There are two types of access control, one for classes and the other for members.
To simplify my discussion, in this context the class mean the top level one, which is directly defined in a compile unit.
As the nested class is actually always associated with another class, so it's not top level class and I regard it as just a member like member variables and member methods.

P.S: package access control has no specifier.

### Class access control

Class access control has just two: public, package.

**NOTE: A java file must contain ONE public class at most. If a public class exists, its name must be the same as the file name.**

Class access control is mainly used for the visibility to other package or class. It can influence the fact whether you can import the class. It can be more convenient if you understand the access of one class as whether you can see it from somewhere.

If you declare some class as package access control, it is only accessed by the classes with the same package.

In this point, you will think it's really reasonable why a class cannot be declared as private or protected.

### Member access control

#### Class member access control

I simply think static fields or methods as the member of a class.

The rules are similar with the ones of object members access control, so I don't plan to discuss them separately.

#### Object member access control

Object members can be declared as: public, protected, package, private.
In this order, the access is more and more limited.

Public: anything can access, object members by object and static members by class.
Protected: package and inherited class can access.
Package: package class can access
Private: just the class itself can access

Now I will discuss other modifiers: static, abstract, final

The meaning of static is very notable, and abstract and final is mutual.

static can be just used for members(fields, methods, nested classes, static initialization block), not top level classes.

<span style="text-decoration: underline;">abstract class</span> (as well as the nested class):
Abstract class has zero or more than one method being left unimplemented.

<span style="text-decoration: underline;">abstract member method</span>:
This method has no body. And the class of this method must be also abstract.

<span style="text-decoration: underline;">final class</span> (as well as the nested class):
Final class means it cannot be inherited.

<span style="text-decoration: underline;">final member method</span>:
This method cannot be overridden.

<span style="text-decoration: underline;">final member field</span>:
This field is unchangeable once initialized.
(Primitive field cannot be changed to another value; Object reference field cannot be changed to another object reference)

**NOTE: Final object reference field doesn't necessarily mean that the object it refers is unchangeable. The fields/properties of the object can be changed at its will.**
**The declaration of final CANNOT ensure an immutable object. **

Sometimes, it needn't declare the specifiers explicitly, as there are some defaults.

interface: abstract (interface can be public or package)
interface member field: public static final
interface member method: public abstract

More comprehensive discussions, please refer to [java modifiers review](http://richdyang.com/java-modifiers-review)
