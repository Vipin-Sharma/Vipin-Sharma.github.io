---
layout:     post
title:      "Can NullPointerException be helpful?"
subtitle:   "NullPointerException "
date:       2020-07-02 01:00:00
author:     "Vipin Sharma"
header-img: "img/posts/blog-post-bg2.jpeg"
comments: true
tags: [java, JDK14]
---

<!-- Attention -->
### Have you ever received NullPointerException ?
Every Java developer has encountered NullPointerExceptions, this is most common programming error new Java Developers face.

This is one simple programming mistake, I am trying to call method size on null list.

```java
public class NullPointerExceptionDemo {
    public static void main(String[] args) {
        List<Integer> list = null;
        System.out.println(list.size());
    }
}
```

This is output of running NullPointerExceptionDemo

```
Exception in thread "main" java.lang.NullPointerException
at com.vip.jdk14.NullPointerExceptionDemo.main(NullPointerExceptionDemo.java:8)

Process finished with exit code 1
```

In above example it is clear list is null but several other situation it is not easy to understand root cause. See following example.

```java
    System.out.println(objectA.getObjectB().getObjectC().getObjectD());
```
Here you may get NullPointerException, when ***objectA*** or ***objectA.getObjectB()*** or ***objectA.getObjectB().getObjectC()*** any of them is null. And you get same NullPointerException message in all these cases.

<br>
<!-- Interest -->
### Java 14 can show you root cause of NullPointerException

Now Java 14 has added language feature to show root cause of NullPointerException.
This language feature has been part of SAP commercial JVM since 2006.


<br>
<!-- Desire -->
### Helpful NullPointerException

In Java 14 you can start passing -XX:+ShowCodeDetailsInExceptionMessages in VM arguments and it helps you to see root cause of NullPointerException.
In Java 14 option is disabled by default and in later releases this will be enabled by default.

```java
public class HelpfulNullPointerExceptionDemo {
    public static void main(String[] args) {
        List<Integer> list = null;
        System.out.println(list.size());
    }
}
```
Running this program prints following Exception message, and root cause is clearly written ***list is null***.

```
Exception in thread "main" java.lang.NullPointerException: Cannot invoke "java.util.List.size()" because "list" is null
at com.vip.jdk14.HelpfulNullPointerExceptionDemo.main(HelpfulNullPointerExceptionDemo.java:8)

Process finished with exit code 1
```

<br>
<!-- Action -->
### At the end

To learn best java language features download my ebook [5 steps to Best Java Jobs](https://jfeatures.com/) for Free.

Follow me on twitter [@vipinbit](https://twitter.com/vipinbit) to get daily tips like this on Java Language Features.
