---
title: 'Unified Identity & Access Control system'
toc: true
id: 1161
categories:
  - Project Architecture
date: "2015-05-08T13:14:50+00:00"
---

The target of this system is to build a unified user registry, enterprise organization hierarchy tree, a centralized point for internal user logon and authentication, a flexible Role-Based access control model and an integrated SSO strategy for all other in-house application.

This is the big picture:

![euac](/media/euac.jpg)

### user registry and organization tree

We build a central repository for employee information except their passwords(these are stored in LDAP for convenient look-up and security). These user information includes all kinds of static attributes related to an employee, like the positions, status, department...a long list.

We also sort out a whole organization tree, which represent the hierarchy relations between a department and another, like upper level or subordinate. These relations are especially useful for some application like workflow which exists very commonly in some office tasks.

So we regard the user information and organization is the pre-condition for enterprise integration and process optimization. Yes, it's the foundation and infrastructure for enterprise applications.

We maintain these information in one central place to ensure its integrity. But we share these data and synchronized them with some applications. These applications own their local copy of these information and do querying locally for performance.

### identity / autentication

Because we store the user name and password in LDAP. So the identification mainly depends on this service.

Keeping user name and password in LDAP is because we think the security and look-up performance concern. A dedicated server for the authentication makes things simple. And this is also the traditional way to implement identification.

### RBAC(Role-Based Access Control)

Another important thing for a big enterprise is the access control. If we distribute the access control into the applications themselves, even hard-code these logic instead of configuring them in a declarative way, that will be difficult to mange and there are some risks for security issues. After all, we cannot expect all our applications to make a enough emphasis on the access control problems.

So we implement a centralized access control model. We configure all kinds of application-specific function control in one shared place -- the management console of our system, and in the development of these applications, the developers just need to apply these rules.

Basically, it's according to the stable RBAC principal. We define:

1\. a Role has many Functions. Functions represent the operations of user, basically you can think its a URL.

2\. a User can have many Roles and a Role can have many users.

3\. We also define Menu for some function which is regards the entry point for some module. And the menu has a hierarchy and can form a menu tree. This is useful to organization the functions of the application.

All these relations are stored in one place and can be share to specified application. Internal we support multiple applications, different applications configure their own profile and synchronized their own part of data.

### SSO

We use the cookie-based WebSphere SSO solution. Because websphere application server is the standard application server in my previous company and nearly every J2EE web application uses it.

The following is the basic implementation.

[![sso](/media/sso.jpg)](/media/sso.jpg)

&nbsp;

You can see from the above figure, we use LDAP for an user registry. And any application needing SSO must do some configuration in their websphere management console to get the ability of SSO.

Obviously, this solution has some limitations:

1.  all the application must be under the same domain and accessed by domain name.
2.  all the backend application server must be websphere app server.
3.  use the shared user registry is a precondition.
4.  time must be synchronized so that the timeout of the logon session can be determined
