---
title: JavaEE Multi-popuse Application Frameworks
toc: true
id: 1426
comment: false
categories:
  - Project Architecture
date: "2015-09-08T20:32:18+00:00"
---

This is an application framework to satisfy all kinds of purposes.

Firstly, it would support all sort of channels, which I mean a variety of **<span style="background-color: #ffff00;">protocols</span>**, <span style="background-color: #ffff00;">**message formats**</span>, channel-in &amp; chennel-out technologies and application scenarios. Typical ones are like <span style="background-color: #ffff99;">**http, jms, tcp**</span>, <span style="background-color: #ffff00;">web service</span> and formats:<span style="background-color: #ffff00;"> json, soap, FIX, iso 8583, customized fix-length</span>.

Secondly, it would abstract the channel-related processing, and make business logic development separate from these channel details. For example, we need to make our service layer transparent for the channel, and not aware of the protocols, devices, client types.

A key design consideration is we use a **data bus conception**. We can extract all the channel-related data into this data bus. This data bus is the data context going through all the processing of a request.

And to acquire the flexibility and the extensibility, we use an **interceptor** chain mechanism., which is very similar to the Servlet filters, or the Spring interceptor.

For the business logic, we designed the **Process and Action concepts**. Basically, a Process is a flow of processing, which can combine multiple actions and can be configured as complicated flow just like the workflow definition.

For the **channel adaptor**, we need to implement them one by one. For example, we implement the HTTP channel just using Spring MVC and extending it. For the TCP channel, we use the Netty library to implement the server and client.

And another important thing is the **message transformation**. All the data format can be defined by XML. and you can freely use all the message format for response or request. The transformation happens automatically.

The following is a high-level conception diagram:

&nbsp;
