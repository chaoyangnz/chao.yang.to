---
title: unreachable statements
toc: true
id: 122
categories:
  - 'Language, Compiler'
date: "2015-01-16T04:42:49+00:00"
---

Sometimes, we will meet this compiler error: unreachable statements.

See the following examples:



```java

public void javapapers() {
    System.out.println(&quot;java&quot;);
    return;
    System.out.println(&quot;papers&quot;); // compile error
}
```




```java

public void javapapers() {
    System.out.println(&quot;java&quot;);
    if(true) {
        return;
    }
    System.out.println(&quot;papers&quot;);
}
//This code compiled successfully
```




```java

public void javapapers() {
    System.out.println(&quot;java&quot;);
    while(true) {
        return;
    }
    System.out.println(&quot;papers&quot;); // compile error
}
```


Wait, but what is strange here?

From Java Language Specification:
<div class="titlepage">
<div>
<div>
> ## 14.21. Unreachable Statements
</div>
</div>
</div>
> <a name="jls-14.21-100"></a>It is a compile-time error if a statement cannot be executed because it is <span class="emphasis">_unreachable_</span>.
> 
> This section is devoted to a precise explanation of the word "reachable." The idea is that there must be some possible execution path from the beginning of the constructor, method, instance initializer, or static initializer that contains the statement to the statement itself. The analysis takes into account the structure of statements. Except for the special treatment of `while`, `do`, and `for` statements whose condition expression has the constant value `true`, the values of expressions are not taken into account in the flow analysis.
> 
> <div class="informalexample">
> 
> 
> For example, a Java compiler will accept the code:
> 
> ``` java
{
> 
>     int n = 5;
> 
>     while (n &gt; 7) k = 2;
> 
> }
> 
> 
```

