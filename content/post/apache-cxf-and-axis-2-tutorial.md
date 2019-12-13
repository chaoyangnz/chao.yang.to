---
title: Apache CXF and Axis 2 tutorial
toc: true
tags:
  - TODO
id: 934
categories:
  - Web Service
date: "2015-04-18T19:47:09+00:00"
---

### Apache Axis2

#### How to build a service

Axis2 supports 3 programming model

##### Create a service from scratch

*   Write a class whose methods use OMElement as arguments and/or return type
*   create a services.xml
*   package  as a .aar file
*   deploy

##### POJO for rpc-style services

##### Generate the service skeleton from WSDL

&nbsp;
<table border="２" width="100%" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td valign="top" width="12%">

**实现方式**

</td>
<td valign="top" width="22%">

**创建方式**

</td>
<td valign="top" width="14%">

**应用文档对象模型解析(**消息绑定)

</td>
<td valign="top" width="50%">**　　　应用(数据绑定框架)解析****　(xml to DO)** 可插入的数据绑定</td>
</tr>
<tr>
<td valign="top" width="12%">

**POJOs**

</td>
<td valign="top" width="22%">

pojo结合rpc模式

</td>
<td valign="top" width="14%">

**AXIOM**

</td>
<td valign="top" width="50%">**　　　　　POJOs**POJO+RPC

优点：自己写客户端，容易出错，不用熟悉WSDL。

缺点：用此方法进行调试不容易</td>
</tr>
<tr>
<td valign="top" width="12%">

**AXIOM**

</td>
<td valign="top" width="22%">

axiom api从底层写

</td>
<td valign="top" width="14%">

**AXIOM**

</td>
<td valign="top" width="50%">_　　　　　　　无_优点：可以进行高效的 SOAP 消息处理

缺点：全部自己实现，不现实。</td>
</tr>
<tr>
<td valign="top" width="12%">

**ADB**

</td>
<td rowspan="3" valign="top" width="22%">

从wsdl生成

相应框架，

然后填写相

应逻辑

</td>
<td valign="top" width="14%">

**AXIOM**

</td>
<td valign="top" width="50%">**　　　　　　ADB**开发符合软件工程的过程，先写设计，定接口　　（写WSDL）然后再实现。用WSDL生成代码，不用关　　心底层的调用。</td>
</tr>
<tr>
<td valign="top" width="12%">

**XMLBeans**

</td>
<td valign="top" width="14%">

**AXIOM**

</td>
<td valign="top" width="50%">　　　　　　XMLBean(BEA)</td>
</tr>
<tr>
<td valign="top" width="12%">

**JiBX**

</td>
<td valign="top" width="14%">

**AXIOM**

</td>
<td valign="top" width="50%">　　　　　　　JiBX</td>
</tr>
</tbody>
</table>
&nbsp;

#### How to deploy a service

Often axis2 uses its own package named .aar, and the layout of this archive is like:


```java
\---BookService
    \---META-INF
    |    services.xml
    |
    \---lib
    |    dependency1.jar
    |    dependency2.jar
    |
    \---com
        +---company
        |    Service.class
        |
```

But this is mainly for a standalone deployment scenario.

##### embedded in .war

Most of time, we embed our service into our web application and the service implementation will reuse the application logic.

In embedded mode, we needn't package as a .aar. Just a directory in WEB-INF/services is OK.


```java
\---book
    |   pom.xml
    |   README
    |
    \---src
        +---main
        |   |   log4j.properties
        |   |
        |   \---samples
        |       \---demo
        |               Book.java
        |               BookService.java
        |
        +---test
        \---webapp
            \---WEB-INF
                |   web.xml
                \----conf
                |        axis2.xml
                |
                \---services
                    \---BookService
                        \---META-INF
                                services.xml
```

