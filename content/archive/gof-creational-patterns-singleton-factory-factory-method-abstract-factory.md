---
title: 'GoF Creational Patterns: Singleton, Factory, Factory Method, Abstract Factory'
toc: true
id: 760
categories:
  - Design Patterns
date: "2015-03-30T11:13:33+00:00"
---

Now, ultimately we start discussing design patterns - really buzzwords.

### Singleton

Singleton pattern is to ensure the unique instance of some class in a JVM.


```java
class LazySingleton {
    private static LazySingleton instance;

    private LazySingleton() {

    }

    public static LazySingleton getInstance() {
        if (instance == null) {
            instance = new LazySingleton();
        }

        return instance;
    }

    public void doSomething() {
        System.out.println("doSomething(): Singleton does something!");
    }
}

// DCL for thread safe
class DoubleCheckedLockingLazySingleton {
    private static DoubleCheckedLockingLazySingleton instance;

    private DoubleCheckedLockingLazySingleton() {
        System.out.println("Singleton(): Initializing Instance");
    }

    public static DoubleCheckedLockingLazySingleton getInstance() {
        if (instance == null) {
            synchronized (DoubleCheckedLockingLazySingleton.class) {
                if (instance == null) {
                    System.out.println("getInstance(): First time getInstance was invoked!");
                    instance = new DoubleCheckedLockingLazySingleton();
                }
            }
        }

        return instance;
    }

    public void doSomething() {
        System.out.println("doSomething(): Singleton does something!");
    }
}

class EagerSingleton {
    private final static EagerSingleton instance = new EagerSingleton();

    private EagerSingleton() {
        System.out.println("Singleton(): Initializing Instance");
    }

    public static EagerSingleton getInstance() {
        return instance;
    }

    public void doSomething() {
        System.out.println("doSomething(): Singleton does something!");
    }
}
```

You can see there is a DCL(Double-checked-Locking) implementation for lazy initialization. But this clever-seeming may not work. Please refer to [Double-checked locking: Clever, but broken](http://www.javaworld.com/article/2074979/java-concurrency/double-checked-locking--clever--but-broken.html)

### Factory

Factory pattern is very easy to understand.

#### classic factory

#### ![Factory Implementation - UML Class Diagram](https://www.oodesign.com/media/stories/factory%20implementation.gif)



```java
interface Product {}

class ConcreteProduct implements Product {
}

class SimpleFactory {

    public static Product createProduct() {
        Product product = new ConcreteProduct();
        // do other tasks for initialization
        return product;
    }
}
```


#### conditional switch factory



```java
class AnotherConcreteProduct implements Product {}

class ConditionalFactory {
    public static Product createProduct(String productId) {
        Product product = null;
        if ("concreteProduct".equals(productId)) {
            product = new ConcreteProduct();
        }
        if ("anotherConcreteProduct".equals(productId)) {
            product = new AnotherConcreteProduct();
        }

        // do other tasks for initialization
        return product;
    }
}
```


#### class registry factory



```java
class ProductClassRegistryFactory {
    private HashMap&lt;String, Class&lt;Product&gt;&gt; registry = new HashMap();

    public void registerProduct(String productID, Class productClass) {
        registry.put(productID, productClass);
    }

    public Product createProduct(String productID) throws Exception {
        Class clazz = (Class) registry.get(productID);
        Constructor productConstructor = clazz.getDeclaredConstructor(new Class[]{String.class});
        return (Product) productConstructor.newInstance(new Object[]{});
    }
}
```


#### instance registry factory



```java
// For registeration on its own
abstract class SelfRegisterableProduct implements Product {
    public abstract Product createProduct();
}

class OneProduct implements Product {
    static {
        new ProductRegistryFactory().registerProduct("ID1", new OneProduct());
    }

    public OneProduct createProduct() {
        return new OneProduct();
    }
}

class ProductRegistryFactory {
    private HashMap&lt;String, Product&gt; registry = new HashMap();

    public void registerProduct(String productID, Product p) {
        registry.put(productID, p);
    }

    public Product createProduct(String productID) {
        return ((SelfRegisterableProduct) registry.get(productID)).createProduct();
    }
}
```


### Factory Method

Factory method is really no difference as a variant of factory pattern. It makes the factory as abstract class instead of implementing the creation logic in itself.

![Factory Method Implementation - UML Class Diagram](https://www.oodesign.com/media/stories/factory%20method%20implementation%20-%20uml%20class%20diagram.gif)

A concrete factory is responsible for a kind of concrete product.

### Abstract Factory

Simply speaking, abstract factory is responsible for the creation of a family of products. So it provides multiple methods to create several related products of different types.

![Abstract Factory Implementation - UML Class Diagram](https://www.oodesign.com/media/creational/abstract-factory-pattern.png)
