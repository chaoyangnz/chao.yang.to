---
title: Spring framework - the big picture
toc: true
id: 834
categories:
  - Spring
date: "2015-04-03T14:42:08+00:00"
---

Spring is a big beast. I have to sort out its design and modules.

![spring-big-pricture](/media/spring-big-pricture.png)
<table border="1" summary="Spring Framework Artifacts">
<thead>
<tr>
<th align="left" valign="top">GroupId</th>
<th align="left" valign="top">ArtifactId</th>
<th align="left" valign="top">Description</th>
</tr>
</thead>
<tbody>
<tr>
<td align="left" valign="top">org.springframework</td>
<td align="left" valign="top">spring-aop</td>
<td align="left" valign="top">Proxy-based AOP support</td>
</tr>
<tr>
<td align="left" valign="top">org.springframework</td>
<td align="left" valign="top">spring-aspects</td>
<td align="left" valign="top">AspectJ based aspects</td>
</tr>
<tr>
<td align="left" valign="top">org.springframework</td>
<td align="left" valign="top">spring-beans</td>
<td align="left" valign="top">Beans support, including Groovy</td>
</tr>
<tr>
<td align="left" valign="top">org.springframework</td>
<td align="left" valign="top">spring-context</td>
<td align="left" valign="top">Application context runtime, including scheduling and remoting abstractions</td>
</tr>
<tr>
<td align="left" valign="top">org.springframework</td>
<td align="left" valign="top">spring-context-support</td>
<td align="left" valign="top">Support classes for integrating common third-party libraries into a Spring application context</td>
</tr>
<tr>
<td align="left" valign="top">org.springframework</td>
<td align="left" valign="top">spring-core</td>
<td align="left" valign="top">Core utilities, used by many other Spring modules</td>
</tr>
<tr>
<td align="left" valign="top">org.springframework</td>
<td align="left" valign="top">spring-expression</td>
<td align="left" valign="top">Spring Expression Language (SpEL)</td>
</tr>
<tr>
<td align="left" valign="top">org.springframework</td>
<td align="left" valign="top">spring-instrument</td>
<td align="left" valign="top">Instrumentation agent for JVM bootstrapping</td>
</tr>
<tr>
<td align="left" valign="top">org.springframework</td>
<td align="left" valign="top">spring-instrument-tomcat</td>
<td align="left" valign="top">Instrumentation agent for Tomcat</td>
</tr>
<tr>
<td align="left" valign="top">org.springframework</td>
<td align="left" valign="top">spring-jdbc</td>
<td align="left" valign="top">JDBC support package, including DataSource setup and JDBC access support</td>
</tr>
<tr>
<td align="left" valign="top">org.springframework</td>
<td align="left" valign="top">spring-jms</td>
<td align="left" valign="top">JMS support package, including helper classes to send and receive JMS messages</td>
</tr>
<tr>
<td align="left" valign="top">org.springframework</td>
<td align="left" valign="top">spring-messaging</td>
<td align="left" valign="top">Support for messaging architectures and protocols</td>
</tr>
<tr>
<td align="left" valign="top">org.springframework</td>
<td align="left" valign="top">spring-orm</td>
<td align="left" valign="top">Object/Relational Mapping, including JPA and Hibernate support</td>
</tr>
<tr>
<td align="left" valign="top">org.springframework</td>
<td align="left" valign="top">spring-oxm</td>
<td align="left" valign="top">Object/XML Mapping</td>
</tr>
<tr>
<td align="left" valign="top">org.springframework</td>
<td align="left" valign="top">spring-test</td>
<td align="left" valign="top">Support for unit testing and integration testing Spring components</td>
</tr>
<tr>
<td align="left" valign="top">org.springframework</td>
<td align="left" valign="top">spring-tx</td>
<td align="left" valign="top">Transaction infrastructure, including DAO support and JCA integration</td>
</tr>
<tr>
<td align="left" valign="top">org.springframework</td>
<td align="left" valign="top">spring-web</td>
<td align="left" valign="top">Web support packages, including client and web remoting</td>
</tr>
<tr>
<td align="left" valign="top">org.springframework</td>
<td align="left" valign="top">spring-webmvc</td>
<td align="left" valign="top">REST Web Services and model-view-controller implementation for web applications</td>
</tr>
<tr>
<td align="left" valign="top">org.springframework</td>
<td align="left" valign="top">spring-webmvc-portlet</td>
<td align="left" valign="top">MVC implementation to be used in a Portlet environment</td>
</tr>
<tr>
<td align="left" valign="top">org.springframework</td>
<td align="left" valign="top">spring-websocket</td>
<td align="left" valign="top">WebSocket and SockJS implementations, including STOMP suppor</td>
</tr>
</tbody>
</table>
&nbsp;
