---
title: 'A problem with HTTP 206 Partial'
toc: true
date: "2016-01-02T10:41:25+00:00"
tags: 
  - 'Trouble Shooting'
---

Recent days, I encountered a problem about video loading and quicktime Basic Auth.

In our customer's system, when they used older version Safari (say, 8.0.5) and opened a page for video playing, an authorization window popped up for username and password.

I captured the HTTP massage by using Charles proxy on my Mac OSX. I found in Safari 8.0.5, it always sent 2 requests, one from Safari itself and another from Quicktime plugin which was obviously observed from the `User-Agent` header.

I guessed the Safari supported HTML5 `<video>` tag in its WebKit core and also used Quicktime plugin as a fallback option.

I tried the video playing in another QA server but failed to replicate the case. The Safari sent only one request then. But why?

Further analyzing the HTTP traffic, I found the responses from the two servers were different. The customer's server responded with `200 OK` and a full length video, while the QA server responded with `206 Partial Content` and the specified range of content, subsequently a serial of partial requests being sent.

It seemed Safari always requested partial content when it found the content-type was video or something like that. And the request headers included a `Range` to indicate what range of content was expected to return.

If the expectation cannot be fulfilled, Safari stopped to process the response, so the video would keep loading and fail at last. This is just my conclusion and explanation.

In the process of trouble shooting, I learnt to what is HTTPS and the knowledge about cryptography.

Basically, in Java, we need to know some security APIs:

- message digest: `MD2`, `MD5`, `checksum`, `HMAC`
- private key cryptography: `DES`, `AES`
- public key cryptography: `RSA`
- digital signature: private key encrypted message digest
- digital certificate: CA, digital signature, public key
- Java specified: key store related APIs

I got the complete knowledge by reading the excellent book [The art of Encryption and Decryption about Java](http://www.amazon.cn/Java%E5%8A%A0%E5%AF%86%E4%B8%8E%E8%A7%A3%E5%AF%86%E7%9A%84%E8%89%BA%E6%9C%AF-%E6%A2%81%E6%A0%8B/dp/B00H1FXX4W/ref=sr_1_1?ie=UTF8&qid=1451709046&sr=8-1&keywords=java+%E5%8A%A0%E5%AF%86) and [java security tutorial](http://www.ibm.com/developerworks/java/tutorials/j-sec1/j-sec1.html) in IBM developerworks.


