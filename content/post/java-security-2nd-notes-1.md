---
title: 'Java Security 2nd - notes 1'
toc: true
date: "2016-02-16T16:45:25+00:00"
---

## security manager vs. access controller

Historically, before the access controller existed, the security manager relied on its internal logic to determine the security policy that should be in effect, and changing the security policy required changing the security manager itself. Starting with Java 2, the security manager defers these decisions to the access controller. Since the security policy enforced by the access controller can be specified by using policy files, this allows a much more flexible mechanism for determining policies.


Java applications (at least by default) have no security manager while Java applets (again, by default) have a very strict security manager.

Specifying the `−Djava.security.manager` JVM option to install a security manager. But by default the security manager is installed programatically by the appletviewer and the Java Plug-in.

How do APIs use `SecurityManager` to check the allowance of an operation?

Take the `FileInputStream` class as an example:



```java
public FileInputStream(String name) throws FileNotFoundException {
    SecurityManager security = System.getSecurityManager(  );
    if (security != null) {
        security.checkRead(name);
    }
    try {
        open(name);         // open() is a private method of this class
    } catch (IOException e) {
        throw new FileNotFoundException(name);
    } 
}

```


`System` has two static methods to get and set security manager.

- `public static SecurityManager getSecurityManager()`
- `public static void setSecurityManager(SecurityManager sm)`

A single security manager per virtual machine.

## The Access Controller

//TODO



