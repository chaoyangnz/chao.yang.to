---
title: WebSphere SSO how-to
toc: true
id: 1163
categories:
  - SSO
date: "2015-05-08T13:19:49+00:00"
---

### **SSO定义**

单点登录的英文名称为Single Sign-On，简写为SSO。在用户有权限访问的多个系统间，只要用户在其中一个系统做过认证(Authentication)后，就不用再次做认证即可以访问其他系统。

IBM对SSO有一个形象的解释：“单点登录、全网漫游”。

#### **实现原理**

目前主流SSO的实现方案中实现机制不尽相同，大体分为Cookie机制和Session机制两大类。

##### **基于Session的机制**

WebLogic通过Session共享认证信息。Session是一种服务器端机制，当客户端访问服务器时，服务器为客户端创建一个唯一的SessionID，以使在整个交互过程中始终保持状态，而交互的信息则可由应用自行指定，因此用Session方式实现SSO，不能在多个浏览器之间实现单点登录，但却可以跨域。

##### **基于Cookie的机制**

WebSphere通过Cookie记录认证信息。Cookie是一种客户端机制，它存储的内容主要包括: 名字、值、过期时间、路径和域，路径与域合在一起就构成了Cookie的作用范围，因此用Cookie方式可实现SSO，但域名必须相同。目前大部分SSO产品采用的是Cookie机制，WAS、CAS也是如此。

注意，这种Cookie机制要求实现单点登录的应用，其域名必须是相同的。例如：a.bankcomm.com, b.bankcomm.com就可以经过配置后实现SSO。

### **WAS SSO机制介绍**

#### **WAS SSO机制原理**

WAS使用Cookie实现单点登录的原理图如图4所示。

![sso-1](/media/sso-1.jpg)

图 4  使用Cookie实现单点登录的原理图

*   某个应用程序首先要发起第1次认证。第一次访问应用程序时会显示登录页面。
*   用户在登录页面中，输入用户名和密码。
*   然后应用服务器会对用户名和密码进行认证。认证本身并不是应用服务器的功能，因此，通常会引入某种认证机制。认证机制可以有很多种，例如自己写一个认证程序，或者使用一些标准的认证方法，例如LDAP或者数据库等等。在大多数情况下，会使用LDAP进行认证。这是因为LDAP在处理用户登录方面，有很多独特的优势。
*   认证通过之后，页面重定向，回到Web应用。Web应用此时就完成了成功的登录。
*   然后应用服务器会在客户端创建一个Cookie，Cookie里面保存了LTPA令牌。Cookie是在用户的客户端，而不是服务端创建一个Cookie。这个Cookie是一个加密的Cookie，其中保存了用户登录的信息。
*   如果用户此时希望进入其他Web应用程序，此时WAS不再要求用户输入用户名和密码，而是首先自动寻找Cookie，根据Cookie中保存的信息，进行登录。登录之后，WAS重定向回到用户的应用程序。
这样，就不再需要用户继续输入用户名和密码，从而实现了单点登录。

这种单点登录体系中，并没有通过http进行密码的传递（但是有用户名的传递），因此是十分安全的。

##### **LDAP简介**

LDAP是英文“Lightweight Directory Access Protocol”的简称，中文翻译为轻量级目录访问协议。它是一种因特网上的访问协议，主要用于从服务器上检索信息。

LDAP服务器用来保存信息，中文有时候也称为目录服务。数据库使用“表”来储存数据，可以把一个数据库想象为日常生活中的“表格”。而LDAP用“树”来存储数据，可以把它想象为日常生活中的“电话号码簿页”，或者干脆把LDAP想象为大家常用的文件系统，就是由文件夹和文件组成的那种树形结构。

由于LDAP的结构方式不同，因此LDAP有一个很特殊的特性，就是查询数据的速度特别快，但是写数据的速度较慢。LDAP的另一个特性就是比较适合于按照层次组织信息，例如企业的组织结构等。

由于LDAP的查询速度比数据库快，因此经常用于诸如地址簿、用户帐号存储、组织结构信息、域名解析系统等需要大量查询的领域。

