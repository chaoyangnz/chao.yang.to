---
title: JPA - runtime
toc: true
id: 322
categories:
  - JPA
  - Transaction Management
date: "2015-02-10T03:51:29+00:00"
---

### Core concepts

<table border="2" frame="hsides" rules="groups" cellspacing="0" cellpadding="6">
<thead>
<tr>
<th class="left" scope="col">org.hibernate</th>
<th class="left" scope="col">javax.persistence</th>
<th class="left" scope="col">说明</th>
</tr>
</thead>
<tbody>
<tr>
<td class="left">cfg.Configuration</td>
<td class="left">PersistenceUnit</td>
<td class="left">读取配置信息</td>
</tr>
<tr>
<td class="left">SessionFactory</td>
<td class="left">EntityManagerFactory</td>
<td class="left">用于创建会话/实体管理器的工厂类</td>
</tr>
<tr>
<td class="left">Session</td>
<td class="left">EntityManager</td>
<td class="left">提供实体操作API，管理事务，创建查询</td>
</tr>
<tr>
<td class="left">Transaction</td>
<td class="left">EntityTransaction</td>
<td class="left">管理事务</td>
</tr>
<tr>
<td class="left">Query</td>
<td class="left">Query</td>
<td class="left">执行查询Here's a quick cheat sheet of the JPA world:</td>
</tr>
</tbody>
</table>

*   A **Cache** is a **copy of data**, copy meaning pulled from but living outside the database.
*   **Flushing** a Cache is the act of putting modified data back into the database.
*   A **PersistenceContext** is essentially a Cache. It also tends to have it's own non-shared database connection.
*   An **EntityManager** represents a PersistenceContext (and therefore a Cache)
*   An **EntityManagerFactory** creates an EntityManager (and therefore a PersistenceContext/Cache)
> **Cache == PersistenceContext**

### Entity Manager

A persistence unit is a named configuration of entity classes.

A persistence context is a  managed set of entity instances.

An `EntityManager` instance is associated with a persistence context.

Saying that an entity is _managed_ meas that it's contained within a persistence context and it can be acted upon by an entity manager.

*   An `EntityManagerFactory` is an expensive-to-create, threadsafe object intended to be shared by all application threads.
*   An `EntityManager` is an inexpensive, non-threadsafe object that should be used once, for a single business process, a single unit of work, and then discarded

#### Container-managed entity manager



```java
@PersistenceContext(unitName="EmployeeService")
private EntityManager em;
```

It can be injected by using the @PersistenceContext, and you don't create or manage the lifecycle of the entity manager. This type of entity manager is typically used in Java EE environment.
> NOTICE: if you use Spring-orm for JPA integration, this @PersistenceContext is also supported by Spring bean context.
<a id="d0e129"></a>A container-managed entity manger requires the use of a JTA transaction.

a. Transaction-scoped
It depends on JTA transaction, it use the transaction to trace persistence context.
Every time when an operation is invoked on entity manager, the proxy of the persitence manager checks to see whether a persistence is associated with the container JTA transaction. If yes, just use it. If not, it creates a new persistence context and associates it with the transaction. When the transaction ends, the persistence context goes away.

b. Extended
Specially designed for stateful bean, the persistence context of extended persistence manager lasts until the end of conversation instead of transaction.

#### Application-managed entity manager



```java
EntityManagerFactory emf = Persistence.createEntityManagerFactory("EmployeeService");
EntityManager em = emf.createEntityManager();
```

You can control the entity manager in your application, including its creating, closing, transaction etc. This type of entity manager is typically used in J2SE or some non-EJB Java EE environment.

The best way to create an entity manager is to use the @PersistenceUnit


```java
@PersistenceUnit(unitName="EmpoyeeService")
private EntityManagerFactory emf;
```

When the application-managed entity manager is created, it creates its own private persistence context that lasts until the entity manager is closed.

<a id="d0e134"></a> An application managed entity manager can be either involved in the current JTA transaction (a JTA entity manager), or the transaction may be controlled through the `EntityTransaction` API (a resource-local entity manager).

### Entity states and operations

![entity states](/media/entity-states.png)

### Transaction management

two transaction management types:

1) **resource-local**: using the native transaction of the JDBC driver

2) **JTA**: Jave EE server, supporting multiple participating resources, transaction lifecycle management, and distributed XA transactions. Also know as global transaction.

####  Query Language

### 
