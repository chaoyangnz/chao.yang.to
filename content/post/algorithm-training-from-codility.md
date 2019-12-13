---
title: algorithm training from codility
toc: true
id: 998
categories:
  - Algorithm
date: "2015-04-22T15:00:22+00:00"
---

### TRAINING

#### counting elements

When every value of the element in a numeric array is in range. We can get a counter array of which the index of every element is the value and the value is the counter.

![counter-array](/media/counter-array.png)

#### Prefix Sums

Prefix sums array defines as:

![prefix-sums-array](/media/prefix-sums-array.png)

So prefix sums array is very useful to compute the sum of any array slice (contiguous segments of array).

For example:

sum(a<sub>2</sub>...a<sub>5</sub>) = p<sub>6</sub> - p<sub>2</sub>

*   **minimum sum of slice**
sort the prefix sum, and find the minimum difference between neighbor elements.

*   **maximum sum of slice**
the difference between the max and min of the prefix sum.

#### leader

The leader of this sequence is the element whose value occurs more than n/2 times.The array can have zero or only one leader.

![leader](/media/leader.png)

A property is:

After removing a pair of elements of different values, the leader remain the same.

Let's validate it:

*   if the two elements contain one leader, then the original leader occurs &gt; n/2 - 1 = (n-2)/2 in the left (n-2) elements, so it's still the leader of the rest.
*   if the two elements contain no leader, then the original leader occurs &gt; n/2 = (n-2)/2 +1 &gt; (n-2)/2 in the left elements, so it's still the leader of the rest.
![leader-1](/media/leader-1.png)           or    ![leader-2](/media/leader-2.png)

So we loop the array, and every time we remove a pair of elements, if the two equals, we reserve; if not, we remove safely.

The left elements MUST be all the same. But they are just candidate. We need to validate in the original elements.

This algorithm is O(n).

![leader-3](/media/leader-3.png)


```java
public class Leader {

    public static int leader(int[] a) {
        int N = a.length;
        int size = 0; // stack size
        int top = -1; // top value
        for(int i = 0; i &lt; N; ++i) {
            if(size == 0) {
                size += 1;
                top = a[i];
            } else {
                if(top != a[i]) {
                    size -= 1;
                } else {
                    size += 1;
                }
            }
        }

        int cadidate = top;

        // validation
        int count = 0;
        for(int i = 0; i &lt; N; ++i) {
            if(a[i] == cadidate) {
                count++;
            }
        }

        return (count &gt; N/2) ? cadidate : -1;
    }

    public static void main(String[] args) {
        int[] a = {4, 6, 6, 6, 6, 8, 5};

        System.out.println(leader(a));
    }
}
```


#### prime and composite numbers

This kind of algorithm is based on one divisor, we can find the <span style="text-decoration: underline;">**symmetric**</span> divisor.

##### counting divisors

![counting-divisors](/media/counting-divisors.png)

for 1 to<span style="font-family: 'comic sans ms', sans-serif;"> √n<span style="font-family: arial, helvetica, sans-serif;">, divide and count it.</span></span>

##### Primality test

for 2 to <span style="font-family: 'comic sans ms', sans-serif;">√n</span>, divide and check it.

##### Sieve of Eratosthenes

#### Caterpillar method

![caterpillar](/media/caterpillar.png)   ![caterpillar-image](/media/caterpillar-image.png)

This idea is a reminiscent of movements of a caterpillar (毛虫).

*   if we can, we move the right end (front) forward and increase the size of the caterpillar;
*   otherwise, we move the left end (back) forward and decrease the size of the caterpillar.

#### Greedy algorithms

Greedy programming is a method by which a solution is determined based on making the locally optimal choice at any given moment.

The greedy method of solving a task may or may not be the best approach

### PROBLEM SET

http://www.martinkysel.com/codility-solutions/

#### Time Complexity

##### [TapeEquilibrium](https://codility.com/demo/take-sample-test/tape_equilibrium)

**Short Problem Definition:**

Minimize the value |(A[0] + … + A[P-1]) – (A[P] + … + A[N-1])|.

**Complexity**

*   expected worst-case time complexity is O(N);
*   expected worst-case space complexity is O(N)
**Execution:**

In the first run I compute the left part up to the point i and the overall sum _last_. Then I compute the minimal difference between 0..i and i+1..n.

**Solution:**


```java
package TapeEquilibrium;

/*
A non-empty zero-indexed array A consisting of N integers is given. Array A represents numbers on a tape.

Any integer P, such that 0 &lt; P &lt; N, splits this tape into two non-empty parts: A[0], A[1], ..., A[P − 1] and A[P], A[P + 1], ..., A[N − 1].

The difference between the two parts is the value of: |(A[0] + A[1] + ... + A[P − 1]) − (A[P] + A[P + 1] + ... + A[N − 1])|

In other words, it is the absolute difference between the sum of the first part and the sum of the second part.

For example, consider array A such that:

  A[0] = 3
  A[1] = 1
  A[2] = 2
  A[3] = 4
  A[4] = 3
We can split this tape in four places:

P = 1, difference = |3 − 10| = 7
P = 2, difference = |4 − 9| = 5
P = 3, difference = |6 − 7| = 1
P = 4, difference = |10 − 3| = 7
Write a function:

class Solution { public int solution(int[] A); }

that, given a non-empty zero-indexed array A of N integers, returns the minimal difference that can be achieved.

For example, given:

  A[0] = 3
  A[1] = 1
  A[2] = 2
  A[3] = 4
  A[4] = 3
the function should return 1, as explained above.

Assume that:

N is an integer within the range [2..100,000];
each element of array A is an integer within the range [−1,000..1,000].
Complexity:

expected worst-case time complexity is O(N);
expected worst-case space complexity is O(N), beyond input storage (not counting the storage required for input arguments).
Elements of input arrays can be modified.

*/

// you can also use imports, for example:
// import java.util.*;

// you can use System.out.println for debugging purposes, e.g.
// System.out.println("this is a debug message");
import static org.junit.Assert.*;
import org.junit.Test;

import static java.lang.Math.*;
import java.util.*;

public class Solution {
    public int solution(int[] A) {
        // write your code in Java SE 8
        int N = A.length;
        long sum = 0L;
        for(int i=0; i &lt; N; ++i) {
            sum += A[i];
        }

        long diff = Long.MAX_VALUE;

        long[] p = new long[N];
        p[0] = 0L;
        for(int i=1; i &lt; N; ++i) {
            p[i] = p[i-1] + A[i-1];

            //System.out.println(p[i]);

            diff = min(diff, abs(2*p[i] - sum));
        }

        return (int)diff;
    }

    //-------------------------------------------------

    @Test
    public void test1() {
        int[] A = {3, 1, 2, 4, 3};

        int result = solution(A);
        assertEquals(1, result);
    }

}
```


