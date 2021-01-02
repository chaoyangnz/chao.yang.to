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
<bean id="only_one_id" name="name1 name2 name3" ..>

<alias name="fromName" alias="toName"/>
```

you can name a bean by use id/name attribute. What's more important is you can specify 0, 1, or multiple names/alias for a bean.

#### instantiating beans

*   Instantiation with a constructor


```java
<bean id="exampleBean" class="examples.ExampleBean"/>  

<bean name="anotherExample" class="examples.ExampleBeanTwo$InnerClass"/>
```


*   Instantiation with a static factory method


```java
<bean id="clientService" class="examples.ClientService" factory-method="createInstance"/>
```


*   Instantiation using an instance factory method


```java
<!-- the factory bean, which contains a method called createInstance() -->
<bean id="serviceLocator" class="examples.DefaultServiceLocator">
    <!-- inject any dependencies required by this locator bean -->
</bean>

<!-- the bean to be created via the factory bean -->
<bean id="clientService"
    factory-bean="serviceLocator"
    factory-method="createClientServiceInstance"/>
```


#### dependencies

*   Constructor-based dependency injection


```java
<bean id="foo" class="x.y.Foo">
    <constructor-arg ref="bar"/>
    <constructor-arg ref="baz"/>
</bean>

<bean id="bar" class="x.y.Bar"/>

<bean id="baz" class="x.y.Baz"/>

<bean id="exampleBean" class="examples.ExampleBean">
    <constructor-arg type="int" value="7500000"/>
    <constructor-arg type="java.lang.String" value="42"/>
</bean>

<bean id="exampleBean" class="examples.ExampleBean">
    <constructor-arg index="0" value="7500000"/>
    <constructor-arg index="1" value="42"/>
</bean>

<!-- compiled with the debug flag -->
<bean id="exampleBean" class="examples.ExampleBean">
    <constructor-arg name="years" value="7500000"/>
    <constructor-arg name="ultimateAnswer" value="42"/>
</bean>
```


**XML shortcut with the c-namespace**



```java
<bean id="bar" class="x.y.Bar"/>
<bean id="baz" class="x.y.Baz"/>

<!-- traditional declaration -->
<bean id="foo" class="x.y.Foo">
    <constructor-arg ref="bar"/>
    <constructor-arg ref="baz"/>
    <constructor-arg value="foo@bar.com"/>
</bean>

<!-- c-namespace declaration -->
<bean id="foo" class="x.y.Foo" c:bar-ref="bar" c:baz-ref="baz" c:email="foo@bar.com"/>
```


*   Setter-based dependency injection


```java
<bean id="exampleBean" class="examples.ExampleBean">
    <!-- setter injection using the nested ref element -->
    <property name="beanOne">
        <ref bean="anotherExampleBean"/>
    </property>

    <!-- setter injection using the neater ref attribute -->
    <property name="beanTwo" ref="yetAnotherBean"/>
    <property name="integerProperty" value="1"/>
</bean>

<bean id="myDataSource" class="org.apache.commons.dbcp.BasicDataSource" destroy-method="close">
    <!-- results in a setDriverClassName(String) call -->
    <property name="driverClassName" value="com.mysql.jdbc.Driver"/>
    <property name="url" value="jdbc:mysql://localhost:3306/mydb"/>
    <property name="username" value="root"/>
    <property name="password" value="masterkaoli"/>
</bean>

<bean id="outer" class="...">
    <!-- instead of using a reference to a target bean, simply define the target bean inline -->
    <property name="target">
        <bean class="com.example.Person"> <!-- this is the inner bean -->
            <property name="name" value="Fiona Apple"/>
            <property name="age" value="25"/>
        </bean>
    </property>
</bean>

<bean id="moreComplexObject" class="example.ComplexObject">
    <!-- results in a setAdminEmails(java.util.Properties) call -->
    <property name="adminEmails">
        <props>
            <prop key="administrator">administrator@example.org</prop>
            <prop key="support">support@example.org</prop>
            <prop key="development">development@example.org</prop>
        </props>
    </property>
    <!-- results in a setSomeList(java.util.List) call -->
    <property name="someList">
        <list>
            <value>a list element followed by a reference</value>
            <ref bean="myDataSource" />
        </list>
    </property>
    <!-- results in a setSomeMap(java.util.Map) call -->
    <property name="someMap">
        <map>
            <entry key="an entry" value="just some string"/>
            <entry key ="a ref" value-ref="myDataSource"/>
        </map>
    </property>
    <!-- results in a setSomeSet(java.util.Set) call -->
    <property name="someSet">
        <set>
            <value>just some string</value>
            <ref bean="myDataSource" />
        </set>
    </property>
</bean>
```

** "" and null**


```java
<bean class="ExampleBean">
    <property name="email" value=""/>
</bean>

<bean class="ExampleBean">
    <property name="email">
        <null/>
    </property>
</bean>
```


**XML shortcut with the p-namespace**



```java
<bean name="john-classic" class="com.example.Person">
    <property name="name" value="John Doe"/>
    <property name="spouse" ref="jane"/>
</bean>

<bean name="john-modern"
      class="com.example.Person"
      p:name="John Doe"
      p:spouse-ref="jane"/>
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
