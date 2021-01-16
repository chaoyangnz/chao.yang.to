---
title: 'Oracle Statement-Level ROLLBACK'
toc: true
date: "2016-03-01T10:45:25+00:00"
---


Oracle Statement-Level ROLLBACK (undo of a transaction)

# Purpose

Use the ROLLBACK statement to [undo](http://gerardnico.com/wiki/database/oracle/undo) work done in the current [transaction](http://gerardnico.com/wiki/database/oracle/transaction) or to manually undo the work done by an in-doubt distributed transaction.

# Articles Related

[Oracle Database - Deadlock](http://gerardnico.com/wiki/database/oracle/deadlock)
[Oracle Database - Distributed Transactions](http://gerardnico.com/wiki/database/oracle/distributed_transaction)
[Oracle Database - Locks](http://gerardnico.com/wiki/database/oracle/lock)
[Oracle Database - Row Locks (TX)](http://gerardnico.com/wiki/database/oracle/row_lock)
[Oracle Database - TableSpace](http://gerardnico.com/wiki/database/oracle/tablespace)
[Oracle Database - Transactions](http://gerardnico.com/wiki/database/oracle/transaction)
[Oracle Database - UNDO (Rollback Segment)](http://gerardnico.com/wiki/database/oracle/undo)
[PL/SQL - Autonomous Transactions (Pragma)](http://gerardnico.com/wiki/plsql/autonomous_transaction)

# Type

## Implicit

An implicit ROLLBACK occurs when the session (or program) abnormally terminates.

## Explicit

An explicit commit occurs when the ROLLBACK statement is executed.

# Syntax

Rollback to savepoint
ROLLBACK [WORK] TO SAVEPOINT;
If the savepoint is unknown, Oracle will display a warning but the transaction can continue.

Rollback. Rollback the whole transaction ignoring the savepoints.
ROLLBACK

# Statement-Level Rollback

Oracle Database supports statement-level atomicity, which means that a SQL statement is an atomic unit of work and either completely succeeds or completely fails.

A successful statement is different from a committed transaction. A single SQL statement executes successfully if the database parses and runs it without error as an atomic unit, as when all rows are changed in a multirow update.

If a SQL statement causes an error during execution, then it is not successful and so all effects of the statement are rolled back. This operation is a statement-level rollback. This operation has the following characteristics:

A SQL statement that does not succeed causes the loss only of work it would have performed itself.

The unsuccessful statement does not cause the loss of any work that preceded it in the current transaction. For example, if the execution of the second UPDATE statement in Figure 10-1 causes an error and is rolled back, then the work performed by the first UPDATE statement is not rolled back. The first UPDATE statement can be committed or rolled back explicitly by the user.

The effect of the rollback is as if the statement had never been run.
Any side effects of an atomic statement, for example, triggers invoked upon execution of the statement, are considered part of the atomic statement. Either all work generated as part of the atomic statement succeeds or none does.

An example of an error causing a statement-level rollback is an attempt to insert a duplicate primary key. Single SQL statements involved in a deadlock, which is competition for the same data, can also cause a statement-level rollback. However, errors discovered during SQL statement parsing, such as a syntax error, have not yet been run and so do not cause a statement-level rollback.

# Example



```sql
gerardnico@orcl>CREATE TABLE big_table AS SELECT * FROM all_objects WHERE 1=0;
 
TABLE created.
 
gerardnico@orcl>SELECT COUNT(*) FROM big_table;
 
  COUNT(*)
----------
         0
 
gerardnico@orcl>INSERT INTO big_table SELECT * FROM all_objects
 
66651 rows created.
gerardnico@orcl>SELECT COUNT(*) FROM big_table;
 
  COUNT(*)
----------
     66651
 
gerardnico@orcl>ROLLBACK;
 
ROLLBACK complete.
 
gerardnico@orcl>SELECT COUNT(*) FROM big_table;
 
  COUNT(*)
----------
         0
         
```


# Reference

[Rollback](http://download.oracle.com/docs/cd/B28359_01/server.111/b28286/statements_9021.htm#SQLRF01610)