首先，我们可以很清楚地看到，LDAP是按照树形目录进行组织的。

假设公司的域名是macromedia.com，则LDAP首先使用域名作为整个树的根，并且用dc=com，dc=macromedia这样的标记来进行标注。dc的意思指“Domain Component”。LDAP中以后会大量使用这些令人费解的缩写。

假设某天macromedia.com这个公司和apple.com这个公司合并，按照LDAP的树形结构，只不过多了一个分叉，而不会导致整棵树需要重新组织，这就是LDAP的好处之一。

dc节点的后面，就是ou节点。ou是Organizational Unit的缩写，可以把ou想象为文件夹，它是用来容纳其他节点的。ou可以对应部门、组或者任何要包含其他节点的东西。

ou下面的节点，可以是另一个ou，也可以是cn节点。cn是“Common Name”的缩写，它是树叶节点，可以把它对比为文件系统中的文件。cn节点通常用来存储用户的信息，例如某个用户的地址等。

这样，各个节点按照树形组织起来，就变成了图5的样子。

![sso-2](/media/sso-2.jpg)

图 5  LDAP结构示意图

##### **LTPA简介**

LTPA(Lightweight Third Party Authentication)技术是IBM的标准。当某用户访问某WebSphere URL时,系统会提示他输入用户名和口令进行登录。这时用户可以输入他的唯一标识符,通过验证后,Web服务器将把该用户的Web 浏览器中显示的Web 站点内容发送回来。在后台,WebSphere入口网站服务器将会建立包含已鉴别使用者认证的单点登录Cookie,并且会一直发送该cookie，而浏览器通常的默认设置是允许接收cookie的,因此用户的浏览器将保存这个cookie。 LTPA cookie是临时的,只在浏览器内存中存留,用户如果关闭浏览器,cookie就会被永久删除。LTPA cookie的特点如下：

(1)LTPA cookie是一种典型的浏览器cookie,它保存的信息表示该用户已经进行了登录。所有的浏览器cookies都有名称等标准属性。LTPA cookie特有的名称是LtpaToken。当配置 SSO时,在配置实用工具中,通常将LTPA cookie称为SSO LTPA“令牌”。LTPA cookie有一个被编码值,隐藏起cookie中包含的重要信息并且通过Internet传输。

(2)LTPA cookie 具有典型的浏览器cookie的相关域的信息，LTPA的实现依赖于具有域信息的浏览器cookies，因此，通常SSO环境必须部署到单一DNS域中,即每台服务器都在同一DNS域中。

(3)在用户已经登录并且该用户的浏览器接收到 LTPA cookie以后,在HTTP通信中不再需要进行特定的配置,浏览器运行的标准方法就是浏览器将自动发送该cookie。浏览器不断地向任何正确的DNS域中的URL目标发送HTTP请求，通过这种途径不断地向外发送LPTA cookie。当SSO服务接收到HTTP请求并且发现请求中包含了LTPA cookie时,服务器将验证cookie，随即可知道该cookie属于哪一位已经登录的用户，服务器就可以允许这个用户对这台服务器进行适当的访问。浏览器的任务就是确定在什么时候应该随同HTTP通信一起发出LTPA cookie。当用户浏览到一个不在同一DNS域中的URL时, 因为该cookie不适用于这个新的DNS域,浏览器则不会发送 LTPA cookie,新的DNS目标的接收服务器就不知道用户是谁,这时会提示用户输入他的用户名和口令。

(4)LTPA cookie是安全的,因为服务器在创建它时，使用一组加密密钥进行了安全加密。加密密钥用于对cookie进行编码,编码后的cookie传送到用户浏览器,而浏览器只对有加密密钥的cookie进行解码和验证cookie的完整性,并随时检测cookie是否被篡改过。在SSO环境中的所有服务器必须共享同一个加密密钥。当SSO服务器接收到HTTP请求并发现其中包含LTPA cookie时,就使用它共享的加密密钥副本验证cookie,这时有效的cookie信息就使服务器能够识别出登录的用户。

