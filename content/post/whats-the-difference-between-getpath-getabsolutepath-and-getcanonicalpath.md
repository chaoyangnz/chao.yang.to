---
title: "What's the difference between getPath(), getAbsolutePath(), and getCanonicalPath()?"
toc: true
id: 1028
categories:
  - Q/A
date: "2015-04-23T18:56:07+00:00"
---

Consider these filenames:

*   C:\temp\file.txt - This is a path, an absolute path, and a canonical path.
*   .\file.txt - This is a path. It's neither an absolute path nor a canonical path.
*   C:\temp\myapp\bin\..\\..\file.txt - This is a path and an absolute path. It's not a canonical path.
A canonical path is always an absolute path.

Converting from a path to a canonical path makes it absolute (usually tack on the current working directory so e.g. ./file.txt becomes c:/temp/file.txt). The canonical path of a file just "purifies" the path, removing and resolving stuff like ..\ and resolving symlinks (on unixes).
