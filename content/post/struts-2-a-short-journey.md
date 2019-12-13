---
title: Struts 2 - a short journey
toc: true
id: 851
categories:
  - Struts
date: "2015-04-08T13:59:38+00:00"
---

### Web framework evolves

#### Servlet

Write all your processing logic and presentation logic in a servlet class. Generating HTML code from within Java code is really awesome!

#### JSP with scriptlet style

Rather than writing HTML in Java code, now write Java code in HTML code. This approach is really like PHP and ASP do.

#### JSP with tags style

To avoid writing Java code in HTML, introduce JSP tags as HTML-like facade for accessing underlying Java code. This approach is more designer-friendly.

#### Action-based framework

Split the page processing into processing logic and presentation logic.

Implement MVC Model 2 pattern, or Front Controller pattern.

In this pattern, a servlet is the Front Controller, providing a centralized point for all client page requests.

Then it maps the request URL to a Unit of Work (aka. action).

The action can do:

*   accessing HTTP request, form and session parameters
*   calling business logic services
*   mapping the response into Model - usually a POJO
*   returning a result, which is mapped to a view for rendering

#### Component-based framework

*   event-driven
*   a close tie between user interface component and its class
Such frameworks are: JSF, Wicket, ASP.NET

#### AJAX and Restful style

AJAX requesting to an action-based framework makes the action-based framework behave like the component-based frameworks do.

### Core components of Struts

Struts2 is a pull-MVC (or MVC 2) framework. The "pull" comes from the views ability to pull data from Action, rather than having a separate model object.

![struts-mvc](/media/struts-mvc.png)

From the above figure, we know Action acts as the functionality of model object and processing logic executor.

#### Configuration

When you develop your web application using Struts2, you'll come across 3 configuration files: web.xml(web application deplopment descriptor), struts.properties, struts.xml

![struts-configure-scope](/media/struts-configure-scope.png)

##### web.xml

You need to config Dispatcher Filter -- which is implemented as a servlet filter to dispatching requests.

##### struts.properties

This file is for configuration of framework itself if you want to change the default options of framework. It includes all kinds of constants.

Of course, you can define these constants in struts.xml by using &lt;constant name="" value=""  /&gt;

So struts.properties is optional.

##### struts.xml

This is about the specific configuration of your web application, such as actions, result type, result, interceptors, etc.

Now the following is a practical configuration I used in my project:


```java
&lt;?xml version="1.0" encoding="UTF-8" ?&gt;
&lt;!DOCTYPE struts PUBLIC
    "-//Apache Software Foundation//DTD Struts Configuration 2.0//EN"
    "http://struts.apache.org/dtds/struts-2.0.dtd"&gt;

&lt;struts&gt;

	&lt;!-- what the URL ends by, such as ***.do --&gt;
	&lt;constant name="struts.action.extension" value="do" /&gt;

	&lt;!-- in development mode, more errors are provided --&gt;
	&lt;constant name="struts.devMode" value="false" /&gt;

	&lt;!--default encoding for web app--&gt;
	&lt;constant name="struts.i18n.encoding" value="utf-8" /&gt;

	&lt;constant name="struts.i18n.reload" value="false" /&gt;

	&lt;constant name="struts.custom.i18n.resources" value="properties/web/lang" /&gt;
	&lt;constant name="struts.configuration.xml.reload" value="true" /&gt;

	&lt;constant name="struts.objectFactory" value="org.apache.struts2.spring.StrutsSpringObjectFactory" /&gt;

	&lt;!-- default package --&gt;
	&lt;package name="my-default" namespace="/" extends="json-default"&gt;

		&lt;!-- define new interceptors and interceptor stack --&gt;
		&lt;interceptors&gt; 
			&lt;interceptor name="myInterceptor" class="com.my.web.interceptor.SecurityInterceptor"/&gt;

			&lt;interceptor-stack name="myDefaultStack"&gt;
		        &lt;interceptor-ref name="defaultStack" /&gt;
		        &lt;interceptor-ref name="myInterceptor" /&gt;
		        &lt;interceptor-ref name ="timer" /&gt; 
				&lt;interceptor-ref name ="logger" /&gt; 
		    &lt;/interceptor-stack&gt;	    
		&lt;/interceptors&gt;

		&lt;!-- set default interceptor --&gt;
	    &lt;default-interceptor-ref name="myDefaultStack"/&gt; 

	    &lt;!-- Global results：error, failure, timeout, relogin --&gt;
	    &lt;global-results&gt;
	    	&lt;result name="success" type="json"&gt;
			  &lt;param name="ignoreHierarchy"&gt;false&lt;/param&gt;
			  &lt;param name="excludeNullProperties"&gt;false&lt;/param&gt;
			&lt;/result&gt;
			&lt;result name="exception" type="json"&gt;
			  &lt;param name="ignoreHierarchy"&gt;false&lt;/param&gt;
			  &lt;param name="excludeNullProperties"&gt;false&lt;/param&gt;
			&lt;/result&gt;
			&lt;result name="login" type="json"&gt;
			  &lt;param name="ignoreHierarchy"&gt;false&lt;/param&gt;
			  &lt;param name="excludeNullProperties"&gt;false&lt;/param&gt;
			&lt;/result&gt;
		&lt;/global-results&gt;
    &lt;/package&gt;

	&lt;!-- modules  --&gt;
	&lt;include file="Struts/struts-alert.xml"/&gt;
	&lt;include file="Struts/struts-systemManage.xml"/&gt;
	&lt;include file="Struts/struts-workflow.xml"/&gt;
	&lt;include file="Struts/struts-revaluation.xml"/&gt;
	&lt;include file="Struts/struts-indexquery.xml"/&gt;
&lt;/struts&gt;
```

