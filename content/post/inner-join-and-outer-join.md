---
title: inner join and outer join
toc: true
id: 812
categories:
  - SQL
date: "2015-04-02T15:08:31+00:00"
---

In this blog, we use two tables: TableA and TableB
<table style="height: 85px;" border="1" width="531">
<tbody>
<tr>
<td>id</td>
<td>name</td>
<td>id</td>
<td>name</td>
</tr>
<tr>
<td>--</td>
<td>----</td>
<td>--</td>
<td>-----</td>
</tr>
<tr>
<td>1</td>
<td>Pirate</td>
<td>1</td>
<td>Rutabaga</td>
</tr>
<tr>
<td>2</td>
<td>Monkey</td>
<td>2</td>
<td>Pirate</td>
</tr>
<tr>
<td>3</td>
<td><span style="color: #ff0000;">Ninja </span></td>
<td>3</td>
<td>Darth Vader</td>
</tr>
<tr>
<td>4</td>
<td>Spaghetti</td>
<td>4</td>
<td>Ninja</td>
</tr>
</tbody>
</table>
> inner or outer keyword can be omitted.

### inner join

**Inner join** produces only the set of records that match in both Table A and Table B.
> The inner keyword can be left out from SQL statements.
![inner-join](/media/inner-join-300x197.png)


```java
SELECT * FROM TableA
INNER JOIN TableB
ON TableA.name = TableB.name

id  name       id   name
--  ----       --   ----
1   Pirate     2    Pirate
3   Ninja      4    Ninja
```


### outer join

There are 3 kinds of outer join.
> The outer keyword can be left out from SQL statements.

#### left outer join

**Left outer join** produces a complete set of records from Table A, with the matching records (where available) in Table B. If there is no match, the right side will contain null.

![left-outer-join](/media/left-outer-join-300x197.png)


```java
SELECT * FROM TableA
LEFT OUTER JOIN TableB
ON TableA.name = TableB.name

id  name       id    name
--  ----       --    ----
1   Pirate     2     Pirate
2   Monkey     null  null
3   Ninja      4     Ninja
4   Spaghetti  null  null
```

&nbsp;

#### right outer join

**Right outer join** produces a complete set of records from Table B, with the matching records (where available) in Table A. If there is no match, the left side will contain null.

![right-outer-join](/media/right-outer-join-300x197.png)


```java
SELECT * FROM TableA
RIGHT OUTER JOIN TableB
ON TableA.name = TableB.name
WHERE TableB.id IS null

id  name       id    name
--  ----       --    ----
null null      1     Rutabaga
1    Pirate    2     Pirate
null null      3     Darth Vader
3    Ninja     4     Ninja
```


#### full outer join

**Full outer join** produces the set of all records in Table A and Table B, with matching records from both sides where available. If there is no match, the missing side will contain null.

![full-outer-join](/media/full-outer-join-300x197.png)


```java
SELECT * FROM TableA
FULL OUTER JOIN TableB
ON TableA.name = TableB.name

id    name       id    name
--    ----       --    ----
1     Pirate     2     Pirate
2     Monkey     null  null
3     Ninja      4     Ninja
4     Spaghetti  null  null
null  null       1     Rutabaga
null  null       3     Darth Vader
```


### **cross join (cartesian product)**



```java
SELECT * FROM TableA
CROSS JOIN TableB
```

This joins "everything to everything", resulting in 4 x 4 = 16 rows, far more than we had in the original sets.

&nbsp;

&nbsp;
