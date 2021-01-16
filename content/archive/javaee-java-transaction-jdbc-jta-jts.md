---
title: 'Java Transaction - JDBC, JTA, JTS'
toc: true
id: 326
categories:
  - JDBC
  - JTA
  - Transaction Management
date: "2015-02-10T08:46:35+00:00"
---

https://www.progress.com/products/datadirect-connect/jdbc-drivers/jdbc-developer-center/jdbc-tutorials/understanding-jta---the-java-transaction-api/accessing-databases

### Concepts and implementation

#### Where these come from?

In transaction processing, there are several participants:

![jta jts](/media/jta-jts.png)

**Resource Manager**: like RDMS, JMS Providers(MQ), JCA Resources, transnational distributed systems

It is important to understand what constitutes a _**<span class="emphasis">resource</span>**_, in this context. For example, if you are using a JMS product, the JMS resource is the single running instance of the JMS product, <span class="emphasis">not</span> the individual queues and topics. Moreover, sometimes, what appears to be multiple resources might actually be a single resource, if the same underlying resource is accessed in different ways. For example, your application might access a relational database both directly (through JDBC) and indirectly (through an object-relational mapping tool like Hibernate). In this case, the same underlying transaction manager is involved, so it should be possible to enrol both of these code fragments in the same transaction.

**Resource Adapters**: JDBC drivers ...

**Transaction Manager**: the part of an application that is responsible for coordinating transactions across one or more resources.

J2EE servers: EJB containers..

&nbsp;

Of these, transaction manager is the core role. Two categories of transaction manager:

**Local transaction manager**:

a transaction manager that can coordinate transactions over a <span class="emphasis">_single_</span> resource only. In this case, the implementation of the transaction manager is typically embedded in the resource itself.

For example, the Oracle database has a built-in transaction manager that supports demarcation operations (using SQL operations, `BEGIN`, `COMMIT`, `ROLLBACK`, or using a native Oracle API) and various levels of transaction isolation. Control over the Oracle transaction manager can be exported through JDBC, which is how Spring is able to abstract and wrap this kind of transaction manager as local transaction manager.

When we use resource local transaction in Java, we completely depend on database (a resource manager) to provide transaction support.

This is supported by JDBC API.

In the class Connection, there are several methods related to the resource local transaction:


```java
setAutoCommit(boolean)
commit()
rollback()
setTransactionIsolation()
```

**Global transaction manager**:

A _global transaction manager_ is a transaction manager that can coordinate transactions over <span class="emphasis">_multiple_</span> resources. In this case, you cannot rely on the transaction manager built into the resource itself. Instead, you require an external system, sometimes called a _transaction processing monitor_ (TP monitor), that is capable of coordinating transactions across different resources.

So the transactions coordinated by global transaction manager are called **global transaction**. And the transactions related with single resource and not coordinated by global transaction manager are called **local transaction**.

&nbsp;

Unlike the above general concepts, in implementation and standard level of Java transaction support:

XA transaction is a kind of global distributed transaction which allow across multiple _X/Open XA resources_.

JTA support XA transaction and the related classes: UserTransaction, TransactionManager, XADataSource, XAResource, XAConnection.

JTA transaction manager is also a kind of global transaction manager, which usually runs in application server as a process and communicates with transaction participants during the _two-phase commit_ protocol.
> **<span style="text-decoration: underline;">XA Transaction and XA Data source</span>**
> 
> 
> The JTA allows distributed transactions across multiple _X/Open XA resources_. XA stands for _Extended Architecture_ which was developed by the X/Open Group to define a transaction which uses more than one back-end data store. The XA standard describes the interface between a global _Transaction Manager (TM)_ and a local resource manager. XA allows multiple resources, such as application servers, databases, caches, and message queues, to participate in the same transaction, while preserving atomicity of the transaction.
> 
> 
> An XA datasource is a datasource which can participate in an XA global transaction.
> 
> 
> <span style="text-decoration: underline;">**2-Phase Commit Protocol**</span>
> 
> 
> The Two-phase commit protocol (2PC) refers to the typical pattern of a database transaction.
> 
> <div class="title">Phase 1</div>
> 
> In the first phase, the transaction participants notify the transaction manager whether they are able to commit the transaction or must roll back.
<div class="formalpara">
> Phase 2
> 
> In the second phase, the transaction manager makes the decision about whether the overall transaction should commit or roll back. If any one of the participants cannot commit, the transaction must roll back. Otherwise, the transaction can commit. The manager directs the transactions about what to do, and they notify the manager when they have done it. At that point, the transaction is finished.
</div>
Because JTA transaction is not distributed across multiple application servers, and cannot be nested, when we need to cross multiple J2EE containers, JTS is for this purpose.

