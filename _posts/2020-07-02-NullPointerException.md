---
layout:     post
title:      "Can NullPointerException be more helpful ?"
subtitle:   "NullPointerException "
date:       2020-07-02 01:00:00
author:     "Vipin Sharma"
header-img: "img/posts/blog-post-bg2.jpeg"
comments: true
tags: [java, JDK14]
---

(***This is initial draft for post, work in progress***)
<!-- Attention -->
### Who has never received NullPointerException ?
Every Java developer has encountered NullPointerExceptions, This is most common programming error professional Java Developers face.

<!-- @todo: Add example of NullPointerException showing how we can not figure out root cause of NullPointerException. -->

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

In real life situation, every time we get NullPointerException we have to carefully analyze which variable was null.
<!-- @todo Add example showing real life difficult to find example -->

<br>
<!-- Interest -->
### Java 14 can show you root cause of NullPointerException

<!-- @todo Show history/background of this feature, SAP JVM has this since 2006 -->
Now Java 14 has added language feature to show root cause of NullPointerException.
This language feature has been part of SAP commercial JVM since 2006.


<br>
<!-- Desire -->
### Helpful NullPointerException
<!-- @todo Show working program and how new language feature helps to find root casue. -->

In Java 14 you can start passing -XX:+ShowCodeDetailsInExceptionMessages in VM arguments and it help you to see root cause of NullPointerException.
Initially This option is disabled by default and in later releases this will be enabled by default.

```java
public class HelpfulNullPointerExceptionDemo {
    public static void main(String[] args) {
        List<Integer> list = null;
        System.out.println(list.size());
    }
}
```
Running this program prints following Exception message, and root cause is clear ***list is null***.

```
Exception in thread "main" java.lang.NullPointerException: Cannot invoke "java.util.List.size()" because "list" is null
at com.vip.jdk14.HelpfulNullPointerExceptionDemo.main(HelpfulNullPointerExceptionDemo.java:8)

Process finished with exit code 1
```

<br>
<!-- Action -->
### At the end
Get your free copy of [5 steps to Best Java Jobs](https://jfeatures.com/)

To learn Java language features you can join [mailing list](https://jfeatures.com/) and follow me on twitter [@vipinbit](https://twitter.com/vipinbit).
