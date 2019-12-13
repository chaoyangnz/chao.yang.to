---
title: 'String#substring(..) implementation'
toc: true
id: 530
categories:
  - Basics
date: "2015-03-19T15:22:01+00:00"
---

I heard that in new version of JDK (&gt;JDK7u5), substring implementation is changed.

In the past, substring() don't copy the specified character string but share the inherent character array reference with the original String. In this case, String substring can result in retaining more memory than you might expect. As such it's not a memory leak as this memory can be recovered normally (potential memory leak).

But note _**this behaviour has changed as of Java 7u6.**_
