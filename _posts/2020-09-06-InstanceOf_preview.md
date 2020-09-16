---
layout:     post
title:      "Pattern matching for the instanceof operator"
subtitle:   "One more step to readable and bug free code in Java"
date:       2020-09-06 01:00:00
author:     "Vipin Sharma"
header-img: "img/posts/blog-post-bg2.jpeg"
comments: true
tags: [java, JDK14, JDK15]
---

The initial draft, work in progress.

<!-- Attention --> 
### Boilerplate and error prone code with instanceof prior to java 14

Let's try to understand problems with instanceof operator prior to java 14.
Following is common programming idiom for instanceof-and-cast pattern:

```java
if (obj instanceof String) 
{
  String s = (String) obj;
  int length = s.length();
}
```

Three tasks are done here:
1. Test (is `obj` a String?)
2. A conversion (casting `obj` to `String`) 
3. The declaration of a new local variable (`s`) so we can use the string value. 

This pattern has 3 problems.

1. ***It is tedious***: doing both the type test and cast should be unnecessary 
(what else would you do after an instanceof test?).
2. ***This boilerplate***: in particular, the three occurrences of the type `String`
 obfuscates the more significant logic that follows.
3. ***Repetition***, repetition of `String` provides opportunities 
for errors to creep unnoticed into programs.

Java solves these problems by the pattern matching.
Now with improved instanceof operator same code is written as below:

```java
if (obj instanceof String s) 
{
    int length = s.length();
}
``` 
Before going to details of this language feature it is worth understanding 
what exactly is the pattern matching and important terminologies related to 
pattern matching in instanceof.

<br>
<!-- Interest -->

### Pattern matching

***A pattern*** is a combination of
1. a match predicate that determines if the pattern matches a target
2. a set of pattern variables that are conditionally extracted if the pattern matches the target.

***A type test pattern*** consists of 
1. a predicate that specifies a type 
2. a single binding variable.


Prior to java 14 the instanceof operator was taking just a type now it
is extended to take `a type test pattern`.

This is example of improved instanceof operator
```java
if (obj instanceof String s) {
    ...
}
```

In this example the instanceof operator ***matches*** the target obj to the type test pattern 
as follows:
1. Check if obj is an instance of String 
2. If above check is true then cast obj to String and assigned to the binding variable s.

This is overall pattern matching for instanceof operator.

<br>

<!-- Desire -->
<!-- ###  Scope of binding variable used with instanceof operator -->
###  Helpful flow sensitive scoping rules for instanceof binding variable

The scope is one of the best parts in this language feature, the binding 
variable is only available where it is required. It makes code bug free.

To understand the scope we will go through 3 code examples.

1. ***Basic case***
2. ***expression with short circuit && operator***
3. ***expression with short circuit \|\| operator***

<br>
#### Basic case
Let's take a look at the basic scenario, following code example.

```java
if (integer instanceof Number number) {
    System.out.println(number.intValue());
} else {
    System.out.println("Binding variable number not accessible here");
}
```

In this example, the `instanceof` operator "matches" the target number to the type
test pattern as follows:
   1. if number is an instance of `Number`, then it is cast to `Number`
        and assigned to the binding variable `number`.
   2. The binding variable is in scope in the true block of the if statement,
        and not in the false block of the if statement.

<br>

#### expression with short circuit && operator
Now we will take an example of a little complex expression short circuit && operator.

```java
Object obj = "Name";
if (obj instanceof String s && s.length() > 3)
{
    System.out.println(s.charAt(1));
}
else
{
    System.out.println("s not accessible here");
}
```

Here in the above example `s.length()` will be called only when `obj` is instance of `String`. 
That's why it makes sense to have `s` accessible on right-hand side of the short circuit operator as well.
When both conditions in short circuit && operator are true, which also means `s` is
 a `String`, `s` is accessible in if block as well.


<br>

#### expression with short circuit \|\| operator
This is an example with `||` operator, it has compilation error at `s.length()` and `s.charAt(1)`

```java
if (obj instanceof String s || s.length() > 3)
{
    System.out.println(s.charAt(1));
}
```

Here when obj is String, it doesn't need to evaluate expression on right side, 
so s not accessible on right side expression (`s.length() > 3`).  
When `obj` is not `String`, it doesn't get cast and then assigned to the binding variable.
The binding variable `s` is not in scope on the right-hand side of the `||`
operator, nor it is in scope in the true block, and we get compilation error.

<br>

### At the end

Knowing language features like this helps you get the best java jobs, that's why to help you
I wrote ebook [5 steps to Best Java Jobs](https://jfeatures.com/).
Download this step by step guide for free!

[<img src="../img/ebook_upd.png" width="200" height="200">](https://jfeatures.com/)

### Resources
1. https://openjdk.java.net/jeps/375, This is Java enhancement proposal for Pattern Matching for instanceof in JDK15.
2. https://github.com/Vipin-Sharma/JDK15Examples, this is link to code examples used in this post.
3. https://www.infoq.com/presentations/java-futures-2019/ , Presentation by Java Language Architect Brian Goetz.