---
title: 'Regex in Java'
toc: true
date: "2016-01-09T22:05:25+00:00"
---

# Some concepts

## metacharacters

`<([{\^-=$!|]})?*+.>`
 these characters have special meaning which can affect the way a pattern is matached.
 
## character classes

a set of characters enclosed within square brackets.

| Construct  	| Description                                             |
|---------------|---------------------------------------------------------|
|[abc]   			| a, b, or c (simple class)                               |
| [^abc]  		| Any character except a, b, or c (negation)              |
|[a-zA-Z]   		| a through z, or A through Z, inclusive (range)          |
|[a-d[m-p]]  	| a through d, or m through p: [a-dm-p] (union)           |
|[a-z&&[def]]   | d, e, or f (intersection)                               |
|[a-z&&[^bc]]   | a through z, except for b and c: [ad-z] (subtraction)   |
|[a-z&&[^m-p]]  | a through z, and not m through p: [a-lq-z] (subtraction)|

## predefined character classes

convenient shorthands for commonly used regular expressions

| Construct | Description                                            |
|-----------|--------------------------------------------------------|
|.          |Any character (may or may not match line terminators)   |
|\d         |A digit: [0-9]                                          |
|\D         |A non-digit: [^0-9]                                     |
|\s         |A whitespace character: [ \t\n\x0B\f\r]                 |
|\S         |A non-whitespace character: [^\s]                       |
|\w         |A word character: [a-zA-Z_0-9]                          |
|\W         |A non-word character: [^\w]                             |

## bounary matchers

| Boundary Construct | Description                                             |
|--------------------|---------------------------------------------------------|
|^                   |The beginning of a line                                  |
|\b                  |A word boundary                                          |
|\B                  |A non-word boundary                                      |
|\A                  |The beginning of the input                               |
|\G                  |The end of the previous match                            |
|\Z                  |The end of the input but for the final terminator, if any|
|\z                  |The end of the input                                     |
	
## qualifier

the number of occurrences to match against
	
| qualifier | meaning                               |
|-----------|---------------------------------------|
|X?         |once or not at all                     |
|X*         |zero or more times                     |
|X+         |one or more times                      |
|X{n}       |exactly n times                        |
|X{n,}      |at least n times                       |
|X{n,m}     |at least n but not more than m times   |
	
Qualifier can be applied on character, character class, capturing group.
	
## capturing groups

treat multiple characters as a single unit

**Capturing groups are numbered by counting their opening parentheses from left to right.**

There is also a special group, group 0, which always represents the entire expression. This group is not included in the total reported by groupCount. 

```

s(\d\w),f(dfd(\Sgd)er)3=pr
 --1---  ------2-----
             --3--
```


Group 1: (\d\w)
Group 2: (dfd(\Sgd)er)
Group 3: (\Sgd)

## backreferences

In your regular expression, you can reference the previous capturing group by a backslash (\) followed by a digit indicating the number of the group to be recalled.

`(\d\d)\1(abc)dfd\2-ellre`

Here, `\1` is refered as **the matched content** of `(\d\d)` and `\2` is refered as **the matched content** of `(abc)`. Note, backreference doesn't mean the replacement of the sub pattern of the group.

# Pattern



```java
Pattern pattern = Pattern.compile("(\\d\\d).(abc)";
```


# Matcher



```java
Matcher matcher = pattern.matcher("string-to-match")
```


## find() vs. matches() vs. lookingAt()

- `matches()` matches the full string starting from 0.
- `lookingAt()` like `matches()` starting from 0, but not requre to match the full string.
- `find()` tries to find the next occurrence within the substring that matches the regex. That means, the result of calling find() multiple times might not be the same.



```java
Pattern pattern = Pattern.compile("\\d\\d");
Matcher matcher = pattern.matcher("2345");
matcher.matches(); //false
matcher.lookingAt(); // true
matcher.find(); // true
```


> If you care about the full string matching, use `matches()`, otherwise use `lookingAt()`.
> `find()` is better to use to match multiple occurrences and call multiple times.

## query state

Once matched, we can always query the state by method `group()`, `start()`, and `end()`.