##### [FrogJmp](https://codility.com/demo/take-sample-test/frog_jmp)

**Short Problem Definition:**

Count minimal number of jumps from position X to Y.

**Complexity:**

*   expected worst-case time complexity is O(1);
*   expected worst-case space complexity is O(1).
**Execution:**

Do not use float division if possible!

**Solution:**


```java
package FrogJmp;

/*
A small frog wants to get to the other side of the road. The frog is currently located at position X and wants to get to a position greater than or equal to Y. The small frog always jumps a fixed distance, D.
Count the minimal number of jumps that the small frog must perform to reach its target.
Write a function:
class Solution { public int solution(int X, int Y, int D); }
that, given three integers X, Y and D, returns the minimal number of jumps from position X to a position equal to or greater than Y.
For example, given:
  X = 10
  Y = 85
  D = 30
the function should return 3, because the frog will be positioned as follows:
after the first jump, at position 10 + 30 = 40
after the second jump, at position 10 + 30 + 30 = 70
after the third jump, at position 10 + 30 + 30 + 30 = 100
Assume that:
X, Y and D are integers within the range [1..1,000,000,000];
X ≤ Y.
Complexity:
expected worst-case time complexity is O(1);
expected worst-case space complexity is O(1).

*/

// you can also use imports, for example:
// import java.util.*;

// you can use System.out.println for debugging purposes, e.g.
// System.out.println("this is a debug message");

import org.junit.Test;
import static org.junit.Assert.*;

import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.util.Random;

public class Solution {
    public int solution(int X, int Y, int D) {
        // write your code in Java SE 8
        int count = (Y-X) / D;
        int left = (Y-X) % D;
        return left == 0 ? count : count+1;
    }

    //-------------------------------------------------

    private Random rdm = new Random();
    private BufferedReader br = new BufferedReader(new InputStreamReader(System.in));

    // [from, to)
    private int random(int from, int to) {
        return rdm.nextInt(to - from) + from;
    }

    // A[0]...A[n-1]
    private int random(int[] A) {
        int index = random(0, A.length);
        return A[index];
    }

    @Test
    public void test1() {
        int X = 0, Y = 20, D = 19;
        int expected = 2;

        int actual = solution(X, Y, D);

        assertEquals(expected, actual);
    }

    @Test
    public void test2() {
        int X = 0, Y = 20, D = 20;
        int expected = 1;

        int actual = solution(X, Y, D);

        assertEquals(expected, actual);
    }

    @Test
    public void test3() {
        int X = 0, Y = 20, D = 21;
        int expected = 1;

        int actual = solution(X, Y, D);

        assertEquals(expected, actual);
    }

    @Test
    public void test4() {
        int X = 10, Y = 85, D = 30;
        int expected = 3;

        int actual = solution(X, Y, D);

        assertEquals(expected, actual);
    }
}
```


##### [PermMissingElem](https://codility.com/demo/take-sample-test/perm_missing_elem)

**Short Problem Definition:**

Find the missing element in a given permutation.

**Complexity:**

*   expected worst-case time complexity is O(N);
*   expected worst-case space complexity is O(1)
**Execution:**

Sum all elements that should be in the list and sum all elements that actually are in the list. The sum is 0 based, so +1 is required. The first solution using the + operator can cause int overflow in not-python languages. Therefore the use of a binary XOR is adequate.

**Solution:**


```java
package PermMissingElem;

/*
A zero-indexed array A consisting of N different integers is given. The array contains integers in the range [1..(N + 1)], which means that exactly one element is missing.
Your goal is to find that missing element.
Write a function:
class Solution { public int solution(int[] A); }
that, given a zero-indexed array A, returns the value of the missing element.
For example, given array A such that:
  A[0] = 2
  A[1] = 3
  A[2] = 1
  A[3] = 5
the function should return 4, as it is the missing element.
Assume that:
N is an integer within the range [0..100,000];
the elements of A are all distinct;
each element of array A is an integer within the range [1..(N + 1)].
Complexity:
expected worst-case time complexity is O(N);
expected worst-case space complexity is O(1), beyond input storage (not counting the storage required for input arguments).
Elements of input arrays can be modified

*/

// you can also use imports, for example:
// import java.util.*;

// you can use System.out.println for debugging purposes, e.g.
// System.out.println("this is a debug message");

import org.junit.Test;
import static org.junit.Assert.*;

import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.util.Random;

public class Solution {
    public int solution(int[] A) {
        // write your code in Java SE 8
        long N = A.length;
        long sum = 0;
        for(int i = 0; i &lt; N; ++i) {
            sum += A[i];
        }

        long missing = (1+(N+1)) * (N+1) /2 - sum;

        return  (int) missing;
    }

    //-----------test------------------------------------------------
    @Test
    public void test() {
        int N = 100001;
        int a = (1+(N+1)) * (N+1) /2;
        System.out.println(a);
    }

    @Test
    public void test1() {
        int[] A = new int[100000];
        for(int i = 0; i &lt; 100000; ++i) {
            A[i] = i+1;
        }
        A[0] = 100001;
        System.out.println(A[100000-1]);
        int expected = 1;

        int actual = solution(A);
        System.out.println(actual);

        assertEquals(expected, actual);
    }

}
```


#### Counting Elements

##### [PermCheck](https://codility.com/demo/take-sample-test/perm_check)

**Short Problem Definition:**

Check whether array N is a permutation.

**Complexity:**

*   expected worst-case time complexity is O(N);
*   expected worst-case space complexity is O(N)
**Execution:**

Mark elements as seen in a boolean array. Elements seen twice or out of bounds of the size indicate that the list is no permutation. The check if the boolean array only contains true elements is not required. This solution only works with permutations starting from 1.

**Solution:**


