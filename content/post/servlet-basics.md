---
title: servlet basics
toc: true
id: 768
categories:
  - Servlet / JSP
date: "2015-03-30T15:57:35+00:00"
---

### Deployment Descriptor: web.xml

The following is a complete Deployment Descriptor.


```java
<?xml version="1.0" encoding="ISO-8859-1"?>
<web-app xmlns="http://java.sun.com/xml/ns/j2ee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd"
         version="2.5">

    <!-- ========================================================== -->
    <!-- General -->
    <!-- ========================================================== -->

    <!-- Name the application -->
    <display-name>Example App</display-name>
    <description>An example application which is used to play with some of the features of Tomcat</description>

    <!-- This app is cluster-ready -->
    <distributable />

    <!-- Set timeout to 120 minutes -->
    <session-config>
        <session-timeout>120</session-timeout>
    </session-config>

    <!-- ========================================================== -->
    <!-- JSP Configuration -->
    <!-- ========================================================== -->

    <!-- Note that you can only have one <jsp-config> element per web.xml -->
    <jsp-config>
        <!-- Taglib declarations are no longer required since JSP 2.0, see Removing taglib from web.xml -->
        <!-- The <taglib> did not need to be a child of <jsp-config> in earlier versions but is required as of Tomcat 7 -->
        <taglib>
            <taglib-uri>mytags</taglib-uri>
            <taglib-location>/WEB-INF/jsp/mytaglib.tld</taglib-location>
        </taglib>
        <jsp-property-group>
            <url-pattern>*.jsp</url-pattern>
            <include-prelude>/WEB-INF/jspf/prelude1.jspf</include-prelude>
            <include-coda>/WEB-INF/jspf/coda1.jspf</include-coda>
        </jsp-property-group>
    </jsp-config>

    <!-- ========================================================== -->
    <!-- Context Parameters -->
    <!-- ========================================================== -->

    <context-param>
        <description>Enable debugging for the application</description>
        <param-name>debug</param-name>
        <param-value>true</param-value>
    </context-param>
    <context-param>
        <description>The email address of the administrator, used to send error reports.</description>
        <param-name>webmaster</param-name>
        <param-value>address@somedomain.com</param-value>
    </context-param>

    <!-- ========================================================== -->
    <!-- Servlets -->
    <!-- ========================================================== -->

    <!-- Simple Servlet, provide a name, class, description and map to URL /servlet/SimpleServlet -->
    <servlet>
        <servlet-name>Simple</servlet-name>
        <servlet-class>SimpleServlet</servlet-class>
        <description>This is a simple Hello World servlet</description>
    </servlet>
    <servlet-mapping>
        <servlet-name>Simple</servlet-name>
        <url-pattern>/servlet/SimpleServlet</url-pattern>
    </servlet-mapping>

    <!-- CMS Servlet, responds to *.cms URL's -->
    <servlet>
        <!-- Identification -->
        <servlet-name>cms</servlet-name>
        <servlet-class>com.metawerx.servlets.ContentManagementSystem</servlet-class>
        <description>This servlet handles requests for the CMS (it is a controller in an MVC architecture)</description>

        <!-- This servlet has two parameters -->
        <init-param>
            <param-name>debug</param-name>
            <param-value>true</param-value>
        </init-param>
        <init-param>
            <param-name>detail</param-name>
            <param-value>2</param-value>
        </init-param>

        <!-- Load this servlet when the application starts (call the init() method of the servlet) -->
        <load-on-startup>5</load-on-startup>
        <!-- <run-at>0:00, 6:00, 12:00, 18:00</run-at> This tag is only valid for Resin -->
    </servlet>

    <!-- Map some URLs to the cms servlet (demonstrates *.extension mapping) -->
    <servlet-mapping>
        <!-- For any URL ending in .cms, the cms servlet will be called -->
        <servlet-name>cms</servlet-name>
        <url-pattern>*.cms</url-pattern>
    </servlet-mapping>

    <!-- Rewriter Servlet, responds to /content/* and /admin/RewriterStatistics URL's -->
    <!-- Define a servlet to respond to /content/* URL's -->
    <servlet>
        <servlet-name>rewriter</servlet-name>
        <servlet-class>com.metawerx.servlets.URLRewriter</servlet-class>
    </servlet>

    <!-- Map some URL's to the rewriter servlet (demonstrates /path/* and specific URL mapping) -->
    <servlet-mapping>
        <!-- For any URL starting with /content/, the rewriter servlet will be called -->
        <servlet-name>rewriter</servlet-name>
        <url-pattern>/content/*</url-pattern>
    </servlet-mapping>
    <servlet-mapping>
        <!-- The rewriter servlet can also be called directly as /admin/RewriterStatistics, to return stats -->
        <servlet-name>rewriter</servlet-name>
        <url-pattern>/admin/RewriterStatistics</url-pattern>
    </servlet-mapping>

    <!-- PathJSP Servlet, maps /shop/item/* URL's to a JSP file -->
    <!-- Define a JSP file to respond to /shop/item/* URL's -->
    <servlet>
        <servlet-name>pathjsp</servlet-name>
        <jsp-file>pathfinder.jsp</jsp-file>
    </servlet>

    <!-- Map some URL's to the pathjsp servlet (demonstrates /long/path/* URL mapping) -->
    <servlet-mapping>
        <!-- For any URL starting with /shop/item/, the pathjsp servlet will be called -->
        <servlet-name>pathjsp</servlet-name>
        <url-pattern>/shop/item/*</url-pattern>
    </servlet-mapping>

    <!-- ========================================================== -->
    <!-- Filters -->
    <!-- ========================================================== -->

    <!-- Example filter to set character encoding on each request (from Tomcat servlets-examples context) -->
    <filter>
        <filter-name>Set Character Encoding</filter-name>
        <filter-class>filters.SetCharacterEncodingFilter</filter-class>
        <init-param>
            <param-name>encoding</param-name>
            <param-value>EUC_JP</param-value>
        </init-param>
    </filter>
    <filter-mapping>
        <filter-name>Set Character Encoding</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>

    <!-- Example filter to dump the HTTP request at the top of each page (from Tomcat servlets-examples context) -->
    <filter>
        <filter-name>Request Dumper Filter</filter-name>
        <filter-class>filters.RequestDumperFilter</filter-class>
    </filter>
    <filter-mapping>
        <filter-name>Request Dumper Filter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>

    <!-- ========================================================== -->
    <!-- Listeners -->
    <!-- ========================================================== -->

    <!-- Define example application events listeners -->
    <listener>
        <listener-class>com.metawerx.listener.ContextListener</listener-class>
    </listener>
    <listener>
        <listener-class>com.metawerx.listener.SessionListener</listener-class>
    </listener>

    <!-- ========================================================== -->
    <!-- Security -->
    <!-- ========================================================== -->

    <!-- Define roles -->
    <security-role>
        <role-name>admin</role-name>
    </security-role>
    <security-role>
        <role-name>cms_editors</role-name>
    </security-role>

    <!-- Define a constraint to restrict access to /private/* -->
    <security-constraint>

        <display-name>Security constraint for the /private folder</display-name>

        <web-resource-collection>

            <web-resource-name>Protected Area</web-resource-name>
            <url-pattern>/private/*</url-pattern>

            <!-- If you list http methods, only those methods are protected. -->
            <!-- Leave this commented out to protect all access -->
            <!--
            <http-method>DELETE</http-method>
            <http-method>GET</http-method>
            <http-method>POST</http-method>
            <http-method>PUT</http-method>
            -->

        </web-resource-collection>

        <auth-constraint>
            <!-- Only only administrator and CMS editors to access this area -->
            <role-name>admin</role-name>
            <role-name>cms_editors</role-name>
        </auth-constraint>

    </security-constraint>

    <!-- FORM based authentication -->
    <!-- Leave this commented out, we will use BASIC (HTTP) authentication instead -->
    <!--
    <login-config>
            <auth-method>FORM</auth-method>
            <form-login-config>
                    <form-login-page>/login.jsp</form-login-page>
                    <form-error-page>/error.jsp</form-error-page>
            </form-login-config>
    </login-config>
    -->
    <!-- This application uses BASIC authentication -->
    <login-config>
        <auth-method>BASIC</auth-method>
        <realm-name>Editor Login</realm-name>
    </login-config>

    <!-- Define a constraint to force SSL on all pages in the application -->
    <security-constraint>

        <web-resource-collection>
            <web-resource-name>Entire Application</web-resource-name>
            <url-pattern>/*</url-pattern>
        </web-resource-collection>

        <user-data-constraint>
            <transport-guarantee>CONFIDENTIAL</transport-guarantee>
        </user-data-constraint>

    </security-constraint>

    <!-- ========================================================== -->
    <!-- Error Handler -->
    <!-- ========================================================== -->

    <!-- Define an error handler for 404 pages -->
    <error-page>
        <error-code>404</error-code>
        <location>/error404.jsp</location>
    </error-page>

    <!-- Define an error handler for java.lang.Throwable -->
    <error-page>
        <exception-type>java.lang.Throwable</exception-type>
        <location>/errorThrowable.jsp</location>
    </error-page>

    <!-- ========================================================== -->
    <!-- Extra MIME types -->
    <!-- ========================================================== -->

    <!-- Set XML mime-mapping so spreadsheets open properly instead of being sent as an octet/stream -->
    <mime-mapping>
        <extension>xls</extension>
        <mime-type>application/vnd.ms-excel</mime-type>
    </mime-mapping>

    <!-- ========================================================== -->
    <!-- Locale -->
    <!-- ========================================================== -->

    <!-- Set Locale Encoding -->
    <locale-encoding-mapping-list>
        <locale-encoding-mapping>
            <locale>ja</locale>
            <encoding>Shift_JIS</encoding>
        </locale-encoding-mapping>
    </locale-encoding-mapping-list>

    <!-- ========================================================== -->
    <!-- Welcome Files -->
    <!-- ========================================================== -->

    <!-- Define, in order of preference, which file to show when no filename is defined in the path -->
    <!-- eg: when user goes to http://yoursite.com/ or http://yoursite.com/somefolder -->
    <!-- Defaults are provided in the server-wide web.xml file, such as index.jsp, index.htm -->
    <!-- Note: using this tag overrides the defaults, so don't forget to add them here -->
    <welcome-file-list>
        <!-- Use index.swf if present, or splash.jsp, otherwise just look for the normal defaults -->
        <welcome-file>index.swf</welcome-file>
        <welcome-file>splash.jsp</welcome-file>
        <welcome-file>index.html</welcome-file>
        <welcome-file>index.htm</welcome-file>
        <welcome-file>index.jsp</welcome-file>
    </welcome-file-list>

    <!-- ========================================================== -->
    <!-- JNDI Environment Variables -->
    <!-- ========================================================== -->

    <env-entry>
        <env-entry-name>webmasterName</env-entry-name>
        <env-entry-value>Ms. W. Master</env-entry-value>
        <env-entry-type>java.lang.String</env-entry-type>
    </env-entry>
    <env-entry>
        <env-entry-name>cms/defaultUserSettings/recordsPerPage</env-entry-name>
        <env-entry-value>30</env-entry-value>
        <env-entry-type>java.lang.Integer</env-entry-type>
    </env-entry>

    <!-- ===========Programmer's virtual name for resource=====================-->
    <resource-ref>
        <res-ref-name>jdbc/iass_ds</res-ref-name>
        <res-type>javax.sql.DataSource</res-type>
        <res-auth>Container</res-auth>
        <res-sharing-scope>Shareable</res-sharing-scope>
    </resource-ref>

    <resource-env-ref>
        <resource-env-ref-name>jms/StockQueue</resource-env-ref-name>
        <resource-env-ref-type>javax.jms.Queue</resource-env-ref-type>
    </resource-env-ref>

</web-app>
```

