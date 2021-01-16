---
title: subquery vs. join
toc: true
id: 824
categories:
  - SQL
date: "2015-04-02T15:41:53+00:00"
---

### what is subquery

**SubQuery in SQL** is a query inside another query


```java
SELECT name FROM City WHERE pincode IN (SELECT pincode FROM pin WHERE zone='west')
```

In this SQL, the part in the brackets is called _**inner query**_, while the part out of the brackets is called _**outer query**_.

#### non-correlated subquery

In non correlated subquery, **inner query doesn't depend on outer query** and can run as stand alone query.

NonCorrelated subquery are used along-with =, IN and NOT IN clause. When you use =, the subquery must return only 1 row.


```java
SELECT COMPANY FROM Stock WHERE LISTED_ON_EXCHANGE = (SELECT RIC FROM Market WHERE COUNTRY='Japan');
```

Here, the inner query is executed before the outer query.

#### correlated subquery

Correlated subqueries are the one in which inner query or subquery reference outer query.

One of the most common _example of correlated subquery_ is using keywords exits and not exits.


```java
SELECT m.NAME FROM Market m WHERE m.RIC EXISTS (SELECT s.LISTED_ON_EXCHANGE FROM Stock s WHERE s.LISTED_ON_EXCHANGE=m.RIC);
```

Here, the outer query is executed without _WHERE_ clause before the inner query.

### which is preferable: subquery or join

Now, almost any subquery can be written in join.


```java
id  name    id  title
--  ----    --  ----
1   Kenny   1   Analyst
1   Rob     2   Sales
4   Molly   3   Manager
1   Greg
2   John

-- non correlated subquery using IN
-- Returns 3 records - Kenny, Rob, and Greg
SELECT  *
FROM    tableA
WHERE   tableA.id IN (SELECT tableB.id FROM tableB WHERE title = 'Analyst');

-- correlated subquery using EXISTS
SELECT  *
FROM    tableA
WHERE   EXISTS (SELECT 1 FROM tableB WHERE title = 'Analyst' AND tableA.id = tableB.id);

-- JOIN (INNER is the default when only JOIN is specified)
SELECT  *
FROM    tableA
JOIN    tableB
    ON  tableA.id = tableB.id
WHERE   tableB.title = 'Analyst';
```

Basically, there are some differences:

*   Subquery is more intuitive and relatively easy to understand.
*   In most cases, EXISTS or JOIN will be much more efficient (and faster) than an IN statement.

