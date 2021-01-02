---
title: Spring framework - AOP
toc: true
id: 845
categories:
  - Spring
date: "2015-04-03T16:17:51+00:00"
---

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