SSO服务器使用的安全加密确保了没有任何伪造cookie的机会。没有加密密钥,其他非法 的cookie不会通过验证,伪造的cookie将被忽略。因此，SSO服务器不会被入侵。

在WebSphere Portal环境中,LTPA加密密钥通常在配置SSO时由WebSphere 创建。管理员可以将密钥导出到文件中,然后转移该文件到其他的SSO服务器（例如Domino）,在那里导入密钥。系统的管理维护人员应该非常小心地处理密钥文件,把所有的副本保护好。

##### **LTPA生成机制**

LTPA由以下部分组成：

*   LTPA token 版本（4字节）
*   创建时间（8字节）
*   过期时间（8字节）
*   用户名（可变长度）
*   LTPA 密钥（20字节）
*   接下来分别说明各部分的具体内容：
*   LTPA token 版本，例如0×0001
*   创建时间和过期时间为以十六进制方式表示的 Unix time，例如 2009-04-09 13:52:42 (GMT +8) = 1239256362 = 49DD8D2A。过期时间为 创建时间 + SSO 配置文档的过期时间（LTPA_TokenExpiration域）
*   用户名为 Names 中用户文档的 FullName 域值
*   Domino LTPA 密钥通过 Base64编码后，保存在 SSO 配置文档的 LTPA_Secret 域中。
![sso-3](/media/sso-3.jpg)

图 6  LTPA结构示意图

在这里当然不能将密钥直接发送给浏览器，所以将上述部分合并起来（如上图），计算 SHA-1 校验和。

![sso-4](/media/sso-4.jpg)

图 7  LTPA 密钥Base64编码图

然后用 SHA-1 校验和替换掉 LTPA 密钥，最后再将内容通过 Base64 编码，形成最终的LTPA Token发送给浏览器（如上图）。这样如果 cookie 中的任何内容被修改，校验和就不对了，达到了防篡改的效果。

##### **LTPA验证机制**

WAS根据事先导入的密钥KEY文件，解密LTPA Token内容，验证LTPA是否有效，并判断该LTPA是否超时。

如果LTPA验证通过，将获取LTPA中的用户信息，并再次与LDAP服务器进行验证该用户的有效性。

#### **自定义认证实现方式**

##### **实现原理**

![sso-5](/media/sso-5.jpg)

自定义认证可以通过WAS中使用“独立定制注册表”方式实现认证。

通过在WAS控制台中指定实现了 com.ibm.websphere.security 包中的 UserRegistry 接口的定制注册表（需要开发自定义实现类），但要求用户自定义实现类必须实现com.ibm.websphere.security.UserRegistry的接口。

基于这个原理，我们可以自己编写一个java类，在接口实现中连接LDAP进行认证（可根据需要访问LDAP业务密码等信息），然后WAS再按照原先方式生成LTPA。

##### **配置方式**

###### **配置使用“独立定制注册表”**

![sso-6](/media/sso-6.jpg)

###### **配置“独立定制注册表”使用的“定制注册表类名”等信息**

![sso-7](/media/sso-7.jpg)

###### **与WAS自带LDAP方式比较**

&nbsp;

![](/media/284e6e10c9f9.jpg)

#### **配置方式**

##### **各业务系统端环境配置**

###### **修改web.xml**

设置应用端的web.xml，使得业务平台可以通过SSO访问资源，下例中红色部分就配置了允许访问的资源。


```java
&lt;security-constraint&gt;
    &lt;web-resource-collection&gt;
        &lt;web-resource-name&gt;sso_GUIP&lt;/web-resource-name&gt;
        &lt;url-pattern&gt;/abf_comm/default4portal.jsp&lt;/url-pattern&gt;
        &lt;url-pattern&gt;/abf_comm/main.jsp&lt;/url-pattern&gt;
        &lt;url-pattern&gt;/abf_comm/default.jsp&lt;/url-pattern&gt;
        &lt;url-pattern&gt;/common/fromITC.jsp&lt;/url-pattern&gt;
        &lt;url-pattern&gt;/itc_agent/agentsync/export.jsp&lt;/url-pattern&gt;
    &lt;/web-resource-collection&gt;
    &lt;auth-constraint&gt;
        &lt;role-name&gt;AllAuthUser&lt;/role-name&gt;
    &lt;/auth-constraint&gt;
    &lt;user-data-constraint&gt;
        &lt;transport-guarantee&gt;NONE&lt;/transport-guarantee&gt;
    &lt;/user-data-constraint&gt;
&lt;/security-constraint&gt;
```