```java
package PermCheck;

/*
A non-empty zero-indexed array A consisting of N integers is given.
A permutation is a sequence containing each element from 1 to N once, and only once.
For example, array A such that:
    A[0] = 4
    A[1] = 1
    A[2] = 3
    A[3] = 2
is a permutation, but array A such that:
    A[0] = 4
    A[1] = 1
    A[2] = 3
is not a permutation, because value 2 is missing.
The goal is to check whether array A is a permutation.
Write a function:
class Solution { public int solution(int[] A); }
that, given a zero-indexed array A, returns 1 if array A is a permutation and 0 if it is not.
For example, given array A such that:
    A[0] = 4
    A[1] = 1
    A[2] = 3
    A[3] = 2
the function should return 1.
Given array A such that:
    A[0] = 4
    A[1] = 1
    A[2] = 3
the function should return 0.
Assume that:
N is an integer within the range [1..100,000];
each element of array A is an integer within the range [1..1,000,000,000].
Complexity:
expected worst-case time complexity is O(N);
expected worst-case space complexity is O(N), beyond input storage (not counting the storage required for input arguments).
Elements of input arrays can be modified.

*/

// you can also use imports, for example:
// import java.util.*;

// you can use System.out.println for debugging purposes, e.g.
// System.out.println("this is a debug message");

import org.junit.Test;
import static org.junit.Assert.*;

import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.util.Random;

public class Solution {
    public int solution(int[] A) {
        // write your code in Java SE 8
        final int N = A.length;
        int[] seen = new int[N + 1];
        for(int i = 0 ;i &lt; N; ++i) {
            if(A[i] &gt; N) return 0; // exceeding the range
            seen[A[i]]++;
        }

        for(int i = 1; i &lt; N+1; ++i) {
            if(seen[i] != 1) return 0; // seen many times
        }

        return 1;
    }

    //-----------------------test------------------------------------

    @Test
    public void test1() {
        int[] A = {4, 1, 3, 2};
        int expected = 1;

        int actual = solution(A);

        assertEquals(expected, actual);
    }

    @Test
    public void test2() {
        int[] A = {4, 1, 3};
        int expected = 0;

        int actual = solution(A);

        assertEquals(expected, actual);
    }

}
```


##### [FrogRiverOne](https://codility.com/demo/take-sample-test/frog_river_one)

**Short Problem Definition:**

Find the earliest time when a frog can jump to the other side of a river.

**Complexity:**

*   expected worst-case time complexity is O(N);
*   expected worst-case space complexity is O(X)
**Execution:**

Mark seen elements as such in a boolean array. I do not like the idea of returning the first second as 0\. But specifications are specifications

**Solution:**


```java
package FrogRiverOne;

/*
A small frog wants to get to the other side of a river. The frog is currently located at position 0, and wants to get to position X. Leaves fall from a tree onto the surface of the river.

You are given a non-empty zero-indexed array A consisting of N integers representing the falling leaves. A[K] represents the position where one leaf falls at time K, measured in minutes.

The goal is to find the earliest time when the frog can jump to the other side of the river. The frog can cross only when leaves appear at every position across the river from 1 to X.

For example, you are given integer X = 5 and array A such that:

  A[0] = 1
  A[1] = 3
  A[2] = 1
  A[3] = 4
  A[4] = 2
  A[5] = 3
  A[6] = 5
  A[7] = 4
In minute 6, a leaf falls into position 5\. This is the earliest time when leaves appear in every position across the river.

Write a function:

class Solution { public int solution(int X, int[] A); }

that, given a non-empty zero-indexed array A consisting of N integers and integer X, returns the earliest time when the frog can jump to the other side of the river.

If the frog is never able to jump to the other side of the river, the function should return −1.

For example, given X = 5 and array A such that:

  A[0] = 1
  A[1] = 3
  A[2] = 1
  A[3] = 4
  A[4] = 2
  A[5] = 3
  A[6] = 5
  A[7] = 4
the function should return 6, as explained above.

Assume that:

N and X are integers within the range [1..100,000];
each element of array A is an integer within the range [1..X].
Complexity:

expected worst-case time complexity is O(N);
expected worst-case space complexity is O(X), beyond input storage (not counting the storage required for input arguments).
Elements of input arrays can be modified.

*/

// you can also use imports, for example:
// import java.util.*;

// you can use System.out.println for debugging purposes, e.g.
// System.out.println("this is a debug message");

import org.junit.Test;

import java.util.Arrays;

import static org.junit.Assert.*;

public class Solution {
    public int solution(int X, int[] A) {
        // write your code in Java SE 8
        final int N = A.length;
        boolean[] seen = new boolean[X+1]; // [0] ignore
        Arrays.fill(seen, false);
        int has_seen = 0;

        for(int i = 0; i &lt; N; ++i) {
            if(seen[A[i]] == false) {
                seen[A[i]] = true;
                has_seen++;
            }

            if(has_seen == X) return i;
        }

        return -1;
    }

    //-----------------------test------------------------------------

    @Test
    public void test1() {
        int X = 5;
        int[] A = {1, 3, 1, 4, 2, 3, 5, 4};
        int expected = 6;

        int actual = solution(X, A);

        assertEquals(expected, actual);
    }

    @Test
    public void test2() {
        int X = 5;
        int[] A = {1, 3, 1, 4, 1, 3, 5, 2};
        int expected = 7;

        int actual = solution(X, A);

        assertEquals(expected, actual);
    }

}
```


##### [MaxCounters](https://codility.com/demo/take-sample-test/max_counters)

**Short Problem Definition:**

Calculate the values of counters after applying all alternating operations: increase counter by 1; set value of all counters to current maximum.

**Complexity:**

*   expected worst-case time complexity is O(N+M);
*   expected worst-case space complexity is O(N)
**Execution:**

The idea is to perform the specified operation as stated. It is not required to iterate over the whole array if a new value is set for all the values. Just save the value and check it when an increase on that position is performed.

**Solution:**


