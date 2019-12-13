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
&lt;?xml version="1.0" encoding="ISO-8859-1"?&gt;
&lt;web-app xmlns="http://java.sun.com/xml/ns/j2ee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd"
         version="2.5"&gt;

    &lt;!-- ========================================================== --&gt;
    &lt;!-- General --&gt;
    &lt;!-- ========================================================== --&gt;

    &lt;!-- Name the application --&gt;
    &lt;display-name&gt;Example App&lt;/display-name&gt;
    &lt;description&gt;An example application which is used to play with some of the features of Tomcat&lt;/description&gt;

    &lt;!-- This app is cluster-ready --&gt;
    &lt;distributable /&gt;

    &lt;!-- Set timeout to 120 minutes --&gt;
    &lt;session-config&gt;
        &lt;session-timeout&gt;120&lt;/session-timeout&gt;
    &lt;/session-config&gt;

    &lt;!-- ========================================================== --&gt;
    &lt;!-- JSP Configuration --&gt;
    &lt;!-- ========================================================== --&gt;

    &lt;!-- Note that you can only have one &lt;jsp-config&gt; element per web.xml --&gt;
    &lt;jsp-config&gt;
        &lt;!-- Taglib declarations are no longer required since JSP 2.0, see Removing taglib from web.xml --&gt;
        &lt;!-- The &lt;taglib&gt; did not need to be a child of &lt;jsp-config&gt; in earlier versions but is required as of Tomcat 7 --&gt;
        &lt;taglib&gt;
            &lt;taglib-uri&gt;mytags&lt;/taglib-uri&gt;
            &lt;taglib-location&gt;/WEB-INF/jsp/mytaglib.tld&lt;/taglib-location&gt;
        &lt;/taglib&gt;
        &lt;jsp-property-group&gt;
            &lt;url-pattern&gt;*.jsp&lt;/url-pattern&gt;
            &lt;include-prelude&gt;/WEB-INF/jspf/prelude1.jspf&lt;/include-prelude&gt;
            &lt;include-coda&gt;/WEB-INF/jspf/coda1.jspf&lt;/include-coda&gt;
        &lt;/jsp-property-group&gt;
    &lt;/jsp-config&gt;

    &lt;!-- ========================================================== --&gt;
    &lt;!-- Context Parameters --&gt;
    &lt;!-- ========================================================== --&gt;

    &lt;context-param&gt;
        &lt;description&gt;Enable debugging for the application&lt;/description&gt;
        &lt;param-name&gt;debug&lt;/param-name&gt;
        &lt;param-value&gt;true&lt;/param-value&gt;
    &lt;/context-param&gt;
    &lt;context-param&gt;
        &lt;description&gt;The email address of the administrator, used to send error reports.&lt;/description&gt;
        &lt;param-name&gt;webmaster&lt;/param-name&gt;
        &lt;param-value&gt;address@somedomain.com&lt;/param-value&gt;
    &lt;/context-param&gt;

    &lt;!-- ========================================================== --&gt;
    &lt;!-- Servlets --&gt;
    &lt;!-- ========================================================== --&gt;

    &lt;!-- Simple Servlet, provide a name, class, description and map to URL /servlet/SimpleServlet --&gt;
    &lt;servlet&gt;
        &lt;servlet-name&gt;Simple&lt;/servlet-name&gt;
        &lt;servlet-class&gt;SimpleServlet&lt;/servlet-class&gt;
        &lt;description&gt;This is a simple Hello World servlet&lt;/description&gt;
    &lt;/servlet&gt;
    &lt;servlet-mapping&gt;
        &lt;servlet-name&gt;Simple&lt;/servlet-name&gt;
        &lt;url-pattern&gt;/servlet/SimpleServlet&lt;/url-pattern&gt;
    &lt;/servlet-mapping&gt;

    &lt;!-- CMS Servlet, responds to *.cms URL's --&gt;
    &lt;servlet&gt;
        &lt;!-- Identification --&gt;
        &lt;servlet-name&gt;cms&lt;/servlet-name&gt;
        &lt;servlet-class&gt;com.metawerx.servlets.ContentManagementSystem&lt;/servlet-class&gt;
        &lt;description&gt;This servlet handles requests for the CMS (it is a controller in an MVC architecture)&lt;/description&gt;

        &lt;!-- This servlet has two parameters --&gt;
        &lt;init-param&gt;
            &lt;param-name&gt;debug&lt;/param-name&gt;
            &lt;param-value&gt;true&lt;/param-value&gt;
        &lt;/init-param&gt;
        &lt;init-param&gt;
            &lt;param-name&gt;detail&lt;/param-name&gt;
            &lt;param-value&gt;2&lt;/param-value&gt;
        &lt;/init-param&gt;

        &lt;!-- Load this servlet when the application starts (call the init() method of the servlet) --&gt;
        &lt;load-on-startup&gt;5&lt;/load-on-startup&gt;
        &lt;!-- &lt;run-at&gt;0:00, 6:00, 12:00, 18:00&lt;/run-at&gt; This tag is only valid for Resin --&gt;
    &lt;/servlet&gt;

    &lt;!-- Map some URLs to the cms servlet (demonstrates *.extension mapping) --&gt;
    &lt;servlet-mapping&gt;
        &lt;!-- For any URL ending in .cms, the cms servlet will be called --&gt;
        &lt;servlet-name&gt;cms&lt;/servlet-name&gt;
        &lt;url-pattern&gt;*.cms&lt;/url-pattern&gt;
    &lt;/servlet-mapping&gt;

    &lt;!-- Rewriter Servlet, responds to /content/* and /admin/RewriterStatistics URL's --&gt;
    &lt;!-- Define a servlet to respond to /content/* URL's --&gt;
    &lt;servlet&gt;
        &lt;servlet-name&gt;rewriter&lt;/servlet-name&gt;
        &lt;servlet-class&gt;com.metawerx.servlets.URLRewriter&lt;/servlet-class&gt;
    &lt;/servlet&gt;

    &lt;!-- Map some URL's to the rewriter servlet (demonstrates /path/* and specific URL mapping) --&gt;
    &lt;servlet-mapping&gt;
        &lt;!-- For any URL starting with /content/, the rewriter servlet will be called --&gt;
        &lt;servlet-name&gt;rewriter&lt;/servlet-name&gt;
        &lt;url-pattern&gt;/content/*&lt;/url-pattern&gt;
    &lt;/servlet-mapping&gt;
    &lt;servlet-mapping&gt;
        &lt;!-- The rewriter servlet can also be called directly as /admin/RewriterStatistics, to return stats --&gt;
        &lt;servlet-name&gt;rewriter&lt;/servlet-name&gt;
        &lt;url-pattern&gt;/admin/RewriterStatistics&lt;/url-pattern&gt;
    &lt;/servlet-mapping&gt;

    &lt;!-- PathJSP Servlet, maps /shop/item/* URL's to a JSP file --&gt;
    &lt;!-- Define a JSP file to respond to /shop/item/* URL's --&gt;
    &lt;servlet&gt;
        &lt;servlet-name&gt;pathjsp&lt;/servlet-name&gt;
        &lt;jsp-file&gt;pathfinder.jsp&lt;/jsp-file&gt;
    &lt;/servlet&gt;

    &lt;!-- Map some URL's to the pathjsp servlet (demonstrates /long/path/* URL mapping) --&gt;
    &lt;servlet-mapping&gt;
        &lt;!-- For any URL starting with /shop/item/, the pathjsp servlet will be called --&gt;
        &lt;servlet-name&gt;pathjsp&lt;/servlet-name&gt;
        &lt;url-pattern&gt;/shop/item/*&lt;/url-pattern&gt;
    &lt;/servlet-mapping&gt;

    &lt;!-- ========================================================== --&gt;
    &lt;!-- Filters --&gt;
    &lt;!-- ========================================================== --&gt;

    &lt;!-- Example filter to set character encoding on each request (from Tomcat servlets-examples context) --&gt;
    &lt;filter&gt;
        &lt;filter-name&gt;Set Character Encoding&lt;/filter-name&gt;
        &lt;filter-class&gt;filters.SetCharacterEncodingFilter&lt;/filter-class&gt;
        &lt;init-param&gt;
            &lt;param-name&gt;encoding&lt;/param-name&gt;
            &lt;param-value&gt;EUC_JP&lt;/param-value&gt;
        &lt;/init-param&gt;
    &lt;/filter&gt;
    &lt;filter-mapping&gt;
        &lt;filter-name&gt;Set Character Encoding&lt;/filter-name&gt;
        &lt;url-pattern&gt;/*&lt;/url-pattern&gt;
    &lt;/filter-mapping&gt;

    &lt;!-- Example filter to dump the HTTP request at the top of each page (from Tomcat servlets-examples context) --&gt;
    &lt;filter&gt;
        &lt;filter-name&gt;Request Dumper Filter&lt;/filter-name&gt;
        &lt;filter-class&gt;filters.RequestDumperFilter&lt;/filter-class&gt;
    &lt;/filter&gt;
    &lt;filter-mapping&gt;
        &lt;filter-name&gt;Request Dumper Filter&lt;/filter-name&gt;
        &lt;url-pattern&gt;/*&lt;/url-pattern&gt;
    &lt;/filter-mapping&gt;

    &lt;!-- ========================================================== --&gt;
    &lt;!-- Listeners --&gt;
    &lt;!-- ========================================================== --&gt;

    &lt;!-- Define example application events listeners --&gt;
    &lt;listener&gt;
        &lt;listener-class&gt;com.metawerx.listener.ContextListener&lt;/listener-class&gt;
    &lt;/listener&gt;
    &lt;listener&gt;
        &lt;listener-class&gt;com.metawerx.listener.SessionListener&lt;/listener-class&gt;
    &lt;/listener&gt;

    &lt;!-- ========================================================== --&gt;
    &lt;!-- Security --&gt;
    &lt;!-- ========================================================== --&gt;

    &lt;!-- Define roles --&gt;
    &lt;security-role&gt;
        &lt;role-name&gt;admin&lt;/role-name&gt;
    &lt;/security-role&gt;
    &lt;security-role&gt;
        &lt;role-name&gt;cms_editors&lt;/role-name&gt;
    &lt;/security-role&gt;

    &lt;!-- Define a constraint to restrict access to /private/* --&gt;
    &lt;security-constraint&gt;

        &lt;display-name&gt;Security constraint for the /private folder&lt;/display-name&gt;

        &lt;web-resource-collection&gt;

            &lt;web-resource-name&gt;Protected Area&lt;/web-resource-name&gt;
            &lt;url-pattern&gt;/private/*&lt;/url-pattern&gt;

            &lt;!-- If you list http methods, only those methods are protected. --&gt;
            &lt;!-- Leave this commented out to protect all access --&gt;
            &lt;!--
            &lt;http-method&gt;DELETE&lt;/http-method&gt;
            &lt;http-method&gt;GET&lt;/http-method&gt;
            &lt;http-method&gt;POST&lt;/http-method&gt;
            &lt;http-method&gt;PUT&lt;/http-method&gt;
            --&gt;

        &lt;/web-resource-collection&gt;

        &lt;auth-constraint&gt;
            &lt;!-- Only only administrator and CMS editors to access this area --&gt;
            &lt;role-name&gt;admin&lt;/role-name&gt;
            &lt;role-name&gt;cms_editors&lt;/role-name&gt;
        &lt;/auth-constraint&gt;

    &lt;/security-constraint&gt;

    &lt;!-- FORM based authentication --&gt;
    &lt;!-- Leave this commented out, we will use BASIC (HTTP) authentication instead --&gt;
    &lt;!--
    &lt;login-config&gt;
            &lt;auth-method&gt;FORM&lt;/auth-method&gt;
            &lt;form-login-config&gt;
                    &lt;form-login-page&gt;/login.jsp&lt;/form-login-page&gt;
                    &lt;form-error-page&gt;/error.jsp&lt;/form-error-page&gt;
            &lt;/form-login-config&gt;
    &lt;/login-config&gt;
    --&gt;
    &lt;!-- This application uses BASIC authentication --&gt;
    &lt;login-config&gt;
        &lt;auth-method&gt;BASIC&lt;/auth-method&gt;
        &lt;realm-name&gt;Editor Login&lt;/realm-name&gt;
    &lt;/login-config&gt;

    &lt;!-- Define a constraint to force SSL on all pages in the application --&gt;
    &lt;security-constraint&gt;

        &lt;web-resource-collection&gt;
            &lt;web-resource-name&gt;Entire Application&lt;/web-resource-name&gt;
            &lt;url-pattern&gt;/*&lt;/url-pattern&gt;
        &lt;/web-resource-collection&gt;

        &lt;user-data-constraint&gt;
            &lt;transport-guarantee&gt;CONFIDENTIAL&lt;/transport-guarantee&gt;
        &lt;/user-data-constraint&gt;

    &lt;/security-constraint&gt;

    &lt;!-- ========================================================== --&gt;
    &lt;!-- Error Handler --&gt;
    &lt;!-- ========================================================== --&gt;

    &lt;!-- Define an error handler for 404 pages --&gt;
    &lt;error-page&gt;
        &lt;error-code&gt;404&lt;/error-code&gt;
        &lt;location&gt;/error404.jsp&lt;/location&gt;
    &lt;/error-page&gt;

    &lt;!-- Define an error handler for java.lang.Throwable --&gt;
    &lt;error-page&gt;
        &lt;exception-type&gt;java.lang.Throwable&lt;/exception-type&gt;
        &lt;location&gt;/errorThrowable.jsp&lt;/location&gt;
    &lt;/error-page&gt;

    &lt;!-- ========================================================== --&gt;
    &lt;!-- Extra MIME types --&gt;
    &lt;!-- ========================================================== --&gt;

    &lt;!-- Set XML mime-mapping so spreadsheets open properly instead of being sent as an octet/stream --&gt;
    &lt;mime-mapping&gt;
        &lt;extension&gt;xls&lt;/extension&gt;
        &lt;mime-type&gt;application/vnd.ms-excel&lt;/mime-type&gt;
    &lt;/mime-mapping&gt;

    &lt;!-- ========================================================== --&gt;
    &lt;!-- Locale --&gt;
    &lt;!-- ========================================================== --&gt;

    &lt;!-- Set Locale Encoding --&gt;
    &lt;locale-encoding-mapping-list&gt;
        &lt;locale-encoding-mapping&gt;
            &lt;locale&gt;ja&lt;/locale&gt;
            &lt;encoding&gt;Shift_JIS&lt;/encoding&gt;
        &lt;/locale-encoding-mapping&gt;
    &lt;/locale-encoding-mapping-list&gt;

    &lt;!-- ========================================================== --&gt;
    &lt;!-- Welcome Files --&gt;
    &lt;!-- ========================================================== --&gt;

    &lt;!-- Define, in order of preference, which file to show when no filename is defined in the path --&gt;
    &lt;!-- eg: when user goes to http://yoursite.com/ or http://yoursite.com/somefolder --&gt;
    &lt;!-- Defaults are provided in the server-wide web.xml file, such as index.jsp, index.htm --&gt;
    &lt;!-- Note: using this tag overrides the defaults, so don't forget to add them here --&gt;
    &lt;welcome-file-list&gt;
        &lt;!-- Use index.swf if present, or splash.jsp, otherwise just look for the normal defaults --&gt;
        &lt;welcome-file&gt;index.swf&lt;/welcome-file&gt;
        &lt;welcome-file&gt;splash.jsp&lt;/welcome-file&gt;
        &lt;welcome-file&gt;index.html&lt;/welcome-file&gt;
        &lt;welcome-file&gt;index.htm&lt;/welcome-file&gt;
        &lt;welcome-file&gt;index.jsp&lt;/welcome-file&gt;
    &lt;/welcome-file-list&gt;

    &lt;!-- ========================================================== --&gt;
    &lt;!-- JNDI Environment Variables --&gt;
    &lt;!-- ========================================================== --&gt;

    &lt;env-entry&gt;
        &lt;env-entry-name&gt;webmasterName&lt;/env-entry-name&gt;
        &lt;env-entry-value&gt;Ms. W. Master&lt;/env-entry-value&gt;
        &lt;env-entry-type&gt;java.lang.String&lt;/env-entry-type&gt;
    &lt;/env-entry&gt;
    &lt;env-entry&gt;
        &lt;env-entry-name&gt;cms/defaultUserSettings/recordsPerPage&lt;/env-entry-name&gt;
        &lt;env-entry-value&gt;30&lt;/env-entry-value&gt;
        &lt;env-entry-type&gt;java.lang.Integer&lt;/env-entry-type&gt;
    &lt;/env-entry&gt;

    &lt;!-- ===========Programmer's virtual name for resource=====================--&gt;
    &lt;resource-ref&gt;
        &lt;res-ref-name&gt;jdbc/iass_ds&lt;/res-ref-name&gt;
        &lt;res-type&gt;javax.sql.DataSource&lt;/res-type&gt;
        &lt;res-auth&gt;Container&lt;/res-auth&gt;
        &lt;res-sharing-scope&gt;Shareable&lt;/res-sharing-scope&gt;
    &lt;/resource-ref&gt;

    &lt;resource-env-ref&gt;
        &lt;resource-env-ref-name&gt;jms/StockQueue&lt;/resource-env-ref-name&gt;
        &lt;resource-env-ref-type&gt;javax.jms.Queue&lt;/resource-env-ref-type&gt;
    &lt;/resource-env-ref&gt;

&lt;/web-app&gt;
```

some confusing tags: &lt;resource-ref&gt;   &lt;resource-env-ref&gt;

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
		out.println("In dispatcherServlet &lt;BR&gt;");

		RequestDispatcher rd = request.getRequestDispatcher("Hello.jsp");
		rd.include(request, response);
	}

}
```

&nbsp;


```java
&lt;%@ page language="java" contentType="text/html; charset=ISO-8859-1"
    pageEncoding="ISO-8859-1"%&gt;
&lt;!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd"&gt;
&lt;html&gt;
&lt;head&gt;
&lt;meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1"&gt;
&lt;title&gt;Insert title here&lt;/title&gt;
&lt;/head&gt;
&lt;body&gt;

Hello page. 

&lt;/body&gt;
&lt;/html&gt;
```

Now the output is:


```java
In dispatcherServlet
Hello page
```

Here we are not hard to know its accurate meaning - including and merging the response content of another servlet, JSP or static resources.

![RequestDispatcherInclude](/media/RequestDispatcherInclude.jpg)

### JNDI declaration tags

We usually use &lt;resource-ref&gt; for DataSource declaration, but there're also other JNDI object declarations which can be used in servlet application.

#### &lt;env-entry&gt;, &lt;resource-ref&gt; and &lt;resource-env-ref&gt;

<div id="post-body-9135694041141534953" class="post-body entry-content">

*   **env-entry** - Environment entry, a single-value parameter that can be used to configure how the application will operate. An example [here](http://www.cs-repository.info/2014/04/bind-and-lookup-wrapper-objects-using.html)
*   **resource-ref** - Resource reference, which is typically to an object factory for resources such as a JDBC DataSource, a JavaMail Session, or custom object factories configured into Tomcat. An example [here](http://www.cs-repository.info/2014/04/using-jndi-lookup-for-datasource-objects.html)
*   **resource-env-ref** - Resource environment reference, a new variation of resource-ref added in Servlet 2.4 that is simpler to configure for resources that do not require authentication information. An example[here ](http://www.cs-repository.info/2014/04/bind-and-lookup-beans-using-resource_1.html)and [here](http://www.cs-repository.info/2014/04/bind-and-lookup-beans-using-resource.html).
</div>

#### Environment Entries

For parameter values that may need to change during deployment, it's better to use environment entries instead, as indicated by the `&lt;env-entry&gt;` tag.


```java
&lt;env-entry&gt;
    &lt;description&gt;Send pincode by mail&lt;/description&gt;
    &lt;env-entry-name&gt;mailPincode&lt;/env-entry-name&gt;
    &lt;env-entry-value&gt;false&lt;/env-entry-value&gt;
    &lt;env-entry-type&gt;java.lang.Boolean&lt;/env-entry-type&gt;
&lt;/env-entry&gt;
```

This is a good way for environment-specific application variables. The type may be a `String`, `Byte`, `Short`, `Integer`, `Long`, `Boolean`, `Double`, or `Float` (all with their full `java.lang` qualification).

Java code can retrieve the `&lt;env-entry&gt;` values using JNDI:


```java
Context initCtx = new InitialContext( );
Boolean mailPincode = (Boolean) initCtx.lookup("java:comp/env/mailPincode");
```

Now you could change the variable value in deployment time:

![env-entry](/media/env-entry.png)

#### References to External Resource Manager Connection Factories

When the environment entry is a resource factory, there's a `&lt;resource-ref&gt;` tag to use. A factory is an object that creates other objects on demand. A resource factory creates resource objects, such as database connections or message queues.


```java
&lt;!-- JDBC DataSources (java:comp/env/jdbc) --&gt;
&lt;resource-ref&gt;
    &lt;description&gt;The default DS&lt;/description&gt;
    &lt;res-ref-name&gt;jdbc/DefaultDS&lt;/res-ref-name&gt;
    &lt;res-type&gt;javax.sql.DataSource&lt;/res-type&gt;
    &lt;res-auth&gt;Container&lt;/res-auth&gt;
&lt;/resource-ref&gt;
&lt;!-- JavaMail Connection Factories (java:comp/env/mail) --&gt;
&lt;resource-ref&gt;
    &lt;description&gt;Default Mail&lt;/description&gt;
    &lt;res-ref-name&gt;mail/DefaultMail&lt;/res-ref-name&gt;
    &lt;res-type&gt;javax.mail.Session&lt;/res-type&gt;
    &lt;res-auth&gt;Container&lt;/res-auth&gt;
&lt;/resource-ref&gt;
&lt;!-- JMS Connection Factories (java:comp/env/jms) --&gt;
&lt;resource-ref&gt;
    &lt;description&gt;Default QueueFactory&lt;/description&gt;
    &lt;res-ref-name&gt;jms/QueueFactory&lt;/res-ref-name&gt;
    &lt;res-type&gt;javax.jms.QueueConnectionFactory&lt;/res-type&gt;
    &lt;res-auth&gt;Container&lt;/res-auth&gt;
&lt;/resource-ref&gt;
```

It can have two values: `CONTAINER` or `SERVLET`. If `CONTAINER` is specified, the servlet container (the J2EE server) handles authentication before binding the factory to JNDI, using credentials provided by the deployer. If `SERVLET` is specified, the servlet must handle authentication duties programmatically.

It's recommended but not required to place the resource factories under a subcontext that describes the resource type:

*   `jdbc/` for a JDBC `javax.sql.DataSource` factory
*   `jms/` for a JMS `javax.jms.QueueConnectionFactory` or `javax.jms.TopicConnectionFactory`
*   `mail/` for a JavaMail `javax.mail.Session` factory
*   `url/` for a `java.net.URL` factory

#### References to Administered Objects



```java
&lt;resource-env-ref&gt;
    &lt;description&gt;This is a reference to a JMS queue&lt;/description&gt;
    &lt;resource-env-ref-name&gt;jms/myqueue&lt;/resource-env-ref-name&gt;
    &lt;resource-env-ref-type&gt;javax.jms.Queue&lt;/resource-env-ref-type&gt;
&lt;/resource-env-ref&gt;
```


#### References to EJB Components



```java
&lt;ejb-ref&gt;
  &lt;description&gt;Cruise ship cabin&lt;/description&gt;
  &lt;ejb-ref-name&gt;ejb/CabinHome&lt;/ejb-ref-name&gt;
  &lt;ejb-ref-type&gt;Entity&lt;/ejb-ref-type&gt;
  &lt;home&gt;com.titan.cabin.CabinHome&lt;/home&gt;
  &lt;remote&gt;com.titan.cabin.Cabin&lt;/remote&gt;
&lt;/ejb-ref&gt;
```

&nbsp;