##### **业务平台端环境配置**

###### **修改web.xml**

需要在web.xml安全认证配置部分中增加用户登录的URL，红色部分需要根据应用修改，修改为允许访问的资源


```java
&lt;security-constraint&gt;
    &lt;web-resource-collection&gt;
        &lt;web-resource-name&gt;sso_EUIF&lt;/web-resource-name&gt;
        &lt;url-pattern&gt;/abf_comm/default4portal.jsp&lt;/url-pattern&gt;
    &lt;/web-resource-collection&gt;
    &lt;auth-constraint&gt;
        &lt;role-name&gt;AllAuthUser&lt;/role-name&gt;
    &lt;/auth-constraint&gt;
    &lt;user-data-constraint&gt;
        &lt;transport-guarantee&gt;NONE&lt;/transport-guarantee&gt;
    &lt;/user-data-constraint&gt;
&lt;/security-constraint&gt;
&lt;login-config&gt;
    &lt;auth-method&gt;FORM&lt;/auth-method&gt;
    &lt;form-login-config&gt;
        &lt;form-login-page&gt;/abf_comm/login4portal.jsp&lt;/form-login-page&gt;
        &lt;form-error-page&gt;/abf_comm/loginfailed4portal.jsp&lt;/form-error-page&gt;
    &lt;/form-login-config&gt;
&lt;/login-config&gt;
&lt;security-role&gt;
    &lt;role-name&gt;AllAuthUser&lt;/role-name&gt;
&lt;/security-role&gt;
```

修改welcome页面配置，这页面是登陆成功后系统指向的页面。


```java
&lt;welcome-file-list&gt;
    &lt;welcome-file&gt;/abf_comm/default4portal.jsp&lt;/welcome-file&gt;
&lt;/welcome-file-list&gt;
```

**重启应用服务器**

正确设置上述配置后，需要重新启动应用服务器。

#### **WAS环境配置（将根据WAS7版本进行更新）**

##### **设置WAS的LTPA协议**

进入WAS控制台，URL是http://IP:consoleport/admin,输入安装时设置的用户名密码。

&nbsp;

###### **设置安全角色到用户/组映射**

![sso-8](/media/sso-8.jpg)

![sso-9](/media/sso-9.jpg)

###### **点击进入“安全管理、应用程序和基础结构”，设置安全性**

![sso-10](/media/sso-10.jpg)

###### **设置LDAP连接**

![sso-11](/media/sso-11.jpg)

###### **设置域名**

![sso-12](/media/sso-12.jpg)

###### **需要导入密钥文件，密钥文件可以从其他的was的控制台导出。**

![sso-13](/media/sso-13.jpg)

###### **保存配置**

![sso-14](/media/sso-14.jpg)

注意：任何修改都需要保存到主配置才能生效。

##### **设置WAS的Session的cookie值**

###### **不同应用系统配置唯一Cookie值，防止多应用集成session丢失**

![sso-15](/media/sso-15.jpg)

##### **重启应用服务器**

进入应用服务器所在目录，重新启动服务器。

以WAS为例，需要输入如下命令：

#cd /epost/was61/IBM/WebSphere/AppServer/profiles/[profileName]/bin

停止server

./stopServer.sh server1 -username wpsadmin -password password

启动server

./startServer.sh server1

#### **使用方式**

##### **要求及条件**

*   所有系统的底层平台都是采用WebSphere
*   系统本身的认证交给WebSphere底层平台来做
*   所有系统采用同一个LDAP或数据一致的LDAP来做认证
*   所有服务器必须在同一个网络域中
*   用全域名方式访问
*   所有服务器必须保证时间一致

