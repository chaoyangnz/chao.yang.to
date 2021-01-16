---
title: 'Inner thread communication: PipedInputStream/PipedOutputStream vs. BlockingQueue'
toc: true
id: 388
categories:
  - Multithreading
date: "2015-02-17T08:39:28+00:00"
---

There are a lot of differences between the two.

For starters, what kind of data do you want to transport? Complex objects will be easier to transport between threads using a Queue, rather than a Pipe. With the Pipes you would have to serialize the objects or transform them in some way to get them through the Stream, then reverse the procedure on the receiving side. With Queues you just put the object in the Queue and pull it out on the consumer.

Another: A [PipedInputStream](http://docs.oracle.com/javase/8/docs/api/java/io/PipedInputStream.html "Java API") (consumer) can only connect to a single [PipedOutputStream](http://docs.oracle.com/javase/8/docs/api/java/io/PipedOutputStream.html "Java API") (producer), and it wouldn't be safe to share the [PipedOutputStream](http://docs.oracle.com/javase/8/docs/api/java/io/PipedOutputStream.html "Java API") with multiple threads without further synchronization. So on the consumer you would need 2 PipedInputStreams, one connecting to each producer's [PipedOutputStream](http://docs.oracle.com/javase/8/docs/api/java/io/PipedOutputStream.html "Java API"). In your situation of 2 producers -&gt; 1 consumer, a thread safe Queue implementation will be easier. Both producers and the consumer can use the same Queue. But that may not be exactly what you want.

A Queue may be easier for the general purpose but if you have pre-written code optimized around stream communication, then perhaps the Pipe connection would be easier to implement - specifically if the data is all primitives or Strings (where PipedReader/Writer could be used).

Which will be faster is hard to predict. If you can get both working safely the only way to make the speed comparison would be to [test](http://www.javaranch.com/unit-testing.jsp "article: evil unit testing"). My guess is that the performance factor will be not be worth comparing though. Either using Streams to communicate or using Queues to collate data will be the better design decision.
