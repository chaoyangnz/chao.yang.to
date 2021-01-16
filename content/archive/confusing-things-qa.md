---
title: confusing things Q/A
toc: true
id: 1015
categories:
  - Q/A
date: "2015-04-22T22:46:26+00:00"
---

# what does 0.0.0.0 mean?
0.0.0.0, in server context, means "**all IP addresses on the local machine**" (in fact probably, "all IPv4 addresses on the local machine"). So, if your webserver machine has two ip addresses, 192.168.1.1 and 10.1.2.1, and you allow a webserver daemon like apache to listen on 0.0.0.0, it will be reachable at both of those IPs. But only to what can contact those IPs and the web port(s).

The only thing is that you cannot say "**<del>all addresses should have acces_s_</del>**" -- that's done in your firewall(s) and/or the server software and/or other security layers like tcpwrappers.

Note that, in a different context (routing) 0.0.0.0 usually means the default route (the route to "the rest of" the internet, aside from routes in your local network etc.).


# what's the difference between getName(), getSimpleName(), getCanonicalName() of Class?

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

# What's the difference between getPath(), getAbsolutePath(), and getCanonicalPath()?
Consider these filenames:

*   C:\temp\file.txt - This is a path, an absolute path, and a canonical path.
*   .\file.txt - This is a path. It's neither an absolute path nor a canonical path.
*   C:\temp\myapp\bin\..\\..\file.txt - This is a path and an absolute path. It's not a canonical path.
A canonical path is always an absolute path.

Converting from a path to a canonical path makes it absolute (usually tack on the current working directory so e.g. ./file.txt becomes c:/temp/file.txt). The canonical path of a file just "purifies" the path, removing and resolving stuff like ..\ and resolving symlinks (on unixes).

# Why two Date classes: java.util.Date and java.sql.Date?

A java.util.Date represents date and time of day, a java.sql.Date only represents a date. The complement of java.sql.Date is java.sql.Time, which only represents a time of day.
The java.sql.Date is a subclass (an extension) of java.util.Date. So, what changed in java.sql.Date:

- toString() generates a different string representation: yyyy-mm-dd
- a static valueOf(String) methods to create a Date from a String with above representation
- the getters and setter for hours, minutes and seconds are deprecated

The java.sql.Date class is used with JDBC and it was intended to not have a time part, that is, hours, minutes, seconds, and milliseconds should be zero… but this is not enforced by the class.

You can still use its setTime(long) method to set the date in the form of millisecond time value.

java.sql.Time is similar to java.sql.Date and inherited to java.util.Date.

java.sql.Timestamp can represent date and time together, just like java.util.Date