```java
package MaxCounters;

/*
You are given N counters, initially set to 0, and you have two possible operations on them:
increase(X) − counter X is increased by 1,
max counter − all counters are set to the maximum value of any counter.
A non-empty zero-indexed array A of M integers is given. This array represents consecutive operations:
if A[K] = X, such that 1 ≤ X ≤ N, then operation K is increase(X),
if A[K] = N + 1 then operation K is max counter.
For example, given integer N = 5 and array A such that:
    A[0] = 3
    A[1] = 4
    A[2] = 4
    A[3] = 6
    A[4] = 1
    A[5] = 4
    A[6] = 4
the values of the counters after each consecutive operation will be:
    (0, 0, 1, 0, 0)
    (0, 0, 1, 1, 0)
    (0, 0, 1, 2, 0)
    (2, 2, 2, 2, 2)
    (3, 2, 2, 2, 2)
    (3, 2, 2, 3, 2)
    (3, 2, 2, 4, 2)
The goal is to calculate the value of every counter after all operations.
Write a function:
class Solution { public int[] solution(int N, int[] A); }
that, given an integer N and a non-empty zero-indexed array A consisting of M integers, returns a sequence of integers representing the values of the counters.
The sequence should be returned as:
a structure Results (in C), or
a vector of integers (in C++), or
a record Results (in Pascal), or
an array of integers (in any other programming language).
For example, given:
    A[0] = 3
    A[1] = 4
    A[2] = 4
    A[3] = 6
    A[4] = 1
    A[5] = 4
    A[6] = 4
the function should return [3, 2, 2, 4, 2], as explained above.
Assume that:
N and M are integers within the range [1..100,000];
each element of array A is an integer within the range [1..N + 1].
Complexity:
expected worst-case time complexity is O(N+M);
expected worst-case space complexity is O(N), beyond input storage (not counting the storage required for input arguments).
Elements of input arrays can be modified.

*/

// you can also use imports, for example:
// import java.util.*;

// you can use System.out.println for debugging purposes, e.g.
// System.out.println("this is a debug message");

import org.junit.Test;
import static org.junit.Assert.*;

public class Solution {
    public int[] solution(int N, int[] A) {
        // write your code in Java SE 8
        final int M = A.length;

        int[] counters = new int[N];
        int current_max = 0;
        int baseline = 0; // new starting point
        for(int k = 0; k &lt; M; ++k) {
            if(A[k] &lt;= N) {
                counters[A[k]-1] = Math.max(counters[A[k]-1], baseline);
                counters[A[k]-1]++;
                current_max = Math.max(counters[A[k]-1], current_max);
            } else {
                baseline = current_max;
            }
        }

        for(int i = 0; i &lt; N; ++i) {
            counters[i] = Math.max(counters[i], baseline);
        }

        return counters;
    }

    //-----------------------test------------------------------------

    @Test
    public void test1() {
        int N = 5;
        int[] A = {3, 4, 4, 6, 1, 4, 4};
        int[] expected = {3, 2, 2, 4, 2};

        int[] actual = solution(N, A);

        assertArrayEquals(expected, actual);
    }

}
```


##### [MissingInteger](https://codility.com/demo/take-sample-test/missing_integer)

**Short Problem Definition:**

Find the minimal positive integer not occurring in a given sequence.

**Complexity:**

*   expected worst-case time complexity is O(N);
*   expected worst-case space complexity is O(N)
**Execution:**

You only need to consider the first (N) positive integers. In this specification 0 does not count as a valid candidate! Any value that is below 1 or above N can be ignored.

**Solution:**


```java
package MissingInteger;

/*
Write a function:
class Solution { public int solution(int[] A); }
that, given a non-empty zero-indexed array A of N integers, returns the minimal positive integer that does not occur in A.
For example, given:
  A[0] = 1
  A[1] = 3
  A[2] = 6
  A[3] = 4
  A[4] = 1
  A[5] = 2
the function should return 5.
Assume that:
N is an integer within the range [1..100,000];
each element of array A is an integer within the range [−2,147,483,648..2,147,483,647].
Complexity:
expected worst-case time complexity is O(N);
expected worst-case space complexity is O(N), beyond input storage (not counting the storage required for input arguments).
Elements of input arrays can be modified.

*/

// you can also use imports, for example:
// import java.util.*;

// you can use System.out.println for debugging purposes, e.g.
// System.out.println("this is a debug message");

import org.junit.Test;
import static org.junit.Assert.*;

public class Solution {
    public int solution(int[] A) {
        // write your code in Java SE 8
        final int N = A.length;
        boolean[] seen = new boolean[N+1]; // [0] ignored
        for(int i= 0; i &lt; N; ++i) {
            if(A[i] &gt; 0 &amp;&amp; A[i] &lt;= N) {
                seen[A[i]] = true;
            }
        }

        for(int i = 1; i &lt; N+1; ++i) {
            if(seen[i] == false) return i;
        }

        return N+1;
    }

    //-----------------------test------------------------------------

    @Test
    public void test1() {
        int[] A = {1, 3, 6, 4, 1, 2};
        int expected = 5;

        int actual = solution(A);

        assertEquals(expected, actual);
    }

    @Test
    public void test2() {
        int[] A = {-2, 29, -9, 0};
        int expected = 1;

        int actual = solution(A);

        assertEquals(expected, actual);
    }
}
```


#### Prefix Sums

##### [PassingCars](https://codility.com/demo/take-sample-test/passing_cars)

**Short Problem Definition:**

Count the number of passing cars on the road.

**Complexity:**

*   expected worst-case time complexity is O(N);
*   expected worst-case space complexity is O(1)
**Execution:**

Count all cars heading in one direction (west). Each car heading the other direction (east) passes all cars that went west so far. Note that east cars at the beginning of the list pass no cars! Also do not forget the upper limit!

**Solution:**


