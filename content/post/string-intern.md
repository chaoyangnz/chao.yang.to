---
title: 'String intern'
toc: true
date: "2016-08-22T21:36:25+00:00"
---

# Several simple code snippets



```java
// Sample1
char[] chars = {'h', 'e', 'l', 'l', 'o'};
String str1 = new String(chars);
System.out.println(str1 == str1.intern());
```

print: true



```java
// Sample2
String str1 = new String("hello");
System.out.println(str1 == str1.intern());
```

print: false



```java
// Sample3
String a = "hello";
String b = new String("hello");
String c = new String("h" + "e" + "l" + "l" + "o");
String d = b.intern();
```

How many String objects is created?
Answer is 3. Are you right?

# questions
- when and where is the String object from string literal created, even if we don't explicitly initialize it?
- what happens for `intern()`?
- how is the "pool of strings" implemented?
- what's the difference of two constructors between String(char[]) and String(String)?

# constant string loading and intern
As we know, string literal is compiled into constant pool of the class file. And the bytecode to load it is `ldc`.



```java
public class StringLiteral {
    public StringLiteral() {
        String str = "hello";
    }
}
```

will be compiled to:
```

Constant pool:
   #1 = Methodref          #4.#16         //  java/lang/Object."<init>":()V
   #2 = String             #17            //  hello
   #3 = Class              #18            //  StringLiteral
   #4 = Class              #19            //  java/lang/Object
   #5 = Utf8               <init>
   #6 = Utf8               ()V
   #7 = Utf8               Code
   #8 = Utf8               LineNumberTable
   #9 = Utf8               LocalVariableTable
  #10 = Utf8               this
  #11 = Utf8               LStringLiteral;
  #12 = Utf8               str
  #13 = Utf8               Ljava/lang/String;
  #14 = Utf8               SourceFile
  #15 = Utf8               StringLiteral.java
  #16 = NameAndType        #5:#6          //  "<init>":()V
  #17 = Utf8               hello
  #18 = Utf8               StringLiteral
  #19 = Utf8               java/lang/Object
{
  public StringLiteral();
    flags: ACC_PUBLIC
    Code:
      stack=1, locals=2, args_size=1
         0: aload_0
         1: invokespecial #1                  // Method java/lang/Object."<init>":()V
         4: ldc           #2                  // String hello
         6: astore_1
         7: return
      LineNumberTable:
        line 5: 0
        line 6: 4
        line 7: 7
      LocalVariableTable:
        Start  Length  Slot  Name   Signature
               0       8     0  this   LStringLiteral;
               7       1     1   str   Ljava/lang/String;
}
```


when `ldc` is executed, it must firstly resolve the symbol to the actual reference if not resolved.

NOTICE:
```

4: ldc           #2                  // String hello
6: astore_1
```

will find #2 in constant pool:
```

#2 = String             #17            //  hello
#17 = Utf8               hello
```

Initially, #2 is Constant_String_info symbol, and it assoiciates with #17 which is Constant_Utf8_info (UTF-8 encoding char array).


```c++
// line2111-2161 share/vm/interpreter/bytecodeInterpreter.cpp
CASE(_ldc):
   {
     u2 index;
     bool wide = false;
     int incr = 2; // frequent case
     if (opcode == Bytecodes::_ldc) {
       index = pc[1];
     } else {
       index = Bytes::get_Java_u2(pc+1);
       incr = 3;
       wide = true;
     }

     ConstantPool* constants = METHOD->constants();
     switch (constants->tag_at(index).value()) {
     ....
     
     case JVM_CONSTANT_String:
       {
         oop result = constants->resolved_references()->obj_at(index);
         if (result == NULL) {
           CALL_VM(InterpreterRuntime::resolve_ldc(THREAD, (Bytecodes::Code) opcode), handle_exception);
           SET_STACK_OBJECT(THREAD->vm_result(), 0);
           THREAD->set_vm_result(NULL);
         } else {
           VERIFY_OOP(result);
           SET_STACK_OBJECT(result, 0);
         }
       break;
       }

      ....
   }
```


when it's JVM_CONSTANT_String, jvm first checks resolved reference in line `oop result = constants->resolved_references()->obj_at(index);`. If it is not resolved, then call `InterpreterRuntime::resolve_ldc(THREAD, (Bytecodes::Code) opcode)`.



