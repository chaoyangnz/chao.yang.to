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

Of course, you can define these constants in struts.xml by using <constant name="" value=""  />

So struts.properties is optional.

##### struts.xml

This is about the specific configuration of your web application, such as actions, result type, result, interceptors, etc.

Now the following is a practical configuration I used in my project:


```java
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE struts PUBLIC
    "-//Apache Software Foundation//DTD Struts Configuration 2.0//EN"
    "http://struts.apache.org/dtds/struts-2.0.dtd">

<struts>

	<!-- what the URL ends by, such as ***.do -->
	<constant name="struts.action.extension" value="do" />

	<!-- in development mode, more errors are provided -->
	<constant name="struts.devMode" value="false" />

	<!--default encoding for web app-->
	<constant name="struts.i18n.encoding" value="utf-8" />

	<constant name="struts.i18n.reload" value="false" />

	<constant name="struts.custom.i18n.resources" value="properties/web/lang" />
	<constant name="struts.configuration.xml.reload" value="true" />

	<constant name="struts.objectFactory" value="org.apache.struts2.spring.StrutsSpringObjectFactory" />

	<!-- default package -->
	<package name="my-default" namespace="/" extends="json-default">

		<!-- define new interceptors and interceptor stack -->
		<interceptors> 
			<interceptor name="myInterceptor" class="com.my.web.interceptor.SecurityInterceptor"/>

			<interceptor-stack name="myDefaultStack">
		        <interceptor-ref name="defaultStack" />
		        <interceptor-ref name="myInterceptor" />
		        <interceptor-ref name ="timer" /> 
				<interceptor-ref name ="logger" /> 
		    </interceptor-stack>	    
		</interceptors>

		<!-- set default interceptor -->
	    <default-interceptor-ref name="myDefaultStack"/> 

	    <!-- Global results：error, failure, timeout, relogin -->
	    <global-results>
	    	<result name="success" type="json">
			  <param name="ignoreHierarchy">false</param>
			  <param name="excludeNullProperties">false</param>
			</result>
			<result name="exception" type="json">
			  <param name="ignoreHierarchy">false</param>
			  <param name="excludeNullProperties">false</param>
			</result>
			<result name="login" type="json">
			  <param name="ignoreHierarchy">false</param>
			  <param name="excludeNullProperties">false</param>
			</result>
		</global-results>
    </package>

	<!-- modules  -->
	<include file="Struts/struts-alert.xml"/>
	<include file="Struts/struts-systemManage.xml"/>
	<include file="Struts/struts-workflow.xml"/>
	<include file="Struts/struts-revaluation.xml"/>
	<include file="Struts/struts-indexquery.xml"/>
</struts>
```

&nbsp;

&nbsp;


```java
<?xml version="1.0" encoding="UTF-8"?>
<web-app version="2.4" xmlns="http://java.sun.com/xml/ns/j2ee"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://java.sun.com/xml/ns/j2ee 
	http://java.sun.com/xml/ns/j2ee/web-app_2_4.xsd">
	<display-name>icms-web</display-name>

	<context-param>
		<param-name>contextConfigLocation</param-name>
		<param-value>classpath*:/SpringContext/applicationContext-*.xml,classpath*:/SpringContext/*/applicationContext-*.xml,classpath*:/SpringContext/*/*/applicationContext-*.xml,classpath*:/SpringContext/*/*/*/applicationContext-*.xml
		</param-value>
	</context-param>

	<listener>
		<listener-class>org.springframework.web.util.Log4jConfigListener</listener-class>
	</listener>

	<listener>
		<listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
	</listener>

	<filter>
		<filter-name>struts2</filter-name>
		<filter-class>org.apache.struts2.dispatcher.FilterDispatcher</filter-class>
		<init-param>
			<param-name>config</param-name>
			<param-value>struts-default.xml,struts-plugin.xml,Struts/struts.xml</param-value>
		</init-param>
	</filter>

	<filter-mapping>
		<filter-name>struts2</filter-name>
		<url-pattern>*.do</url-pattern>
	</filter-mapping>

	<welcome-file-list>
		<welcome-file>index.htm</welcome-file>
	</welcome-file-list>

	<session-config>
		<session-timeout>30</session-timeout>
	</session-config>

</web-app>
```


