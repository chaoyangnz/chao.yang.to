---
title: JNDI how-to
toc: true
id: 1149
categories:
  - 'JNDI, LDAP'
date: "2015-05-07T10:09:04+00:00"
---

JNDI - Java Naming and Directory Interface

### Object storing

There are several ways an object satisfies the condition to be bound to a JDNI context.

*   serializable objects: `Serializable`
*   referenceable objects and references: `Reference, ObjectFactory, Referenceable, RefAddr`
*   objects with attributes: `DirContext`
*   remote objects: `Remote`
&nbsp;

Let's look at the real life example: how we bind DataSource and UserTransaction to a JNDI context

#### MySQL



```java
package com.mysql.jdbc.jdbc2.optional;

public class MysqlDataSource extends ConnectionPropertiesImpl implements DataSource, Referenceable, Serializable {

    //....
    public Reference getReference() throws NamingException {
        String factoryName = "com.mysql.jdbc.jdbc2.optional.MysqlDataSourceFactory";
        Reference ref = new Reference(this.getClass().getName(), factoryName, (String)null);
        ref.add(new StringRefAddr("user", this.getUser()));
        ref.add(new StringRefAddr("password", this.password));
        ref.add(new StringRefAddr("serverName", this.getServerName()));
        ref.add(new StringRefAddr("port", "" + this.getPort()));
        ref.add(new StringRefAddr("databaseName", this.getDatabaseName()));
        ref.add(new StringRefAddr("url", this.getUrl()));
        ref.add(new StringRefAddr("explicitUrl", String.valueOf(this.explicitUrl)));

        try {
            this.storeToRef(ref);
            return ref;
        } catch (SQLException var4) {
            throw new NamingException(var4.getMessage());
        }
    }
    //....
}
```

Yes, it implements `Referenceable`, and defines an `ObjectFactory`: `com.mysql.jdbc.jdbc2.optional.MysqlDataSourceFactory`

#### H2



```java
package org.h2.jdbcx;

public class JdbcDataSource extends TraceObject implements XADataSource, DataSource, ConnectionPoolDataSource, Serializable, Referenceable {

    //....
    public Reference getReference() {
        this.debugCodeCall("getReference");
        String var1 = JdbcDataSourceFactory.class.getName();
        Reference var2 = new Reference(this.getClass().getName(), var1, (String)null);
        var2.add(new StringRefAddr("url", this.url));
        var2.add(new StringRefAddr("user", this.userName));
        var2.add(new StringRefAddr("password", this.convertToString(this.passwordChars)));
        var2.add(new StringRefAddr("loginTimeout", String.valueOf(this.loginTimeout)));
        var2.add(new StringRefAddr("description", this.description));
        return var2;
    }
    //....

}
```

It also implements `Referenceable`, and defines an `ObjectFactory`: `org.h2.jdbcx.JdbcDataSourceFactory`

#### JBoss JTA



```java
package com.arjuna.ats.internal.jta.transaction.arjunacore;

public class UserTransactionImple extends BaseTransaction implements UserTransaction, ObjectFactory {
    //....
}
```

You can see, `UserTrancation` implmentation class `UserTransactionImple` doesn't implement `Referenceable`, but actions as `ObjectFactory`.

Now, let's look at the bind code:


```java
package com.arjuna.ats.jta.utils;

public class JNDIManager {
    //....
    public static void bindJTAUserTransactionImplementation(InitialContext initialContext) throws NamingException {
        String utImplementation = getUserTransactionImplementationClassname();
        Reference ref = new Reference(utImplementation, utImplementation, (String)null);
        initialContext.rebind(getUserTransactionJNDIName(), ref);
    }
    //....
}
```

Yes, JBoss JTA binds `UserTransaction` using Reference with its `ObjectFactory`. As said above, `UserTransactionImple` is both the bound target and the `ObjectFactory`.

### Object binding

So once the object can be bound, the binding operation is simple.


```java
context.bind("cn=favorite", fruit);
```


### Object lookup / list / search



```java
context.lookup(..)
```
