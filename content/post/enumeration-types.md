---
title: Enumeration types
toc: true
id: 34
categories:
  - Basics
  - Java 5
date: "2013-01-12T02:58:13+00:00"
---

### enum's underlying implementation

Define enumerated types by using **enum** keyword.


```java
enum MetaColor {
    RED, YELLOW, BLUE
}
```

Now, MetaColor class has three **<span style="text-decoration: underline;">final static members</span>** all of which are of MetaColor class: RED, YELLOW and BLUE

MetaColor has 2 static methods: **_values(), valueOf(..) _**and other instance methods: **_name()_**, **_ordinal()_** and **_toString(), hashCode(), equals()_**.

You can override the _toString()_, but _hashCode()_ and equals() are **final** which cannot be override. Actually, enum maintains a singleton for ever enum object, so overriding hashCode() and equals() is not required.

Although **enum**s appear to be a new data type, the keyword only produces some compiler behavior while generating a class for the **enum**, so in many ways you can treat an **enum **as
if it were any other class.

The following is the equivalent one like compiler generates:


```java
class MetaColor extends Enum&lt;MetaColor&gt; {
    public final static MetaColor RED = new MetaColor("RED", 0);
    public final static MetaColor GREEN= new MetaColor("GREEN", 1);
    public final static MetaColor BLUE= new MetaColor("BLUE", 2);

    private MetaColor() {}
}
```

But if you really extend Enum class, the compiler will complain and the code cannot be compiled.

### adding your own method, including constructor

In fact, enums _are _classes and can have their own methods.

Of course, you can associate the enum instance with an inherit value or something.


```java
enum MetaColor {
    RED(1), YELLOW(2), BLUE(3);

    private int value;

    // public MetaColor(int value) { // not permit
    // protected MetaColor(int value) { // not permit
    // private MetaColor(int value) { // permit
    MetaColor(int value) { //permit
        this.value = value;
    }

    public int value() {
       return this.value;
    }
}
```


### some constraints

In the above code, the constractor of MetaColor **cannot be applied public or protected modifier**.

And also you **cannot instantiate the enum class** by yourself.

### go further: abstract enum



```java
public enum Season {
    WINTER("WT") {
        @Override
        public void execute( ) {
            System.out.println("Winter...");
        }
    },
    SPRING("SP") {
        @Override
        public void execute( ) {
            System.out.println("Spring....");
        }
    },

    SUMMER("SM") {
        @Override
        public void execute( ) {
            System.out.println("Summer...");
        }
    },
    FALL("FL") {
        @Override
        public void execute( ) {
            System.out.println("Fall...");
        }
    };

    //template method
    public abstract void execute();
}
```

&nbsp;

### immutable?

No, enum object is not immutable on its own. But enum class limits **the number and order of its instances**, as we cannot initialize a new instance of the enum class.

That is the essential of enumerable class.

### enum vs. static final variables

What are the advantages of enums if compared with the traditional final static fields of a class?

*   Enum class **does the correct thing** of what it can do and it's designed just for the enumeration objects.
*   Enum is just **normal class**, so the **extensibility** is far more than that of plain final static variables.
*   Enum can be really **self-descriptive**.