####  Actions

If you don't specify the method of an action, its execute() will be invoked by default.

An action method can return a _String_ or _Result _or no return.


```java
<action name="submitDiscardColAppAction"
			class="com.my.ManageDiscardColAction"
			method="submitDiscardColApp">
</action>
```

An action instance should be one per request. So when you integrate Struts2 with Spring, the bean scope of action should be **_sterotype_**.

The above example uses the global result setting. You can specify the action-specific configs of result mapping, like


```java
<action name="submitDiscardColAppAction"
			class="com.my.ManageDiscardColAction"
			method="submitDiscardColApp">
    <result name="success" type="dispatcher">1.jsp</result>
</action>
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

#### <s:property>

This tag is used to access value stack value.

*   escape html or javascript


```java
<s:property value="propWithHtml" escape="false"/>
```



```java
<script type="text/javascript">
    var jsFromTag1 = '<s:property value="javascriptExample"
    escapeJavaScript="true"/>';
    document.writeln(jsFromTag1);
</script>
```


*   default value


```java
<s:property value="nullExample" default="A default value."/>
```


*   access scope object: request, session, application with prefix #


```java
<s:property value="#application.anAppAttribute"/>
<s:property value="#session.aSessionAttribute"/>
<s:property value="#request.aRequestAttribute"/>
<s:property value="#parameters.aRequestParameter"/>
```

parameters is for request parameters

No page object, but instead you use #attr to search all the attributes of application, request, session and page in order.


```java
<s:property value="#attr.anAppAttribute"/>
<s:property value="#attr.aSessionAttribute"/>
<s:property value="#attr.aRequestAttribute"/>
<s:property value="#attr.aPageAttribute"/>
```


#### `<s:debug>`

show all the stack

#### ${anActionProperty}

This will search the value stack first, then fall off to use the JSP EL lookup. This is a little tricky.

#### <s:set>

#### `<s:if>, <s:elseif>`, and`<s:else>`



```java
<s:if test="aBooleanExpression">
Printed when test is true.
</s:if>
<s:elseif test="aDifferentBooleanExpression">
<sif> tagPrinted if previous test was false and this one is true.
<selseif> tag</s:elseif>
<s:else>
Printed if neither were true.
<selse> tag</s:else>
```


#### <s:iterator>



```java
<s:iterator value="listOfStrings">
<li><s:property/></li>
</s:iterator>
```



```java
<s:iterator value="listOfStrings" var="anItem">
<li><s:property value="anItem"/></li>
</s:iterator>
```



```java
<s:iterator value="mapStringString">
  <li>
     <s:property value="key"/> : <s:property value="value"/>
  </li>
</s:iterator>
```



```java
<s:iterator value="mapStringString" var="entry">
   <li>
     <s:property value="#entry.key"/> ==
     <s:property value="#entry.value"/>
   </li>
</s:iterator>
```



```java
<s:iterator value="list1" status="stat">
<tr>
<td><s:property value="#stat.index"/></td>
<td><s:property value="#stat.count"/></td>
<td><s:property value="#stat.even"/></td>
<td><s:property value="#stat.odd"/></td>
<td><s:property value="#stat.first"/></td>
<td><s:property value="#stat.last"/></td>
<td><s:property value="#stat.modulus(2)"/></td>
<td><s:property value="#stat.modulus(4)"/></td>
<td><s:property value="#stat.count % 4"/></td>
</tr>
</s:iterator>
```


#### <s:include>

similar to <jsp:include>

#### <s:action>



```java
<s:action name="includedAction"/>
```


#### <s:url>

dynamically generate the url mapped to the action

### Form Validation and Type conversion

//TODO

&nbsp;