```java
package PassingCars;

/*
A non-empty zero-indexed array A consisting of N integers is given. The consecutive elements of array A represent consecutive cars on a road.

Array A contains only 0s and/or 1s:

0 represents a car traveling east,
1 represents a car traveling west.
The goal is to count passing cars. We say that a pair of cars (P, Q), where 0 ≤ P &lt; Q &lt; N, is passing when P is traveling to the east and Q is traveling to the west.

For example, consider array A such that:

  A[0] = 0
  A[1] = 1
  A[2] = 0
  A[3] = 1
  A[4] = 1
We have five pairs of passing cars: (0, 1), (0, 3), (0, 4), (2, 3), (2, 4).

Write a function:

class Solution { public int solution(int[] A); }

that, given a non-empty zero-indexed array A of N integers, returns the number of pairs of passing cars.

The function should return −1 if the number of pairs of passing cars exceeds 1,000,000,000.

For example, given:

  A[0] = 0
  A[1] = 1
  A[2] = 0
  A[3] = 1
  A[4] = 1
the function should return 5, as explained above.

Assume that:

N is an integer within the range [1..100,000];
each element of array A is an integer that can have one of the following values: 0, 1.
Complexity:

expected worst-case time complexity is O(N);
expected worst-case space complexity is O(1), beyond input storage (not counting the storage required for input arguments).
Elements of input arrays can be modified.

*/

// you can also use imports, for example:
// import java.util.*;

// you can use System.out.println for debugging purposes, e.g.
// System.out.println("this is a debug message");

import org.junit.Test;
import static org.junit.Assert.*;

public class Solution {
    public int solution(int[] A) {
        // write your code in Java SE 8
        final int N = A.length;

        int east_cars = 0; //before current, how many cars head east?
        int cnt_passing = 0;
        for(int i = 0; i &lt; N; ++i) {
            if(A[i] == 0) {
                east_cars++;
            } else {
                cnt_passing += east_cars;
                if(cnt_passing &gt; 1000000000) return -1;
            }
        }

        return cnt_passing;
    }

    //-----------------------test------------------------------------

    @Test
    public void test1() {
        int[] A = {0, 1, 0, 1, 1};
        int expected = 5;

        int actual = solution(A);

        assertEquals(expected, actual);
    }
}
```


##### [GenomicRangeQuery](https://codility.com/demo/take-sample-test/genomic_range_query)

**Short Problem Definition:**

Find the minimal nucleotide from a range of sequence DNA.

**Complexity:**

*   expected worst-case time complexity is O(N+M);
*   expected worst-case space complexity is O(N)
**Execution:**

Remember the last position on which was the genome (A, C, G, T) was seen. If the distance between Q and P is lower than the distance to the last seen genome, we have found the right candidate.

**Solution:**

##### [MinAvgTwoSlice](https://codility.com/demo/take-sample-test/min_avg_two_slice)

**Short Problem Definition:**

Find the minimal average of any slice containing at least two elements.

**Complexity:**

*   expected worst-case time complexity is O(N);
*   expected worst-case space complexity is O(N)
**Execution:**

Every slice must be of size two or three. Slices of bigger sizes are created from such smaller slices. Therefore should any bigger slice have an optimal value, all sub-slices must be the same, for this case to hold true. Should this not be true, one of the sub-slices must be the optimal slice. The others being bigger. Therefore we check all possible slices of size 2/3 and return the smallest one. The first such slice is the correct one, do not use &lt;=!

**Solution:**


```java
package MinAvgTwoSlice;

/*
A non-empty zero-indexed array A consisting of N integers is given. A pair of integers (P, Q), such that 0 ≤ P &lt; Q &lt; N, is called a slice of array A (notice that the slice contains at least two elements). The average of a slice (P, Q) is the sum of A[P] + A[P + 1] + ... + A[Q] divided by the length of the slice. To be precise, the average equals (A[P] + A[P + 1] + ... + A[Q]) / (Q − P + 1).
For example, array A such that:
    A[0] = 4
    A[1] = 2
    A[2] = 2
    A[3] = 5
    A[4] = 1
    A[5] = 5
    A[6] = 8
contains the following example slices:
slice (1, 2), whose average is (2 + 2) / 2 = 2;
slice (3, 4), whose average is (5 + 1) / 2 = 3;
slice (1, 4), whose average is (2 + 2 + 5 + 1) / 4 = 2.5.
The goal is to find the starting position of a slice whose average is minimal.
Write a function:
class Solution { public int solution(int[] A); }
that, given a non-empty zero-indexed array A consisting of N integers, returns the starting position of the slice with the minimal average. If there is more than one slice with a minimal average, you should return the smallest starting position of such a slice.
For example, given array A such that:
    A[0] = 4
    A[1] = 2
    A[2] = 2
    A[3] = 5
    A[4] = 1
    A[5] = 5
    A[6] = 8
the function should return 1, as explained above.
Assume that:
N is an integer within the range [2..100,000];
each element of array A is an integer within the range [−10,000..10,000].
Complexity:
expected worst-case time complexity is O(N);
expected worst-case space complexity is O(N), beyond input storage (not counting the storage required for input arguments).
Elements of input arrays can be modified.

*/

// you can also use imports, for example:
// import java.util.*;

// you can use System.out.println for debugging purposes, e.g.
// System.out.println("this is a debug message");

import org.junit.Test;
import static org.junit.Assert.*;

public class Solution {
    public int solution(int[] A) {
        // write your code in Java SE 8
        final int N = A.length;
        int min_start_index = 0;
        double min_average = Integer.MAX_VALUE;

        for(int i = 0; i &lt; N-1; ++i) {
            double avg = (A[i] + A[i+1]) / 2.0;
            if(avg &lt; min_average) {
                min_start_index = i;
                min_average = avg;
            }
            if(i+2 &lt; N) {
                avg = (A[i] + A[i+1] + A[i+2]) / 3.0;
                if(avg &lt; min_average) {
                    min_start_index = i;
                    min_average = avg;
                }
            }
        }

        return min_start_index;
    }

    //-----------------------test------------------------------------

    @Test
    public void test1() {
        int[] A = {4, 2, 2, 5, 1, 5, 8};
        int expected = 1;

        int actual = solution(A);

        assertEquals(expected, actual);
    }

}
```

&nbsp;

##### [CountDiv](https://codility.com/demo/take-sample-test/count_div)

**Short Problem Definition:**

Compute number of integers divisible by k in range [a..b].

**Complexity:**

*   expected worst-case time complexity is O(1);
*   expected worst-case space complexity is O(1)
**Execution:**

This little check required a bit of experimentation. One needs to start from the first valid value that is bigger than A and a multiply of K.

**Solution:**

#### Sorting

##### [Triangle](https://codility.com/demo/take-sample-test/triangle)

**Short Problem Definition:**

Determine whether a triangle can be built from a given set of edges.

**Complexity:**

*   expected worst-case time complexity is O(N*log(N));
*   expected worst-case space complexity is O(N)
**Execution:**

By sorting the array, we have guaranteed that P+R &lt; Q and Q+R &lt; P (because R is always the biggest). Now what remains, is the proof that P+Q &gt; R, that can be found out by traversing the array. The chance to find such a combination is with three adjacent values as they provide the highest P and Q.

