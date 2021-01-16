---
title: PostgreSQL Concurrency with MVCC
toc: true
id: 393
categories:
  - Databases
  - Transaction Management
date: "2015-03-04T08:12:20+00:00"
---

<section id="table-of-contents">[https://devcenter.heroku.com/articles/postgresql-concurrency#disadvantages-of-mvcc](https://devcenter.heroku.com/articles/postgresql-concurrency#disadvantages-of-mvcc)

### Table of Contents

*   [How MVCC works](https://devcenter.heroku.com/articles/postgresql-concurrency#how-mvcc-works)
*   [Disadvantages of MVCC](https://devcenter.heroku.com/articles/postgresql-concurrency#disadvantages-of-mvcc)
</section>One of the big selling points of Postgres is how it handles concurrency. The promise is simple: reads never block writes and vice versa. Postgres achieves this via a mechanism called Multi Version Concurrency Control. This technique is not unique to Postgres: there are several databases that implement some form of MVCC including Oracle, Berkeley DB, CouchDB and [many more](http://en.wikipedia.org/wiki/Multiversion_concurrency_control#Databases_with_MVCC). Understanding how MVCC is implemented in Postgres is important when designing highly concurrent apps on PostgreSQL. It’s actually a very elegant and simple solution to a hard problem.

## [How MVCC works](https://devcenter.heroku.com/articles/postgresql-concurrency#how-mvcc-works)

Every transaction in postgres gets a transaction ID called XID. This includes single one statement transactions such as an insert, update or delete, as well as explicitely wrapping a group of statements together via `BEGIN` - `COMMIT`. When a transaction starts, Postgres increments an XID and assigns it to the current transaction. Postgres also stores transaction information on every row in the system, which is used to determine whether a row is visible to the transaction or not.

For example, when you insert a row, postgres will store the XID in the row and call it `xmin`. Every row that has been committed and has an`xmin` that is less than the current transaction’s XID is visible to the transaction. This means that you can start a transaction and insert a row, and until that transaction `COMMIT`s that row will not be visible to other transactions. Once it commits and other transactions get created, they will be able to view the new row because they satisfy the `xmin &lt; XID` condition – and the transaction that created the row has completed.

A similar mechanism occurs for `DELETE`s and `UPDATE`s, only in these cases Postgres stores an `xmax` value on each row in order to determine visibility. This diagram shows two concurrent transactions inserting and reading rows, and how MVCC comes into play in terms of transaction isolation.

For the following charts, assume the following DDL:
<div class="CodeRay">
<div class="code">
<pre><span class="class">CREATE</span> <span class="type">TABLE</span> numbers (value <span class="predefined-type">int</span>);
```

</div>
</div>
![mvcc-1](/media/mvcc-1.png)

While the `xmin` and `xmax` values are hidden from daily operations, you can actually just ask for them and Postgres will hapilly give them to you:
<div class="CodeRay">
<div class="code">
<pre><span class="class">SELECT</span> *, xmin, xmax <span class="keyword">FROM</span> numbers;
```

</div>
</div>
You can also get the XID for the current transaction pretty easily:
<div class="CodeRay">
<div class="code">
<pre><span class="class">SELECT</span> txid_current();
```

</div>
</div>
Neat!

I know what you’re thinking though: what about a two transactions updating the same row at the same time? This is where transaction isolation levels come in. Postgres basically supports two models that allow you to control how this situation should be handled. The default,`READ COMMITTED`, reads the row after the inital transaction has completed and then executes the statement. It basically starts over if the row changed while it was waiting. For instance, if you issue an `UPDATE` with a `WHERE` clause, the `WHERE` clause will rerun after the initial transaction commits, and the `UPDATE` takes place if the `WHERE` clause is still satisfied. Here’s an example of two transactions modifying a row where the initial`UPDATE` causes the `WHERE` clause of the second transaction to return no rows. Therefore the second transaction does not update any rows at all:

![mvcc-2](/media/mvcc-2.png)

If you need finer control over this behavior, you can set the transactionisolation level to `SERIALIZABLE`. With this strategy the above scenario will just fail, because it says “If the row I’m modifying has been modified by another transaction, don’t even try,” and Postgres will respond with the error message `ERROR: could not serialize access due to concurrent update`. It’s up to your app to handle that error and try again, or to give up if that’s what makes sense.

![mvcc-3](/media/mvcc-3.png)

## [Disadvantages of MVCC](https://devcenter.heroku.com/articles/postgresql-concurrency#disadvantages-of-mvcc)

Now that you know how MVCC and transaction isolation actualy works, you’ve added another tool for solving the kinds of problems where a`SERIALIZABLE` isolation level comes in handy. While the advantages of MVCC are clear it also has some disadvantages.

Because different transactions will have visibility to a different set of rows, Postgres needs to maintain potentially obsolete records. This is why an `UPDATE` actually creates a new row and why `DELETE` doesn’t_really_ remove the row: it merely marks it as deleted and sets the XID values appropriately. As transactions complete, there will be rows in the database that cannot possibly be visible to any future transactions. These are called dead rows. Another problem that comes from MVCC is that transaction IDs can only ever grow so much – they are 32 bits and can “only” support around 4 billion transactions. When the XID reaches its max, it will wraparound and start back at zero. Suddenly _all_rows appear to be in future transactions, and no new transactions would have visibility into those rows.

Both dead rows and the transaction XID wraparound problem are solved with `VACUUM`. This should be routine maintenance, but thankfuly Postgres comes with an auto_vacuum daemon that will run at a configurable frequency. It’s important to keep an eye on this because different deployments will have different needs when it comes to vacuum frequency. You can read more about what `VACUUM` actually does on the [Postgres docs](http://www.postgresql.org/docs/current/static/routine-vacuuming.html) and how [Heroku handles it](https://devcenter.heroku.com/articles/heroku-postgres-database-tuning).
