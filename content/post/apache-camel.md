---
title: Apache Camel
toc: true
tags:
  - TODO
id: 925
categories:
  - EIP frameworks
date: "2015-04-18T18:41:54+00:00"
---

### Core concepts

#### Endpoint

it refers to an address or a software entity that supports communication.

#### Component

_it _is confusing terminology; _EndpointFactory_ would have been more appropriate because a `Component` is a factory for creating `Endpoint` instances.

A component has a name, which is the prefix of the URI.

#### CamelContext

the Camel runtime system. You typically have one `CamelContext` object in an application.

#### CamelTemplate

It's previously called CamelClient. It can send a Message or Exchange to an endpoint.

#### Message and Exchange

Message - an abstraction of a single message: request, reply or exception message

Exchange - an abstraction for an exchange of messages, that is, a request message and its corresponding reply or exception message.

`in` message: request

`out` message: reply

`fault` message: expecption

#### Processor

In camel library, there are many processors to implement the design patterns in EIP book.

An application-level developer might implement the `Processor` interface with a class that executes some business logic.

#### Routes, RouteBuilders and Java DSL

&nbsp;

&nbsp;