&nbsp;

&nbsp;


```java
&lt;?xml version="1.0" encoding="UTF-8"?&gt;
&lt;web-app version="2.4" xmlns="http://java.sun.com/xml/ns/j2ee"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://java.sun.com/xml/ns/j2ee 
	http://java.sun.com/xml/ns/j2ee/web-app_2_4.xsd"&gt;
	&lt;display-name&gt;icms-web&lt;/display-name&gt;

	&lt;context-param&gt;
		&lt;param-name&gt;contextConfigLocation&lt;/param-name&gt;
		&lt;param-value&gt;classpath*:/SpringContext/applicationContext-*.xml,classpath*:/SpringContext/*/applicationContext-*.xml,classpath*:/SpringContext/*/*/applicationContext-*.xml,classpath*:/SpringContext/*/*/*/applicationContext-*.xml
		&lt;/param-value&gt;
	&lt;/context-param&gt;

	&lt;listener&gt;
		&lt;listener-class&gt;org.springframework.web.util.Log4jConfigListener&lt;/listener-class&gt;
	&lt;/listener&gt;

	&lt;listener&gt;
		&lt;listener-class&gt;org.springframework.web.context.ContextLoaderListener&lt;/listener-class&gt;
	&lt;/listener&gt;

	&lt;filter&gt;
		&lt;filter-name&gt;struts2&lt;/filter-name&gt;
		&lt;filter-class&gt;org.apache.struts2.dispatcher.FilterDispatcher&lt;/filter-class&gt;
		&lt;init-param&gt;
			&lt;param-name&gt;config&lt;/param-name&gt;
			&lt;param-value&gt;struts-default.xml,struts-plugin.xml,Struts/struts.xml&lt;/param-value&gt;
		&lt;/init-param&gt;
	&lt;/filter&gt;

	&lt;filter-mapping&gt;
		&lt;filter-name&gt;struts2&lt;/filter-name&gt;
		&lt;url-pattern&gt;*.do&lt;/url-pattern&gt;
	&lt;/filter-mapping&gt;

	&lt;welcome-file-list&gt;
		&lt;welcome-file&gt;index.htm&lt;/welcome-file&gt;
	&lt;/welcome-file-list&gt;

	&lt;session-config&gt;
		&lt;session-timeout&gt;30&lt;/session-timeout&gt;
	&lt;/session-config&gt;

&lt;/web-app&gt;
```


####  Actions

If you don't specify the method of an action, its execute() will be invoked by default.

An action method can return a _String_ or _Result _or no return.


```java
&lt;action name="submitDiscardColAppAction"
			class="com.my.ManageDiscardColAction"
			method="submitDiscardColApp"&gt;
&lt;/action&gt;
```

An action instance should be one per request. So when you integrate Struts2 with Spring, the bean scope of action should be **_sterotype_**.

The above example uses the global result setting. You can specify the action-specific configs of result mapping, like


```java
&lt;action name="submitDiscardColAppAction"
			class="com.my.ManageDiscardColAction"
			method="submitDiscardColApp"&gt;
    &lt;result name="success" type="dispatcher"&gt;1.jsp&lt;/result&gt;
&lt;/action&gt;
```

> NOTE: the "dispatcher" result type is default if you don't specify the result type.
Most of time, the logic service object is injected by DI framework (Struts only support setter injection), but there are many non-managed objects needing to acquire in the action, like request, session, servletContext, response, etc. In this case, we can use **Aware interface.

*   SessionAware
*   ServletRequestAware
*   RequestAware
*   ApplicationAware
*   ServletResponseAware
*   ParameterAware
*   PrincipalAware
*   ServletContextAware

#### Result type

Struts provides many result type, but you can implement your custom implementation by implements _**Result** _interface.

*   dispatcher
*   httpheader
*   redirect
*   redirectAction
*   plainText
*   stream
*   velocity

#### Interceptors

Interceptors are conceptually the same as servlet filters or proxy, which provide a way to supply pro-processing and post-processing around the action.

Many features in Struts are implemented using interceptors: exception handling, lifecycle callbacks, validation, servlet-based object injection, application authentication etc.


