---
title: 'Swift 3.0 for a Java or ES6/TypeScript developer'
toc: true
date: "2017-01-19T17:11:25+00:00"
---

# Basic Operators

## Assignment Operator



```swift
let b = 10 // constant
var a = 5	  // variable
a = b
```


`let` is not the same as that in ES6. 
It is more or like `final` in Java. It means the variable (value or reference) cannot be changed once initialization.

But `let` in Swift has stronger semantics, which enable *immutability* or constant. It means not only the reference cannot be mutable but also the object it refers cannot be mutable.



```swift
// swift
let arr = [1, 2, 3]
arr[0] = 3 // error
```




```java
// java
final int[] arr = [1, 2, 3]
arr[0] = 3 // ok
```


multiple assignment with tuple:



```swift
let (x, y) = (1, 2)
```


## == vs. ===, != vs. !==

`==` is the equal operator, while `===` is identity operator which is used to test wthether two object references both refer to the same object instance.

tuples are compared from left to right.



```swift
(1, "zebra") < (2, "apple)

(3, "apple") < (3, "bird")
```


## Nil-Colaescing Operator



```swift
var a : String? = "aaaa"
var b = a ?? b
```


`a ?? b` is shorthand for `a != nil  ? a! : b`

## Range Operators

- closed range operator: `a...b` 
- half-open range operator: `a..<b`

# Strings and Characters

Every string is composed of encoding-indepent Unicode characters.

`String` type is a value type. When you pass a String to a function or assign to a constant/variable, its value is copied.

> Actually, it seems to have the similar attribute of immutability as that in Java.

## Character

Character can be created from a single-character string literal.

`let ch : Character = "1"`


## String Interpolation

String interpolation in Swift is different from that in Javascript.



```swift
let a = 3
let message = "the number is: \(a), its double is: \(Double(a) * 2)"
```


In ES6:



```javascript
let a = 3
let message = `the number is: ${a}, its double is: ${a*2}`
```


## Unicode

> Unicode code point is a number for every character in Unicode.
> Unicode scalar is any Unicode code point in the range U+0000 ~ U+D7FF or U+E000 ~ U+10FFFF.
> The range in U+D800 ~ U+DFFF is for surrogate pair code points.

- escaped special charaters: `\0, \\, \t, \n, \r, \", \'` ..
- Unicode scalar: `\u{00FF}` ...

The unicode literal is different from that in Java and Javascript. In Java/Javascript, it is like `\u00FF` without brackets.

Swift `Character` is not only single Unicode character, it can combine one or more Unicode scalars. Actually, every instance of Swift's `Character` represents a single *extended grapheme cluster*.



```swift
let eAcute: Character = "\u{E9}"          
let combinedEacute: Character = "\u{65}\u{301}" // e followed by  ́
// both are rednered as é
```


> Using **extended grapheme clusters** makes Swift's `Character` is so much different from characters in other programming languages.

## Accessing and Mofifying a String

Due to the representation of Swift `Character`, the access and modification ways are special accordingly.

Each `String` value has an associated *index type*, `String.Index`.

`String` has `startIndex` and `endIndex` properties, which is the half-open range with `endIndex` exclusive.



```swift
let greeting = "Hello world!"
greeting[greeting.startIndex]
// H
var index = greeting.index(before: greeting.endIndex)
greeting[index]
// !
index = greeting.index(after: greeting.startIndex)
greeting[index]
// u
index = greeting.index(greeting.startIndex, offsetBy: 2)
greeting[index]
// l
```


# Control Flow

## switch

No need to use explict `break` in each `case` branch.

But if you want the behaviours when missing `break` in Java, you can use the `fallthrough` keyword.

- Interval Matching



```swift
let number = 62
let desc : String
switch number {
	case 0:
		desc = "no"
	case 1..<5:
		desc = "a few"
	case 5..<12:
		desc = "several"
	default:
		desc = "many"
}
```


- compound cases



```swift
let someCharacter: Character = "e"
switch someCharacter {
	case "a", "e", "i", "o", "u":
	    print("\(someCharacter) is a vowel")
	case "b", "c", "d", "f", "g", "h", "j", "k", "l", "m",
	     "n", "p", "q", "r", "s", "t", "v", "w", "x", "y", "z":
	    print("\(someCharacter) is a consonant")
	default:
	    print("\(someCharacter) is not a vowel or a consonant")
}
```


- test multiple values with `tuple`



```swift
let somePoint = (1, 1)
switch somePoint {
	case (0, 0):
	    print("(0, 0) is at the origin")
	case (_, 0):
	    print("(\(somePoint.0), 0) is on the x-axis")
	case (0, _):
	    print("(0, \(somePoint.1)) is on the y-axis")
	case (-2...2, -2...2):
	    print("(\(somePoint.0), \(somePoint.1)) is inside the box")
	default:
	    print("(\(somePoint.0), \(somePoint.1)) is outside of the box")
}”
```


- value binding



```swift
let anotherPoint = (2, 0)
switch anotherPoint {
	case (let x, 0):
	    print("on the x-axis with an x value of \(x)")
	case (0, let y):
	    print("on the y-axis with a y value of \(y)")
	case let (x, y):
	    print("somewhere else at (\(x), \(y))")
}
```


- where



```swift
let yetAnotherPoint = (1, -1)
switch yetAnotherPoint {
	case let (x, y) where x == y:
	    print("(\(x), \(y)) is on the line x == y")
	case let (x, y) where x == -y:
	    print("(\(x), \(y)) is on the line x == -y")
	case let (x, y):
	    print("(\(x), \(y)) is just some arbitrary point")
}
```



