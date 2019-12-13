---
title: CORS revisit
date: 2018-03-24T22:55:00Z
categories:
- Web
tags:
- Web
toc: true
---

# Origin

The issue stems from the same-origin policy which forces the browsers to restrict the resource access of a different origin (e.g. different domain) when:
- AJAX request
- Web Fonts (for cross-domain font usage in @font-face within CSS)
- Images/video frames drawn to a canvas using drawImage.
- Stylesheets (for CSSOM access).
- others

CORS is a technique for relaxing the same-origin policy and similar techniques include JSONP, or server-side proxy which were used in the past.

# Handling simple CORS request

In the simplest scenario, cross-origin communications don't need preflight requests when satisfying the following:
- `GET`, `POST`, `HEAD`
- content type of a POST request is: `application/x-www-form-urlencoded`, `multipart/form-data`, `text/plain`
- `Origin` header

Then the server will consider the request's `Origin` and either allow or disallow the request. If allowing, then it will respond with:
- `Access-Control-Allow-Origin`




