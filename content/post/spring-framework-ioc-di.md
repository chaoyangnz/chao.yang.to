---
title: Spring framework - IoC / DI
toc: true
id: 839
categories:
  - Spring
date: "2015-04-03T16:06:55+00:00"
---

### IoC/DI and beans

IoC is also known as <span class="emphasis">_dependency injection_</span> (DI).

A bean just need to define its dependencies through its constructor arguments, its factory method arguments or its properties setter. And then the container will inject these dependencies when initializing this bean.

Traditionally, the bean itself control the initialization process or location of its dependencies by directly invoking its constructor of the class or some look-up mechanism such as Service Locator pattern(e.g. JNDI or something).

So DI is a converse of the above traditional process, and that's the reason it's called Inverse-of-Control (IoC).

A **bean** is a manged object, whose creation, assembly, even whole lifecycle is controlled by the container.

![container-magic](/media/container-magic.png)

Configuration metadata can be specified by XML-based configuration style or Annotation-based configuration style.

### bean definition

#### bean name



```java
&lt;bean id="only_one_id" name="name1 name2 name3" ..&gt;

&lt;alias name="fromName" alias="toName"/&gt;
```

you can name a bean by use id/name attribute. What's more important is you can specify 0, 1, or multiple names/alias for a bean.

#### instantiating beans

*   Instantiation with a constructor


```java
&lt;bean id="exampleBean" class="examples.ExampleBean"/&gt;  

&lt;bean name="anotherExample" class="examples.ExampleBeanTwo$InnerClass"/&gt;
```


*   Instantiation with a static factory method


```java
&lt;bean id="clientService" class="examples.ClientService" factory-method="createInstance"/&gt;
```


*   Instantiation using an instance factory method


```java
&lt;!-- the factory bean, which contains a method called createInstance() --&gt;
&lt;bean id="serviceLocator" class="examples.DefaultServiceLocator"&gt;
    &lt;!-- inject any dependencies required by this locator bean --&gt;
&lt;/bean&gt;

&lt;!-- the bean to be created via the factory bean --&gt;
&lt;bean id="clientService"
    factory-bean="serviceLocator"
    factory-method="createClientServiceInstance"/&gt;
```


#### dependencies

*   Constructor-based dependency injection


```java
&lt;bean id="foo" class="x.y.Foo"&gt;
    &lt;constructor-arg ref="bar"/&gt;
    &lt;constructor-arg ref="baz"/&gt;
&lt;/bean&gt;

&lt;bean id="bar" class="x.y.Bar"/&gt;

&lt;bean id="baz" class="x.y.Baz"/&gt;

&lt;bean id="exampleBean" class="examples.ExampleBean"&gt;
    &lt;constructor-arg type="int" value="7500000"/&gt;
    &lt;constructor-arg type="java.lang.String" value="42"/&gt;
&lt;/bean&gt;

&lt;bean id="exampleBean" class="examples.ExampleBean"&gt;
    &lt;constructor-arg index="0" value="7500000"/&gt;
    &lt;constructor-arg index="1" value="42"/&gt;
&lt;/bean&gt;

&lt;!-- compiled with the debug flag --&gt;
&lt;bean id="exampleBean" class="examples.ExampleBean"&gt;
    &lt;constructor-arg name="years" value="7500000"/&gt;
    &lt;constructor-arg name="ultimateAnswer" value="42"/&gt;
&lt;/bean&gt;
```


**XML shortcut with the c-namespace**



```java
&lt;bean id="bar" class="x.y.Bar"/&gt;
&lt;bean id="baz" class="x.y.Baz"/&gt;

&lt;!-- traditional declaration --&gt;
&lt;bean id="foo" class="x.y.Foo"&gt;
    &lt;constructor-arg ref="bar"/&gt;
    &lt;constructor-arg ref="baz"/&gt;
    &lt;constructor-arg value="foo@bar.com"/&gt;
&lt;/bean&gt;

&lt;!-- c-namespace declaration --&gt;
&lt;bean id="foo" class="x.y.Foo" c:bar-ref="bar" c:baz-ref="baz" c:email="foo@bar.com"/&gt;
```


*   Setter-based dependency injection


