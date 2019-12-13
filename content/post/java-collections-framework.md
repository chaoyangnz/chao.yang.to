---
title: Java Collections Framework
toc: true
id: 1357
categories:
  - Basics
date: "2015-06-26T19:22:37+00:00"
---

### (natural) ordered structure

Red-Black Tree implementation to maintain the natural order (Comparable)

*   TreeSet -&gt; NavigableSet -&gt; OrderedSet
*   TreeMap -&gt; NavigableMap -&gt; OrderedMap

### hash structure

a bucket array, each bucket is a LinkedList; the index is computed by hashCode(), then traverse the LinkedList using equals() until finding the expected element.

*   HashMap
*   HashSet: backed by HashMap, just the element is the key of `Entry&lt;K,V&gt; while the value is ignored.`
**with insertion order**

maintain the insertion order using an extra LinkedList

*   LinkedHashSet
*   LinkedHashMap: as above
**special map**

*   IdentityHashMap: compare the key using == instead of equals()
*   WeekHashMap: key is week reference


```java
public void test() {
    Map map = new IdentityHashMap();

    String a = new String("aa");
    String b = new String("aa");

    map.put(a, 1);
    map.put(b, 2);

    System.out.println(a == b); //false
    System.out.println(a.equals(b)); //true

    System.out.println(map.get(a)); //1
    System.out.println(map.get(b)); //2

}
```


#### immutable, hash key

#### capacity, load factor and rehash

### Collections, Arrays

#### static factory

*   Arrays.asList(..)
*   Collections.singleton(..) / singletonList(..) / singletonMap(..)
*   Collections.emptySet/ emptyList(..)  / emptyMap(..) / emptyOrderedMap(..)
*   Collections.checkedSet(..) / checkedList(..) / checkedMap(..)
*   Collections.`nCopies`(..)

#### sort / search

*   sort(..) / sort(.., Comparator)
*   binarySearch(..) / binarySearch(.., Comparator)

#### unmodifiedXX()

#### other common operations

*   shuffle(..)
*   reverse(..)
*   rotate(.., distance)
*   fill(..)
*   swap(..)
*   copy(..)
*   replaceAll(..)
*   indexOfSubList(..)
*   min(..) / max(..)
*   disjoint(..)
*   frequency(..)

#### a comparator

*   reverseOrder(..)

### concurrent collection

#### fail-fast mechanism, ConcurrentModificationException and Iterator

Java collections use fail-fast mechanism to prevent multiple threads to modify the collection when traversing it. Even there is only one thread, you modify the collection when you traverse it in the single thread, fail-fast will also be applied.

That will case ConcurrentModificationException.

Using Interator.remove(..) method will avoid this.

#### sychronizedXX(), JUC collections

sychronizedXX() makes every method wrapped in a `synchronized` block. So the efficiency is a big problem.
<table style="height: 182px;" border="1" width="522">
<tbody>
<tr>
<td>**not thread-safe collections**</td>
<td>**thread-safe collections**</td>
</tr>
<tr>
<td>ArrayList</td>
<td>CopyOnWriteArrayList</td>
</tr>
<tr>
<td>HashSet</td>
<td>CopyOnWriteArraySet</td>
</tr>
<tr>
<td>LinkedList</td>
<td>ConcurrentLinkedQueue</td>
</tr>
<tr>
<td>HashMap</td>
<td>ConcurrentHashMap</td>
</tr>
<tr>
<td>TreeMap</td>
<td>ConcurrentSkipListMap</td>
</tr>
<tr>
<td>TreeSet</td>
<td>ConcurrentSkipListSet</td>
</tr>
</tbody>
</table>

*   CopyOnWriteArrayList
This implementation always copy the backed array and set the backed array to this new one when modifying the List.

The modification operations are thread-safe by using a `ReentrantLock`.

So it's suitable for the case that writing is greatly less than reading.

Once an interator has been gotten, it will see a **snapshot** of the array.


```java
public void test() {
    List&lt;Integer&gt; list = new CopyOnWriteArrayList&lt;Integer&gt;(Arrays.asList(0, 1, 2, 3, 4, 5, 6, 7));

    for(int i : list) {
        if(i == 2) {
            list.add(8, 8);
        }

        System.out.println(i);
    }

    System.out.println(list);
}

0
1
2
3
4
5
6
7
[0, 1, 2, 3, 4, 5, 6, 7, 8]
```

&nbsp;

[http://www.cnblogs.com/skywang12345/p/java_threads_category.html](http://www.cnblogs.com/skywang12345/p/java_threads_category.html)

&nbsp;

&nbsp;

&nbsp;
