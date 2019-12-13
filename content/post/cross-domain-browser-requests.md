---
title: Cross-domain Browser Requests
toc: true
id: 374
categories:
  - Javascript
date: "2015-02-16T06:48:09+00:00"
---

#### Problem origin

Browsers prohibit scripts in a page from one domain to request some resources from another domain.

Take a scenario as an example:

A page named 1.html from http://dev.company.com/1.html has a script requesting a resource from another domain by use of url: http://sit.company.com/dosomething. This case is called **Cross Domain Request**. This is due to the browser's **Same-origin Policy.**

This is only limited to Javascript request like AJAX, and not limited to <span style="text-decoration: underline;">normal resource references</span>:

1\. hypertext link by _&lt;a href="..."&gt;_

_2\. _image by_ &lt;img src="..."&gt;_

_3\. external _css stylesheet link by_ &lt;link type="text/css" href="..."&gt;_

4\. external javascript by _&lt;script type="text/javascript" src="..."&gt;_

5\. form action by_ &lt;form action="..."&gt;_

6\. iframe by &lt;iframe src="..."&gt;

#### How to resolve this issue

##### 1\. JSONP

JSONP, aka JSON with Padding. It actually makes use of the normal external javascript reference. a HTML `&lt;script&gt;` element specifies for its `src` attribute a URL that returns JSON.

![web-basics-jsonp](/media/web-basics-jsonp.jpg)

For example, in the page of url http://localhost:8080/aes-gui/simple/poc/main/sum:


```java
&lt;script type="text/javascript"&gt;
function callback_for_response(data) {
// data is the response
}
&lt;/script&gt;

&lt;script type="text/javascript" src="http://DEV:8080/aes-gui/simple/poc/main/add?callback=callback_for_response"&gt;&lt;/script&gt;
```

When the application on domain sit.company.com has received this request, and found the query parameter "callback"(this is a kind of contract, others also use "jsonp" or anything others), then it generates the response like this:


```java
callback_for_response({a: 1, b: 2, c: "3"})
```

You see, the response JSON data {a:1, b:2, c:"3"} is surrounded/padded by the passed parameter "callback_for_response", which is the origin of JSON with Padding.

JSONP has a number of limitations like, it supports only GET requests and not PUT, POST, DELETE, etc and it does not also send headers across.

##### 2\. Proxying

Proxying is so simple that the client doesn't need to know the cross domain requests. It accesses the urls all from the same domain. And the proxy server is responsible for the request to another domain.

In fact, it's more like a solution of system integration.

##### 3. CORS (Cross Origin Resource Sharing)

_CORS_ is an evolution of JSONP.

**CORS** stands for Cross Origin Resource Sharing, which allows you to share GET, POST, PUT, and DELETE requests and CORS is supported by the modern browsers.The CORS make use of 2 requests.

**Request 1:** “**OPTIONS**” request as part of the handshake to determine if cross domain is allowed by the server.

If server allows the request, it adds a response header - **Access-Control-Allow-Origin: '*'**

**Request 2**: **GET**, **POST**, **PUT**, or **DELETE** request that performs the actual operation on the server.

![web-basics-CORS](/media/web-basics-CORS.jpg)

&nbsp;

&nbsp;
