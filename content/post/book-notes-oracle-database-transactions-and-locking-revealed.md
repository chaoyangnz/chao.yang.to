---
title: 'Book notes: Oracle Database Transactions and Locking revealed'
toc: true
date: "2016-01-10T20:05:25+00:00"
---

# Chapter 1 Getting Started

The focus of this book: locking, concurrency control, multiversioning, transactions, redo/undo.

These concepts are all interrelated.

## Locking

The database uses *locks* mechanism that allows for concurrency.

Each database implements locking differently: 

- some have page-level locking, others row-level
- some implementations escalate locks from row level to page level, some do not
- some use read locks, others don't
- some implement serializable transactions via locking, others via read-consistent views of data (no locks)

Oracle's locking policy:

* Oracle locks data at the row level on modification. There is **no lock escalation** to a block or table level.
* Oracle never locks data just to read it. There are no locks placed on rows of data simply by reads.
* reads are not blocked by writes or reads.
* writes are not blocked by reads.
* A write is blocked only when another write has already locked the row it was going after.

## Concurrency Control

*Concurrency control* ensures that no two transactions modify the same piece of data at the same time.

## Multiversioning

Multiversioning is related to concurrency control, and it forms the foundation for Oracle's concurrency control mechanism.

Oracle operates a multiversion, read-consistent concurrency model.

This mechanism provides:

- Read-consistent query: query produces consitent result with respect to a time point.
- Nonblocking query: query is never blocked by data writes.

If you understand how multiversioning and read consitency work together, you will always understand the answers you get from the database.

> Oracle doesn't "pre-answer" the query. It doesn't copy the data anywhere when you open a cursor. The cursor opens instantly and it answers the query as it goes along. 
> The cursor just reads data from the table as you fetch from it.

The following example demonstrates how multiversioning is implemented.

![multiversioning-query-delete](/media/multiversioning-query-delete.png)

In sesssion 1, We need to query a table with 1 billion rows. Obviously, you need time to query the data. This query opens the curson at time t1, then retrieves row 20,000 at time t2, until gets the last row at time t5.

So just at the start point t1 of the query, Oracle doesn't know what's row 100,000,000 like.

And session 2 deletes the row 100,000 and commited at time t3 before t4.

From our cursor's perspective, the result is immutable even if it gets the full data until the time t5.

But Oracle doesn't copy all of the preceding data to other location when we opened the cursor; it was actually the DELETE command that preserved our data for us by placing it into a data area called an undo or rollback segment.

So at the time t4, the actual row 100,000 didn't exist, but the read-consistent query can be implemented by getting the data from the undo segment.

## Transactions

A transaction comprises a unit of database work. 

Transactions are a core feature of database technology, which is part of what distinguishes a database from a file system.

Transactions take the database from one consistent state to the next consistent state.

## Redo and Undo

Key to Oracle's durability (recovery) mechanism is redo, and core to multiversioning (read consistency) is undo.

# Chapter 2 Locking and Issues




