---
title: java modifiers revisit
toc: true
id: 619
categories:
  - Basics
date: "2015-03-23T12:31:07+00:00"
---

### acess control modifiers

In Java, there are four different types of access modifiers for fields and methods:

*   public: every class can access the element
*   protected: only subclasses and classes in the same package can access the element
*   default: only classes in the same package can access the element
*   private: only this class can access the element

#### what does accessing mean?

Accessing means you can refer. As in java only class can be the top-level element, thus top-level class access just use its name without a prefix. But for other enclosed elements like fields(instance or class), methods(instance or class), inner classes(member, local or anonymous class), nested classes/interfaces, you must access them with a prefix(class name or instance name) explicitly or implicitly if possible.

#### where does accessing happen?

Many places are where accessing happen, including instance field assignments, intialization blocks, static field assignments, static intialization blocks, instance methods, static methods(class methods), inner class definition body, nested class definition body...


```java
package accessing.somepackage;

public class BaseClass {

    private int private_instance_field = 1;
    long package_instance_field = 2l;
    protected double protected_instance_field = 2.0;
    public String public_instance_field = "d";

    private void private_instance_method() {}

    void package_instance_method() {}

    protected void protected_instance_method() {}

    public void public_instance_method() {}

    private static int private_class_field = 1;
    static long package_class_field = 2l;
    protected static double protected_class_field = 3.0;
    public static String public_class_field = "h";

    private static void private_class_method() {}

    static void package_class_method() {}

    protected static void protected_class_method() {}

    public static void public_class_method() {}

    private class Private_instance_inner_class_A {}

    class Package_instance_inner_class_B {}

    protected class Protected_instance_inner_class_C {}

    public class Public_instance_inner_class_D {}

    private static class Private_class_nested_class_A {}

    static class Package_class_nested_class_B {}

    protected static class Protected_class_nested_class_C {}

    public static class Public_class_nested_class_D {}

    public void testAccessInInstanceMethod() {
        Object o = null;
        o = BaseClass.private_class_field;
        o = BaseClass.package_class_field;
        o = BaseClass.protected_class_field;
        o = BaseClass.public_class_field;
        BaseClass.private_class_method();
        BaseClass.package_class_method();
        BaseClass.protected_class_method();
        BaseClass.public_class_method();

        Private_instance_inner_class_A a = null;
        Package_instance_inner_class_B b = null;
        Protected_instance_inner_class_C c = null;
        Public_instance_inner_class_D d = null;

        Package_class_nested_class_B aa = null;
        Package_class_nested_class_B bb = null;
        Protected_class_nested_class_C cc = null;
        Public_class_nested_class_D dd = null;

        //////////////////////////////////////////
        BaseClass base = new BaseClass();
        o = base.private_instance_field;
        o = base.package_instance_field;
        o = base.protected_instance_field;
        o = base.public_instance_field;
        base.private_instance_method();
        base.package_instance_method();
        base.protected_instance_method();
        base.public_instance_method();

        o = base.private_class_field;
        o = base.package_class_field;
        o = base.protected_class_field;
        o = base.public_class_field;
        base.private_class_method();
        base.package_class_method();
        base.protected_class_method();
        base.public_class_method();

        ///////////////////////////////
        o = this.private_instance_field;
        o = this.package_instance_field;
        o = this.protected_instance_field;
        o = this.public_instance_field;
        this.private_instance_method();
        this.package_instance_method();
        this.protected_instance_method();
        this.public_instance_method();

        o = this.private_class_field;
        o = this.package_class_field;
        o = this.protected_class_field;
        o = this.public_class_field;
        this.private_class_method();
        this.package_class_method();
        this.protected_class_method();
        this.public_class_method();

        Private_instance_inner_class_A aaa = null;
        Package_instance_inner_class_B bbb = null;
        Protected_instance_inner_class_C ccc = null;
        Public_instance_inner_class_D ddd = null;

        Private_class_nested_class_A aaaa = null;
        Package_class_nested_class_B bbbb = null;
        Protected_class_nested_class_C cccc = null;
        Public_class_nested_class_D dddd = null;
    }

    public static void testAccessInClassMethod() {
        Object o = null;
        o = BaseClass.private_class_field;
        o = BaseClass.package_class_field;
        o = BaseClass.protected_class_field;
        o = BaseClass.public_class_field;
        BaseClass.private_class_method();
        BaseClass.package_class_method();
        BaseClass.protected_class_method();
        BaseClass.public_class_method();

        Private_instance_inner_class_A a = null;
        Package_instance_inner_class_B b = null;
        Protected_instance_inner_class_C c = null;
        Public_instance_inner_class_D d = null;

        Package_class_nested_class_B aa = null;
        Package_class_nested_class_B bb = null;
        Protected_class_nested_class_C cc = null;
        Public_class_nested_class_D dd = null;

        //////////////////////////////////////////
        BaseClass base = new BaseClass();
        o = base.private_instance_field;
        o = base.package_instance_field;
        o = base.protected_instance_field;
        o = base.public_instance_field;
        base.private_instance_method();
        base.package_instance_method();
        base.protected_instance_method();
        base.public_instance_method();

        o = base.private_class_field;
        o = base.package_class_field;
        o = base.protected_class_field;
        o = base.public_class_field;
        base.private_class_method();
        base.package_class_method();
        base.protected_class_method();
        base.public_class_method();

        //////////////////////////////////////////
        o = private_class_field;
        o = package_class_field;
        o = protected_class_field;
        o = public_class_field;
        private_class_method();
        package_class_method();
        protected_class_method();
        public_class_method();

        Private_instance_inner_class_A aaa = null;
        Package_instance_inner_class_B bbb = null;
        Protected_instance_inner_class_C ccc = null;
        Public_instance_inner_class_D ddd = null;
        Private_class_nested_class_A aaaa = null;
        Package_class_nested_class_B bbbb = null;
        Protected_class_nested_class_C cccc = null;
        Public_class_nested_class_D dddd = null;
    }
}
```

