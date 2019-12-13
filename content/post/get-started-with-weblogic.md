---
title: Get started with WebLogic
toc: true
id: 348
categories:
  - Middlewares
date: "2015-02-12T07:43:05+00:00"
---

### Concepts:

Domain, Admin Server, Managed Server, Node Manager
<table width="1016">
<tbody>
<tr>
<td style="font-weight: 400;" width="308">**<span style="color: #808000;">WebSphere</span>**</td>
<td style="font-weight: 400;" width="708">**<span style="color: #808000;">WebLogic</span>**</td>
</tr>
<tr>
<td style="font-weight: 400;" width="308">Profile</td>
<td style="font-weight: 400;" width="708">Domain</td>
</tr>
<tr>
<td style="font-weight: 400;" width="308">Node Agent</td>
<td style="font-weight: 400;" width="708">Node Manager</td>
</tr>
<tr>
<td></td>
<td style="font-weight: 400;" width="708">Admin Server</td>
</tr>
<tr>
<td style="font-weight: 400;" width="308">Application Server</td>
<td style="font-weight: 400;" width="708">Managed Server</td>
</tr>
</tbody>
</table>
WebSphere has standalone version and Network Deploy version (ND). In ND version, there is a central management node and another application nodes. And the node agent is a communication bridge among the management unit.

In WebLogic, the concepts are clearer and the management function is more intuitive.

![weblogic](/media/weblogic.png)

&nbsp;

This figure is a typical plan of a WebLogic domain. In it, all the node managers, managed servers, and admin server are seperated JVM processes. It's worth noticing that machine 3 has two managed server processes.

As mentioned previously, node manager is also a process running on a physical or virtualised machine called Node for responding the management tasks from admin server.

A managed server is managed by admin server, and is a standard J2EE application server including many services and servlet / EJB container which you can deploy your enterprise or web application into.

Admin server provides all sorts of management and a web admin console. In a domain, there can be only one admin server.
