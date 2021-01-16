---
title: JDBC Driver register
toc: true
id: 1466
comment: false
categories:
  - Uncategorized
date: "2015-11-19T23:24:13+00:00"
---

JDBC use the way of registry to manage its drivers by the method of `DriverManager.registerDriver(...)`

This method is static one, usually there're several ways to invoke it:
1. in your code, write `Class.forName("com.xx.jdbc.Driver");`, this is a way to load the specified `Driver` class explicitly. You can get the effect because the jdbc driver venders usually get the `registerDriver(..)` method invoked just in the static block of the `Driver` implementation class.
2. you can tell the JVM to load which driver by setting the `jdbc.drivers` system property.
3. Another way is supported by the `ServiceLocator` mode. This needs the jdbc driver vender to package their jdbc driver jar following the service provider convention.

If you see the source code of JDBC API, you will find that the behaviours of way 2) and 3) are defined by the `DriverManager.loadInitialDrivers()` static method. So you needn't do anything in your code to register `Driver`.

For example:
* DB2 JDBC driver `db2jcc4.jar` is packaged following the service provider convention, so you needn't invoke `Class.forName(..)` in your code.
* MySQL JDBC driver `mysql-connector-java.jar` uses the same way.