&nbsp;


```java
package accessing.somepackage;

public class PackageClass {
    public void testAccessInInstanceMethod() {
        Object o = null;
        o = BaseClass.package_class_field;
        o = BaseClass.protected_class_field;
        o = BaseClass.public_class_field;
        BaseClass.package_class_method();
        BaseClass.protected_class_method();
        BaseClass.public_class_method();

        BaseClass.Package_instance_inner_class_B b = null;
        BaseClass.Protected_instance_inner_class_C c = null;
        BaseClass.Public_instance_inner_class_D d = null;

        BaseClass.Package_class_nested_class_B bb = null;
        BaseClass.Protected_class_nested_class_C cc = null;
        BaseClass.Public_class_nested_class_D dd = null;

        //////////////////////////////////////////
        BaseClass base = new BaseClass();
        o = base.package_instance_field;
        o = base.protected_instance_field;
        o = base.public_instance_field;
        base.package_instance_method();
        base.protected_instance_method();
        base.public_instance_method();

        o = base.package_class_field;
        o = base.protected_class_field;
        o = base.public_class_field;
        base.package_class_method();
        base.protected_class_method();
        base.public_class_method();
    }

    public static void testAccessInClassMethod() {
        Object o = null;
        o = BaseClass.package_class_field;
        o = BaseClass.protected_class_field;
        o = BaseClass.public_class_field;
        BaseClass.package_class_method();
        BaseClass.protected_class_method();
        BaseClass.public_class_method();

        BaseClass.Package_instance_inner_class_B b = null;
        BaseClass.Protected_instance_inner_class_C c = null;
        BaseClass.Public_instance_inner_class_D d = null;

        BaseClass.Package_class_nested_class_B bb = null;
        BaseClass.Protected_class_nested_class_C cc = null;
        BaseClass.Public_class_nested_class_D dd = null;

        //////////////////////////////////////////
        BaseClass base = new BaseClass();
        o = base.package_instance_field;
        o = base.protected_instance_field;
        o = base.public_instance_field;
        base.package_instance_method();
        base.protected_instance_method();
        base.public_instance_method();

        o = base.package_class_field;
        o = base.protected_class_field;
        o = base.public_class_field;
        base.package_class_method();
        base.protected_class_method();
        base.public_class_method();
    }

}
```

