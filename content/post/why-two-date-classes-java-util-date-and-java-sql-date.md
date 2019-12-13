---
title: 'Why two Date classes: java.util.Date and java.sql.Date?'
toc: true
id: 527
categories:
  - Basics
date: "2015-03-19T14:35:54+00:00"
---

A java.util.Date represents date and time of day, a java.sql.Date only represents a date. The complement of java.sql.Date is java.sql.Time, which only represents a time of day.
The java.sql.Date is a subclass (an extension) of java.util.Date. So, what changed in java.sql.Date:

-- toString() generates a different string representation: yyyy-mm-dd
-- a static valueOf(String) methods to create a Date from a String with above representation
-- the getters and setter for hours, minutes and seconds are deprecated

The java.sql.Date class is used with JDBC and it was intended to not have a time part, that is, hours, minutes, seconds, and milliseconds should be zero… but this is not enforced by the class.

You can still use its setTime(long) method to set the date in the form of millisecond time value.

java.sql.Time is similar to java.sql.Date and inherited to java.util.Date.

java.sql.Timestamp can represent date and time together, just like java.util.Date