```c++
// line125-142 share/vm/interpreter/interpreterRuntime.cpp
IRT_ENTRY(void, InterpreterRuntime::resolve_ldc(JavaThread* thread, Bytecodes::Code bytecode)) {
  assert(bytecode == Bytecodes::_fast_aldc ||
         bytecode == Bytecodes::_fast_aldc_w, "wrong bc");
  ResourceMark rm(thread);
  methodHandle m (thread, method(thread));
  Bytecode_loadconstant ldc(m, bci(thread));
  oop result = ldc.resolve_constant(CHECK);
#ifdef ASSERT
  {
    // The bytecode wrappers aren't GC-safe so construct a new one
    Bytecode_loadconstant ldc2(m, bci(thread));
    oop coop = m->constants()->resolved_references()->obj_at(ldc2.cache_index());
    assert(result == coop, "expected result for assembly code");
  }
#endif
  thread->set_vm_result(result);
}
IRT_END
```


here, it then calls `Bytecode_loadconstant::resolve_constant(..)`.



```c++
// line217-226 share/vm/interpreter/bytecode.cpp 
oop Bytecode_loadconstant::resolve_constant(TRAPS) const {
  assert(_method.not_null(), "must supply method to resolve constant");
  int index = raw_index();
  ConstantPool* constants = _method->constants();
  if (has_cache_index()) {
    return constants->resolve_cached_constant_at(index, THREAD);
  } else {
    return constants->resolve_constant_at(index, THREAD);
  }
}
```

here, no cache temporarily, it calls `constants->resolve_constant_at(index, THREAD)`.



```c++
// line705-709 share/vm/oops/constantPool.hpp
  // Resolve late bound constants.
  oop resolve_constant_at(int index, TRAPS) {
    constantPoolHandle h_this(THREAD, this);
    return resolve_constant_at_impl(h_this, index, _no_index_sentinel, THREAD);
  }
```




```c++
// line614-718 shared/vm/oops/contantPool.cpp
// Called to resolve constants in the constant pool and return an oop.
// Some constant pool entries cache their resolved oop. This is also
// called to create oops from constants to use in arguments for invokedynamic
oop ConstantPool::resolve_constant_at_impl(constantPoolHandle this_oop, int index, int cache_index, TRAPS) {
  oop result_oop = NULL;
  Handle throw_exception;

  if (cache_index == _possible_index_sentinel) {
    // It is possible that this constant is one which is cached in the objects.
    // We'll do a linear search.  This should be OK because this usage is rare.
    assert(index > 0, "valid index");
    cache_index = this_oop->cp_to_object_index(index);
  }
  assert(cache_index == _no_index_sentinel || cache_index >= 0, "");
  assert(index == _no_index_sentinel || index >= 0, "");

  if (cache_index >= 0) {
    result_oop = this_oop->resolved_references()->obj_at(cache_index);
    if (result_oop != NULL) {
      return result_oop;
      // That was easy...
    }
    index = this_oop->object_to_cp_index(cache_index);
  }

  jvalue prim_value;  // temp used only in a few cases below

  int tag_value = this_oop->tag_at(index).value();

  switch (tag_value) {

  ....

  case JVM_CONSTANT_String:
    assert(cache_index != _no_index_sentinel, "should have been set");
    if (this_oop->is_pseudo_string_at(index)) {
      result_oop = this_oop->pseudo_string_at(index, cache_index);
      break;
    }
    result_oop = string_at_impl(this_oop, index, cache_index, CHECK_NULL);
    break;

  ....
  }

  if (cache_index >= 0) {
    // Cache the oop here also.
    Handle result_handle(THREAD, result_oop);
    MonitorLockerEx ml(this_oop->lock());  // don't know if we really need this
    oop result = this_oop->resolved_references()->obj_at(cache_index);
    // Benign race condition:  resolved_references may already be filled in while we were trying to lock.
    // The important thing here is that all threads pick up the same result.
    // It doesn't matter which racing thread wins, as long as only one
    // result is used by all threads, and all future queries.
    // That result may be either a resolved constant or a failure exception.
    if (result == NULL) {
      this_oop->resolved_references()->obj_at_put(cache_index, result_handle());
      return result_handle();
    } else {
      // Return the winning thread's result.  This can be different than
      // result_handle() for MethodHandles.
      return result;
    }
  } else {
    return result_oop;
  }
}
```


And then calls `string_at_impl`.



```c++
// line816-825 share/vm/oops/contantPool.cpp
oop ConstantPool::string_at_impl(constantPoolHandle this_oop, int which, int obj_index, TRAPS) {
  // If the string has already been interned, this entry will be non-null
  oop str = this_oop->resolved_references()->obj_at(obj_index);
  if (str != NULL) return str;
  Symbol* sym = this_oop->unresolved_string_at(which);
  str = StringTable::intern(sym, CHECK_(NULL));
  this_oop->string_at_put(which, obj_index, str);
  assert(java_lang_String::is_instance(str), "must be string");
  return str;
}
```

Finally, we find these two lines:


