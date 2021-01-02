---
title: Java Serialization
toc: true
id: 1375
categories:
  - Basics
date: "2015-06-27T00:28:45+00:00"
---

### Serialization

#### What is Serialization?

Serialization is the process to transfer a Java object into a sequence of bytes, often in the form of stream.

At another time, we can unserialize from the bytes and rebuild the object in JVM.

#### How to do

*   make the class to be serialized implement Serializable interface (Serializable is a marker interface)
*   use ObjectOutputStream to serialize object
*   use OjbectInputStream to unserialize the bytes
In order to better control the serialization process, you can implement ObjectInput and ObjectOutput on your own.

ObjectOutputStream has implemented ObjectOutput interface; ObjectInputStream has implemented ObjectInput interface

#### What information is serialized

Simply, the sequence of bytes includes:

*   instance fields (excluding the transient one)
*   class name (just name!!)
*   class serialVersionUID
> So when you serialize an object in a JVM, and unserialize it in another JVM. The latter JVM just knows the class name. If you don't provide the class file, then you cannot cast it to the correct specified class even if you know the class name.
Java RMI heavily uses the Serialization technology and extends it, because remote invocation usually happens in different JVMs. So class file and related dependent classes also need to be transferred along with the object serialization stream.

#### What is serialVersionUID?

serialVersionUID is used to version the serializable class. When the unique id of the class in serialization and unserialzation is different, `java.io.InvalidClassException` will be thrown.

All serializable class automatically generates this unique id implicitly. But you can specify it explicitly:


```java
static final long serialVersionUID = 229383940L;
```

Alternatively, the JDK comes with a **serialver** tool.

#### Example



```java
public class SerializationInOneJVM {

    @Test
    public void testWrite() throws Exception {
        FileOutputStream fos = new FileOutputStream("a.bin");
        ObjectOutputStream oos = new ObjectOutputStream(fos);
        oos.writeObject(new SerializableObject());
        fos.close();
    }

}

// SerializableObject not in the classpath of this JVM
public class UnserializationInAnotherJVM {

    @Test
    public void testRead() throws Exception {
        FileInputStream fis = new FileInputStream("a.bin");
        ObjectInputStream ois = new ObjectInputStream(fis);
        Object o = ois.readObject();
        System.out.println(o.getClass());
        //here we cannot cast to the concrete class. 

        fis.close();
    }
}
```

