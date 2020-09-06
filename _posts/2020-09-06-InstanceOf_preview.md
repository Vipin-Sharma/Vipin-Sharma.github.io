---
layout:     post
title:      "Pattern matching for the instanceof operator"
subtitle:   "Java 15 instanceof operator"
date:       2020-09-06 01:00:00
author:     "Vipin Sharma"
header-img: "img/posts/blog-post-bg2.jpeg"
comments: true
tags: [java, JDK14, JDK15]
---

The initial draft, work in progress.

<!-- Attention -->
### Why pattern matching for instanceof was required?

let's try to understand problems in current instanceof we are trying to solve.
Following is common programming idiom for instanceof-and-cast pattern: 

```java
if (obj instanceof String) {
    String s = (String) obj;
    // use s
}
```

Three tasks are done here:
1. Test (is obj a String?)
2. A conversion (casting obj to String) 
3. The declaration of a new local variable (s) so we can use the string value. 

This pattern has 3 problems.

1. It is tedious: doing both the type test and cast should be unnecessary 
(what else would you do after an instanceof test?).
2. This boilerplate: in particular, the three occurrences of the type String 
 obfuscates the more significant logic that follows.
3. Most importantly, the repetition provides opportunities for errors to creep 
unnoticed into programs.

Java solves these problems by the pattern matching.
<!-- 
    Pattern matching allows the desired 'shape' of an object to be expressed concisely 
    ***(the pattern)***, and for various statements and expressions to test that 
    'shape' against their input ***(the matching)***.
-->

<br>

<!-- Interest -->
### Type test pattern with instanceof operator

A pattern is a combination of
 1. a predicate that can be applied to a target, and 
 2. a set of binding variables that are extracted from the target only 
    if the predicate successfully applies to it.

A type test pattern consists of a predicate that specifies a type,
along with a single binding variable.


The instanceof operator is extended to take a type test pattern instead of just a type. 
In the code below, Number number is the type test pattern:

```java
if (integer instanceof Number number)
{
    System.out.println(number.intValue());
}
```


<br>

<!-- Desire -->
<!-- ###  Scope of binding variable used with instanceof operator -->
###  Flow sensitive scoping rules for instanceof binding variable

The scope is one of the best parts in this language feature, the binding 
variable is only available where it is required. It makes code bug free.

To understand the scope we will go through 3 code examples.

##### Basic case
Let's take a look at the basic scenario, following code example.

```java             
if (integer instanceof Number number) {
    System.out.println(number.intValue());
} else {
    System.out.println("Binding variable number not accessible here");
}
```

In this example, the instanceof operator "matches" the target number to the type 
test pattern as follows:
   1. if number is an instance of String, then it is cast to String
        and assigned to the binding variable s.
   2. The binding variable is in scope in the true block of the if statement, 
        and not in the false block of the if statement.

<br>

##### expression with short circuit && operator
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

Here in the above example s.length() will be called only when obj is instance of String. 
That's why it makes sense to have s accessible on right-hand side of the short circuit operator as well.
When both conditions in short circuit && operator are true, which also means s is
 a String, s is accessible in if block as well.


<br>

##### expression with short circuit \|\| operator
Following is an example with \|\| operator.

```java
if (obj instanceof String s || s.length() > 3)
{
    System.out.println(s.charAt(1));
}
```

In the above example when obj is not String, it doesn't get cast and then assigned to the binding variable.s
The binding variable s is not in scope on the right-hand side of the ||
operator, nor is it in scope in the true block. 
s at these points refers to a field in the enclosing class, if any available. 
Otherwise, it shows compilation error.

<br>

### At the end

To learn the best java language features download my ebook [🔥***5 steps to Best Java Jobs***🔥](https://jfeatures.com/) for Free.

Follow me on twitter [🔥***@vipinbit***🔥](https://twitter.com/vipinbit) to get daily tips like this on Java Language Features.

### Resources
1. https://openjdk.java.net/jeps/375, This is Java enhancement proposal for Pattern Matching for instanceof in JDK15.
2. https://github.com/Vipin-Sharma/JDK15Examples, this is link to code examples used in this post.
3. https://www.infoq.com/presentations/java-futures-2019/ , Presentation by Java Language Architect Brian Goetz.