some confusing tags: <resource-ref>   <resource-env-ref>

### ServletContext, ServletConfig

Basically, ServletContext defines a set of methods that a servlet uses to communicate with its servlet container.

There is one context per "web application" per Java Virtual Machine. (A web application" is a collection of servlets and content installed under a specific subset of the server's URL namespace such as /catalog and possibly installed via a .war file.)

ServletConfig is used to pass parameters to a servlet during initialization.

its methods include:


```java
getServletName();
getServletContext();
getInitParameter(String name);
getInitParameterNames();
```

GenericServlet has implemented ServletConfig interface.

### thread safe consideration

![servlet-lifecycle](/media/servlet-lifecycle.jpg)

Generally, a typical servlet lifecyle creates a single instance of a servlet class, but pools multiple threads to handle the service() method.

So the shared resources like instance variables, helpers must be coded in thread-safe manners.

### forward vs. redirect

RequestDispatcher.forward(...), Response.sendRedirect(..)

![forward_redirect](/media/forward_redirect.png)

As per what is RequestDespatcher include(), now look at the following test:


```java
public class DispatcherServlet extends HttpServlet {
	private static final long serialVersionUID = 1L;

	protected void doGet(HttpServletRequest request,
			HttpServletResponse response) throws ServletException, IOException {

		PrintWriter out = response.getWriter();
		out.println("In dispatcherServlet <BR>");

		RequestDispatcher rd = request.getRequestDispatcher("Hello.jsp");
		rd.include(request, response);
	}

}
```

&nbsp;


```java
<%@ page language="java" contentType="text/html; charset=ISO-8859-1"
    pageEncoding="ISO-8859-1"%>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
<title>Insert title here</title>
</head>
<body>

Hello page. 

</body>
</html>
```

Now the output is:


```java
In dispatcherServlet
Hello page
```

Here we are not hard to know its accurate meaning - including and merging the response content of another servlet, JSP or static resources.

![RequestDispatcherInclude](/media/RequestDispatcherInclude.jpg)

### JNDI declaration tags

We usually use <resource-ref> for DataSource declaration, but there're also other JNDI object declarations which can be used in servlet application.

#### <env-entry>, <resource-ref> and <resource-env-ref>

<div id="post-body-9135694041141534953" class="post-body entry-content">

*   **env-entry** - Environment entry, a single-value parameter that can be used to configure how the application will operate. An example [here](http://www.cs-repository.info/2014/04/bind-and-lookup-wrapper-objects-using.html)
*   **resource-ref** - Resource reference, which is typically to an object factory for resources such as a JDBC DataSource, a JavaMail Session, or custom object factories configured into Tomcat. An example [here](http://www.cs-repository.info/2014/04/using-jndi-lookup-for-datasource-objects.html)
*   **resource-env-ref** - Resource environment reference, a new variation of resource-ref added in Servlet 2.4 that is simpler to configure for resources that do not require authentication information. An example[here ](http://www.cs-repository.info/2014/04/bind-and-lookup-beans-using-resource_1.html)and [here](http://www.cs-repository.info/2014/04/bind-and-lookup-beans-using-resource.html).
</div>

#### Environment Entries

For parameter values that may need to change during deployment, it's better to use environment entries instead, as indicated by the `<env-entry>` tag.


```java
<env-entry>
    <description>Send pincode by mail</description>
    <env-entry-name>mailPincode</env-entry-name>
    <env-entry-value>false</env-entry-value>
    <env-entry-type>java.lang.Boolean</env-entry-type>
</env-entry>
```

This is a good way for environment-specific application variables. The type may be a `String`, `Byte`, `Short`, `Integer`, `Long`, `Boolean`, `Double`, or `Float` (all with their full `java.lang` qualification).

Java code can retrieve the `<env-entry>` values using JNDI:


```java
Context initCtx = new InitialContext( );
Boolean mailPincode = (Boolean) initCtx.lookup("java:comp/env/mailPincode");
```

Now you could change the variable value in deployment time:

![env-entry](/media/env-entry.png)

#### References to External Resource Manager Connection Factories

When the environment entry is a resource factory, there's a `<resource-ref>` tag to use. A factory is an object that creates other objects on demand. A resource factory creates resource objects, such as database connections or message queues.


```java
<!-- JDBC DataSources (java:comp/env/jdbc) -->
<resource-ref>
    <description>The default DS</description>
    <res-ref-name>jdbc/DefaultDS</res-ref-name>
    <res-type>javax.sql.DataSource</res-type>
    <res-auth>Container</res-auth>
</resource-ref>
<!-- JavaMail Connection Factories (java:comp/env/mail) -->
<resource-ref>
    <description>Default Mail</description>
    <res-ref-name>mail/DefaultMail</res-ref-name>
    <res-type>javax.mail.Session</res-type>
    <res-auth>Container</res-auth>
</resource-ref>
<!-- JMS Connection Factories (java:comp/env/jms) -->
<resource-ref>
    <description>Default QueueFactory</description>
    <res-ref-name>jms/QueueFactory</res-ref-name>
    <res-type>javax.jms.QueueConnectionFactory</res-type>
    <res-auth>Container</res-auth>
</resource-ref>
```

It can have two values: `CONTAINER` or `SERVLET`. If `CONTAINER` is specified, the servlet container (the J2EE server) handles authentication before binding the factory to JNDI, using credentials provided by the deployer. If `SERVLET` is specified, the servlet must handle authentication duties programmatically.

It's recommended but not required to place the resource factories under a subcontext that describes the resource type:

*   `jdbc/` for a JDBC `javax.sql.DataSource` factory
*   `jms/` for a JMS `javax.jms.QueueConnectionFactory` or `javax.jms.TopicConnectionFactory`
*   `mail/` for a JavaMail `javax.mail.Session` factory
*   `url/` for a `java.net.URL` factory

#### References to Administered Objects



```java
<resource-env-ref>
    <description>This is a reference to a JMS queue</description>
    <resource-env-ref-name>jms/myqueue</resource-env-ref-name>
    <resource-env-ref-type>javax.jms.Queue</resource-env-ref-type>
</resource-env-ref>
```


#### References to EJB Components



```java
<ejb-ref>
  <description>Cruise ship cabin</description>
  <ejb-ref-name>ejb/CabinHome</ejb-ref-name>
  <ejb-ref-type>Entity</ejb-ref-type>
  <home>com.titan.cabin.CabinHome</home>
  <remote>com.titan.cabin.Cabin</remote>
</ejb-ref>
```

&nbsp;
