---
title: what does 0.0.0.0 mean?
toc: true
id: 1015
categories:
  - Q/A
date: "2015-04-22T22:46:26+00:00"
---

0.0.0.0, in server context, means "**all IP addresses on the local machine**" (in fact probably, "all IPv4 addresses on the local machine"). So, if your webserver machine has two ip addresses, 192.168.1.1 and 10.1.2.1, and you allow a webserver daemon like apache to listen on 0.0.0.0, it will be reachable at both of those IPs. But only to what can contact those IPs and the web port(s).

The only thing is that you cannot say "**<del>all addresses should have acces_s_</del>**" -- that's done in your firewall(s) and/or the server software and/or other security layers like tcpwrappers.

&nbsp;

Note that, in a different context (routing) 0.0.0.0 usually means the default route (the route to "the rest of" the internet, aside from routes in your local network etc.).

&nbsp;
