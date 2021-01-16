---
categories:
- Tutorial
date: 2017-05-27T02:19:00Z
tags:
- Kotlin
title: Kotlin Pitfalls
toc: true
---

# Type system



```kotlin
val list: List<String> = java.util.ArrayList()
```


Why does it work?
`java.util.ArrayList` is not a subclass of `kotlin.List`, and `kotlin.List` is actually read-only.

Because in bytecode level, `kotlin.List` is just `java.util.List`! I decompile the class Kotlin compiled:



```java
import java.util.ArrayList;
import java.util.List;
import kotlin.Metadata;

@Metadata(mv={1, 1, 6}, bv={1, 0, 1}, k=2, d1={"\000\b\n\000\n\002\020\002\n\000\032\006\020\000\032\0020\001��\006\002"}, d2={"a", "", "kotlin_demo"})
public final class _1Kt
{
  public static final void a()
  {
    List list = (List)new ArrayList();
  }
}
```


You can think as if `java.util.ArrayList` implements `kotlin.List`

# operator

## `in`

`in` is an operator in Kotlin. But it means different things in different context.

### `in` as expression
When you use `in` as an expression, it is evaluated as a `boolean` value: it means `contains`. So unsurprising, you just need to implement a `contains()` method in your class.



```kotlin
class MyInClass {
    operator fun contains(bit: Int): Boolean {
        return true
    }
}

fun main(args: Array<String>) {
    val mic = MyInClass()
    println(1 in mic) // always print true
}
```


### `in` within a for-loop
When you use `in` in a for-loop, it means: iteration. So you need to implement a `iterator()` method.

Let's extends `MyInClass`:



```kotlin
class MyInClass {
    operator fun contains(bit: Int): Boolean {
        return true
    }
    
    operator fun iterator(): Iterator<Int> {
        return MyIterator()
    }
    
    class MyIterator: Iterator<Int> {
        int i: Int = 0
        
        override fun hasNext(): Boolean {
            return if(i < 10) true else false
        }

        override fun next(): Int {
            return i++
        }
    }
}

fun main(args: Array<String>) {
    val mic = MyInClass()
    println(1 in mic) // always print true
    
    for(b in mic) { // print 1, 2, 3, 4, ...9
        println(b)
    }
}
```

# Generics

Kotlin's Generic is as complex as Java's. It introduces `in` `out` type parameters and `declaration-site` vs. `use-site`..

```kotlin
val map = mutableMapOf<Int,Int>()
map[1] = 1
map[1] + 1 // compiler complains here
```

Although we declare the value of map is `Int` which means it cannot be null. But `map.get(..)` return `Int?` which can be null. Wield?!

```kotlin
val map = mutableMapOf<Int,Int?>()
println(map[1])
println(1 in map.keys)
map[1] = null
println(map[1])
println(1 in map.keys)
```

So `map.get(..)` cannot differentiate if the key doesn't exist or just its value is set as null.
The best way to differentiate is to use `key in map.keys`.

 






