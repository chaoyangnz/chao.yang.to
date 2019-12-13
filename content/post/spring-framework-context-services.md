---
title: Spring framework - context services
toc: true
id: 1037
categories:
  - Spring
date: "2015-04-25T20:17:32+00:00"
---

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
&lt;?xml version="1.0" encoding="UTF-8"?&gt;
&lt;beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:p="http://www.springframework.org/schema/p"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd"&gt;

       &lt;bean class="context.lifecycle.LifecycleExposureBean" init-method="initMethod" destroy-method="destroyMethod"&gt;
              &lt;property name="injectedProperty" value="This is the value of injected property" /&gt;
       &lt;/bean&gt;
&lt;/beans&gt;
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

*   implement `FactoryBean&lt;T&gt;`
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
&lt;bean id="customEditorConfigurer" class="org.springframework.beans.factory.config.CustomEditorConfigurer"&gt;
	&lt;property name="customEditors"&gt;
		&lt;map&gt;
			&lt;entry key="context.propertyeditor.CustomBean" value="context.propertyeditor.CustomBeanEditor" /&gt;
		&lt;/map&gt;
	&lt;/property&gt;
&lt;/bean&gt;
```


### ApplicationContext capabilities

Basically, AppliationContext has more extra capabilities than BeanFactory.

You can regard ApplicationContext as a `MessageSource`, an `ApplicationEventPublisher`, a `ResourceLoader`.

#### MessageSource



```java
&lt;bean id="messageSource" class="org.springframework.context.support.ResourceBundleMessageSource"&gt;
    &lt;property name="basenames"&gt;
        &lt;list&gt;
            &lt;value&gt;message&lt;/value&gt;
        &lt;/list&gt;
    &lt;/property&gt;
&lt;/bean&gt;
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

`ApplicationListener&lt;T&gt;` interface

`ApplicationEventPublisher.publishEvent()`

`ApplicationContext` implements `ApplicationEventPublisher` interface

#### resource accessing

org.springframework.core.io.Resource

ResourceLoader

ApplicationContext implements ResourceLoader

#### Environment and PropertySource Abstraction

Environment --&gt; PropertySource


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
&lt;beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd"&gt;

    &lt;context:property-placeholder location="classpath:application.properties"/&gt;

    &lt;bean id="somebean" class="com.apress.prospring4.ch4.AppProperty"&gt;
        &lt;property name="applicationHome" value="${application.home}"/&gt;
        &lt;property name="userHome" value="${user.home}"&gt;&lt;/property&gt;
    &lt;/bean&gt;
&lt;/beans&gt;
```

You can see, once you get the properties, you can use them in configuration files using `{....}`

&nbsp;

### Profiles

Basically, a _profile_ instructs Spring to configure only the<span class="FontName1">ApplicationContext</span> that was defined when the specified profile was active.

*   How to associated with a profile?


```java
&lt;beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd"
        profile="devProfile"&gt;
...
&lt;/bean&gt;
```

Those beans in the file should be instantiated only when the specified profile is <span class="FontName1">active. If its profile is not active, actually these beans will not be initialized.</span>

*   How to activate

1.  JVM argument <span class="FontName1">-Dspring.profiles.active="devProfile"</span>
2.  ctx.getEnvironment().setActiveProfiles("devProfile")

### 

&nbsp;

&nbsp;
