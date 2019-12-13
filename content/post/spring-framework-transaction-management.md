---
title: Spring framework - transaction management
toc: true
id: 847
categories:
  - Spring
  - Transaction Management
date: "2015-04-03T16:32:56+00:00"
---

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
