---
title: "what's the difference between getName(), getSimpleName(), getCanonicalName() of Class?"
toc: true
id: 1030
categories:
  - Q/A
date: "2015-04-23T19:02:25+00:00"
---



```java
//primitive
System.out.println(int.class.getName());
System.out.println(int.class.getCanonicalName());
System.out.println(int.class.getSimpleName());

System.out.println();

//class
System.out.println(String.class.getName());
System.out.println(String.class.getCanonicalName());
System.out.println(String.class.getSimpleName());

System.out.println();

//inner class
System.out.println(HashMap.SimpleEntry.class.getName());
System.out.println(HashMap.SimpleEntry.class.getCanonicalName());
System.out.println(HashMap.SimpleEntry.class.getSimpleName());

System.out.println();

//anonymous inner class
System.out.println(new Serializable(){}.getClass().getName());
System.out.println(new Serializable(){}.getClass().getCanonicalName());
System.out.println(new Serializable(){}.getClass().getSimpleNam());
```

&nbsp;

Prints:
> int
> 
> int
> 
> int
> 
> 
> java.lang.String
> 
> java.lang.String
> 
> String
> 
> 
> java.util.AbstractMap$SimpleEntry
> 
> java.util.AbstractMap.SimpleEntry
> 
> SimpleEntry
> 
> 
> ClassnameTest$1
> 
> null
There's an empty line in the last block where getSimpleName returns an empty string.

The upshot looking at this is:

the name is the name that you'd use to dynamically load the class with, for example, a call to Class.forName with the default classloader.
the canonical name is the name that would be used in an import statement and uniquely identifies the class. Might be useful during toString or logging operations.
the simple name loosely identifies the class, again might be useful during toString or logging operations but is not guaranteed to be unique.

&nbsp;

&nbsp;
