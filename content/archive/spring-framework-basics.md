---
title: Spring framework basics
toc: true
id: 845
categories:
  - Spring
date: "2015-04-03T16:17:51+00:00"
---

# DI

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

# Context services

### Bean Lifecycle Mangement

&nbsp;

![spring-bean-lifecycle](/media/spring-bean-lifecycle.jpg)


```java
package context.lifecycle;

import org.springframework.beans.BeansException;
import org.springframework.beans.factory.BeanClassLoaderAware;
import org.springframework.beans.factory.BeanNameAware;
import org.springframework.beans.factory.DisposableBean;
import org.springframework.beans.factory.InitializingBean;
import org.springframework.context.ApplicationContext;
import org.springframework.context.ApplicationContextAware;

import javax.annotation.PostConstruct;
import javax.annotation.PreDestroy;

public class LifecycleExposureBean implements BeanNameAware, BeanClassLoaderAware, ApplicationContextAware, InitializingBean, DisposableBean {

    private String injectedProperty;

    public void setInjectedProperty(String injectedProperty) {
        System.out.println("Injection: injectedProperty = [" + injectedProperty + "]");
        this.injectedProperty = injectedProperty;
    }

    //----------------context aware-------------------------

    public void setBeanName(String beanName) {
        System.out.println("BeanNameAware: beanName = [" + beanName + "]");
    }

    public void setBeanClassLoader(ClassLoader classLoader) {
        System.out.println("BeanClassLoaderAware: classLoader = [" + classLoader + "]");
    }

    public void setApplicationContext(ApplicationContext applicationContext) throws BeansException {
        System.out.println("ApplicationAware: applicationContext = [" + applicationContext + "]");
    }

    //----------------initialization hook-------------------------

    @PostConstruct
    public void postContructCallBack() {
        System.out.println("@PostContruc: Post contruct invocation");
    }

    public void afterPropertiesSet() throws Exception {
        System.out.println("IntializingBean: afterPropertiesSet() called");
    }

    public void initMethod() {
        System.out.println("init-method: called");
    }

    //----------------destroy hook-------------------------
    @PreDestroy
    public void preDestroy() {
        System.out.println("@PreDestory: called");
    }

    public void destroy() throws Exception {
        System.out.println("DisposableBean: destroy() called");
    }

    public void destroyMethod() {
        System.out.println("destroy-method: called");
    }

    public static void main(String[] args) {
        GenericXmlApplicationContext context = new GenericXmlApplicationContext("classpath:spring-lifecycle.xml");

        context.destroy();
    }
}
```



```java
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:p="http://www.springframework.org/schema/p"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

       <bean class="context.lifecycle.LifecycleExposureBean" init-method="initMethod" destroy-method="destroyMethod">
              <property name="injectedProperty" value="This is the value of injected property" />
       </bean>
</beans>
```

