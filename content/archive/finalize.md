---
title: finalize()
toc: true
id: 588
categories:
  - Basics
date: "2015-03-20T15:29:39+00:00"
---

finalize() method is a protected method of Object class. It's intended to be invoked by GC thread before GC reclaim the memory allocated to the object.

Misguidingly it's regarded as a place for doing some cleaning works, like releasing resources.

_**But this is NOT recommended!!**_

As we cannot know when GC do the thing, so we do some cleaning work becomes **not guaranteed** at all and **uncertain**.

Other drawbacks:

1) **exceptions** thrown in the finalize() method will **be ignored by the GC thread** and will not be propagated further. This is pretty bad, we cannot know what happened and no log left for error handling.

2) Not like the constructor invoking the constructor of its super classes implicitly, you should invoke **super class**'s finalize() **explicitly**.

Runtime has some methods to make best efforts to do something.

*   System.gc()   --- this is a shortcut of Runtime.getRuntime().gc()
*   Runtime.getRuntime().runFinalization()
*   <del>Runtime.runFinalizersOnExit(true)  </del>   this method is deprecated because of being unsafe
**“This method is inherently unsafe. It may result in finalizers being called on live objects while other threads are concurrently manipulating those objects, resulting in erratic behavior or deadlock.”**


```java
class ObjectWithFinalizer {

    protected void finalized() throws Throwable {
        try {
            System.out.println("finalize() invoked");
            int i = 1 / 0; // expect to throw ArithmeticException
        } finally {
            super.finalize();
        }
    }

    public static void main(String[] args) {
        ObjectWithFinalizer o = new ObjectWithFinalizer();
        o = null; // wish GC to reclaim the memory
//        System.gc();
        Runtime.runFinalizersOnExit(true);
    }
}
```

After running the above code, we cannot see the expected result.
