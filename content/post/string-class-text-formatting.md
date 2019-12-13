---
title: 'String class, text formatting'
toc: true
id: 660
categories:
  - Basics
date: "2015-03-23T20:28:12+00:00"
---

### working with String class

String is most frequently used class in Java.

#### immutable and flyweight design

String is immutable

String is designed with the Flyweight design pattern. A pool of Strings is maintained by the String class.

##### pool of Strings and intern()

You can always get the string in the pool by invoking _**intern()**._

For any two Strings s1 &amp; s2, _s1.intern( ) _== _s2.intern( ) _only if _s1.equals(s2) _is true.

All literal strings and string-valued constant expressions are interned.

![string-intern](/media/string-intern.png)

For more details about intern(), please refer to [http://java-performance.info/string-intern-in-java-6-7-8/](http://java-performance.info/string-intern-in-java-6-7-8/)

#### stings concatenation

*   Use plus (“+”) operator for concatenating constants.
*   Use _concat( ) _method for concatenating 2 string variables.
*   Use _StringBuilder _or _StringBuffer _for concatenating a number of _String _variables, and most importantly in loops.
*   Prefer _StringBuilder _to _StringBuffer _unless multiple threads can have access to it.
Notice:  Plus operator is more efficient than the other two as the JVM optimizes constants.

### Text formatting

In Java, it's convenient to format something as text representations.

####  printf-style formatting

This is supported by java.util.Formatter class.

Some class uses this Formatter indirectly:


```java
System.out.printf(String, Object...)
String.format(String, Object...)
```


####  java.text.Format family

Format

|--- NumberFormat

|--- ChoiceFormat

|--- DecimalFormat

|--- DateFormat

|--- SimpleDateFormat

|--- MessageFormat
