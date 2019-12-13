---
title: finally execution in try-catch-finnally blocks
toc: true
id: 455
categories:
  - Basics
date: "2015-03-16T19:02:01+00:00"
---

The finally block will always execute, except for System.exit() is called first, or the thread has terminated or the JVM crashes. Anything that is returned in the finally block will actually **_override_** any exception or returned value that is inside the try/catch block.



```java

public class Finally {

    //exception-free code, try-catch-finally blocks all have returns
    private int scenario1() {
        int i = 0;
        try {
            System.out.println(&quot;Inside try block of testMethod!&quot;);
            i = 100;
            return i;
        } catch(Exception e){
            System.out.println(&quot;Inside catch block of testMethod!&quot;);
            i = 200;
            return i;
        } finally{
            System.out.println(&quot;Inside finally block of testMethod!&quot;);
            i = 300;
            return i;
        }
    }

    //try block throws exception, try-catch blocks all have returns
    private int scenario2() {
        int i = 0;
        try {
            System.out.println(&quot;Inside try block of testMethod!&quot;);
            i = 100/0;
            return i;
        } catch(Exception e){
            System.out.println(&quot;Inside catch block of testMethod!&quot;);
            i = 200;
            return i;
        } finally{
            System.out.println(&quot;Inside finally block of testMethod!&quot;);
            i = 300;
            //return i;
        }
    }

    //try-catch blocks throw exception, try-catch-finally blocks all have returns
    private int scenario3() {
        int i = 0;
        try {
            System.out.println(&quot;Inside try block of testMethod!&quot;);
            i = 100/0;
            return i;
        } catch(Exception e){
            System.out.println(&quot;Inside catch block of testMethod!&quot;);
            i = 200/0;
            return i;
        } finally{
            System.out.println(&quot;Inside finally block of testMethod!&quot;);
            i = 300;
            return i;
        }
    }

    //try block throws exception, try-catch-finally blocks all have returns
    private int scenario4() {
        int i = 0;
        try {
            System.out.println(&quot;Inside try block of testMethod!&quot;);
            i = 100/0;
            return i;
        } catch(Exception e){
            System.out.println(&quot;Inside catch block of testMethod!&quot;);
            i = 200;
            return i;
        } finally{
            System.out.println(&quot;Inside finally block of testMethod!&quot;);
            i = 300;
            return i;
        }
    }

    //try-catch blocks throw exception, try-catch blocks all have returns
    private int scenario5() {
        int i = 0;
        try {
            System.out.println(&quot;Inside try block of testMethod!&quot;);
            i = 100/0;
            return i;
        } catch(Exception e){
            System.out.println(&quot;Inside catch block of testMethod!&quot;);
            i = 200/0;
            return i;
        } finally{
            System.out.println(&quot;Inside finally block of testMethod!&quot;);
            i = 300;
            //return i;
        }
    }

    //try-catch-finally blocks throw exception, try-catch-finally blocks all have returns
    private int scenario6() {
        int i = 0;
        try {
            System.out.println(&quot;Inside try block of testMethod!&quot;);
            i = 100/0;
            return i;
        } catch(Exception e) {
            System.out.println(&quot;Inside catch block of testMethod!&quot;);
            i = 200/0;
            return i;
        } finally{
            System.out.println(&quot;Inside finally block of testMethod!&quot;);
            i = 300;
            return i/0;
        }
    }

    //try and catch both fine; finally throws exception
    private int scenario7() {
        int i = 0;
        try {
            System.out.println(&quot;Inside try block of testMethod!&quot;);
            i = 100;
            return i;
        } catch(Exception e){
            System.out.println(&quot;Inside catch block of testMethod!&quot;);
            i = 200;
            return i;
        } finally{
            System.out.println(&quot;Inside finally block of testMethod!&quot;);
            i = 300;
            return i/0;
        }
    }

    //try and catch both fine; finally doesn't have any return
    private int scenario8() {
        int i = 0;
        try {
            System.out.println(&quot;Inside try block of testMethod!&quot;);
            i = 100;
            return i;
        } catch(Exception e){
            System.out.println(&quot;Inside catch block of testMethod!&quot;);
            i = 200;
            return i;
        } finally{
            System.out.println(&quot;Inside finally block of testMethod!&quot;);
            i = 300;
            //return i;
        }
    }

    @Test
    public void testPlayWithFinally() {
        System.out.println(&quot;\n-----------Senrio1------------------&quot;);
        System.out.println(&quot;Return value: &quot; + scenario1());

        System.out.println(&quot;\n-----------Senrio2------------------&quot;);
        System.out.println(&quot;Return value: &quot; + scenario2());

        System.out.println(&quot;\n-----------Senrio3------------------&quot;);
        System.out.println(&quot;Return value: &quot; + scenario3());

        System.out.println(&quot;\n-----------Senrio4------------------&quot;);
        System.out.println(&quot;Return value: &quot; + scenario4());

//        System.out.println(&quot;\n-----------Senrio5------------------&quot;);
//        System.out.println(&quot;Return value: &quot; + scenario5());

//        System.out.println(&quot;\n-----------Senrio6------------------&quot;);
//        System.out.println(&quot;Return value: &quot; + scenario6());

//        System.out.println(&quot;\n-----------Senrio7------------------&quot;);
//        System.out.println(&quot;Return value: &quot; + scenario7());

        System.out.println(&quot;\n-----------Senrio8------------------&quot;);
        System.out.println(&quot;Return value: &quot; + scenario8());

    }
}
```


/*
-----------Senrio1------------------
Inside try block of testMethod!
Inside finally block of testMethod!
Return value: 300

-----------Senrio2------------------
Inside try block of testMethod!
Inside catch block of testMethod!
Inside finally block of testMethod!
Return value: 200

-----------Senrio3------------------
Inside try block of testMethod!
Inside catch block of testMethod!
Inside finally block of testMethod!
Return value: 300

-----------Senrio4------------------
Inside try block of testMethod!
Inside catch block of testMethod!
Inside finally block of testMethod!
Return value: 300

-----------Senrio5------------------
Inside try block of testMethod!
Inside catch block of testMethod!
Inside finally block of testMethod!

java.lang.ArithmeticException: / by zero

-----------Senrio6------------------
Inside try block of testMethod!
Inside catch block of testMethod!
Inside finally block of testMethod!

java.lang.ArithmeticException: / by zero

-----------Senrio7------------------
Inside try block of testMethod!
Inside finally block of testMethod!

java.lang.ArithmeticException: / by zero

-----------Senrio8------------------
Inside try block of testMethod!
Inside finally block of testMethod!
Return value: 100
*/
