---
title: 'No mapping found for HTTP request with URI [WEB-INF/views/xxx.jsp]'
toc: true
tags:
  - Spring MVC
id: 1411
categories:
  - Project problems
date: "2015-08-01T22:21:28+00:00"
---

When I use the InternalResourceViewResolver for the JSP rendering, I found the spring complains:


```log
No mapping found for HTTP request with URI [WEB-INF/views/xxx.jsp]
```

I don't know why. Normally, it certainly uses this resolver, because spring got the prefix and suffix I set. I open the debug logging, and found the log says:


```log
-mvc-servlet forwards to WEB-INF/views/xxx.jsp
-No handler method found
```


So it goes to find a hander method, rather than renders directly. Why? Why?

At last, I found it's the servlet-mapping problem. I map the dispatcher servlet to "`/*`" instead of "`/`". Thus all files include `*.jsp` also go through the dispatcher servlet. Obviously, I never map the jsp URL to any handler method.

## url-pattern: /* and /

the difference is :
> SRV.11.2 Specification of Mappings
> 
> 
> In theWeb application deployment descriptor, the following syntax is used to define mappings:
> 
> 
> * A [string](http://www.coderanch.com/t/410859/java/java/String-StringBuffer-StringBuilder-Performance "String vs. StringBuilder/StringBuffer") beginning with a / character and ending with a /* suffix is used for path mapping.
> 
> * A string beginning with a *. prefix is used as an extension mapping.
> 
> * A string containing only the / character indicates the "default" servlet of the application. In this case the servlet path is the request URI minus the context path and the path info is null.
> 
> * All other strings are used for exact matches only.
> 
> 
> The [pattern](http://www.javaranch.com/patterns/ "article: evil design patterns") /* will force everything through your servlet.
> 
> The pattern / will make your servlet the default servlet for the app, meaning it will pick up as fallback every pattern that doesn't have another exact match.
So that's it!