> Injection: injectedProperty = [This is the value of injected property]
> 
> BeanNameAware: beanName = [context.lifecycle.LifecycleExposureBean#0]
> 
> BeanClassLoaderAware: classLoader = [sun.misc.Launcher$AppClassLoader@330bedb4]
> 
> ApplicationAware: applicationContext = [org.springframework.context.support.GenericXmlApplicationContext@18769467: startup date [Sat Apr 25 19:38:10 CST 2015]; root of context hierarchy]
> 
> IntializingBean: afterPropertiesSet() called
> 
> init-method: called
> 
> DisposableBean: destroy() called
> 
> destroy-method: called

### FactoryBean

When a bean depends on some beans which cannot be simply intialized by using `new`

*   implement `FactoryBean<T>`
*   specify `<span class="FontName1">factory-bean</span>` and `<span class="FontName1">factory-method</span>`

### PropertyEditor

In String, PropertyEditior is mainly used as a kind of converter from String to POJO JavaBean.

You implement your custom PropertyEditor for the specified JavaBean class, and then configure it using `org.springframework.beans.factory.config.CustomEditorConfigurer`


```java
public class CustomBean {
    private String firstName;
    private String lastName;

    public CustomBean(String firstName, String lastName) {
    	this.firstName = firstName;
    	this.lastName = lastName;
    }

    public String toString() {
    	return "First name: " + firstName + ", last name: " + lastName;
    }

}

public class CustomBeanEditor extends PropertyEditorSupport {

    public void setAsText(String text) {
        String[] segs = text.split(" ");
        CustomBean customBean = new CustomBean(segs[0], segs[1]);

        setValue(customBean);
    }
}
```



```java
<bean id="customEditorConfigurer" class="org.springframework.beans.factory.config.CustomEditorConfigurer">
	<property name="customEditors">
		<map>
			<entry key="context.propertyeditor.CustomBean" value="context.propertyeditor.CustomBeanEditor" />
		</map>
	</property>
</bean>
```


### ApplicationContext capabilities

Basically, AppliationContext has more extra capabilities than BeanFactory.

You can regard ApplicationContext as a `MessageSource`, an `ApplicationEventPublisher`, a `ResourceLoader`.

#### MessageSource



```java
<bean id="messageSource" class="org.springframework.context.support.ResourceBundleMessageSource">
    <property name="basenames">
        <list>
            <value>message</value>
        </list>
    </property>
</bean>
```



```java
GenericXmlApplicationContext context = new GenericXmlApplicationContext("classpath:spring-messagesource.xml");

String name = "Richard";
String greeting = context.getMessage("greeting", new String[]{name}, Locale.US);
String greetingCN = context.getMessage("greeting", new String[]{name}, Locale.CHINA);

System.out.println("greeting = [" + greeting + "]");
System.out.println("greetingCN = [" + greetingCN + "]");
```

Then I have two message file: message_en_US.properties and message_zh_CN


```java
greeting=你好, {0}！
```



```java
greeting=How is it going, {0}?
```

Output:
> greeting = [How is it going, Richard?]
> 
> greetingCN = [你好, Richard！]

#### Application Events

`ApplicationEvent`  -- derive from java.util.`EventObject`

`ApplicationListener<T>` interface

`ApplicationEventPublisher.publishEvent()`

`ApplicationContext` implements `ApplicationEventPublisher` interface

#### resource accessing

org.springframework.core.io.Resource

ResourceLoader

ApplicationContext implements ResourceLoader

#### Environment and PropertySource Abstraction

Environment --> PropertySource


```java
ConfigurableEnvironment env =&amp;nbsp;ctx.getEnvironment();
MutablePropertySources propertySources =&amp;nbsp;env.getPropertySources();

Map appMap =&amp;nbsp;new HashMap();
appMap.put("application.home", "application_home");

propertySources.addLast(new MapPropertySource("application_properties", appMap));
```


For the <span class="FontName1">PropertySource</span> abstraction, Spring will access the properties in the following default order:<a id="cXXX.328"></a>

*   System properties for the running JVM (-Dxxxx)
*   Environment variables (like JAVA_HOME, Path)
*   Application-defined properties
Most of time, we use PropertyPlaceHolder


```java
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd">

    <context:property-placeholder location="classpath:application.properties"/>

    <bean id="somebean" class="com.apress.prospring4.ch4.AppProperty">
        <property name="applicationHome" value="${application.home}"/>
        <property name="userHome" value="${user.home}"></property>
    </bean>
</beans>
```

You can see, once you get the properties, you can use them in configuration files using `{....}`

&nbsp;

### Profiles

Basically, a _profile_ instructs Spring to configure only the<span class="FontName1">ApplicationContext</span> that was defined when the specified profile was active.

*   How to associated with a profile?


```java
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd"
        profile="devProfile">
...
</bean>
```

Those beans in the file should be instantiated only when the specified profile is <span class="FontName1">active. If its profile is not active, actually these beans will not be initialized.</span>

*   How to activate

1.  JVM argument <span class="FontName1">-Dspring.profiles.active="devProfile"</span>
2.  ctx.getEnvironment().setActiveProfiles("devProfile")

### 

&nbsp;

&nbsp;


# AOP
_Crosscutting concerns_ refers to logic in an application that cannot be decomposed from the rest of the application and may result in code duplication and tight coupling.

Typical use cases:

*   logging
*   security
*   transaction

Concepts:

*   **Pointcut** -> where to do: class filtering and method matching
*   **Advice** -> when to do: before/after/returning/throws/...
*   **Aspect** -> what to do, do what the application specified things. This is the only code application needs to define, others concepts are supported and configured by framework.
*   **Advisor** -> associate Advice and Pointcut
*   **JoinPoint** -> the execution point: the occurrence context including which class, which methods, proceeding method execution..
*   **weaving** -> when to proxy target: build time(compile-time), load-time, run-time

### Spring AOP style

Spring AOP depends on proxies. You must use `ProxyFactoryBean` to produce the proxy for target object.

![spring-aop](/media/spring-aop.jpg)

Internally, Spring has two proxy implementations: JDK dynamic proxies and CGLIB proxies.

*   By default, when the target object to be advised implements an interface, Spring will use a JDK dynamic proxy to create proxy instances of the target.
*   when the advised target object doesn’t implement an interface (for example, it’s a concrete class), CGLIB will be used for proxy instance creation.

#### Joinpoints in Spring

Spring AOP supports only one joinpoint type: **method invocation**.

#### Aspects in Spring: Adviser (Advice+Pointcut)

An aspect in Spring is represented by an instance of a class that implements the `Advisor` interface.

*   PointcutAdvisor
*   IntroductionAdvisor
The main difference between `Advice`and `Advisor`is that `Advisor` carries `Advice` with the associated `Pointcut`.

![spring-aop-concepts](/media/spring-aop-concepts.png)
> `ProxyFactory.addAdvice()` delegates to `addAdvisor()` behind the scenes, creating an instance of `DefaultPointcutAdvisor` and configuring it with a pointcut that points to all methods.

#### Pointcut

Pointcut acutally does something filtering class and matching methods.

eight `Pointcut` interface implementation:

*   `org.springframework.aop.support.NameMatchMethodPointcut`
*   `org.springframework.aop.support.JdkRegexpMethodPointcut`
*   `org.springframework.aop.support.annotation.AnnotationMatchingPointcut`
*   `org.springframework.aop.aspectj.AspectJExpressionPointcut`
*   `org.springframework.aop.support.DynamicMethodMatcherPointcut`
*   `org.springframework.aop.support .StaticMethodMatcherPointcut`
*   `org.springframework.aop.support.ComposablePointcut`
*   `org.springframework.aop.support.ControlFlowPointcut`

#### Advice types

<table style="height: 122px;" border="1" width="631">
<tbody>
<tr>
<td>Advice type</td>
<td></td>
</tr>
<tr>
<td>before</td>
<td>org.springframework.aop.BeforeAdviceorg.springframework.aop.MethodBeforeAdvice</td>
</tr>
<tr>
<td>after(finally)</td>
<td>org.springframework.aop.AfterAdvice</td>
</tr>
<tr>
<td>after returning</td>
<td>org.springframework.aop.AfterReturningAdvice</td>
</tr>
<tr>
<td>around</td>
<td>org.springframework.aop.Interceptororg.springframework.aop.MethodInterceptor</td>
</tr>
<tr>
<td>throws</td>
<td>org.springframework.aop.ThrowsAdvice</td>
</tr>
<tr>
<td>introduction</td>
<td></td>
</tr>
</tbody>
</table>
![advice-types](/media/advice-types.jpg)


#### schema-based configuration

##### <aop:aspect>



```java
<aop:config>
    <!-- shared top-level pointcut -->
    <aop:pointcut id="fooExecution" expression="execution(* com.apress.prospring4.ch5..foo*(int))"/>

    <aop:aspect ref="myAspect">
        <!-- <aop:pointcut ... /> -->
        <aop:before method="simpleBeforeAdvice" pointcut-ref="fooExecution" />
        <aop:around method="simpleAroundAdvice" pointcut-ref="fooExecution" />
    </aop:aspect>
</aop:config>
```


`myAspect` bean is a plain bean, which don't necessarily implement any interface.

##### <aop:advisor>

This is called aspect instantiation model.


```java
<aop:config>
    <aop:pointcut id="businessService"
        expression="execution(* com.xyz.myapp.service.*.*(..))"/>

    <aop:advisor pointcut-ref="businessService" advice-ref="tx-advice"/>
</aop:config>

<tx:advice id="tx-advice">
    <tx:attributes>
        <tx:method name="*" propagation="REQUIRED"/>
    </tx:attributes>
</tx:advice>
```


`advice-ref` is a bean which must implement one of the `Advice` interfaces family.

### @AspectJ style

Bascially, they use @Aspect, @Pointcut, @Around, @Before, ...

To enable @AspectJ style:


```java
<aop:aspectj-autoproxy/>
```


### Load-time weaving

enabled by:


```java
<context:load-time-weaver>
```

The default `LoadTimeWeaver` is the `DefaultContextLoadTimeWeaver` class, which attempts to decorate an automatically detected`LoadTimeWeaver`: the exact type of `LoadTimeWeaver` that will be _automatically detected_ is dependent upon your runtime environment (summarized in the following table).

You can also specific the exact load-time weaver rather than the automatic one in xml configuration file:


```java
<context:load-time-weaver weaver-class="org.springframework.instrument.classloading.ReflectiveLoadTimeWeaver"/>
```

<table border="1" summary="DefaultContextLoadTimeWeaver LoadTimeWeavers">
<tbody>
<tr>
<td align="left" valign="top">Running in [BEA’s Weblogic 10](http://www.bea.com/framework.jsp?CNT=index.htm&amp;FP=/content/products/weblogic/server)</td>
<td align="left" valign="top">`WebLogicLoadTimeWeaver`</td>
</tr>
<tr>
<td align="left" valign="top">Running in [IBM WebSphere Application Server 7](http://www-01.ibm.com/software/webservers/appserv/was/)</td>
<td align="left" valign="top">`WebSphereLoadTimeWeaver`</td>
</tr>
<tr>
<td align="left" valign="top">Running in [GlassFish](http://glassfish.dev.java.net/)</td>
<td align="left" valign="top">`GlassFishLoadTimeWeaver`</td>
</tr>
<tr>
<td align="left" valign="top">Running in [JBoss AS](http://www.jboss.org/jbossas/)</td>
<td align="left" valign="top">`JBossLoadTimeWeaver`</td>
</tr>
<tr>
<td align="left" valign="top">JVM started with Spring `InstrumentationSavingAgent` <span class="emphasis">_(java -javaagent:path/to/spring-instrument.jar)_</span>e.g. J2SE applications (no LTW support, if no instrument agent specified to JVM, errors will be thrown)</td>
<td align="left" valign="top">`InstrumentationLoadTimeWeaver`</td>
</tr>
<tr>
<td align="left" valign="top">Others all, expecting the underlying ClassLoader to follow common conventions (e.g. applicable to`TomcatInstrumentableClassLoader` and [Resin](http://www.caucho.com/))</td>
<td align="left" valign="top">`ReflectiveLoadTimeWeaver`</td>
</tr>
</tbody>
</table>

#### WebLogic, WebSphere, Resin, GlassFish, JBoss

These environments have Load-time weaving support, no need for `-javaagent:path/to/``org.springframework.instrument-{version}.jar`

#### Tomcat

not support. You need do extra things to LTW

*   Copy `org.springframework.instrument.tomcat.jar` into <span class="emphasis">_$CATALINA_HOME_</span>/lib
*   Instruct Tomcat to use the custom class loader (instead of the default) by editing the web application context file:


```java
<Context path="/myWebApp" docBase="/my/webApp/location">
    <Loader loaderClass="org.springframework.instrument.classloading.tomcat.TomcatInstrumentableClassLoader"/>
</Context>
```


#### Generic J2SE applications

Spring provides `InstrumentationLoadTimeWeaver`, which requires a Spring-specific (but very general) VM agent,` -javaagent:path/to/org.springframework.instrument-{version}.jar`

# Transaction management

In Java, there are multiple persistence APIs and all of them provides their own ways of transaction management.

*   JDBC transaction: this is a resource-specific transaction, which is implemented by connection.
*   JTA transaction: this is a transaction API for global transaction, it usually needs the support of application server.
*   JPA transaction: JPA has its own transaction API by EntityManager.
*   Hibernate transaction: Hibernate also provides its way to manage transaction by SessionFactory
*   JDO: ...
Now Spring transaction management abstracts and unifies all these API.

### Infrastructure abstraction

*   TransactionStatus
*   PlatformTransactionManager (SPI)
*   TransactionDefinition

    *   Isolation
    *   Propagation
    *   Timeout
    *   Read-only
Spring implements many built-in transaction manager implements:
<table style="height: 212px;" border="1" width="477">
<tbody>
<tr>
<td>transaction manager</td>
<td>linked resource</td>
</tr>
<tr>
<td>DataSourceTransactionManger</td>
<td>JDBC DataSource</td>
</tr>
<tr>
<td>JTATransactionManager</td>
<td></td>
</tr>
<tr>
<td>   |-- WebSphereTransactionManager</td>
<td></td>
</tr>
<tr>
<td>   |-- WebLogicTransactionManager</td>
<td></td>
</tr>
<tr>
<td>HibernateTransactionManager</td>
<td>SessionFactory</td>
</tr>
<tr>
<td>JPATransactionManager</td>
<td>EntityManager</td>
</tr>
</tbody>
</table>

### AOP implementation

#### TransactionInterceptor

This is the secret for the declarative transaction.

![transaction-interceptor](/media/transaction-interceptor.png)


```java
public interface FooService {

    Foo getFoo(String fooName);

    Foo getFoo(String fooName, String barName);

    void insertFoo(Foo foo);

    void updateFoo(Foo foo);

}

public class DefaultFooService implements FooService {

    public Foo getFoo(String fooName) {
        throw new UnsupportedOperationException();
    }

    public Foo getFoo(String fooName, String barName) {
        throw new UnsupportedOperationException();
    }

    public void insertFoo(Foo foo) {
        throw new UnsupportedOperationException();
    }

    public void updateFoo(Foo foo) {
        throw new UnsupportedOperationException();
    }

}

class Foo {

}
```



```java
public static void main(String[] args) {
    GenericXmlApplicationContext context = new GenericXmlApplicationContext("classpath:spring-tx.xml");
    FooService fooService = (FooService)context.getBean("fooService");

    TransactionInterceptor txAdvice = (TransactionInterceptor)context.getBean("txAdvice");
    fooService.insertFoo(new Foo());
}
```

This is the log4j log which can show some implementation details:


```java
&lt;!-- the DefaultFooService is actually proxied --&gt;
DEBUG {org.springframework.aop.aspectj.autoproxy.AspectJAwareAdvisorAutoProxyCreator}  - Creating implicit proxy for bean 'fooService' with 0 common interceptors and 2 specific interceptors
DEBUG {org.springframework.aop.framework.JdkDynamicAopProxy}  - Creating JDK dynamic proxy: target source is SingletonTargetSource for target object [tx.DefaultFooService@14f9390f]
DEBUG {org.springframework.beans.factory.support.DefaultListableBeanFactory}  - Finished creating instance of bean 'fooService'

&lt;!--=========== Before: the insertFoo(..) method is now being invoked on the proxy ================--&gt;
&lt;!-- the transactional advice kicks in here... --&gt;

&lt;!-- acquire connection --&gt;
DEBUG {org.springframework.jdbc.datasource.DataSourceTransactionManager}  - Acquired Connection [jdbc:h2:file:D:/Projects/spring-sample/activejdbc/src/main/resources/test, UserName=, H2 JDBC Driver] for JDBC transaction

&lt;!-- disable autocommit --&gt;
DEBUG {org.springframework.jdbc.datasource.DataSourceTransactionManager}  - Switching JDBC Connection [jdbc:h2:file:D:/Projects/spring-sample/activejdbc/src/main/resources/test, UserName=, H2 JDBC Driver] to manual commit

&lt;!-- !!! this is the way: bound resource to the thread --&gt;
TRACE {org.springframework.transaction.support.TransactionSynchronizationManager}  - Bound value [org.springframework.jdbc.datasource.ConnectionHolder@2101b44a] for key [org.apache.commons.dbcp.BasicDataSource@52d645b1] to thread [main]

&lt;!-- initialize transaction synchronization --&gt;
TRACE {org.springframework.transaction.support.TransactionSynchronizationManager}  - Initializing transaction synchronization
TRACE {org.springframework.transaction.interceptor.TransactionInterceptor}  - Getting transaction for [tx.DefaultFooService.insertFoo]

=================foo() invoked=============

&lt;!--============ Exception: the insertFoo(..) method from DefaultFooService throws an exception...========== --&gt;
TRACE {org.springframework.transaction.interceptor.TransactionInterceptor}  - Completing transaction for [tx.DefaultFooService.insertFoo] after exception: java.lang.UnsupportedOperationException
TRACE {org.springframework.transaction.interceptor.RuleBasedTransactionAttribute}  - Applying rules to determine whether transaction should rollback on java.lang.UnsupportedOperationException
TRACE {org.springframework.transaction.interceptor.RuleBasedTransactionAttribute}  - Winning rollback rule is: null
TRACE {org.springframework.transaction.interceptor.RuleBasedTransactionAttribute}  - No relevant rollback rule found: applying default rules
TRACE {org.springframework.jdbc.datasource.DataSourceTransactionManager}  - Triggering beforeCompletion synchronization

&lt;!-- rollback depending on the rollback rules --&gt;
DEBUG {org.springframework.jdbc.datasource.DataSourceTransactionManager}  - Initiating transaction rollback
DEBUG {org.springframework.jdbc.datasource.DataSourceTransactionManager}  - Rolling back JDBC transaction on Connection [jdbc:h2:file:D:/Projects/spring-sample/activejdbc/src/main/resources/test, UserName=, H2 JDBC Driver]
TRACE {org.springframework.jdbc.datasource.DataSourceTransactionManager}  - Triggering afterCompletion synchronization

&lt;!-- clear transaction synchronization --&gt;
TRACE {org.springframework.transaction.support.TransactionSynchronizationManager}  - Clearing transaction synchronization
TRACE {org.springframework.transaction.support.TransactionSynchronizationManager}  - Removed value [org.springframework.jdbc.datasource.ConnectionHolder@2101b44a] for key [org.apache.commons.dbcp.BasicDataSource@52d645b1] from thread [main]

&lt;!-- release connection --&gt;
DEBUG {org.springframework.jdbc.datasource.DataSourceTransactionManager}  - Releasing JDBC Connection [jdbc:h2:file:D:/Projects/spring-sample/activejdbc/src/main/resources/test, UserName=, H2 JDBC Driver] after transaction
DEBUG {org.springframework.jdbc.datasource.DataSourceUtils}  - Returning JDBC Connection to DataSource
```

&nbsp;
