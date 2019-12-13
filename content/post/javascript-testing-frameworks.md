---
categories:
- Javascript
date: 2017-11-27T19:27:00Z
tags:
- Javascript
- Test
title: Javascript testing frameworks 
toc: true
---

I wrote front-end testing code less frequently. I heard a lot of frameworks used by my colleagues, like `Karma`, `Jasmine`, `Chai`, `Mocha`, `Jest`. you name it.

# Why so much complex?

In the Java ecosystem, we have `JUnit` for the test runner, it provides test suites and all kinds of `assert` expressions. If we need to mock some dependencies, we use `Mokito` or others like. That's enough.

So when it comes to Javascript, I am really confused with so many frameworks/libraries. What is worse is I don't know their roles only from their name.

So the following I give a one sentence introduction for each of them.

- Karma: spectacular **test runner** for JavaScript that allows you to execute JavaScript code in multiple real browsers.
- Chai: a BDD / TDD **assertion library** for node and the browser 
- Jasmine: a a Behaviour Driven Development testing framework for JavaScript
- Mocha: simple, flexible, fun javascript test framework
- Jest: another javascript test framework developed by Facebook

So obviously, Jasmine/Mocha/Jest can be of the similar role with some overlays.

# Look at them a bit more

## Jasmine
Jasmine provides with everything you are expected to need for your tests: a running environment, structure, reporting, assertion, and mocking tools. Has widespread Angular support.

One word: ready-to-go

## Mocha

Mocha is usually used with third party assertion, mocking, and spying tools (usually Enzyme and Chai).

One word: flexible and extensible

## Jest

Like Jasmine, everything is there.

One word: performance


See more in [A complete guide to testing Javascript in 2017](https://medium.com/powtoon-engineering/a-complete-guide-to-testing-javascript-in-2017-a217b4cd5a2a)