```java
&lt;bean id="exampleBean" class="examples.ExampleBean"&gt;
    &lt;!-- setter injection using the nested ref element --&gt;
    &lt;property name="beanOne"&gt;
        &lt;ref bean="anotherExampleBean"/&gt;
    &lt;/property&gt;

    &lt;!-- setter injection using the neater ref attribute --&gt;
    &lt;property name="beanTwo" ref="yetAnotherBean"/&gt;
    &lt;property name="integerProperty" value="1"/&gt;
&lt;/bean&gt;

&lt;bean id="myDataSource" class="org.apache.commons.dbcp.BasicDataSource" destroy-method="close"&gt;
    &lt;!-- results in a setDriverClassName(String) call --&gt;
    &lt;property name="driverClassName" value="com.mysql.jdbc.Driver"/&gt;
    &lt;property name="url" value="jdbc:mysql://localhost:3306/mydb"/&gt;
    &lt;property name="username" value="root"/&gt;
    &lt;property name="password" value="masterkaoli"/&gt;
&lt;/bean&gt;

&lt;bean id="outer" class="..."&gt;
    &lt;!-- instead of using a reference to a target bean, simply define the target bean inline --&gt;
    &lt;property name="target"&gt;
        &lt;bean class="com.example.Person"&gt; &lt;!-- this is the inner bean --&gt;
            &lt;property name="name" value="Fiona Apple"/&gt;
            &lt;property name="age" value="25"/&gt;
        &lt;/bean&gt;
    &lt;/property&gt;
&lt;/bean&gt;

&lt;bean id="moreComplexObject" class="example.ComplexObject"&gt;
    &lt;!-- results in a setAdminEmails(java.util.Properties) call --&gt;
    &lt;property name="adminEmails"&gt;
        &lt;props&gt;
            &lt;prop key="administrator"&gt;administrator@example.org&lt;/prop&gt;
            &lt;prop key="support"&gt;support@example.org&lt;/prop&gt;
            &lt;prop key="development"&gt;development@example.org&lt;/prop&gt;
        &lt;/props&gt;
    &lt;/property&gt;
    &lt;!-- results in a setSomeList(java.util.List) call --&gt;
    &lt;property name="someList"&gt;
        &lt;list&gt;
            &lt;value&gt;a list element followed by a reference&lt;/value&gt;
            &lt;ref bean="myDataSource" /&gt;
        &lt;/list&gt;
    &lt;/property&gt;
    &lt;!-- results in a setSomeMap(java.util.Map) call --&gt;
    &lt;property name="someMap"&gt;
        &lt;map&gt;
            &lt;entry key="an entry" value="just some string"/&gt;
            &lt;entry key ="a ref" value-ref="myDataSource"/&gt;
        &lt;/map&gt;
    &lt;/property&gt;
    &lt;!-- results in a setSomeSet(java.util.Set) call --&gt;
    &lt;property name="someSet"&gt;
        &lt;set&gt;
            &lt;value&gt;just some string&lt;/value&gt;
            &lt;ref bean="myDataSource" /&gt;
        &lt;/set&gt;
    &lt;/property&gt;
&lt;/bean&gt;
```

** "" and null**


```java
&lt;bean class="ExampleBean"&gt;
    &lt;property name="email" value=""/&gt;
&lt;/bean&gt;

&lt;bean class="ExampleBean"&gt;
    &lt;property name="email"&gt;
        &lt;null/&gt;
    &lt;/property&gt;
&lt;/bean&gt;
```


**XML shortcut with the p-namespace**



```java
&lt;bean name="john-classic" class="com.example.Person"&gt;
    &lt;property name="name" value="John Doe"/&gt;
    &lt;property name="spouse" ref="jane"/&gt;
&lt;/bean&gt;

&lt;bean name="john-modern"
      class="com.example.Person"
      p:name="John Doe"
      p:spouse-ref="jane"/&gt;
```


#### bean scopes

