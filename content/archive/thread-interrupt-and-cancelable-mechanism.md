---
title: 'thread interrupt and cancelable mechanism'
toc: true
date: "2016-08-23T10:25:25+00:00"
---

To begin with, I issue some questions:
- what does `Thread#interrupt()` do?
- what is the difference between `Thread#isInterrupted()` and `Thread.interrupted`?
- do I have to respond to interruption event?
- when an `InterruptedException` is thrown?
- how should I handle the caught `InterruptedException`?
- how do I implement a cancelable mechanism for my task by polling interruption event?
- apart from `wait()`, `sleep()`, `join()`, are there other scenarios throwing `InterruptedException`?
- can we implement the similar interruption handling ways as `wait()` or like?
- can `LockSupport#park()` respond to interruption and throw `InterruptedException`?
- is interruption event underrated? do we need to especially consider responding interruption when we write multi-thread code?

# thread interruption APIs
Now there are two thread t and u.

Thread interruption is a collaboration mechanism which is NOT peremptory. Thread t can interrupt thread u, and the result depends on whether thread u responds this interruption.

A thread has an interruption status. `Thread#interrupt()` just set this status.
This status can be checked by invoke `Thread#isInterrupted()`. And `Thread.interrupted()` is just a shortcut of getting the interruption status of current thread and then clearing this status.

Additionally, in the following scenarios, the interrupt status will be clear with throwing InterruptedException:
- m.wait(), then thread u will be removed from the wait set of m, after re-locking the m's monitor, throw InterruptedException.
- tt.join(), actually equivalent to tt.wait() until not active, same as the above.
- sleep(), not need to reacquire any lock before throwing InterruptedException.





