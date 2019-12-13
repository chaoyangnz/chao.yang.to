---
title: Logging problem in Spring MVC project
toc: true
tags:
  - Logging
id: 1413
categories:
  - Project problems
date: "2015-08-01T22:33:08+00:00"
---

Spring MVC uses JCL as its logging framework.

A strange problem is when front-end reports an error, I cannot find exception stack in the log file.

### Why does Spring "swallow" the exception log?

A reason is I don't config any exception handler and the default HanderExceptionResolvers don't log the exceptions at all.

Another reason is the logging API delegation problem.

I use SLF4J as the logging API, and use Log4j as its inherit implementation.

SLF4J supports different implementation adapter, and all kinds of other logging API bridge.

Basically, adapter is used to tell SLF4j what is the implementation for the API, and bridge is used to delegate other logging API to SLF4J if you use some components using other logging framework, like JCL, JUL or logback.

But notice, you cannot use the adapter and bridge together for the same logging framework, for example, slf4j-log4j12.jar and log4j-over-slf4j.jar cannot be in the classpath at the same time.

A sample configurations is shown as following.



```xml
<!-- Logging -->
<dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>slf4j-api</artifactId>
    <version>1.7.10</version>
</dependency>
<!-- logging implementation -->
<dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>slf4j-log4j12</artifactId>
    <version>1.6.6</version>
    <scope>runtime</scope>
</dependency>
<!-- logging implementation adaptor -->
<dependency>
    <groupId>log4j</groupId>
    <artifactId>log4j</artifactId>
    <version>1.2.17</version>
    <scope>runtime</scope>
</dependency>
<!-- logging bridges -->
<dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>jcl-over-slf4j</artifactId>
    <version>1.7.10</version>
    <scope>runtime</scope>
</dependency>
<dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>jul-to-slf4j</artifactId>
    <version>1.7.10</version>
    <scope>runtime</scope>
</dependency>
```

