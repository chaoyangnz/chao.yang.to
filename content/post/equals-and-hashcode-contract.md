---
title: 'equals() / hashCode() contract, Comparable contract'
toc: true
id: 464
categories:
  - Basics
date: "2015-03-16T19:13:32+00:00"
---

Although Java encourage and support to maintain contract by interface, but there are some implicit contracts that should be noticed when you are implementing some special functions.

### What is equals and hashCode contract?

> **The interface contract for `Object` requires that if two objects are equal according to `equals()`, then they must have the same `hashCode()`value.**
> 
> 
> 1\. If two objects are equal, then they must have the same hash code.
> 
> 2\. If two objects have the same hashcode, they may or may not be equal.

#### Why override equals() and hashCode()?

The `hashCode()` method exists purely for efficiency. The Java platform architects anticipated the importance of hash-based collection classes -- such as `Hashtable`, `HashMap`, and `HashSet` -- in typical Java applications, and comparing against many objects with `equals()` can be computationally expensive. Having every Java object support `hashCode()` allows for efficient storage and retrieval using hash-based collections.

---------------

For the class as **key** of HashMap or HashSet(which is inner implemented by HashMap and the entry with a dummy value), it must rewrite the hashCode() and equals() methods.

![java-hashcode-650x369](/media/java-hashcode-650x3691.jpeg)

Some developers rewrite the equals() methods but don't rewrite the hashCode() method, it will be error prone. The following is **a common mistake**.



```java

public class BrokenEqualsHashCode {

    @Test
    public void testEuals() {
        SomeObject a = new SomeObject(1,&quot;1&quot;);
        SomeObject b = new SomeObject(2, &quot;1&quot;);

        System.out.println(a.hashCode());
        System.out.println(b.hashCode());

        System.out.println(b.equals(a));

        Map&lt;SomeObject, String&gt; map = new HashMap&lt;SomeObject, String&gt;(10);
        map.put(a, &quot;1&quot;);
        map.put(b, &quot;2&quot;);

		System.out.println(map.get(new SomeObject(1, &quot;1&quot;)));
	}
}

class SomeObject {
    int a;
    String b;

    public SomeObject(int a, String b) {
        this.a = a;
        this.b = b;
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;

        SomeObject that = (SomeObject) o;

        if (!b.equals(that.b)) return false;

        return true;
    }
}
```


3790865
26759037
true
null

You can see, although the two objects as key are equal, but it cannot get the expected effect.

Actually, the two entries are stored in the hash map, but it cannot be retrieved by the equal object.

#### How to design a good key for HashMap

The very basic need for designing a good key is that “_**we should be able to retrieve the value object back from the map without failure**_“, otherwise no matter how fancy data structure you build, it will be of no use.

For this basic reasoning, key objects are suggested to be IMMUTABLE. IMMUTABILITY allows you to get same hash code every time, for a key object.

But remember that **immutability is recommended and not mandatory**.

An example is always better for demonstration, right? Then lets have one.

In this example, I have created an account class with only two fields for simplicity. I have overridden the hash code and equals method such that it uses only account number to verify the uniqueness of Account object. All other possible attributes of Account class can be changed on runtime.



```java

public class Account
{
    private int accountNumber;
    private String holderName;

    public Account(int accountNumber) {
        this.accountNumber = accountNumber;
    }

    public String getHolderName() {
        return holderName;
    }

    public void setHolderName(String holderName) {
        this.holderName = holderName;
    }

    public int getAccountNumber() {
        return accountNumber;
    }

    //Depends only on account number
    @Override
    public int hashCode() {
        final int prime = 31;
        int result = 1;
        result = prime * result + accountNumber;
        return result;
    }

    //Compare only account numbers
    @Override
    public boolean equals(Object obj) {
        if (this == obj)
            return true;
        if (obj == null)
            return false;
        if (getClass() != obj.getClass())
            return false;
        Account other = (Account) obj;
        if (accountNumber != other.accountNumber)
            return false;
        return true;
    }

}
```


Output:

A_ONE
A_TWO
A_ONE

### What is Comparable contract?

Basically, Comparable contract is related to sorting or ordering.

So to do the following things, you must implement Comparable:

*   calling <tt>[Collections](http://docs.oracle.com/javase/7/docs/api/java/util/Collections.html).sort</tt> and <tt>Collections.binarySearch</tt>
*   calling <tt>[Arrays](http://docs.oracle.com/javase/7/docs/api/java/util/Arrays.html).sort</tt> and <tt>Arrays.binarySearch</tt>
*   using objects as keys in a <tt>[TreeMap](http://docs.oracle.com/javase/7/docs/api/java/util/TreeMap.html)</tt>
*   using objects as elements in a <tt>[TreeSet](http://docs.oracle.com/javase/7/docs/api/java/util/TreeSet.html)</tt>
It's not hard to notice that these are all related to ordering.

Compare the various types of fields as follows:

*   numeric primitive : use <tt>&lt;</tt> and <tt>&gt;</tt>. There is an exception to this rule: <tt>float</tt> and <tt>double</tt> primitives should be compared using Float.[compare(float, float)](http://docs.oracle.com/javase/7/docs/api/java/lang/Float.html#compare(float,%20float)) and Double.[compare(double, double)](http://docs.oracle.com/javase/7/docs/api/java/lang/Double.html#compare(double,%20double)). This avoids problems associated with special border values.
*   <tt>boolean</tt> primitive :  use tests of the form <tt>(x &amp;&amp; !y)</tt>
*   All primitive wrapper classes implement <tt>Comparable</tt>
*   <tt>Object</tt> : use <tt>compareTo</tt>. (Note that possibly-null fields present a problem : while <tt>x.equals(null)</tt> returns <tt>false</tt>, <tt>x.compareTo(null)</tt> will always throw a <tt>NullPointerException</tt>)
*   type-safe enumeration : use <tt>compareTo</tt>, like any <tt>Object</tt>
*   collection or array : <tt>Comparable</tt> does not seem to be intended for these kinds of fields. For example, <tt>List</tt>, <tt>Map</tt> and <tt>Set</tt> do not implement <tt>Comparable</tt>. As well, some collections have no definite order of iteration, so doing an element-by-element comparison cannot be meaningful in those cases.
If the task is to perform a sort of items which are stored in a relational database, then it is usually much preferred to let the database perform the sort using the ORDER BY clause, rather than in code.

An alternative to implementing <tt>Comparable</tt> is passing **<tt>[Comparator](http://docs.oracle.com/javase/7/docs/api/java/util/Comparator.html)</tt>** objects as parameters. Be aware that if a <tt>Comparator</tt> compares only one of several significant fields, then the <tt>Comparator</tt> is very likely not consistent with <tt>equals</tt>.
