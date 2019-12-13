---
title: mybatis cache
toc: true
id: 1463
comment: false
categories:
- Uncategorized
date: "2015-11-06T21:41:29+00:00"
---

mybatis的cache是基于statement的, 有人也称之为query cache，以有别于基于id的cache。你需要指定哪些statements来useCache, 哪些statements触发flushCache.

一旦触发了flushCache, cache将invalidate并清空，下次的查询都将从数据库查询，并放入cache

限制：
* 同一个应用，一旦使用cache，需遵循：
1. 查询：先查询cache，命中返回; 未命中，查询数据库后放入cache
2. 修改：修改数据库，flush cache
* 一旦修改了cache区域中数据，一定要flush，否则会出现cache与数据库不一致
* 使用cache的应用，外部程序(工具)直接修改数据库一定要慎重，因为极有可能导致数据不一致

下面是一个例子：

``` xml mybatis-config.xml
<?xml version=”1.0” encoding=”UTF-8” ?>
<!DOCTYPE configuration
        PUBLIC “-//mybatis.org//DTD Config 3.0//EN”
        “http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <settings>
        <setting name=”cacheEnabled” value=”true”/>
    </settings>
    <environments default=”development”>
        <environment id=”development”>
            <transactionManager type=”JDBC”/>
            <dataSource type=”POOLED”>
                <property name=”driver” value=”com.mysql.jdbc.Driver”/>
                <property name=”url” value=”jdbc:mysql://localhost:3306/test”/>
                <property name=”username” value=”root”/>
                <property name=”password” value=””/>
            </dataSource>
        </environment>
    </environments>
    <mappers>
        <mapper resource=”SqlMapper.xml”/>
    </mappers>
</configuration>
```



``` xml SqlMapper.xml
<?xml version=”1.0” encoding=”UTF-8” ?>
<!DOCTYPE mapper
        PUBLIC “-//mybatis.org//DTD Mapper 3.0//EN”
        “http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace=”person”>
    <cache eviction="FIFO"
       flushInterval="60000"
       size="512"
       readOnly="true"/>

<select id="selectPerson" resultType="java.util.HashMap" flushCache="false" useCache="true">
    select * from person
</select>

<insert id="insertPerson">
    insert into person values ('ccccc', 23, 'shenzhen')
</insert>

<insert id="insertStaff">
    insert into staff values ('richard')
</insert>
</mapper>
```



``` java Main.java
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.util.List;
import java.util.Map;

public class Main {

	private static SqlSessionFactory sqlSessionFactory;
	private static BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	
	public static void main(String[] args) throws IOException {
	    String resource = "mybatis-config.xml";
	    InputStream inputStream = Resources.getResourceAsStream(resource);
	    sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
	
	    String command = null;
	    while ((command = br.readLine()) != null) {
	
	        SqlSession sqlSession = sqlSessionFactory.openSession();
	        if(command.equals("q")) {
	            System.out.println("sql query:");
	            List<Map> list = sqlSession.selectList("person.selectPerson");
	            System.out.println(list);
	        } else if(command.equals("i")) {
	            System.out.println("sql insert:");
	            sqlSession.insert("person.insertPerson");
	        } else if(command.equals("is")) {
	            System.out.println("sql insert:");
	            sqlSession.insert("person.insertStaff");
	        }
	
	        sqlSession.commit();
	        sqlSession.close();
	    }
	}
}
```


如何测试：

1. 控制台输入"q"触发查询，可以看到首次查询，会使用sql从数据库查询
2. 这时，我们控制台或数据库客户端(如mysql客户端)再向数据库中插入一些数据
3. 再次控制台输入"q"再次触发查询，日志显示，这次cache命中，直接从cache中读数据，程序外部（客户端）插入的数据并没有得到。很容易解释，因为这些外部的插入，mybatis并不知道。
4. 控制台输入"i"触发插入数据，然后再输入"q"触发查询，这次可以看到，会使用sql从数据库查询。可见insert触发了flushCache
5. 控制台输入"q"触发查询，发现cache命中, 不执行SQL
6. 控制台输入"is"触发插入另一个表的数据，然后再输入"q"触发查询，可以看到这时同样使用sql从数据库查询。可见虽然是不同的表的插入，也将flush整个cache, 因为他们共用位于同一个SqlMapper的namespace中的cache
7. 控制台输入"q"触发查询，发现cache命中, 不执行SQL



