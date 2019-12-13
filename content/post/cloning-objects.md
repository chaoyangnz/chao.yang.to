---
title: cloning objects
toc: true
id: 658
categories:
  - Basics
date: "2015-03-23T20:26:14+00:00"
---

Java has built-in support for shadow copy by Object.clone() and Cloneable interface.

### shadow copy

**This is default implementation in java**.

Object class has a method named clone(), but if we'd like to succeed with this built-in cloning capability, we need to use Cloneable marker interface.

The method performs a specific cloning operation. If the class of this object does not implement the interface, then a CloneNotSupportedException is thrown.

Note that all arrays are considered to implement the interface Cloneable.

**By default, java cloning is ‘field by field copy’** i.e. as the [Object](http://docs.oracle.com/javase/1.5.0/docs/api/java/lang/Object.html "Object class") class does not have idea about the structure of class on which [clone()](http://docs.oracle.com/javase/1.5.0/docs/api/java/lang/Object.html#clone%28%29 "clone method in object class") method will be invoked. So, JVM when called for cloning, do following things:

**1)** If the class has **only primitive data type members** then a completely new copy of the object will be created and the reference to the **new object copy will be returned**.

**2)** If the class contains **members of any class type** then only the object references to those members are copied and hence the **member references in both the original object as well as the cloned object refer to the same object**.


```java
package basics;

import org.junit.Test;
import static org.junit.Assert.*;

public class Clones {

    @Test
    public void testClone() throws Exception {
        Employee emp = new Employee(1, "Jack", new Department(2, "HR"));
        Employee clone = (Employee)emp.clone();

        assertTrue(emp.empoyeeId == clone.empoyeeId); // true
        assertTrue(emp.employeeName == clone.employeeName); // true
        assertTrue(emp.department == clone.department); // true

        Department dept = new Department(1, "IT");
        // throw CloneNotSupportedException, as Department hasn't implement Cloneable
        Department deptClone = (Department)dept.clone();
    }
}

class Employee implements Cloneable{

    protected int empoyeeId;
    protected String employeeName;
    protected Department department;

    public Employee(int id, String name, Department dept)
    {
        this.empoyeeId = id;
        this.employeeName = name;
        this.department = dept;
    }
    @Override
    protected Object clone() throws CloneNotSupportedException {
        return super.clone();
    }

    //Accessor/mutators methods will go there
}

class Department
{
    protected int id;
    protected String name;

    public Department(int id, String name)
    {
        this.id = id;
        this.name = name;
    }
    //Accessor/mutators methods will go there

    @Override
    protected Object clone() throws CloneNotSupportedException {
        return super.clone();
    }
}
```


### deep copy

In most of time, default clone mechanism is enough for us. But in some special case, we need real deep copy.

There are several method you can achieve this aim.

#### overriding clone() method and write your own



```java
//Modified clone() method in Employee class
@Override
protected Object clone() throws CloneNotSupportedException {
    Employee cloned = (Employee)super.clone();
    cloned.setDepartment((Department)cloned.getDepartment().clone());
    return cloned;
}
```


#### in-memory serialization

This method is for Serializable object.


```java
public static  T cloneThroughSerialize(T t) throws Exception {
   ByteArrayOutputStream bos = new ByteArrayOutputStream();
   serializeToOutputStream(t, bos);
   byte[] bytes = bos.toByteArray();
   ObjectInputStream ois = new ObjectInputStream(new ByteArrayInputStream(bytes));
   return (T)ois.readObject();
}

private static void serializeToOutputStream(Serializable ser, OutputStream os) throws IOException {
   ObjectOutputStream oos = null;
   try {
      oos = new ObjectOutputStream(os);
      oos.writeObject(ser);
      oos.flush();
   } finally {
      oos.close();
   }
}

// using our custom method
Object cloned = cloneThroughSerialize (someObject);

// or with Apache Commons
cloned = org.apache.commons.lang. SerializationUtils.clone(someObject);
```


#### options for non Serializable object

But what if the class we want to clone isn’t Serializable and we have no control over the source code or can’t make it Serializable?

**Option 1 – Java Deep Cloning Library**

There’s a nice little library which can deep clone virtually any Java Object – [cloning](http://code.google.com/p/cloning/ "Java Deep Cloning library"). It takes advantage of Java’s excellent reflection capabilities to provide optimized deep-cloned versions of objects.
<div id="highlighter_808936" class="syntaxhighlighter ">
<div class="bar">
<div class="toolbar">


```java
Cloner cloner=new Cloner();
Object cloned = cloner.deepClone(someObject);
```

**Option 2 – JSON Cloning**

This is very useful and straightforward, especially now JDK 7 has the out-of-the-box JSON library support - Java API for JSON Processing ([JSR 353](http://jcp.org/en/jsr/detail?id=353)) .

&nbsp;

</div>
</div>
</div>
