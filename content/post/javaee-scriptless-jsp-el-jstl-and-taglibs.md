---
title: 'scriptless JSP - EL, JSTL and taglibs'
toc: true
id: 295
categories:
  - Servlet / JSP
date: "2015-02-02T06:11:56+00:00"
---

#### EL (Expression Language) for non-Java programmer or designers

email: &lt;%= application.getAttribute("email") %&gt;

email: ${applicationScope.email}

##### Implicit objects in EL:

*   `pageContext`: The context for the JSP page.
NOTE: you cannot directly get the objects: application, session, request, response, page, but you can get them through pageContext.

In addition, several implicit objects are available that allow easy access to the following objects:

*   `param`: Maps a request parameter name to a single value
*   `paramValues`: Maps a request parameter name to an array of values
*   `header`: Maps a request header name to a single value
*   `headerValues`: Maps a request header name to an array of values
*   `cookie`: Maps a cookie name to a single cookie
*   `initParam`: Maps a context initialization parameter name to a single value
Finally, there are objects that allow access to the various scoped variables.

*   `pageScope`: Maps page-scoped variable names to their values
*   `requestScope`: Maps request-scoped variable names to their values
*   `sessionScope`: Maps session-scoped variable names to their values
*   `applicationScope`: Maps application-scoped variable names to their values
