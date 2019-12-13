---
title: MySQL InnoDB Locking and Isolation Level
toc: true
tags:
  - InnoDB
  - MySQL
id: 1324
categories:
  - Databases
date: "2015-06-10T16:59:43+00:00"
---

### Basic knowledge

#### Isolation Level

> MySQL innodb engine support ACID appliance. (The following will be always applied to innodb)
Innodb supports 4 Isolation Levels: READ UNCOMMITTED, READ COMMITTED, UNREPEATABLE READ, SERIALIZABLE

Innodb's Isolation level is slightly different from the standard SQL Isolation level definition:
<table style="height: 122px;" border="1" width="715">
<tbody>
<tr>
<td></td>
<td>Read Uncommitted</td>
<td>Read Committed</td>
<td>Repeatable Read</td>
<td>Serializable</td>
</tr>
<tr>
<td>dirty reads</td>
<td>yes</td>
<td>no</td>
<td>no</td>
<td>no</td>
</tr>
<tr>
<td>unrepeatable reads</td>
<td>yes</td>
<td>yes</td>
<td><span style="background-color: #ff00ff;">**no**</span></td>
<td>no</td>
</tr>
<tr>
<td>phantom reads</td>
<td>yes</td>
<td>yes</td>
<td><span style="background-color: #ff00ff;">**no**</span></td>
<td>no</td>
</tr>
</tbody>
</table>
MySQL suggests to use Read Uncommitted and Serializable rarely.

Repeatable Read is the default Isolation Level, which can ensure repeatable reads and no phantom reads.

#### Index structure

InnoDB has two types of index: clustered index, secondary indexes

