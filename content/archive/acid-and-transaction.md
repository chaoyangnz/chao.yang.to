---
title: ACID and transaction
toc: true
id: 882
categories:
  - Databases
date: "2015-04-13T14:49:27+00:00"
---

Transaction provides data integrity in highly concurrent environments. It's also called Unit of Work.

*   For database systems, transaction is a must-have feature. But also some exceptions, like MySQL MyISAM engine.
*   For messaging systems like [JMS](http://en.wikipedia.org/wiki/Java_Message_Service), transactions are not mandatory which has  non-transacted [acknowledgement modes](http://docs.oracle.com/javaee/6/api/javax/jms/Session.html).
*   File system operations are usually non-managed, but if your business requirements demand transaction file operations, you might make use a tool such as [XADisk](https://xadisk.java.net/).

### ACID

Inherently a transaction is characterized by four properties (commonly referred as ACID) :

1.  Atomicity  _- process_
2.  Consistency _- data state_
3.  Isolation _- across transactions_
4.  Durability _- storage_
> Atomicity takes individual operations and turns them into an all-or-nothing unit of work, succeeding if and only if all contained operations succeed.
> 
> 
> Consistency is about the state change. A transaction must always leave the system in a consistent state.
> 
> 
> Isolation brings us the benefit of hiding uncommitted state changes from the outside world (other transactions), as failing transactions shouldn’t ever corrupt the state of the system.Isolation is achieved through [concurrency control](http://en.wikipedia.org/wiki/Concurrency_control) using pessimistic or optimistic locking mechanisms.
> 
> 
> Durability ensure permanently change the state of a system after successful committed transaction, or logging the state changes before commit.
Of the four properties, the atomicity is a fixed property, but everything else may be traded-off for performance/scalability reasons.

### three phenomena and isolation

The SQL standard defines four Isolation levels:

*   READ_UNCOMMITTED
*   READ_COMMITTED
*   REPEATABLE_READ
*   SERIALIZABLE
<table style="height: 152px;" border="1" width="604">
<tbody>
<tr>
<th>Isolation Level</th>
<th>Dirty read</th>
<th>Non-repeatable read</th>
<th>Phantom read</th>
</tr>
<tr>
<td>READ_UNCOMMITTED</td>
<td>allowed</td>
<td>allowed</td>
<td>allowed</td>
</tr>
<tr>
<td>READ_COMMITTED</td>
<td>prevented</td>
<td>allowed</td>
<td>allowed</td>
</tr>
<tr>
<td>REPEATABLE_READ</td>
<td>prevented</td>
<td>prevented</td>
<td>allowed</td>
</tr>
<tr>
<td>SERIALIZABLE</td>
<td>prevented</td>
<td>prevented</td>
<td>prevented</td>
</tr>
</tbody>
</table>
Every isolation level resolves one or multiple phenomena.

#### Dirty read

![acid-dirty-read](/media/acid-dirty-read.gif)

Transaction 2 reads the uncommitted(temporary or dirty) data of transaction 1\. It's called dirty because the change may be discarded if rollback.

#### Non-repeatable read

![acid-non-repeatable-read](/media/acid-non-repeatable-read.gif)

Transaction 2 commits after transaction 1, so the queried record in memory differs in <span style="text-decoration: underline;">_state_ </span>(changed) from that in database when commit.

#### Phantom read

#### ![acid-phantom-read](/media/acid-phantom-read.gif)

Transaction 2 commits after transaction 1, so the queried records in memory differ in <span style="text-decoration: underline;">_range _</span>(more or less) from that in database when commit.

You can regard phantom read is an extensive version of unrepeatable read.

&nbsp;

Even if the SQL standard mandates the use of the SERIALIZABLE isolation level, most database management system use a different default level.
<table border="1">
<tbody>
<tr>
<th>Database</th>
<th>Default isolation Level</th>
</tr>
<tr>
<td>Oracle</td>
<td>READ_COMMITTED</td>
</tr>
<tr>
<td>MySQL</td>
<td>REPEATABLE_READ</td>
</tr>
<tr>
<td>Microsoft SQL Server</td>
<td>READ_COMMITTED</td>
</tr>
<tr>
<td>PostgreSQL</td>
<td>READ_COMMITTED</td>
</tr>
<tr>
<td>DB2</td>
<td>CURSOR STABILITY (a.k.a READ_COMMITTED)</td>
</tr>
</tbody>
</table>
Usually, READ_COMMITED is the right choice, since even SERIALIZABLE cannot protect you from a “lost update” where the read/write happen in different transactions (and web requests).

### a classic database problem - lost update

#### transactional scenario

![lost-update](/media/lost-update.png)

#### offline concurrency scenario

![offline-lost-update](/media/offline-lost-update.png)

In this case, we should differentiate the _**system transaction boundary**_ and _**business transaction boundary**_. All the above discussed is about system transaction.

### locking and MVCC

Two ways for concurrency control:

*   MVCC
*   locking
But as we all know, locking increases the serializable portion of the executed code, affecting [parallelization](http://en.wikipedia.org/wiki/Amdahl%27s_law#Parallelization).

In low level, how the database uses locks or MVCC to control concurrency is very complicated and different from one to another. The explanation is worth explaining in a big book.

Here we consider locking and MVCC in a high level.

(We consider implicit locking is database-specific and ubiquitous when interacting with database using SQL, like read lock, write lock, share lock, mutually exclusive lock, table lock, row lock, range lock, lock escalation etc....the list goes, so many buzzwords!)

Basically, we mention locking just in pessimistic locking context, where locking is explicitly requested. This is from the SQL:
> select .... for update/insert/delete
And we refer to MVCC mainly for <span style="text-decoration: underline;">_application-level_</span> optimistic locking implementation -- version column. Other optimistic locking implementations include:

*   **_old/new value comparison in committing time_**
*   _**checksum or hash computation**_ on original data
