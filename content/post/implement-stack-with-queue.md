---
title: 'How to implement a Stack with Queue'
toc: true
date: "2016-01-18T20:04:25+00:00"
---

How do you implement a Stack using Queue?

As is known to all, the difference between Stack and Queue is the order to add and remove elements. So it's possible to implement a Stack using Queue by controlling the operation logic.


- push 3 elements to the queue

```

|		|
|	3	|  <- top
|	2	|
|	1	|
```


- pop a element

for the elements other than the top, repush into the queue

```

|		|
|	1	|
|	3	|  <- top       
|	2	|

|		|
|	2	|
|	1	|
|	3	|  <- top
```


until the top, just remove and reset the top

```

|		|
|	2	|  <- top
|	1	|
```


So here is the code:



```java
import org.junit.Test;

import java.util.LinkedList;
import java.util.Queue;

public class QueuedStack<E> {
    private Queue<Wrapper<E>> queue = new LinkedList<>();
    private Wrapper<E> top = null;

    private static class Wrapper<E> {
        private E value;
        public Wrapper(E value) {
            this.value = value;
        }
    }

    public void push(E value) {
        Wrapper<E> elem = new Wrapper<E>(value);
        queue.offer(elem);
        top = elem;
        System.out.printf("⇢ %s %n", value);
    }

    public E pop() {
        if(queue.isEmpty()) return null;

        Wrapper<E> nextTop = null;
        while(true) {
            Wrapper<E> elem = queue.poll();
            if (elem == top) {
                System.out.printf("⇠ %s %n", elem.value);
                top = nextTop;
                return elem.value;
            }

            queue.offer(elem);
            nextTop = elem;
        }
    }

    @Test
    public void test() {
        QueuedStack<String> stack = new QueuedStack<>();
        stack.push("1");
        stack.push("2");
        stack.pop();
        stack.push("3");
        stack.push("4");
        stack.pop();
        stack.push("5");
        stack.push("1");
        stack.pop();
        stack.pop();
        stack.pop();
        stack.pop();
    }
	//    Output
	//    ⇢ 1
	//    ⇢ 2
	//    ⇠ 2
	//    ⇢ 3
	//    ⇢ 4
	//    ⇠ 4
	//    ⇢ 5
	//    ⇢ 1
	//    ⇠ 1
	//    ⇠ 5
	//    ⇠ 3
	//    ⇠ 1
}
```


Why do I need to wrap the value? Because I use the reference to trace the top pointer, if using the value rawly, it will be not unique when some type uses Flyweight pattern to pool some values, like `String` does.