**Solution:**

##### [Distinct ](https://codility.com/demo/take-sample-test/distinct)

**Short Problem Definition:**

Compute number of distinct values in an array.

**Complexity:**

*   expected worst-case time complexity is O(N * log(N));
*   expected worst-case space complexity is O(N)
**Execution:**

Sorting in both C++ and Python takes N log N time. We know that for this particular problem sorting the array will be the dominant runtime complexity. This is the case for the second python solution. What about the other ones?

The first solution is a neat pythonic way of solving a distinct entries problem. The set is implemented as a hash table so it is possible that it will degrade to a linked list. Therefore the actual worst case would be N^2.

This is not the case with C++ (as code 3 shows). The std set is a Red-Black Tree and therefore has insertion complexity of log N. (overall N log N)

**Solution:**

##### [MaxProductOfThree](https://codility.com/demo/take-sample-test/max_product_of_three)

**Short Problem Definition:**

Maximize A[P] * A[Q] * A[R] for any triplet (P, Q, R).

**Complexity:**

*   expected worst-case time complexity is O(N*log(N));
*   expected worst-case space complexity is O(1)
**Execution:**

After sorting the largest product can be found as a combination of the last three elements. Additionally, two negative numbers add to a positive, so by multiplying the two largest negatives with the largest positive, we get another candidate. If all numbers are negative, the three largest (closest to 0) still get the largest element!

**Solution:**

##### NumberOfDiscIntersections

#### Stacks and Queues

##### [Brackets](https://codility.com/demo/take-sample-test/brackets)

**Short Problem Definition:**

Determine whether a given string of parentheses is properly nested.

**Complexity:**

*   expected worst-case time complexity is O(N);
*   expected worst-case space complexity is O(N)
**Execution:**

Put every opening bracket on a stack. If a closing bracket is not the same as the top stack bracket, the string is not properly nested.

**Solution:**

##### <span style="text-decoration: underline;">Nesting</span>

**Short Problem Definition:**

Determine whether given string of parentheses is properly nested.

**Complexity:**

*   expected worst-case time complexity is O(N);
*   expected worst-case space complexity is O(1)
**Execution:**

