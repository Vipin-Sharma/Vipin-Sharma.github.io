---
layout:     post
title:      "Pattern matching for the instanceof operator"
subtitle:   "One more step towards readable and bug-free code in Java"
date:       2020-09-06 01:00:00
author:     "Vipin Sharma"
header-img: "img/posts/blog-post-bg2.jpeg"
comments: true
tags: [java, JDK14, JDK15]
---

### Repetitive and error-prone code with instanceof pre java 14

<!-- 
Let's try to understand problems with instanceof operator before java 14.
Following is common programming idiom for instanceof-and-cast pattern:
-->
Following is typical example of instanceof operator before java 14.

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

Java solves these problems by pattern matching. instanceof pattern matching is preview features in Java 14 and 15.
Using this same code is written as below, here you will not see the three problems we discussed.

```java
if (obj instanceof String s)
{
    int length = s.length();
}
```

It was basic example of instanceof pattern matching.
It simplifies messy operations. We will take 2 more examples to show how it simplifies code.   

<br>

#### 1. Simplification of equals method implementation
For a class Point, we might implement equals() as follows

```java
class Point
{
    int x;
    int y;
}
```

```java
public boolean equals(Object o) {
    if (!(o instanceof Point))
        return false;
    Point other = (Point) o;
    return x == other.x 
        && y == other.y;
}
```

Using a pattern match instead, we can combine this into a single expression, 
eliminating the repetition and simplifying the control flow

```java
public boolean equals(Object o) {
    return (o instanceof Point other)
        && x == other.x
        && y == other.y;
}
```

<br>

#### 2. Simplifying if else chain with instanceof pattern matching

Following is the code of if else chain with instanceof before java 14

```java
private static void oldInstanceOf(Object obj)
    {
        String formatted = "unknown";
        if (obj instanceof Integer) {
            int i = (Integer) obj;
            formatted = String.format("int %d", i);
        }
        else if (obj instanceof Byte) {
            byte b = (Byte) obj;
            formatted = String.format("byte %d", b);
        }
        else if (obj instanceof Long) {
            long l = (Long) obj;
            formatted = String.format("long %d", l);
        }
        else if (obj instanceof Double) {
            double d = (Double) obj;
            formatted = String.format("double %f", d);
        }
        else if (obj instanceof String) {
            String s = (String) obj;
            formatted = String.format("String %s", s);
        }
        System.out.println(formatted);
    }
```

Now below is the code of if else chain using instanceof pattern matching

```java
    private static void newInstanceOf(Object obj) {
        String formatted = "unknown";
        if (obj instanceof Integer i) {
            formatted = String.format("int %d", i);
        }
        else if (obj instanceof Byte b) {
            formatted = String.format("byte %d", b);
        }
        else if (obj instanceof Long l) {
            formatted = String.format("long %d", l);
        }
        else if (obj instanceof Double d) {
            formatted = String.format("double %f", d);
        }
        else if (obj instanceof String s) {
            formatted = String.format("String %s", s);
        }
        System.out.println(formatted);
    }
```

Before going to details of this language feature it is worth understanding what exactly is the pattern matching and important terminologies related to pattern matching in instanceof.

<br>
### What is Pattern matching?

***A pattern*** is a combination of
1. a match predicate that determines if the pattern matches a target
2. a set of pattern variables that are conditionally extracted if the pattern matches the target.

***A type test pattern*** consists of 
1. a predicate that specifies a type 
2. a single binding variable.


Before java 14 the instanceof operator was taking just a type now it
is extended to take `a type test pattern`.

This is an example of an improved instanceof operator
```java
if (obj instanceof String s) {
    ...
}
```

In this example the instanceof operator ***matches*** the target obj to the type test pattern 
as follows:
1. Check if `obj` is an instance of `String` 
2. If the above check is true then cast `obj` to `String` and assigned to the binding variable `s`.

This is overall pattern matching for instanceof operator.

To understand this feature completely we will need to understand scoping rules for binding variable. Next section we will try to understand scoping rules. 

<br>

###  Helpful flow sensitive scoping rules for instanceof binding variable

The scope is one of the best part of this language feature, the binding variable is available only where it is required. ***It makes code bug free.***

To understand the scope of binding variable we will go through 3 examples.

1. ***Basic case***
2. ***expression with short circuit && operator***
3. ***expression with short circuit \|\| operator***

<br>
#### Basic case
Let's take a look at the basic scenario, using following code example.

```java
if (integer instanceof Number number) {
    System.out.println(number.intValue());
} else {
    System.out.println("Binding variable number not accessible here");
}
```

In this example, the `instanceof` operator "matches" the target number to the type
test pattern as follows:
   1. if the number is an instance of `Number`, then it is cast to `Number`
        and assigned to the binding variable `number`.
   2. The binding variable is in scope of the true block of the if statement,
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

Here when obj is String, it doesn't need to evaluate the expression on the right side, 
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
4. https://cr.openjdk.java.net/~briangoetz/amber/pattern-match.html