Clustered index is synonymous with the [primary key](http://dev.mysql.com/doc/refman/5.7/en/glossary.html#glos_primary_key "primary key"), which means the record is stored in the leaf node of the index B+ Tree.

The key of leaf is the primary key. Even if you haven't specified any primary key, innodb will generate a hidden one for you.

Secondary indexes are the ones other than the primary index. It doesn't store the record data, but has a pointer to the primary index.

![primary_index](/media/primary_index.png)


```java
CREATE TABLE article (
  id INT UNSIGNED NOT NULL AUTO_INCREMENT,
  userid VARCHAR(20) NOT NULL,
  blogid CHAR(4),
  pubtime TIMESTAMP NOT NULL,
  comment VARCHAR(1024),
  PRIMARY KEY(id),
  KEY when_who (pubtime, userid)
) ENGINE=InnoDB DEFAULT CHARSET latin1;
```

![mysql_index](/media/mysql_index.png)

#### Locking

MySQL support multiple granularity locking .

##### Lock modes

&nbsp;

*   row-level:  `S` lock and `X` lock (`S` - shared/read,  `X` - exclusive/write)
*   table-level: `IS` lock and `IX` lock (`IS` - intention shared/read, `IX` - intention exclusive/write)
<div class="informaltable">
<table style="height: 159px;" border="1" summary=" Compatibility matrix showing which combinations of&lt;br /&gt;&lt;br /&gt;&lt;br /&gt;&lt;br /&gt;&lt;br /&gt;&lt;br /&gt;&lt;br /&gt;
          lock types X, IX, S, and IS are allowed. Each cell in the&lt;br /&gt;&lt;br /&gt;&lt;br /&gt;&lt;br /&gt;&lt;br /&gt;&lt;br /&gt;&lt;br /&gt;
          matrix is marked as either &quot;Compatible&quot; or &quot;Conflict&quot;.&lt;br /&gt;&lt;br /&gt;&lt;br /&gt;&lt;br /&gt;&lt;br /&gt;&lt;br /&gt;&lt;br /&gt;
" width="464"><colgroup> <col /> <col /> <col /> <col /> <col /></colgroup>
<thead>
<tr>
<th scope="col"></th>
<th scope="col">_`X`_</th>
<th scope="col">_`IX`_</th>
<th scope="col">_`S`_</th>
<th scope="col">_`IS`_</th>
</tr>
</thead>
<tbody>
<tr>
<td scope="row">_`X`_</td>
<td><span style="background-color: #ff0000;">Conflict</span></td>
<td><span style="background-color: #ff0000;">Conflict</span></td>
<td><span style="background-color: #ff0000;">Conflict</span></td>
<td><span style="background-color: #ff0000;">Conflict</span></td>
</tr>
<tr>
<td scope="row">_`IX`_</td>
<td><span style="background-color: #ff0000;">Conflict</span></td>
<td><span style="background-color: #00ff00;">Compatible</span></td>
<td><span style="background-color: #ff0000;">Conflict</span></td>
<td><span style="background-color: #00ff00;">Compatible</span></td>
</tr>
<tr>
<td scope="row">_`S`_</td>
<td><span style="background-color: #ff0000;">Conflict</span></td>
<td><span style="background-color: #ff0000;">Conflict</span></td>
<td><span style="background-color: #00ff00;">Compatible</span></td>
<td><span style="background-color: #00ff00;">Compatible</span></td>
</tr>
<tr>
<td scope="row">_`IS`_</td>
<td><span style="background-color: #ff0000;">Conflict</span></td>
<td><span style="background-color: #00ff00;">Compatible</span></td>
<td><span style="background-color: #00ff00;">Compatible</span></td>
<td><span style="background-color: #00ff00;">Compatible</span></td>
</tr>
</tbody>
</table>
</div>
[`SELECT ... LOCK IN SHARE MODE`](http://dev.mysql.com/doc/refman/5.1/en/select.html "13.2.8 SELECT Syntax") sets an _`IS`_  lock and [`SELECT ... FOR UPDATE`](http://dev.mysql.com/doc/refman/5.1/en/select.html "13.2.8 SELECT Syntax") sets an _`IX `_lock.

A lock is granted to a requesting transaction if it is compatible with existing locks, but not if it conflicts with existing locks. A transaction waits until the conflicting existing lock is released.

Intention locks do not block anything except full table requests (for example, `LOCK TABLES ... WRITE`)

##### Lock types

*   Record lock: This is a lock on an index record.
*   Gap lock: This is a lock on a gap between index records, or a lock on the gap before the first or after the last index record.
*   Next-key lock: This is a combination of a record lock on the index record and a gap lock on the gap before the index record. A next-key lock is an index-record lock plus a gap lock on the gap preceding the index record.
InnoDB uses clustered index and the row data are always in the leaf of the index B+ Tree. Thus, the row-level locks are actually index-record locks

#### Nonlocking - Consistent Nonlocking reads

Consistent read is the default mode in which `InnoDB` processes [`SELECT`](http://dev.mysql.com/doc/refman/5.1/en/select.html "13.2.8 SELECT Syntax") statements in [`READ COMMITTED`](http://dev.mysql.com/doc/refman/5.1/en/set-transaction.html#isolevel_read-committed) and[`REPEATABLE READ`](http://dev.mysql.com/doc/refman/5.1/en/set-transaction.html#isolevel_repeatable-read) isolation levels.

In `REPEATABLE READ`,  all consistent reads within the same transaction read the snapshot established by **the first such read** in that transaction.

In `READ COMMITTED`, each consistent read within a transaction sets and reads its own fresh snapshot. (snapshot update at every read statement)

MVCC is used to naturally implement REPEATABLE READ.

#### Locking in different Isolation Level

<table style="height: 594px;" border="1" width="1125">
<tbody>
<tr>
<td colspan="2"></td>
<td>Read Uncommitted(RU)</td>
<td>Read Committed(RC)</td>
<td>Repeatable Read(RR)</td>
<td>Serializable</td>
</tr>
<tr>
<td>consistent reads</td>
<td>[`SELECT ... FROM`](http://dev.mysql.com/doc/refman/5.6/en/select.html "13.2.9 SELECT Syntax")</td>
<td>no lockearlier version might be used(dirty read)</td>
<td>no lockeach consistent read reads its own fresh snapshot</td>
<td>no lockall consistent reads read the snapshot established by the first read</td>
<td>implicitly converts [`SELECT`](http://dev.mysql.com/doc/refman/5.6/en/select.html "13.2.9 SELECT Syntax") statements to [`SELECT ... LOCK IN SHARE MODE`](http://dev.mysql.com/doc/refman/5.6/en/select.html "13.2.9 SELECT Syntax")</td>
</tr>
<tr>
<td rowspan="4">locking reads</td>
<td>[`SELECT ... FROM ... LOCK IN SHARE MODE`](http://dev.mysql.com/doc/refman/5.6/en/select.html "13.2.9 SELECT Syntax")</td>
<td rowspan="4"> like RC</td>
<td rowspan="4">locks only index records, no gap locks except for foreign-key constraint checking and duplicate-key checking</td>
<td rowspan="4">unique index: locks only the index record foundother search condition: locks the index range scanned, using [gap locks](http://dev.mysql.com/doc/refman/5.6/en/glossary.html#glos_gap_lock "gap lock") or [next-key locks](http://dev.mysql.com/doc/refman/5.6/en/glossary.html#glos_next_key_lock "next-key lock")</td>
<td rowspan="4">like RR</td>
</tr>
<tr>
<td>[`SELECT ... FROM ... FOR UPDATE`](http://dev.mysql.com/doc/refman/5.6/en/select.html "13.2.9 SELECT Syntax")</td>
</tr>
<tr>
<td>[`UPDATE ... WHERE ...`](http://dev.mysql.com/doc/refman/5.6/en/update.html "13.2.11 UPDATE Syntax")</td>
</tr>
<tr>
<td>[`DELETE FROM ... WHERE ...`](http://dev.mysql.com/doc/refman/5.6/en/delete.html "13.2.2 DELETE Syntax")</td>
</tr>
<tr>
<td></td>
<td>[`INSERT`](http://dev.mysql.com/doc/refman/5.6/en/insert.html "13.2.5 INSERT Syntax")</td>
<td colspan="4">index-record exclusive lock, not a next-key lockif a duplicate-key error occurs, a shared lock on the duplicate index record is set.</td>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</tbody>
</table>
