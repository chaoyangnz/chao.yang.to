---
title: JPA - mapping
toc: true
id: 301
categories:
  - JPA
date: "2015-02-06T02:52:11+00:00"
---

### Table mapping



```java
@Entity
@Table(name="EMP", schema="scott")
```


### Basic mapping

<table border="1" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td>Java type</td>
<td>Database type</td>
</tr>
<tr>
<td>String (char, char[])</td>
<td>VARCHAR (CHAR, VARCHAR2, CLOB, TEXT)</td>
</tr>
<tr>
<td>Number (BigDecimal, BigInteger, Integer, Double, Long, Float, Short, Byte)</td>
<td>NUMERIC (NUMBER, INT, LONG, FLOAT, DOUBLE)</td>
</tr>
<tr>
<td>int, long, float, double, short, byte</td>
<td>NUMERIC (NUMBER, INT, LONG, FLOAT, DOUBLE)</td>
</tr>
<tr>
<td>byte[]</td>
<td>VARBINARY (BINARY, BLOB)</td>
</tr>
<tr>
<td>boolean (Boolean)</td>
<td>BOOLEAN (BIT, SMALLINT, INT, NUMBER)</td>
</tr>
<tr>
<td>java.util.Date</td>
<td>TIMESTAMP (DATE, DATETIME)</td>
</tr>
<tr>
<td>java.sql.Date</td>
<td>DATE (TIMESTAMP, DATETIME)</td>
</tr>
<tr>
<td>java.sql.Time</td>
<td>TIME (TIMESTAMP, DATETIME)</td>
</tr>
<tr>
<td>java.sql.Timestamp</td>
<td>TIMESTAMP (DATETIME, DATE)</td>
</tr>
<tr>
<td>java.util.Calendar</td>
<td>TIMESTAMP (DATETIME, DATE)</td>
</tr>
<tr>
<td>java.lang.Enum</td>
<td>NUMERIC (VARCHAR, CHAR)</td>
</tr>
<tr>
<td>java.util.Serializable</td>
<td>VARBINARY (BINARY, BLOB)</td>
</tr>
</tbody>
</table>
_**@Basic**_ annotation is optional.  Any attributes that have no other annotations and do not reference other entities will be automatically mapped as basic, and even serialized if not a basic type. This is auto-mapping.

customize the default mapping

#### lazy fetching

@Basic can specify the lazy fetching mode.


```java
@Basic(fetch=FetchType.LAZY)
```


#### specify the column name - @Column



```java
@Column(name="ADDR")
```


#### large object - @Lob



```java
@Lob
```


#### temporal types for java.util.Date and java.util.Calendar - @Temporal

If you just want to map DATE, TIME, TIMESTAMP to java.sql.Date, java.sql.Time, java.sql.Timestamp, the basic mapping is just OK.


```java
@Temporal(TemporalType.DATE)
private Calendar dob;
```


#### transient state - @Transient



```java
transit private String s;

@Transient private String s;
```


#### enums - @Enumerated



```java
@Basic
@Enumerated(EnumType.STRING)
private Gender gender;
```


### primary Key

*   primitive type: byte, int, short, long, char, (float, double)
*   wrapper classes of primitives: Byte, Integer, Short, Long, Character, (Float, Double)
*   string: String
*   large numeric type: java.math.BigInteger, (java.math.BigDecimal)
*   temporal type: java.sql.Date, java.util.Date
_WARNING: using floating types for primary keys is risky endevour and is not recommended._

there are 4 types of identity generation.

#### Automatic



```java
@Id @GeneratedValue(strategy=GenerationType.AUTO)
private long id;
```

The provider can choose table-based strategy or sequence-based strategy. This strategy may need schema generation and is a choice for development or prototyping. It's not recommended for other cases. You should always specify the other three strategies.

#### Table



```java
@Id @GeneratedValue(stategy=GenerationType.TABLE)
private long id;
```

This will use the default table assumed by the provider. So it's better to declare more detailed generator by giving the specified table.


```java
@TableGenerator(name="Emp_Gen", table="ID_GEN", pkColumnName="GEN_NAME", valueColumnName="GEN_VAL", pkColumnValue="Emp_Gen", initialValue=1000, allocationSize=100000)
@Id @GeneratedValue(generator="Emp_Gen")
private long id;
```


#### Sequence



```java
@Id @GeneratedValue(strategy=GenerationType.SEQUENCE)
private long id;
```

This will also use the default sequence assumed by the provider. But a problem is that this default sequence will be shared by different entity.


```java
@SequenceGenerator(name="Emp_Gen", sequenceName="Emp_Seq", initialValue=1, allocationSize=10000)
@Id @GeneratedValue(generator="Emp_Gen")
private long id;
```

It depends on whether you enable the schema generation. If no, the sequence must already exist; If yes, the SQL to create the sequence is run by the provider.

#### Identity



```java
@Id @GeneratedValue(strategy=GenerationType.IDENTITY)
private long id;
```

Identity column is supported by some databases. Using this mode means the identifier is not available until after commit time.

### Relationship

<table border="2" frame="hsides" rules="groups" cellspacing="0" cellpadding="6">
<thead>
<tr>
<th class="left" scope="col">Relationship</th>
<th class="left" scope="col">Owning-Side</th>
<th class="left" scope="col">Inverse-Side</th>
</tr>
</thead>
<tbody>
<tr>
<td class="left">one-to-one</td>
<td class="left">@OneToOne</td>
<td class="left">@OneToOne(mappedBy="othersideName")</td>
</tr>
<tr>
<td class="left">one-to-many / many-to-one</td>
<td class="left">@ManyToOne</td>
<td class="left">@OneToMany(mappedBy="othersideName")</td>
</tr>
<tr>
<td class="left">many-to-many</td>
<td class="left">@ManyToMany</td>
<td class="left">@ManyToMany(mappedBy ="xxx")</td>
</tr>
</tbody>
</table>
For many-to-many mapping, you can using join table to declare the many-to-many relationship


```java
@ManyToMany
@JoinTable(name = "BOOK_AUTHOR", 
joinColumns = { @JoinColumn(name = "BOOK_ID", referencedColumnName = "id") }, 
inverseJoinColumns = { @JoinColumn(name = "AUTHORID", referencedColumnName = "id") })
```

The three relationships can use <span style="text-decoration: underline;">_fetch=FetchType.LAZY_</span>.

### Inheritance

JPA通过在父类增加@Inheritance(strategy=InheritanceType.xxx)来声明继承关系。A支持3种继承策略：

1.  单表继承（InheritanceType.SINGLE<sub>TABLE），所有继承树上的类共用一张表，在父类指定（</sub>@DiscriminatorColumn）声明并在每个类指定@DiscriminatorValue来区分类型。
2.  类表继承（InheritanceType.JOINED），父子类共同的部分公用一张表，其余部分保存到各自的表，通过join进行关联。
3.  具体表继承（InheritanceType.TABLE<sub>PER</sub><sub>CLASS</sub>)，每个具体类映射到自己的表。
其中1和2能够支持多态，但是1需要允许字段为NULL，2需要多个JOIN关系；3最适合关系数据库，对多态支持不好。具体应用时根据需要取舍。

### Embeddables

### Advanced mapping

#### element collection