> 
> 
> even though the value of `n` is known at compile time and in principle it can be known at compile time that the assignment to `k` can never be executed.
> 
> </div>
> 
> 
> <a name="jls-14.21-200"></a>The rules in this section define two technical terms:
> 
> <div class="norm">
> 
> 
> *   <a name="jls-14.21-200-A"></a>whether a statement is <span class="emphasis">_reachable_</span>
> 
> *   <a name="jls-14.21-200-B"></a>whether a statement <span class="emphasis">_can complete normally_</span>
> 
> </div>
> 
> 
> <a name="jls-14.21-210"></a>The definitions here allow a statement to complete normally only if it is reachable.
> 
> <a name="jls-14.21-220"></a>To shorten the description of the rules, the customary abbreviation "iff" is used to mean "if and only if."
> 
> <a name="jls-14.21-230"></a>A reachable `break` statement <span class="emphasis">_exits a statement_</span> if, within the break target, either there are no `try` statements whose `try` blocks contain the`break` statement, or there are `try` statements whose `try` blocks contain the `break` statement and all `finally` clauses of those `try`statements can complete normally.
> 
> This definition is based on the logic around "attempts to transfer control" in [§14.15](http://docs.oracle.com/javase/specs/jls/se7/html/jls-14.html#jls-14.15 "14.15. The break Statement").
> 
> <a name="jls-14.21-240"></a>A `continue` statement <span class="emphasis">_continues a `do` statement_</span> if, within the `do` statement, either there are no `try` statements whose `try` blocks contain the`continue` statement, or there are `try` statements whose `try` blocks contain the `continue` statement and all `finally` clauses of those `try`statements can complete normally.
> 
> <a name="jls-14.21-300"></a>The rules are as follows:
> 
> <div class="norm">
> 
> 
> *   <a name="jls-14.21-300-A"></a>The block that is the body of a constructor, method, instance initializer, or static initializer is reachable.
> 
> *   <a name="jls-14.21-300-B"></a>An empty block that is not a switch block can complete normally iff it is reachable.
> 
> <a name="jls-14.21-300-B.1"></a>A non-empty block that is not a switch block can complete normally iff the last statement in it can complete normally.
> 
> <a name="jls-14.21-300-B.2"></a>The first statement in a non-empty block that is not a switch block is reachable iff the block is reachable.
> 
> <a name="jls-14.21-300-B.3"></a>Every other statement `S` in a non-empty block that is not a switch block is reachable iff the statement preceding `S` can complete normally.
> 
> *   <a name="jls-14.21-300-C"></a>A local class declaration statement can complete normally iff it is reachable.
> 
> *   <a name="jls-14.21-300-D"></a>A local variable declaration statement can complete normally iff it is reachable.
> 
> *   <a name="jls-14.21-300-E"></a>An empty statement can complete normally iff it is reachable.
> 
> *   <a name="jls-14.21-300-F"></a>A labeled statement can complete normally if at least one of the following is true:
> 
> <div class="norm">
> 
> 
>     *   <a name="jls-14.21-300-F-1"></a>The contained statement can complete normally.
> 
>         *   <a name="jls-14.21-300-F-2"></a>There is a reachable `break` statement that exits the labeled statement.
> 
> </div>
> 
> 
> <a name="jls-14.21-300-F.1"></a>The contained statement is reachable iff the labeled statement is reachable.
> 
> *   <a name="jls-14.21-300-G"></a>An expression statement can complete normally iff it is reachable.
> 
> *   <a name="jls-14.21-300-H-1"></a>An `if-then` statement can complete normally iff it is reachable.
> 
> <a name="jls-14.21-300-H-2"></a>The `then`-statement is reachable iff the `if-then` statement is reachable.
> 
> <a name="jls-14.21-300-H-3"></a>An `if-then-else` statement can complete normally iff the `then`-statement can complete normally or the `else`-statement can complete normally.
> 
> <a name="jls-14.21-300-H-4"></a>The `then`-statement is reachable iff the `if-then-else` statement is reachable.
> 
> <a name="jls-14.21-300-H-5"></a>The `else`-statement is reachable iff the `if-then-else` statement is reachable.
> 
> This handling of an `if` statement, whether or not it has an `else` part, is rather unusual. The rationale is given at the end of this section.
> 
> *   <a name="jls-14.21-300-I"></a>An `assert` statement can complete normally iff it is reachable.
> 
> *   <a name="jls-14.21-300-J"></a>A `switch` statement can complete normally iff at least one of the following is true:
> 
> <div class="norm">
> 
> 
>     *   <a name="jls-14.21-300-J-1"></a>The switch block is empty or contains only switch labels.
> 
>         *   <a name="jls-14.21-300-J-2"></a>The last statement in the switch block can complete normally.
> 
>         *   <a name="jls-14.21-300-J-3"></a>There is at least one switch label after the last switch block statement group.
> 
>         *   <a name="jls-14.21-300-J-4"></a>The switch block does not contain a `default` label.
> 
>         *   <a name="jls-14.21-300-J-5"></a>There is a reachable `break` statement that exits the `switch` statement.
> 
> </div>
> *   <a name="jls-14.21-300-K"></a>A switch block is reachable iff its `switch` statement is reachable.
> 
> *   <a name="jls-14.21-300-L"></a>A statement in a switch block is reachable iff its `switch` statement is reachable and at least one of the following is true:
> 
> <div class="norm">
> 
> 
>     *   <a name="jls-14.21-300-L-1"></a>It bears a `case` or `default` label.
> 
>         *   <a name="jls-14.21-300-L-2"></a>There is a statement preceding it in the switch block and that preceding statement can complete normally.
> 
> </div>
> *   <a name="jls-14.21-300-M"></a>A `while` statement can complete normally iff at least one of the following is true:
> 
> <div class="norm">
> 
> 
>     *   <a name="jls-14.21-300-M-1"></a>The `while` statement is reachable and the condition expression is not a constant expression ([§15.28](http://docs.oracle.com/javase/specs/jls/se7/html/jls-15.html#jls-15.28 "15.28. Constant Expressions")) with value `true`.
> 
>         *   <a name="jls-14.21-300-M-2"></a>There is a reachable `break` statement that exits the `while` statement.
> 
> </div>
> 
> 
> <a name="jls-14.21-300-M.1"></a>The contained statement is reachable iff the `while` statement is reachable and the condition expression is not a constant expression whose value is `false`.
> 
> *   <a name="jls-14.21-300-N"></a>A `do` statement can complete normally iff at least one of the following is true:
> 
> <div class="norm">
> 
> 
>     *   <a name="jls-14.21-300-N-1"></a>The contained statement can complete normally and the condition expression is not a constant expression ([§15.28](http://docs.oracle.com/javase/specs/jls/se7/html/jls-15.html#jls-15.28 "15.28. Constant Expressions")) with value`true`.
> 
>         *   <a name="jls-14.21-300-N-2"></a>The `do` statement contains a reachable `continue` statement with no label, and the `do` statement is the innermost `while`, `do`, or `for`statement that contains that `continue` statement, and the `continue` statement continues that `do` statement, and the condition expression is not a constant expression with value `true`.
> 
>         *   <a name="jls-14.21-300-N-3"></a>The `do` statement contains a reachable `continue` statement with a label `L`, and the `do` statement has label `L`, and the `continue`statement continues that `do` statement, and the condition expression is not a constant expression with value `true`.
> 
>         *   <a name="jls-14.21-300-N-4"></a>There is a reachable `break` statement that exits the `do` statement.
> 
> </div>
> 
> 
> <a name="jls-14.21-300-N.1"></a>The contained statement is reachable iff the `do` statement is reachable.
> 
> *   <a name="jls-14.21-300-O"></a>A basic `for` statement can complete normally iff at least one of the following is true:
> 
> <div class="norm">
> 
> 
>     *   <a name="jls-14.21-300-O-1"></a>The `for` statement is reachable, there is a condition expression, and the condition expression is not a constant expression ([§15.28](http://docs.oracle.com/javase/specs/jls/se7/html/jls-15.html#jls-15.28 "15.28. Constant Expressions")) with value `true`.
> 
>         *   <a name="jls-14.21-300-O-2"></a>There is a reachable `break` statement that exits the `for` statement.
> 
> </div>
> 
> 
> <a name="jls-14.21-300-O.1"></a>The contained statement is reachable iff the `for` statement is reachable and the condition expression is not a constant expression whose value is `false`.
> 
> *   <a name="jls-14.21-300-P"></a>An enhanced `for` statement can complete normally iff it is reachable.
> 
> *   <a name="jls-14.21-300-Q"></a>A `break`, `continue`, `return`, or `throw` statement cannot complete normally.
> 
> *   <a name="jls-14.21-300-R"></a>A `synchronized` statement can complete normally iff the contained statement can complete normally.
> 
> <a name="jls-14.21-300-R.1"></a>The contained statement is reachable iff the `synchronized` statement is reachable.
> 
> *   <a name="jls-14.21-300-S"></a>A `try` statement can complete normally iff both of the following are true:
> 
> <div class="norm">
> 
> 
>     *   <a name="jls-14.21-300-S-1"></a>The `try` block can complete normally or any `catch` block can complete normally.
> 
>         *   <a name="jls-14.21-300-S-2"></a>If the `try` statement has a `finally` block, then the `finally` block can complete normally.
> 
> </div>
> *   <a name="jls-14.21-300-T"></a>The `try` block is reachable iff the `try` statement is reachable.
> 
> *   <a name="jls-14.21-300-U"></a>A `catch` block `C` is reachable iff both of the following are true:
> 
> <div class="norm">
> 
> 
>     *   <a name="jls-14.21-300-U-1"></a>Either the type of `C`'s parameter is an unchecked exception type or `Throwable`; or some expression or `throw` statement in the `try`block is reachable and can throw a checked exception whose type is assignable to the parameter of the `catch` clause `C`.
> 
>     <a name="jls-14.21-300-U-1.1"></a>An expression is reachable iff the innermost statement containing it is reachable.
> 
>     See [§15.6](http://docs.oracle.com/javase/specs/jls/se7/html/jls-15.html#jls-15.6 "15.6. Normal and Abrupt Completion of Evaluation") for normal and abrupt completion of expressions.
> 
>         *   <a name="jls-14.21-300-U-2"></a>There is no earlier `catch` block `A` in the `try` statement such that the type of `C`'s parameter is the same as or a subclass of the type of`A`'s parameter.
> 
> </div>
> *   <a name="jls-14.21-300-V"></a>The <span class="emphasis">_Block_</span> of a `catch` block is reachable iff the `catch` block is reachable.
> 
> *   <a name="jls-14.21-300-W"></a>If a `finally` block is present, it is reachable iff the `try` statement is reachable.
> 
> </div>
> 
> 
> One <span class="emphasis">_might expect_</span> the `if` statement to be handled in the following manner:
> 
> <div class="note">
> 
> 
> *   An `if-then` statement can complete normally iff at least one of the following is true:
> 
> <div class="note">
> 
> 
>     *   The `if-then` statement is reachable and the condition expression is not a constant expression whose value is `true`.
> 
>         *   The `then`-statement can complete normally.
> 
> </div>
> 
> 
> The `then`-statement is reachable iff the `if-then` statement is reachable and the condition expression is not a constant expression whose value is `false`.
> 
> *   An `if-then-else` statement can complete normally iff the `then`-statement can complete normally or the `else`-statement can complete normally.
> 
> The `then`-statement is reachable iff the `if-then-else` statement is reachable and the condition expression is not a constant expression whose value is`false`.
> 
> The `else`-statement is reachable iff the `if-then-else` statement is reachable and the condition expression is not a constant expression whose value is `true`.
> 
> </div>
> 
> 
> This approach would be consistent with the treatment of other control structures. However, in order to allow the if statement to be used conveniently for "conditional compilation" purposes, the actual rules differ.
> 
> As an example, the following statement results in a compile-time error:
> 
> ``` java
while (false) { x=3; }
> 
> 
```

> 
> 
> because the statement `x=3;` is not reachable; but the superficially similar case:
> 
> ``` java
if (false) { x=3; }
> 
> 
```

> 
> 
> does not result in a compile-time error. An optimizing compiler may realize that the statement `x=3;` will never be executed and may choose to omit the code for that statement from the generated `class` file, but the statement `x=3;` is not regarded as "unreachable" in the technical sense specified here.
> 
> The rationale for this differing treatment is to allow programmers to define "flag variables" such as:
> 
> ``` java
static final boolean DEBUG = false;
> 
> 
```

> 
> 
> and then write code such as:
> 
> ``` java
if (DEBUG) { x=3; }
> 
> 
```

> 
> 
> The idea is that it should be possible to change the value of `DEBUG` from `false` to `true` or from `true` to `false` and then compile the code correctly with no other changes to the program text.
> 
> This ability to "conditionally compile" has a significant impact on, and relationship to, binary compatibility ([§13](http://docs.oracle.com/javase/specs/jls/se7/html/jls-13.html "Chapter 13. Binary Compatibility")). If a set of classes that use such a "flag" variable are compiled and conditional code is omitted, it does not suffice later to distribute just a new version of the class or interface that contains the definition of the flag. A change to the value of a flag is, therefore, not binary compatible with pre-existing binaries ([§13.4.9](http://docs.oracle.com/javase/specs/jls/se7/html/jls-13.html#jls-13.4.9 "13.4.9. final Fields and Constants")). (There are other reasons for such incompatibility as well, such as the use of constants in `case` labels in `switch` statements; see [§13.4.9](http://docs.oracle.com/javase/specs/jls/se7/html/jls-13.html#jls-13.4.9 "13.4.9. final Fields and Constants").)