**_Java Transaction Service (JTS)_** is a mechanism for supporting Java Transaction API (JTA) transactions when participants of the transactions reside in multiple J2EE containers (application servers). In JTS transactions, the Transaction Coordinator manages interactions between transaction managers on different servers.

From an application standpoint, a JTS transaction behaves in the same ways as a JTA transaction. The difference is that transaction participants and datasources reside in different containers.

#### How JTA is supported?

The Java Transaction API consists of three elements: a high-level application transaction demarcation interface, a high-level transaction manager interface intended for an application server, and a standard Java mapping of the X/Open XA protocol intended for a transactional resource manager.

`[javax.transaction.UserTransaction](http://docs.oracle.com/javaee/7/api/javax/transaction/UserTransaction.html)`, that is used by general client code such as a servlet or an EJB to manage the transactions, but it is usually acquired by JNDI reference (it should be available under `java:comp/UserTransaction)`, and is usually injected into EJB when you use Container-Managed Transaction(CMT).

`[javax.transaction.TransactionManager](http://docs.oracle.com/javaee/7/api/javax/transaction/TransactionManager.html)`, that is implemented by the application server itself to begin, commit and rollback the transactions.

`[javax.transaction.xa.XAResource](http://docs.oracle.com/javaee/7/api/javax/transaction/xa/XAResource.html)` interface is required to implement  by each resource manager in order to be managed by the TP monitor. Each resource will have its own specific API, for instance:

*   relational databases use JDBC (XADataSource, XAConnection are implemented by XA JDBC driver)
*   messaging services use JMS
*   generalized EIS (Enterprise Information System) resources use JCA (Java EE Connector API).
> _If you are interested in how to implement a simple/naive transaction manager just like it'done in application server, please refer to:__http://blog.csdn.net/liu78778/article/details/4805308_

#### How can we use them in traditional J2EE enviorment?

<span style="text-decoration: underline;"><span id="UserTransaction_support_in_JNDI" class="mw-headline">UserTransaction support in servlet :</span></span>


```java
import javax.transaction.*；
import javax.naming.*；
// ...
InitialContext ctx = new InitialContext()；
Object txObj = ctx.lookup("java:comp/UserTransaction"；)；
UserTransaction utx = (UserTransaction) txObj；
utx.begin();
// ...
DataSource ds = obtainXADataSource();
Connection conn = ds.getConnection();
pstmt = conn.prepareStatement("UPDATE MOVIES ...");
pstmt.setString(1, "Spinal Tap");
pstmt.executeUpdate();
// ...
utx.commit();
```

<span style="text-decoration: underline;">UserTransaction support in EJB:
</span>


```java
@Stateless
@TransactionManagement(BEAN)
public class ExampleBean {

    @Resource
    private UserTransaction utx;

    public void foo() {
        // start a transaction
        utx.begin();

        // Do work

        // Commit it
        utx.commit();
    }
}
```

For more details, you need to refer to the EJB transaction management(CMT).

### Spring transaction management abstraction

Spring intends to abstract these transaction management technology, and integrate them into a unified API.

PlatformTransactionManager is a service provider interface Spring provides. (NOTE, it's not the subclass of javax.transaction.TransactionManager).

Currently, there are the following implementations:
<div class="simplesect">

Table 1 summarizes the local transaction manager implementations provided by the Spring framework. These transaction managers are distinguished by the fact that they support a <span class="emphasis">_single resource only_</span>.

**Table 1. Local Transaction Managers**
<div class="table">
<div class="table-contents">
<table border="1" summary="Local Transaction Managers" width="100%" cellpadding="2"><colgroup> <col class="c1" /> <col class="c2" /></colgroup>
<thead>
<tr>
<th>Transaction Manager</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr>
<td>[`JmsTransactionManager`](http://static.springsource.org/spring/docs/3.0.x/javadoc-api/org/springframework/jms/connection/JmsTransactionManager.html)</td>
<td>A transaction manager implementation that is capable of managing a <span class="emphasis">_single_</span> JMS resource. That is, you can connect to any number of queues or topics, but <span class="emphasis">_only_</span> if they belong to the same underlying JMS messaging product instance. Moreover, you cannot enlist any other types of resource in a transaction.For example, using this transaction manager, it would <span class="emphasis">_not_</span> be possible to enlist both a SonicMQ resource and an Apache ActiveMQ resource in the same transaction. But see Table 2.</td>
</tr>
<tr>
<td>[`DataSourceTransactionManager`](http://static.springsource.org/spring/docs/3.0.x/javadoc-api/org/springframework/jdbc/datasource/DataSourceTransactionManager.html)</td>
<td>A transaction manager implementation that is capable of managing a <span class="emphasis">_single_</span> JDBC database resource. That is, you can update any number of different database tables, but <span class="emphasis">_only_</span> if they belong to the same underlying database instance.</td>
</tr>
<tr>
<td>[`HibernateTransactionManager`](http://static.springsource.org/spring/docs/3.0.x/javadoc-api/org/springframework/orm/hibernate3/HibernateTransactionManager.html)</td>
<td>A transaction manager implementation that is capable of managing a [Hibernate](http://www.hibernate.org/) resource. It is not possible, however, to simultaneously enlist any other kind of resource in a transaction.</td>
</tr>
<tr>
<td>[`JdoTransactionManager`](http://static.springsource.org/spring/docs/3.0.x/javadoc-api/org/springframework/orm/jdo/JdoTransactionManager.html)</td>
<td>A transaction manager implementation that is capable of managing a Java Data Objects ([JDO](http://db.apache.org/jdo/)) resource. It is not possible, however, to simultaneously enlist any other kind of resource in a transaction.</td>
</tr>
<tr>
<td>[`JpaTransactionManager`](http://static.springsource.org/spring/docs/3.0.x/javadoc-api/org/springframework/orm/jpa/JpaTransactionManager.html)</td>
<td>A transaction manager implementation that is capable of managing a Java Persistence API ([JPA](http://java.sun.com/javaee/technologies/persistence.jsp)) resource. It is not possible, however, to simultaneously enlist any other kind of resource in a transaction.</td>
</tr>
<tr>
<td>[`CciLocalTransactionManager`](http://static.springsource.org/spring/docs/3.0.x/javadoc-api/org/springframework/jca/cci/connection/CciLocalTransactionManager.html)</td>
<td>A transaction manager implementation that is capable of managing a Java Connection Architecture ([JCA](http://java.sun.com/j2ee/connector/)) resource. It is not possible, however, to simultaneously enlist any other kind of resource in a transaction.</td>
</tr>
</tbody>
</table>
</div>
</div>
</div>
<div class="simplesect">

[Table 2](https://access.redhat.com/documentation/en-US/Fuse_ESB_Enterprise/7.1/html/EIP_Transaction_Guide/files/TxnManagers-Impls.html#TxnManagers-Impls-TableGTM "Table 2. Global Transaction Managers") summarizes the global transaction manager implementations provided by the Spring framework. These transaction managers are distinguished by the fact that they can support <span class="emphasis">_multiple resources_</span>.

**Table 2. Global Transaction Managers**
<div class="table">
<div class="table-contents">
<table border="1" summary="Global Transaction Managers" width="100%" cellpadding="2"><colgroup> <col class="c1" /> <col class="c2" /></colgroup>
<thead>
<tr>
<th>Transaction Manager</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr>
<td>[`JtaTransactionManager`](http://static.springsource.org/spring/docs/3.0.x/javadoc-api/org/springframework/transaction/jta/JtaTransactionManager.html)</td>
<td>If you require a transaction manager that is capable of enlisting more than one resource in a transaction, use the JTA transaction manager, which is capable of supporting the XA transaction API. You <span class="emphasis">_must_</span> deploy your application inside either an OSGi container or a J2EE server to use this transaction manager.</td>
</tr>
<tr>
<td>[`OC4JJtaTransactionManagner`](http://static.springsource.org/spring/docs/3.0.x/javadoc-api/org/springframework/transaction/jta/OC4JJtaTransactionManager.html)</td>
<td>A specialization of the `JtaTransactionManager` to work with Oracle's OC4J. The advantage of this implementation is that it makes Spring-driven transactions visible in OC4J's transaction monitor</td>
</tr>
<tr>
<td>[`WebLogicJtaTransactionManager`](http://static.springsource.org/spring/docs/3.0.x/javadoc-api/org/springframework/transaction/jta/WebLogicJtaTransactionManager.html)</td>
<td>A specialization of the `JtaTransactionManager` to work with the BEA WebLogic container. Makes certain advanced transaction features available: transaction names, per-transaction isolation levels, and proper suspension/resumption of transactions.</td>
</tr>
<tr>
<td>[`WebSphereUowTransactionManager`](http://static.springsource.org/spring/docs/3.0.x/javadoc-api/org/springframework/transaction/jta/WebSphereUowTransactionManager.html)</td>
<td>A specialization of the `JtaTransactionManager` to work with the IBM WebSphere container. Enables proper suspension/resumption of transactions.</td>
</tr>
</tbody>
</table>
</div>
</div>
</div>
