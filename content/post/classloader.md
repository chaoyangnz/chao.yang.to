---
title: ClassLoader
toc: true
id: 1141
categories:
  - JVM
date: "2015-05-05T14:20:23+00:00"
---

### class loader

#### Delegation Principal

> Class loaders request their parent to load the class first before attempting to load it themselves.
![classloader1](/media/classloader1.jpg)           ![classloader2](/media/classloader2.jpg)

#### Visibility Principal

> Classes loaded by the parent class loaders have visibility into classes loaded by its children down the hierarchy, but the reverse is not true
![classloader3](/media/classloader3.jpg)

#### Uniqueness Principal

> When a class loader loads a class, the child class loaders in the hierarchy will never reload the class again. Hence uniqueness is maintained.
&nbsp;

Although this, you are easy to violate this by manually loading the class. Thus, in a classloader hierarchy, there exists two Class with the same qualified name but loaded by different classloaders.

Then if you cast them, a ClassCastException will be thrown.

&nbsp;

### class loading ways

*   explicitly invoke new XXX()
*   load its subclass, then the parent class will be also loaded
*   dynamically load class: Class.forName("com.ss.xx.XXX")
&nbsp;

&nbsp;