&nbsp;


```java
package accessing;

import accessing.somepackage.BaseClass;

public class InheritedClass extends BaseClass {
    public void testAccessInInstanceMethod() {
        Object o = null;
        o = BaseClass.protected_class_field;
        o = BaseClass.public_class_field;
        BaseClass.protected_class_method();
        BaseClass.public_class_method();

        Protected_instance_inner_class_C c = null;
        Public_instance_inner_class_D d = null;

        Protected_class_nested_class_C cc = null;
        Public_class_nested_class_D dd = null;

        //////////////////////////////////////////
        BaseClass base = new BaseClass();
//        o = base.protected_instance_field;
        o = base.public_instance_field;
//        base.protected_instance_method();
        base.public_instance_method();

        o = base.protected_class_field;
        o = base.public_class_field;
        base.protected_class_method();
        base.public_class_method();

        //////////////////////////////////////////
        o = this.protected_instance_field;
        o = this.public_instance_field;
        this.protected_instance_method();
        this.public_instance_method();

        o = this.protected_class_field;
        o = this.public_class_field;
        this.protected_class_method();
        this.public_class_method();

        Protected_instance_inner_class_C ccc = null;
        Public_instance_inner_class_D ddd = null;

        Protected_class_nested_class_C cccc = null;
        Public_class_nested_class_D dddd = null;

    }

    public static void testAccessInClassMethod() {
        Object o = null;
        o = BaseClass.protected_class_field;
        o = BaseClass.public_class_field;

        BaseClass.protected_class_method();
        BaseClass.public_class_method();

        Protected_instance_inner_class_C c = null;
        Public_instance_inner_class_D d = null;

        Protected_class_nested_class_C cc = null;
        Public_class_nested_class_D dd = null;

        //////////////////////////////////////////
        BaseClass base = new BaseClass();
//        o = base.protected_instance_field;
        o = base.public_instance_field;
//        base.protected_instance_method();
        base.public_instance_method();

        o = base.protected_class_field;
        o = base.public_class_field;
        base.protected_class_method();
        base.public_class_method();

        //////////////////////////////////////////
        o = protected_class_field;
        o = public_class_field;
        protected_class_method();
        public_class_method();

        Protected_instance_inner_class_C ccc = null;
        Public_instance_inner_class_D ddd = null;
        Protected_class_nested_class_C cccc = null;
        Public_class_nested_class_D dddd = null;
    }
}
```

&nbsp;


```java
package accessing;

import basics.accessing.somepackage.BaseClass;

public class AnyClass {
    public void testAccessInInstanceMethod() {
        Object o = null;
        o = BaseClass.public_class_field;
        BaseClass.public_class_method();

        BaseClass.Public_instance_inner_class_D d = null;

        BaseClass.Public_class_nested_class_D dd = null;

        //////////////////////////////////////////
        BaseClass base = new BaseClass();
        o = base.public_instance_field;
        base.public_instance_method();

        o = base.public_class_field;
        base.public_class_method();

    }

    public static void testAccessInClassMethod() {
        Object o = null;
        o = BaseClass.public_class_field;

        BaseClass.public_class_method();

        BaseClass.Public_instance_inner_class_D d = null;

        BaseClass.Public_class_nested_class_D dd = null;

        //////////////////////////////////////////
        BaseClass base = new BaseClass();
        o = base.public_instance_field;
        base.public_instance_method();

        o = base.public_class_field;
        base.public_class_method();
    }
}
```


#### a pitfall for protected access

A point that is worth noticing is the Line 21, 23, 66 and 68 of InheritedClass.

But why?