Because `find()` can call multiple times to get all the matched substrings, we can get more detailed group state by `group(i)`, `start(i)`, and `end(i)`.

``` java typical usage of find()
while(matcher.find())
    out.prinf("Found the text \"%s\" from %d to %d", 
              matcher.group(), 
              matcher.start(), 
              matcher.end()
    );
    
    // further group details
    // although group 0 is excluded from groupCount(), we can safely get it
    // group() is equivlent with group(0) here
    for(int i=0; i <= matcher.groupCount(); ++i) {
        out.printf("Group %d: %s from %d to %d",
                   matcher.group(i),
                   matcher.start(i),
                   matcher.end(i)
        );
    }
}
```


## replacement

`replaceAll(..)` and `replaceFirst(..)` is very useful to replace the whole matches.
And in the replacement, you can use `$0, $1, $2, ..` to represent respective group matches.

But the powerful way is using `appendReplacement(..)` and `applendTail()`.

The following example, I need to replace the domain part of all emails in multiple lines.



```java
String emails = "richd.yang@googlemail.com\n" +
                "richdyang@yahoo.com";
String EMAIL_PATTERN = "^([a-zA-Z0-9._]+)@((\\w+)\\.(\\w+))$";
StringBuffer sbf = new StringBuffer();
Matcher m = Pattern.compile(EMAIL_PATTERN, Pattern.MULTILINE).matcher(emails);
while(m.find()) {
    String domain = m.group(3);
    if("googlemail".equals(domain)) {
        m.appendReplacement(sbf, "$1@gmail.$4");
    }
}
m.appendTail(sbf);

System.out.printf("%s", sbf);
```


# Examples

``` java SimpleEmailMatcher
String EMAIL_PATTERN = "([a-zA-Z0-9._]+)@((\\w+\\.)+\\w+)";
Matcher m = Pattern.compile(EMAIL_PATTERN).matcher("richd.yang@gmail.com");
while(m.find()) {
    System.out.printf("Found \"%s\" (%d - %d) %n", m.group(), m.start(), m.end());
    for(int i=0; i <= m.groupCount(); ++i) {
        System.out.printf("Group %d: %s (%d - %d) %n", i, m.group(i), m.start(i), m.end(i));
    }
}
```


> Found "richd.yang@gmail.com" (0 - 20) 
> Group 0: richd.yang@gmail.com (0 - 20) 
> Group 1: richd.yang (0 - 10) 
> Group 2: gmail.com (11 - 20) 
> Group 3: gmail. (11 - 17) 

Now we can get the Email domain from group 2.

``` java SimpleIPMatcher
String IPADDRESS_PATTERN =
        "^([0-9]|[1-9]\\d|2[0-4]\\d|25[0-5])\\." +
         "([0-9]|[1-9]\\d|2[0-4]\\d|25[0-5])\\." +
         "([0-9]|[1-9]\\d|2[0-4]\\d|25[0-5])\\." +
         "([0-9]|[1-9]\\d|2[0-4]\\d|25[0-5])$";
Matcher m = Pattern.compile(IPADDRESS_PATTERN).matcher("10.233.29.5");
while(m.find()) {
    System.out.printf("Found \"%s\" (%d - %d) %n", m.group(), m.start(), m.end());
    for(int i=0; i <= m.groupCount(); ++i) {
        System.out.printf("Group %d: %s (%d - %d) %n", i, m.group(i), m.start(i), m.end(i));
    }
}
```


> Found "10.233.29.5" (0 - 11) 
> Group 0: 10.233.29.5 (0 - 11) 
> Group 1: 10 (0 - 2) 
> Group 2: 233 (3 - 6) 
> Group 3: 29 (7 - 9) 
> Group 4: 5 (10 - 11) 

reference: [how to validate ip address](http://www.mkyong.com/regular-expressions/how-to-validate-ip-address-with-regular-expression/)

# Further references

- [Oracle Java Tutorial for Regular Expressions](https://docs.oracle.com/javase/tutorial/essential/regex/index.html)

- [Mastering Regular Expressions](http://www.amazon.com/Mastering-Regular-Expressions-Jeffrey-Friedl/dp/0596528124)

- [Java Regex Tester online](http://regexr.com/)