Because there is only one type of brackets, the problem is easier than [Brackets](http://www.martinkysel.com/codility-brackets-solution/ "Codility ‘Brackets’ Solution"). Just check if there is always a opening bracket before a closing one.

##### [StoneWall](https://codility.com/demo/take-sample-test/stone_wall)

**Short Problem Definition:**

Cover “Manhattan skyline” using the minimum number of rectangles.

**Complexity:**

*   expected worst-case time complexity is O(N);
*   expected worst-case space complexity is O(N)
**Execution:**

The explanation to this challenge has been posted on the Codility web, you can read it [here](http://blog.codility.com/2012/06/sigma-2012-codility-programming.html).

**Solution:**

##### [Fish](https://codility.com/demo/take-sample-test/fish)

**Short Problem Definition:**

N voracious fish are moving along a river. Calculate how many fish are alive.

**Complexity:**

*   expected worst-case time complexity is O(N);
*   expected worst-case space complexity is O(N)
**Execution:**

Put all downstream swimming fishes on a stack. Any upstream swimming fish has to fight(eat) all fishes on the stack. If there is no fish on the stack, the fish survives. If the stack has some downstream fishes at the end, they also survive.

**Solution:**

#### Leader

##### [Dominator](https://codility.com/demo/take-sample-test/dominator)

**Short Problem Definition:**

Find an index of an array such that its value occurs at more than half of indices in the array.

**Complexity:**

*   expected worst-case time complexity is O(N);
*   expected worst-case space complexity is O(1)
**Execution:**

As explained in the training material…

**Solution:**

##### [EquiLeader](https://codility.com/demo/take-sample-test/equi_leader)

**Short Problem Definition:**

Find the index S such that the leaders of the sequences A[0], A[1], …, A[S] and A[S + 1], A[S + 2], …, A[N – 1] are the same.

**Complexity:**

*   expected worst-case time complexity is O(N);
*   expected worst-case space complexity is O(N)
**Execution:**

Get the leader as in the training material. Afterwards check every position if both sides have enough leader occurrences.

**Solution:**

#### Maximum Slice Problem

##### [MaxProfit](https://codility.com/demo/take-sample-test/max_profit)

**Short Problem Definition:**

Given a log of stock prices compute the maximum possible earning.

**Complexity:**

*   expected worst-case time complexity is O(N);
*   expected worst-case space complexity is O(1)
**Execution:**

Keep the minimal value up to day. The profit on day i is_ profit[i] – min_profit_.

**Solution:**

##### [MaxSliceSum](https://codility.com/demo/take-sample-test/max_slice_sum)

**Short Problem Definition:**

Find a maximum sum of a compact subsequence of array elements.

**Complexity:**

*   expected worst-case time complexity is O(N);
*   expected worst-case space complexity is O(N)
**Execution:**

The only difference to the example given by Codility is the minimal slice length, which is 1.

**Solution:**

##### [MaxDoubleSliceSum](https://codility.com/demo/take-sample-test/max_double_slice_sum)

**Short Problem Definition:**

Find the maximal sum of any double slice.

**Complexity:**

*   expected worst-case time complexity is O(N);
*   expected worst-case space complexity is O(N)
**Execution:**

To solve this task, you need to keep track of two slice arrays. The optimal double slice can be found at an index that has the maximal sum of those two arrays. It can not be the 0th or the last index.

**Solution:**

#### Prime and composite numbers

##### [MinPerimeterRectangle](https://codility.com/demo/take-sample-test/min_perimeter_rectangle)

**Short Problem Definition:**

Find the minimal perimeter of any rectangle whose area equals N.

**Complexity:**

*   expected worst-case time complexity is O(sqrt(N));
*   expected worst-case space complexity is O(1).
**Execution:**

Trivial search for the largest prime.

##### [CountFactors](https://codility.com/demo/take-sample-test/count_factors)

**Short Problem Definition:**

Count factors of given number N.

**Complexity:**

*   expected worst-case time complexity is O(sqrt(N));
*   expected worst-case space complexity is O(1).
**Execution:**

This example can be found in the lesson document.

##### [Peaks](https://codility.com/demo/take-sample-test/peaks)

**Short Problem Definition:**

A non-empty zero-indexed array A consisting of N integers is given. A peak is an array element which is larger than its neighbors.

**Complexity:**

*   expected worst-case time complexity is O(N*log(log(N)));
*   expected worst-case space complexity is O(N)
**Execution:**

I first compute all peaks. Because each block must contain a peak I start from the end and try to find a integral divisor sized block. If each block contains a peak I return the size.

**Solution:**

##### <span style="text-decoration: underline;">Flags</span>

#### Sieve or Eratosthenes

##### [CountSemiprimes](https://codility.com/demo/take-sample-test/count_semiprimes)

**Short Problem Definition:**

Count the semiprime numbers in the given range [a..b]

**Complexity:**

*   expected worst-case time complexity is O(N*log(log(N))+M);
*   expected worst-case space complexity is O(N+M)
**Execution:**

First get all semiprimes from an adaptation of the Sieve of Eratosthenes. Because we will be computing the difference many times a prefix sum is adequate. Get the number of semiprimes up to the point. The index P is decreased by 1 because we want to know all primes that start from P.

**Solution:**

##### [CountNonDivisible](https://codility.com/demo/take-sample-test/count_non_divisible)

**Short Problem Definition:**

Calculate the number of elements of an array that are not divisors of each element.

**Complexity:**

*   expected worst-case time complexity is O(N*log(N));
*   expected worst-case space complexity is O(N)
**Execution:**

Using the Sieve of Eratosthenes, you generate divisors for all input elements of A. If a given number x is a divisor of element _(x*N == element)_, then N also is a divisor. _(N = element//x)._ After all divisors are computed, we simply subtract those (multiplied by their counts or 0) from the total number of elements in A.

**Solution:**

#### Euclidean Algorithm

##### [ChocolatesByNumbers](https://codility.com/demo/take-sample-test/chocolates_by_numbers)

**Short Problem Definition:**

There are N chocolates in a circle. Count the number of chocolates you will eat.

&nbsp;

**Complexity:**

*   expected worst-case time complexity is O(log(N+M));
*   expected worst-case space complexity is O(1)
**Execution:**

N and M meet at their least common multiply. Dividing this LCM by M gets the number of steps(chocolates) that can be eaten.

**Solution:**

##### [CommonPrimeDivisors](https://codility.com/demo/take-sample-test/common_prime_divisors)

**Short Problem Definition:**

Check whether two numbers have the same prime divisors.

**Complexity:**

*   expected worst-case time complexity is O(Z*log(max(A)+max(B))<sup>2</sup>);
*   expected worst-case space complexity is O(1)
**Execution:**

I will post an explaining image soon!

**Solution:**

#### Fibonacci Numbers

##### [FibFrog](https://codility.com/demo/take-sample-test/fib_frog)

**Short Problem Definition:**

Count the minimum number of jumps required for a frog to get to the other side of a river.

**Complexity:**

*   expected worst-case time complexity is O(N*log(N))
*   expected worst-case space complexity is O(N)
**Execution:**

This problem can be solved by in a Dynamic Programming way. You need to know the optimal count of jumps that can reach a given leaf. You get those by either reaching the leaf from the first shore or by reaching it from another leaf.

The N*log(N) time complexity is given by the fact, that there are approximately log(N) Fibonacci numbers up to N and you visit each position once.

As for the sequence hack: there are 26 Fibonacci numbers smaller than 100k, so I just preallocate an array of this size.

**Solution:**

##### [Ladder](https://codility.com/demo/take-sample-test/ladder)

**Short Problem Definition:**

Count the number of different ways of climbing to the top of a ladder.

**Complexity:**

*   expected worst-case time complexity is O(L)
*   expected worst-case space complexity is O(L)
**Execution:**

We first compute the Fibonacci sequence for the first L+2 numbers. The first two numbers are used only as fillers, so we have to index the sequence as A[idx]+1 instead of A[idx]-1\. The second step is to replace the modulo operation by removing all but the n lowest bits. A discussion can be found on [Stack Overflow](http://stackoverflow.com/a/6670766 "Mod of power 2").

**Solution:**

#### Binary Search

##### [MinMaxDivision](https://codility.com/demo/take-sample-test/min_max_division)

##### Short Problem Definition:

Divide array A into K blocks and minimize the largest sum of any block.

**Complexity:**

*   expected worst-case time complexity is O(N*log(N+M));
*   expected worst-case space complexity is O(1)
**Execution:**

Binary search for the minimal size of a block. A valid block can be checked in a boolean fashion. Two special cases can be sped up (courtesy to [CodeSays](http://codesays.com/2014/solution-to-min-max-division-by-codility/)). At the time of writing (19.9.2014) do not use the variable passed to the solution function as M! It is NOT the maximum element in the test cases! The specification says, that no element is larger than M, yet there is not guarantee that M == max(A).

**Solution:**

##### [NailingPlanks](https://codility.com/demo/take-sample-test/nailing_planks)

**Short Problem Definition:**

Count the minimum number of nails that allow a series of planks to be nailed..

**Complexity:**

*   expected worst-case time complexity is O((N+M)*log(M));
*   expected worst-case space complexity is O(M)
**Execution:**

The solution gets 100/100, but I am skeptical. The runtime is rather O(N * (log(M)+M)) than O((N+M)*log(M)).  Maybe it can be proven that the execution of the linear scan will never have to scan all possible positions. I also violate the space complexity by creating a copy of A+B.

**Solution:**

#### Caterpillar method

##### [AbsDistinct](https://codility.com/demo/take-sample-test/abs_distinct)

**Short Problem Definition:**

Compute number of distinct absolute values of sorted array elements.

**Complexity:**

*   expected worst-case time complexity is O(N);
*   expected worst-case space complexity is O(N)
**Execution:**

Additional storage is allowed. Therefore a simple python solution will suffice.

**Solution:**

##### [CountDistinctSlices](https://codility.com/demo/take-sample-test/count_distinct_slices)

**Short Problem Definition:**

Count the number of distinct slices (containing only unique numbers).

**Complexity:**

*   expected worst-case time complexity is O(N);
*   expected worst-case space complexity is O(M)
**Execution:**

Using the caterpillar method I expand the caterpillar to the right as long as a duplicate element is found. The right side has to retract as long as this duplicate element has been eliminated from the next slice. An observation showed that the number of sub-slices is equal to front-back+1.

**Solution:**

##### [CountTriangles](https://codility.com/demo/take-sample-test/count_triangles)

**Short Problem Definition:**

<span class="task-synopsis">Count the number of triangles that can be built from a given set of edges.</span>

**Complexity:**

*   expected worst-case time complexity is O(N<sup>2</sup>);
*   expected worst-case space complexity is O(1)
**Execution:**

Apply the caterpillar method. We know that in a sorted array every position between Q and R will be bigger than Q and therefore P+Q will be bigger than R. I therefore either increment Q if P+Q is not larger than R or increment R as far as possible.

**Solution:**

##### [MinAbsSumOfTwo](https://codility.com/demo/take-sample-test/min_abs_sum_of_two)

**Short Problem Definition:**

Find the minimal absolute value of a sum of two elements.

**Complexity:**

*   expected worst-case time complexity is O(N*log(N));
*   expected worst-case space complexity is O(1)
**Execution:**

Using the caterpillar method on a sorted list.

**Solution:**

#### Greedy algorithms

##### [TieRopes](https://codility.com/demo/take-sample-test/tie_ropes)

**Short Problem Definition:**

Tie adjacent ropes to achieve the maximum number of ropes of length &gt;= K.

**Complexity:**

*   expected worst-case time complexity is O(N);
*   expected worst-case space complexity is O(N)
**Execution:**

I am a bit skeptical about the correctness of my solution. It gets 100/100 through…

**Solution:**

##### [MaxNonoverlappingSegments](https://codility.com/demo/take-sample-test/max_nonoverlapping_segments)

**Short Problem Definition:**

Find a maximal set of non((-))overlapping segments.

**Complexity:**

*   expected worst-case time complexity is O(N)
*   expected worst-case space complexity is O(N)
**Execution:**

This can be solved by using greedy search. The beginning of the next segment must come strictly after its predecessor.

**Solution:**

#### Dynamic Programming

##### [NumberSolitaire](https://codility.com/demo/take-sample-test/number_solitaire)

**Short Problem Definition:**

In a given array, find the subset of maximal sum in which the distance between consecutive elements is at most 6.

**Complexity:**

*   expected worst-case time complexity is O(N);
*   expected worst-case space complexity is O(N)
**Execution:**

Prototypical Dynamic Programming. Remember all sub-solutions and use them to compute the next step. I prefixed the array by 6 min_values to use the same computation for the whole algorithm (besides the first). Do not forget to set the minimal_value small enough to handle a purely negative input array.

**Solution:**

##### <span style="text-decoration: underline;"><span style="line-height: 1.5;">MinAbsSum</span></span>

#### Future Training

##### [BinaryGap](https://codility.com/demo/take-sample-test/binary_gap)

**Short Problem Definition:**

Find longest sequence of zeros in binary representation of an integer.

**Complexity:**

*   expected worst-case time complexity is O(log(N));
*   expected worst-case space complexity is O(1)
**Execution:**

The solution is straight-forward! Use of binary shift.

**Solution:**

##### [StrSymmetryPoint](https://codility.com/demo/take-sample-test/str_symmetry_point)

**Short Problem Definition:**

Find a symmetry point of a string, if any.

**Complexity:**

*   expected worst-case time complexity is O(length(S));
*   expected worst-case space complexity is O(1) (not counting the storage required for input arguments).
**Execution:**

This problem gave me a lot of headache. It is so trivial I that over-complicated it. I thought that you should find a symmetry point at any possible position, ignoring the residual characters. You would obviously try to maximize the length of this symmetrical sub-array. I was not able to come with any O(S) algorithm for this problem derivation. So just to remind you,** this problem is a simple palindrome check**. Additionally, you drop all evenly sized strings as their symmetry point is between the indexes.

**Solution:**

##### [OddOccurencesInArray](https://codility.com/demo/take-sample-test/odd_occurrences_in_array)

**Short Problem Definition:**

Find value that occurs in odd number of elements.

**Complexity:**

*   expected worst-case time complexity is O(N);
*   expected worst-case space complexity is O(1)
**Execution:**

This problem can be found in many algorithm books. A xor A cancels itself and B xor 0 is B. Therefore A xor A xor B xor C xor C is B.

**Solution:**

##### [TreeHeight](https://codility.com/demo/take-sample-test/tree_height)

**Short Problem Definition:**

Compute the height of a binary link-tree.

**Complexity:**

*   expected worst-case time complexity is O(N);
*   expected worst-case space complexity is O(N)
**Execution:**

The height of a tree is the maximal height +1 of its subtrees. In this specification a tree with just the root node has a height of 0.

**Solution:**

##### [ArrayInversionCount](https://codility.com/demo/take-sample-test/array_inversion_count)

**Short Problem Definition:**

Compute number of inversion in an array.

**Complexity:**

*   expected worst-case time complexity is O(N*log(N));
*   expected worst-case space complexity is O(N)
**Execution:**

Any sorting algorithm with a NlogN runtime will do the trick. It is important to count all (remaining) bigger elements on the left side. Do not forget to check for the maximal return value!

**Solution:**

#### Challenges

*   [Kalium 2015 – SqlSegmentsSum](http://www.martinkysel.com/codility-sqlsegmentssum-kalium-2015-solution/)
*   Boron 2013 – Flags
*   [Psi 2012 – Wire Burnouts](http://www.martinkysel.com/codility-wireburnouts-2012-psi-solution/ "Codility ‘WireBurnouts’ 2012 Psi Solution")
*   [Chi 2012 – Cannon Balls](http://www.martinkysel.com/codility-cannonballs-2012-chi-solution/)
*   [Sigma 2012 – StoneWall](http://www.martinkysel.com/codility-stonewall-solution/ "Codility ‘StoneWall’ Solution")
*   Delta 2011 – MinAbsSum
*   [Beta 2010 – NumberOfDiscIntersections](http://www.martinkysel.com/codility-number-of-disc-intersections-2010-beta-solution/)
*   [Alpha 2010 – PrefixSet](http://www.martinkysel.com/codility-prefixset-2010-alpha-solution/ "Codility ‘PrefixSet’ 2010 Alpha Solution")