As always, this is covered in the JLS, 6.6.2:
> A protected member or constructor of an object may be accessed from outside the package in which it is declared only by code that is **responsible for the implementation of that object**.
That means you can only access the protected member by using _**this** _or _**super**_.

### static modifier

We can use static for non top-level class (inner classes or nested class), class methods, class variables.

### abstract / final modifiers

abstract / final is mutually exclusive. This means we cannot use them at the same time.
<table style="height: 234px;" border="1" width="837">
<tbody>
<tr>
<td></td>
<td>_class_</td>
<td>_method_</td>
<td>_variable_</td>
</tr>
<tr>
<td>_abstract_</td>
<td>

*   intend to inherit it, overriding implemented methods or completing the unimplemented methods
*   include &gt;=0 abstract methods
*   cannot be initialized
</td>
<td>

*   declare an unimplemented method without body
*   the class it defines in must be also abstract
</td>
<td>n/a</td>
</tr>
<tr>
<td>_final_</td>
<td>intend to not inherit it</td>
<td>intend to not override it</td>
<td>cannot be changed after initialization</td>
</tr>
</tbody>
</table>

### other modifiers: _synchronized, __volatile, transient_

*   synchronized can be used before instance/class methods or blocks for synchronization semantics.
*   volatile can be used before instance/class variables for atomicity and visibility purpose.
*   transient can be used before instance variables for marking the fields as non-serialization ones.

### combination of modifiers

&nbsp;
<table style="height: 588px; font-size: 0.9em;" border="1" width="808">
<tbody>
<tr>
<td></td>
<td></td>
<td style="text-align: left;">_** abstract**_</td>
<td>_**final**_</td>
</tr>
<tr>
<td>_**top level class**_</td>
<td>public/package</td>
<td style="text-align: left;">public/package abstract</td>
<td>public/package final</td>
</tr>
<tr>
<td>_**instance method**_</td>
<td>public/protected/package/private</td>
<td style="text-align: left;"><span style="color: #0000ff;">public/protected/package </span>abstract</td>
<td>public/protected/package/private final</td>
</tr>
<tr style="background-color: #ccc;">
<td>_**class method**_</td>
<td>public/protected/package/private static</td>
<td></td>
<td>public/protected/package/private static final</td>
</tr>
<tr>
<td>_**instance variable**_</td>
<td>public/protected/package/private</td>
<td style="text-align: left;"></td>
<td>public/protected/package/private final</td>
</tr>
<tr style="background-color: #ccc;">
<td>_**class variable**_</td>
<td>public/protected/package/private static</td>
<td></td>
<td>public/protected/package/private static final</td>
</tr>
<tr>
<td>_**local variable**_</td>
<td></td>
<td style="text-align: left;"></td>
<td>final</td>
</tr>
<tr>
<td>_**inner class**_</td>
<td>public/protected/package/private</td>
<td style="text-align: left;">public/protected/package/private abstract</td>
<td>public/protected/package/private final</td>
</tr>
<tr>
<td>_**local class**_</td>
<td></td>
<td style="text-align: left;">abstract</td>
<td>final</td>
</tr>
<tr>
<td>_**anonymous &lt;local&gt; class**_</td>
<td></td>
<td style="text-align: left;"></td>
<td></td>
</tr>
<tr style="background-color: #ccc;">
<td>_**nested class**_</td>
<td>public/protected/package/private static</td>
<td>public/protected/package/private static abstract</td>
<td>public/protected/package/private static final</td>
</tr>
<tr>
<td>_**interface**_</td>
<td>public/package</td>
<td style="text-align: left;">public/package &lt;abstract&gt;</td>
<td></td>
</tr>
<tr style="background-color: #ccc;">
<td>_**nested interface**_</td>
<td>public/protected/package/private</td>
<td>public/protected/package/private &lt;static&gt; &lt;abstract&gt;</td>
<td></td>
</tr>
</tbody>
</table>
> NOTICE: &lt;...&gt; represents this is default modifier implicitly.
