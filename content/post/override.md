---
title: Override - some requirements
toc: true
id: 127
categories:
  - OOP
date: "2015-01-16T06:35:35+00:00"
---

#### 1\. Override methods must have the identical method signature as the methods overridden

Return type doesn't belong to the method signature, but Java supports covariant return type for overriding.

Identical signature means:
1) the number of formal arguments must be equal.
2) the type of relevant argument must be the same. The subtype of the methond argument cannot be considered as identical.

#### 2\. Override methods can less restricted in access control



```java

class AA {
    protected void foo() {}
}

class BB extends AA {

    @Override
    public void foo()  {
    }
}
```


We make the foo() method accessible from weaker privilege "protected" to less restricted specifier "public".

#### 3\. Override methods and throw clause

More precisely, suppose that B is a class or interface, and A is a superclass or superinterface of B, and a method declaration n in B overrides or hides a method declaration m in A. Then:

If n has a throws clause that mentions any checked exception types, then m must have a throws clause, or a compile-time error occurs.

For every checked exception type listed in the throws clause of n, that same exception class or one of its supertypes must occur in the erasure (§4.6) of the throws clause of m; otherwise, a compile-time error occurs.

![IMG_2690](/media/IMG_2690.png)



```java

class AA {
    protected void foo() throws IOException {}
}

class BB extends AA {

    //This is naturally OK
//    @Override
//    public void foo() throws IOException {
//    }

    //This is also OK, since EOFException is a subtype of IOException
//    @Override
//    public void foo() throws EOFException {
//    }

    //This is also OK, since we don't increase new checked Exception
//    @Override
//    public void foo() {
//    }

    //This is OK, since RuntimeException is unchecked exception, which is not requested
//    @Override
//    public void foo() throws RuntimeException {
//    }

    //This is OK, since IllegalArgumentException is a subtype of RuntimeException
//    @Override
//    public void foo() throws IllegalArgumentException {
//    }

    //This is OK, since Error is unchecked exception, which is not requested
//    @Override
//    public void foo() throws Error {
//    }

    //This is OK, since AssertionError is a subtype of Error
//    @Override
//    public void foo() throws AssertionError {
//    }

    // Compile error: class AA don't have AccessControlException declaration
//    public void foo() throws AccessControlException {
//    }

    // Compile error: Throwable is also a checked exception class AA don't have Throwable declaration
//    public void foo() throws Throwable {
//    }
}
```

