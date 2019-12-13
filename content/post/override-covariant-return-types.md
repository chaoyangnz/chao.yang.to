---
title: 'override:  covariant return types'
toc: true
id: 41
categories:
  - OOP
date: "2015-01-12T03:37:15+00:00"
---



```java


class AA {
    void foo(CharSequence a) {}
    CharSequence bar() { return &quot;1&quot;; }
}

class BB extends AA {
    void foo(String a) {}
    @Override
    String bar() { return &quot;2&quot;; }
}
```


It's obvious that method "bar" overrides successfully.

Java多态时，允许协变的返回类型，但是形式参数列<wbr />表必须一模一样(不支持协变参数类型)！