```c++
Symbol* sym = this_oop->unresolved_string_at(which);
str = StringTable::intern(sym, CHECK_(NULL));
```

Here sym is `Utf8` the String is associate with.



```c++
// vm/classfile/symbolTable.cpp
oop StringTable::intern(Symbol* symbol, TRAPS) {
  if (symbol == NULL) return NULL;
  ResourceMark rm(THREAD);
  int length;
  jchar* chars = symbol->as_unicode(length);
  Handle string;
  oop result = intern(string, chars, length, CHECK_NULL);
  return result;
}

oop StringTable::intern(Handle string_or_null, jchar* name,
                        int len, TRAPS) {
  unsigned int hashValue = hash_string(name, len);
  int index = the_table()->hash_to_index(hashValue);
  oop found_string = the_table()->lookup(index, name, len, hashValue);

  // Found
  if (found_string != NULL) return found_string;

  debug_only(StableMemoryChecker smc(name, len * sizeof(name[0])));
  assert(!Universe::heap()->is_in_reserved(name),
         "proposed name of symbol must be stable");

  Handle string;
  // try to reuse the string if possible
  if (!string_or_null.is_null()) {
    string = string_or_null;
  } else {
    string = java_lang_String::create_from_unicode(name, len, CHECK_NULL);
  }

  // Grab the StringTable_lock before getting the_table() because it could
  // change at safepoint.
  MutexLocker ml(StringTable_lock, THREAD);

  // Otherwise, add to symbol to table
  return the_table()->basic_add(index, string, name, len,
                                hashValue, CHECK_NULL);
}

oop StringTable::basic_add(int index_arg, Handle string, jchar* name,
                           int len, unsigned int hashValue_arg, TRAPS) {

  assert(java_lang_String::equals(string(), name, len),
         "string must be properly initialized");
  // Cannot hit a safepoint in this function because the "this" pointer can move.
  No_Safepoint_Verifier nsv;

  // Check if the symbol table has been rehashed, if so, need to recalculate
  // the hash value and index before second lookup.
  unsigned int hashValue;
  int index;
  if (use_alternate_hashcode()) {
    hashValue = hash_string(name, len);
    index = hash_to_index(hashValue);
  } else {
    hashValue = hashValue_arg;
    index = index_arg;
  }

  // Since look-up was done lock-free, we need to check if another
  // thread beat us in the race to insert the symbol.

  oop test = lookup(index, name, len, hashValue); // calls lookup(u1*, int)
  if (test != NULL) {
    // Entry already added
    return test;
  }

  HashtableEntry<oop, mtSymbol>* entry = new_entry(hashValue, string());
  add_entry(index, entry);
  return string();
}
```

Firstly, it converts the Utf8 symbol to Java char array. Then using this char array to crate a `String` object by `java_lang_String::create_from_unicode(name, len, CHECK_NULL);`. Finally, add this oop of String to the table and return its oop. Here the_table is StringTable. StringTable is just a Hashtable<oop, mtSymbol>.



```c++
// line185-192 shared/vm/classfile/javaClasses.cpp
Handle java_lang_String::create_from_unicode(jchar* unicode, int length, TRAPS) {
  Handle h_obj = basic_create(length, CHECK_NH);
  typeArrayOop buffer = value(h_obj());
  for (int index = 0; index < length; index++) {
    buffer->char_at_put(index, unicode[index]);
  }
  return h_obj;
}
```


# explicitly call String#intern()

This is the JNI method of String#intern():


```c
// share/native/java/lang/String.c
JNIEXPORT jobject JNICALL
Java_java_lang_String_intern(JNIEnv *env, jobject this)
{
    return JVM_InternString(env, this);
}
```




```c++
// share/vm/prims/jvm.cpp
JVM_ENTRY(jstring, JVM_InternString(JNIEnv *env, jstring str))
  JVMWrapper("JVM_InternString");
  JvmtiVMObjectAllocEventCollector oam;
  if (str == NULL) return NULL;
  oop string = JNIHandles::resolve_non_null(str);
  oop result = StringTable::intern(string, CHECK_NULL);
  return (jstring) JNIHandles::make_local(env, result);
JVM_END
```


You can see: `oop result = StringTable::intern(string, CHECK_NULL);`
It calls the same method as the above code when loading constant string.


# visualization
StringTable has a static field _the_table and it can be accessed directly by StringTable::the_table(); So actually this is a JVM internal data structure belonging to JVM. It is globally created in universe. It makes no sense to say it is located in the method area.

## for Sample1
![memory layout for Sample1](/media/string-table1.png)

## for Sample2
![memory layout for Sample2](/media/string-table2.png)

## for Sample3

![memory layout for Sample3](/media/string-table3.png)