```undefined
q
sql query:
2015-11-06 21:39:26,103 [DEBUG] (org.apache.ibatis.logging.commons.JakartaCommonsLoggingImpl.debug(JakartaCommonsLoggingImpl.java:54)) -
Cache Hit Ratio [person]: 0.0
2015-11-06 21:39:26,417 [DEBUG] (org.apache.ibatis.logging.commons.JakartaCommonsLoggingImpl.debug(JakartaCommonsLoggingImpl.java:54)) -
==>  Preparing: select * from person
2015-11-06 21:39:26,447 [DEBUG] (org.apache.ibatis.logging.commons.JakartaCommonsLoggingImpl.debug(JakartaCommonsLoggingImpl.java:54)) -
==> Parameters:
2015-11-06 21:39:26,463 [DEBUG] (org.apache.ibatis.logging.commons.JakartaCommonsLoggingImpl.debug(JakartaCommonsLoggingImpl.java:54)) -
<==      Total: 0
[]
i
sql insert:
2015-11-06 21:39:42,303 [DEBUG] (org.apache.ibatis.logging.commons.JakartaCommonsLoggingImpl.debug(JakartaCommonsLoggingImpl.java:54)) -
==> Preparing: insert into person values (‘ccccc’, 23, ‘shenzhen’)
2015-11-06 21:39:42,304 [DEBUG] (org.apache.ibatis.logging.commons.JakartaCommonsLoggingImpl.debug(JakartaCommonsLoggingImpl.java:54)) -
==> Parameters:
2015-11-06 21:39:42,305 [DEBUG] (org.apache.ibatis.logging.commons.JakartaCommonsLoggingImpl.debug(JakartaCommonsLoggingImpl.java:54)) -
<== Updates: 1
q
sql query:
2015-11-06 21:39:44,711 [DEBUG] (org.apache.ibatis.logging.commons.JakartaCommonsLoggingImpl.debug(JakartaCommonsLoggingImpl.java:54)) -
Cache Hit Ratio [person]: 0.0
2015-11-06 21:39:44,712 [DEBUG] (org.apache.ibatis.logging.commons.JakartaCommonsLoggingImpl.debug(JakartaCommonsLoggingImpl.java:54)) -
==> Preparing: select * from person
2015-11-06 21:39:44,712 [DEBUG] (org.apache.ibatis.logging.commons.JakartaCommonsLoggingImpl.debug(JakartaCommonsLoggingImpl.java:54)) -
==> Parameters:
2015-11-06 21:39:44,718 [DEBUG] (org.apache.ibatis.logging.commons.JakartaCommonsLoggingImpl.debug(JakartaCommonsLoggingImpl.java:54)) -
<== Total: 1
[{city=shenzhen, name=ccccc, age=23}]
q
sql query:
2015-11-06 21:39:50,878 [DEBUG] (org.apache.ibatis.logging.commons.JakartaCommonsLoggingImpl.debug(JakartaCommonsLoggingImpl.java:54)) -
Cache Hit Ratio [person]: 0.3333333333333333
[{city=shenzhen, name=ccccc, age=23}]
is
sql insert:
2015-11-06 21:40:00,431 [DEBUG] (org.apache.ibatis.logging.commons.JakartaCommonsLoggingImpl.debug(JakartaCommonsLoggingImpl.java:54)) -
==> Preparing: insert into staff values (‘richard’)
2015-11-06 21:40:00,432 [DEBUG] (org.apache.ibatis.logging.commons.JakartaCommonsLoggingImpl.debug(JakartaCommonsLoggingImpl.java:54)) -
==> Parameters:
2015-11-06 21:40:00,439 [DEBUG] (org.apache.ibatis.logging.commons.JakartaCommonsLoggingImpl.debug(JakartaCommonsLoggingImpl.java:54)) -
<== Updates: 1
q
sql query:
2015-11-06 21:40:03,310 [DEBUG] (org.apache.ibatis.logging.commons.JakartaCommonsLoggingImpl.debug(JakartaCommonsLoggingImpl.java:54)) -
Cache Hit Ratio [person]: 0.25
2015-11-06 21:40:03,315 [DEBUG] (org.apache.ibatis.logging.commons.JakartaCommonsLoggingImpl.debug(JakartaCommonsLoggingImpl.java:54)) -
==> Preparing: select * from person
2015-11-06 21:40:03,316 [DEBUG] (org.apache.ibatis.logging.commons.JakartaCommonsLoggingImpl.debug(JakartaCommonsLoggingImpl.java:54)) -
==> Parameters:
2015-11-06 21:40:03,317 [DEBUG] (org.apache.ibatis.logging.commons.JakartaCommonsLoggingImpl.debug(JakartaCommonsLoggingImpl.java:54)) -
<== Total: 1
[{city=shenzhen, name=ccccc, age=23}]
q
sql query:
2015-11-06 21:40:04,337 [DEBUG] (org.apache.ibatis.logging.commons.JakartaCommonsLoggingImpl.debug(JakartaCommonsLoggingImpl.java:54)) -
Cache Hit Ratio [person]: 0.4
[{city=shenzhen, name=ccccc, age=23}]

```


