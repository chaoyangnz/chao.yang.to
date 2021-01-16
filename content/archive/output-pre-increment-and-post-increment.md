---
title: output - pre-increment and post-increment
toc: true
id: 297
categories:
  - Basics
date: "2015-02-02T09:05:24+00:00"
---



```java
    public static void main(String [] args)
    {
        int x= 0;
        int y= 0;
        for (int z = 0; z &lt; 5; z++)
        {
            if (( x++ &gt; 2 ) || (y++ &gt; 2))
            {
                x++;
            }
        }
        System.out.println(x + &quot; &quot; + y);
    }
```


Output is: 7 3



```java
    public static void main(String [] args)
    {
        int x= 0;
        int y= 0;
        for (int z = 0; z &lt; 5; z++)
        {
            if (( ++x > 2 ) || (++y > 2))
            {
                x++;
            }
        }
        System.out.println(x + " " + y);
    }
```


Output is: 8 2

Two things you should notice:
1) defference between pre-increment and post-increment
2) boolean expression:
a. for logic add, if the former condition is false, the following conditions won't be evaluated
b. for logic or, if the former condition is true, the following conditions won't be evaluated
