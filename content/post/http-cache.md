---
title: HTTP cache
toc: true
id: 1432
comment: false
categories:
  - Uncategorized
date: "2015-09-26T21:47:47+00:00"
---

Request headers:


```java
If-Modified-Since: {time}      
If-None-Match: {etag}
```

These two headers are used to check if the cached resource is still fresh.

If expired, response status is 200 and fetch the resource

If not, response status is 304 and cache is still fresh.

Response headers:


```java
Last-Modified: {time}
ETag: {etag}
Expire: {time}
Cache-Control: maxAge={seconds}
```

![image](/media/image.jpeg)