```java
public interface Interceptor extends Serializable {
    void destroy();
    void init();
    String intercept(ActionInvocation invocation) throws Exception;
}
```


####  Value Stack / OGNL

Value stack and OGNL are all related to accessing data. OGNL is an expression language, aka. Object Graphic Navigational Language.

We use OGNL to access data from value stack in some cases:

*   JSP tags
*   Velocity or Freemarker template

##### What are there in Value stack?

![struts-value-stack](/media/struts-value-stack.png)

### Tags

#### &lt;s:property&gt;

This tag is used to access value stack value.

*   escape html or javascript


```java
&lt;s:property value="propWithHtml" escape="false"/&gt;
```



```java
&lt;script type="text/javascript"&gt;
    var jsFromTag1 = '&lt;s:property value="javascriptExample"
    escapeJavaScript="true"/&gt;';
    document.writeln(jsFromTag1);
&lt;/script&gt;
```


*   default value


```java
&lt;s:property value="nullExample" default="A default value."/&gt;
```


*   access scope object: request, session, application with prefix #


```java
&lt;s:property value="#application.anAppAttribute"/&gt;
&lt;s:property value="#session.aSessionAttribute"/&gt;
&lt;s:property value="#request.aRequestAttribute"/&gt;
&lt;s:property value="#parameters.aRequestParameter"/&gt;
```

parameters is for request parameters

No page object, but instead you use #attr to search all the attributes of application, request, session and page in order.


```java
&lt;s:property value="#attr.anAppAttribute"/&gt;
&lt;s:property value="#attr.aSessionAttribute"/&gt;
&lt;s:property value="#attr.aRequestAttribute"/&gt;
&lt;s:property value="#attr.aPageAttribute"/&gt;
```


#### `&lt;s:debug&gt;`

show all the stack

#### ${anActionProperty}

This will search the value stack first, then fall off to use the JSP EL lookup. This is a little tricky.

#### &lt;s:set&gt;

#### `&lt;s:if&gt;, &lt;s:elseif&gt;`, and`&lt;s:else&gt;`



```java
&lt;s:if test="aBooleanExpression"&gt;
Printed when test is true.
&lt;/s:if&gt;
&lt;s:elseif test="aDifferentBooleanExpression"&gt;
&lt;sif&gt; tagPrinted if previous test was false and this one is true.
&lt;selseif&gt; tag&lt;/s:elseif&gt;
&lt;s:else&gt;
Printed if neither were true.
&lt;selse&gt; tag&lt;/s:else&gt;
```


#### &lt;s:iterator&gt;



```java
&lt;s:iterator value="listOfStrings"&gt;
&lt;li&gt;&lt;s:property/&gt;&lt;/li&gt;
&lt;/s:iterator&gt;
```



```java
&lt;s:iterator value="listOfStrings" var="anItem"&gt;
&lt;li&gt;&lt;s:property value="anItem"/&gt;&lt;/li&gt;
&lt;/s:iterator&gt;
```



```java
&lt;s:iterator value="mapStringString"&gt;
  &lt;li&gt;
     &lt;s:property value="key"/&gt; : &lt;s:property value="value"/&gt;
  &lt;/li&gt;
&lt;/s:iterator&gt;
```



```java
&lt;s:iterator value="mapStringString" var="entry"&gt;
   &lt;li&gt;
     &lt;s:property value="#entry.key"/&gt; ==
     &lt;s:property value="#entry.value"/&gt;
   &lt;/li&gt;
&lt;/s:iterator&gt;
```



```java
&lt;s:iterator value="list1" status="stat"&gt;
&lt;tr&gt;
&lt;td&gt;&lt;s:property value="#stat.index"/&gt;&lt;/td&gt;
&lt;td&gt;&lt;s:property value="#stat.count"/&gt;&lt;/td&gt;
&lt;td&gt;&lt;s:property value="#stat.even"/&gt;&lt;/td&gt;
&lt;td&gt;&lt;s:property value="#stat.odd"/&gt;&lt;/td&gt;
&lt;td&gt;&lt;s:property value="#stat.first"/&gt;&lt;/td&gt;
&lt;td&gt;&lt;s:property value="#stat.last"/&gt;&lt;/td&gt;
&lt;td&gt;&lt;s:property value="#stat.modulus(2)"/&gt;&lt;/td&gt;
&lt;td&gt;&lt;s:property value="#stat.modulus(4)"/&gt;&lt;/td&gt;
&lt;td&gt;&lt;s:property value="#stat.count % 4"/&gt;&lt;/td&gt;
&lt;/tr&gt;
&lt;/s:iterator&gt;
```


#### &lt;s:include&gt;

similar to &lt;jsp:include&gt;

#### &lt;s:action&gt;



```java
&lt;s:action name="includedAction"/&gt;
```


#### &lt;s:url&gt;

dynamically generate the url mapped to the action

### Form Validation and Type conversion

//TODO

&nbsp;
