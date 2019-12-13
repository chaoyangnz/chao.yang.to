---
title: JSP basics
toc: true
id: 276
categories:
  - Servlet / JSP
date: "2015-02-02T04:03:45+00:00"
---

### How JSP work

![jsp](/media/jsp.png)

### Syntax

#### scriptlet



```java
&lt;% int count=0; out.println("This is a scriptlet"); %&gt;
```

This block is placed in the body of method _jspService(request, response)

#### derective



```java
&lt;%@ page import="java.util.*,java.xml.*" %&gt;
```



```java
&lt;%@ include file="xxx" %&gt;
```



```java
&lt;%@ taglib tagdir="/WEB-INF/tags/xxx" prefix="xxx" %&gt;
```


#### expression



```java
&lt;%= "This is a expression argument" %&gt;
```


#### declaration



```java
&lt;%! int count=0 %&gt;
```



```java
&lt;%! int doubleCount() { count = count*2; return count; } %&gt;
```

This is equivlant with the member field and member method of the generated servlet.

#### comment



```java
&lt;!-- HTML Comments --&gt;
&lt;%-- JSP Comments --%&gt;
```

The first is called <span style="text-decoration: underline;">_output comments_</span>, while the second is called _<span style="text-decoration: underline;">hidden comments</span>_.

### implicit Objects in scriptlets and expressions

<table style="height: 272px;" border="1" width="349">
<tbody>
<tr>
<td>Type</td>
<td>implicit variable</td>
</tr>
<tr>
<td>JspWriter</td>
<td>out</td>
</tr>
<tr>
<td>HttpServletRequest</td>
<td>request</td>
</tr>
<tr>
<td>HttpServletResponse</td>
<td>response</td>
</tr>
<tr>
<td>HttpSession</td>
<td>session</td>
</tr>
<tr>
<td>ServletContext</td>
<td>application</td>
</tr>
<tr>
<td>ServletConfig</td>
<td>config</td>
</tr>
<tr>
<td>Throwable</td>
<td>exception</td>
</tr>
<tr>
<td>PageContext</td>
<td>pageContext</td>
</tr>
<tr>
<td>HttpJspPage</td>
<td>page</td>
</tr>
</tbody>
</table>

### about the generated servlet

it's a subclass of HttpJspPage, which inherited JspPage

In JSP, you can get its reference by a implicit object named _**page.**_

![jsppage](/media/jsppage.png)

The methods of _**JspPage**_:


```java
jspInit()
jspDestroy()
```

The methods of _**HttpJspPage**_


```java
_jspService()
```

You can override the jspInit() and jspDestroy() methods, but not for _jspService() method.

### about the PageContext

PageContext is a subclass of JspContext.
![jspContext](/media/jspContext.png)![pageContext](/media/pageContext.png)

### about the configuration

1\. config jsp for global in deployment descriptor


```java
&lt;web-app ...&gt;

...

&lt;jsp-config&gt;

&lt;jsp-property-group&gt;

&lt;url-pattern&gt;*.jsp&lt;/url-pattern&gt;

&lt;el-ignored&gt;true&lt;/el-ignored&gt;

&lt;scripting-invalid&gt;true&lt;/scripting-invalid&gt;

&lt;/jsp-property-group&gt;

...

&lt;/web-app&gt;

```

2\. config jsp in the page directive


```java
&lt;%@ page isELIgnored="true" isScriptingEnabled="false" .... %&gt;
```

&nbsp;