##### **场景描述**

![sso-16](/media/sso-16.jpg)

1) 用户成功登录新一代UI；

2) 在用户的浏览器cookie值中保存WAS生成SSO加密信息(LTPA令牌)；

3) 用户通过新一代UI系统访问其它系统资源；

4) 由于浏览器cookie中保存SSO加密信息，且不过期，所以用户不需要登录，而由各个系统验证LTPA令牌达到访问业务系统资源的目的。

##### **安全性分析**

##### **优势**

*   WAS提供的SSO解决方案中只传递用户名，不传递密码，保证了密码安全。
*   LTPA经过加密处理，不会被暴露在外部；即使token被截取，如果没有密钥和口令，也无法获取到其中的内容。
*   最新版的WAS Ltpa使用先进的加密算法：Random salt；
*   强 AES 密码；
*   对数据签名；
*   对数据加密

##### 存在的风险

WAS SSO机制安全性依赖于密钥（KEY）文件、密钥口令以及有权限读取LDAP信息的帐号和密码。如果这三项全部被人获得，其即可仿造一个在WAS信任域中的系统，并仿造用户信息。

需要做好密钥文件及口令的保管工作，并做好LDAP访问权限的管理，可预防此类风险的发生。

另外，此风险在实际操作过程中难度较大，不容易出现。

### **附录一：常见SSO方案介绍**

#### **使用统一单点登录机制**

前提是所有系统所使用的底层平台必须是同一家公司的，而且系统本身的认证(Authentication)会交给底层平台来做。

WAS的SSO方案就属于这样类型，WAS和Domino等都是IBM的产品，都基于统一的SSO机制，所以它们可以直接支持统一单点登录机制。

![sso-17](/media/sso-17.jpg)

图 1  使用统一单点登录机制方案图

#### **使用第三方系统来做单点登录**

例如IBM Tivoli Access Manager或者Netegrity iteminder。

![sso-18](/media/sso-18.jpg)

图 2  使用第三方系统来做单点登录方案图

#### **利用用户名/密码映射(maping)的方式**

如果有系统提供映射方式的单点登录功能，例如WebSphere Portal Server。

![sso-19](/media/sso-19.jpg)

图 3  利用用户名/密码映射(mappiing)的方式方案图

#### **其他方式**

各个系统约定SSO规则，如：各系统判断cookie里面是否存在用户信息以及加密串，从而实现SSO登录。

### **附录二：单点登录产品介绍**

#### **商业SSO软件**

1) 专门的SSO商业软件

主要有：Netgrity的Siteminder，已经被CA收购。Novell 公司的iChain。RSA公司的ClearTrust等。

2) 门户产品供应商自己的SSO产品，

如： IBM 的Tivoli Access Manager，WAS自带的SSO解决方案，BEA的WLES，Sun 公司的identity Server，Oracle公司的OID等。

上述商业软件一般适用于客户对SSO的需求很高，并且企业内部采用WAS、Domino、SAP、Sieble等系统比较多的情况下。单点登录产品通常需要在应用软件中增加代理模块，而商业SSO产品主要针对大型软件制作了代码模块。

#### **开源SSO软件**

1) Opensso

 https://opensso.dev.java.net/

 OpenSSO基于Sun Java System Access Manager，是Sun公司支持的一个开源的SSO项目。

 OpenSSO体系结构设计合理，功能比较强大。然而缺点是客户端支持不够广泛，似乎只是对基于J2EE的应用支持的比较好。

2) josso

 http://www.josso.org/

 这是另一个Java写的单点登录产品，通常认为比OpenSSO更成熟一些。

 JOSSO支持的客户端包括Java，PHP和ASP。

3) CAS

 http://www.ja-sig.org/products/cas/

 这是耶鲁大学开发的单点登录产品。

 CAS的优点很多，例如设计理念先进、体系结构合理、配置简单、客户端支持广泛、技术成熟等等。以后我们还要仔细介绍。