<table border="1" summary="Bean scopes">
<thead>
<tr>
<th align="left" valign="top">Scope</th>
<th align="left" valign="top">Description</th>
</tr>
</thead>
<tbody>
<tr>
<td align="left" valign="top">[singleton](http://docs.spring.io/spring/docs/current/spring-framework-reference/htmlsingle/#beans-factory-scopes-singleton "5.5.1 The singleton scope")</td>
<td align="left" valign="top">(Default) Scopes a single bean definition to a single object instance per Spring IoC container.</td>
</tr>
<tr>
<td align="left" valign="top">[prototype](http://docs.spring.io/spring/docs/current/spring-framework-reference/htmlsingle/#beans-factory-scopes-prototype "5.5.2 The prototype scope")</td>
<td align="left" valign="top">Scopes a single bean definition to any number of object instances.</td>
</tr>
<tr>
<td align="left" valign="top">[request](http://docs.spring.io/spring/docs/current/spring-framework-reference/htmlsingle/#beans-factory-scopes-request "Request scope")</td>
<td align="left" valign="top">Scopes a single bean definition to the lifecycle of a single HTTP request; that is, each HTTP request has its own instance of a bean created off the back of a single bean definition. Only valid in the context of a web-aware Spring `ApplicationContext`.</td>
</tr>
<tr>
<td align="left" valign="top">[session](http://docs.spring.io/spring/docs/current/spring-framework-reference/htmlsingle/#beans-factory-scopes-session "Session scope")</td>
<td align="left" valign="top">Scopes a single bean definition to the lifecycle of an HTTP `Session`. Only valid in the context of a web-aware Spring `ApplicationContext`.</td>
</tr>
<tr>
<td align="left" valign="top">[global session](http://docs.spring.io/spring/docs/current/spring-framework-reference/htmlsingle/#beans-factory-scopes-global-session "Global session scope")</td>
<td align="left" valign="top">Scopes a single bean definition to the lifecycle of a global HTTP `Session`. Typically only valid when used in a portlet context. Only valid in the context of a web-aware Spring `ApplicationContext`.</td>
</tr>
<tr>
<td align="left" valign="top">[application](http://docs.spring.io/spring/docs/current/spring-framework-reference/htmlsingle/#beans-factory-scopes-application "Application scope")</td>
<td align="left" valign="top">Scopes a single bean definition to the lifecycle of a `ServletContext`. Only valid in the context of a web-aware Spring `ApplicationContext`.</td>
</tr>
</tbody>
</table>
request / session / application scopes need special initial configuration.

#### annotations and bean scanning

**Spring annotations vs. standard annotations**

<div class="table-contents">
<table border="1" summary="Spring annotations vs. standard annotations"><colgroup> <col class="col_1" /> <col class="col_2" /> <col class="col_3" /></colgroup>
<thead>
<tr>
<th align="left" valign="top">Spring</th>
<th align="left" valign="top">javax.inject.*</th>
<th align="left" valign="top">javax.inject restrictions / comments</th>
</tr>
</thead>
<tbody>
<tr>
<td align="left" valign="top">@Autowired</td>
<td align="left" valign="top">@Inject</td>
<td align="left" valign="top">@Inject has no <span class="emphasis">_required_</span> attribute</td>
</tr>
<tr>
<td align="left" valign="top">@Component</td>
<td align="left" valign="top">@Named</td>
<td align="left" valign="top">-</td>
</tr>
<tr>
<td align="left" valign="top">@Scope("singleton")</td>
<td align="left" valign="top">@Singleton</td>
<td align="left" valign="top">The JSR-330 default scope is like Spring’s `prototype`. However, in order to keep it consistent with Spring’s general defaults, a JSR-330 bean declared in the Spring container is a `singleton` by default. In order to use a scope other than `singleton`, you should use Spring’s `@Scope` annotation.`javax.inject` also provides a [@Scope](http://download.oracle.com/javaee/6/api/javax/inject/Scope.html) annotation. Nevertheless, this one is only intended to be used for creating your own annotations.</td>
</tr>
<tr>
<td align="left" valign="top">@Qualifier</td>
<td align="left" valign="top">@Named</td>
<td align="left" valign="top">-</td>
</tr>
<tr>
<td align="left" valign="top">@Value</td>
<td align="left" valign="top">-</td>
<td align="left" valign="top">no equivalent</td>
</tr>
<tr>
<td align="left" valign="top">@Required</td>
<td align="left" valign="top">-</td>
<td align="left" valign="top">no equivalent</td>
</tr>
<tr>
<td align="left" valign="top">@Lazy</td>
<td align="left" valign="top">-</td>
<td align="left" valign="top">no equivalent</td>
</tr>
</tbody>
</table>
</div>
Spring also supports injection using the JSR-250 `@Resource` annotation on fields or bean property setter methods.
