---
layout:     post
title:      "Java Multi release jar"
subtitle:   "JDK 9 introduced new features Multi release jar with JEP 238"
date:       2017-10-28 12:00:00
author:     "Vipin Sharma"
header-img: "img/posts/blog-post-bg1.jpeg"
comments: true
tags: java
---

**In this post I would like to explain how to build multi release jar. To provide brief overview of this: It is new feature introduced in jdk9 [JEP-238](http://openjdk.java.net/jeps/238), now you can have 2 versions of class files and can control which version should be picked up for which jdk version.**

Here I want to build 2 versions of A.java in one jar, jdk8 should run jdk8 specific version and jdk9 should run jdk9 specific version.

Created dir javacode, inside this java8 and java9. and both have A.java

```
XXXX@XXX-MacBook-Air.local:~/javacode/java8/com/vipin/exp$ pwd
/Users/XXXX/javacode/java8/com/vipin/exp
XXXX@XXX-MacBook-Air.local:~/javacode/java8/com/vipin/exp$ cat A.java 
package com.vipin.exp;
public class A{
    public static void main(String[] args){
	System.out.println("Inside java8 version");
    }
}
```

```
XXXX@XXX-MacBook-Air.local:~/javacode/java9/com/vipin/exp$ cat A.java 
package com.vipin.exp;
public class A{
    public static void main(String[] args){
	System.out.println("Inside java9 version");
    }
}
```

**This is my directory structure:**

```
XXXX@XXX-MacBook-Air.local:~/javacode$ tree
.
|____java8
| |____com
| | |____vipin
| | | |____exp
| | | | |____A.java
|____java9
| |____com
| | |____vipin
| | | |____exp
| | | | |____A.java
```


**Compiling source:**

```
XXXX@XXX-Air:~/javacode$ javac --release 9 -d /Users/XXXX/javacode/java9/ java9/com/vipin/exp/A.java
XXXX@XXX-Air:~/javacode$ javac --release 8 -d /Users/XXXX/javacode/java8/ java8/com/vipin/exp/A.java
```
Dir structure after compiling source code:
```
XXXX@XXX-MacBook-Air.local:~/javacode$ tree
.
|____java8
| |____com
| | |____vipin
| | | |____exp
| | | | |____A.class
| | | | |____A.java
|____java9
| |____com
| | |____vipin
| | | |____exp
| | | | |____A.class
| | | | |____A.java
```

**Creating multi release jar:**

```
XXXX@XXX-MacBook-Air.local:~/javacode$ jar -c -f vipin.jar -C java8 . --release 9 -C java9 .
Warning: entry META-INF/versions/9/com/vipin/exp/A.java, multiple resources with same name
XXXX@XXX-MacBook-Air.local:~/javacode$ jar -tvf vipin.jar 
     0 Wed Oct 18 19:06:26 IST 2017 META-INF/
    82 Wed Oct 18 19:06:26 IST 2017 META-INF/MANIFEST.MF
     0 Tue Oct 17 18:02:04 IST 2017 com/
     0 Tue Oct 17 18:02:04 IST 2017 com/vipin/
     0 Tue Oct 17 23:26:56 IST 2017 com/vipin/exp/
   430 Wed Oct 18 19:00:38 IST 2017 com/vipin/exp/A.class
   136 Tue Oct 17 22:49:20 IST 2017 com/vipin/exp/A.java
     0 Tue Oct 17 20:00:34 IST 2017 META-INF/versions/9/
     0 Tue Oct 17 20:00:34 IST 2017 META-INF/versions/9/com/
     0 Tue Oct 17 20:00:34 IST 2017 META-INF/versions/9/com/vipin/
     0 Tue Oct 17 23:27:04 IST 2017 META-INF/versions/9/com/vipin/exp/
   430 Wed Oct 18 19:02:04 IST 2017 META-INF/versions/9/com/vipin/exp/A.class
   135 Tue Oct 17 22:49:26 IST 2017 META-INF/versions/9/com/vipin/exp/A.java
```


**Now multi release jar is ready, lets see it if it works as expected.**
   
Running class A with jdk9:
```
XXXX@XXX-MacBook-Air.local:~/javacode$ java -version
java version "9"
Java(TM) SE Runtime Environment (build 9+181)
Java HotSpot(TM) 64-Bit Server VM (build 9+181, mixed mode)
XXXX@XXX-MacBook-Air.local:~/javacode$ java -cp vipin.jar com.vipin.exp.A
Inside java9 version
```


Running class A with jdk8:
```
XXXX@XXX-MacBook-Air.local:~/javacode$ /Library/Java/JavaVirtualMachines/jdk1.8.0_111.jdk/Contents/Home/bin/java -version
java version "1.8.0_111"
XXXX@XXX-MacBook-Air.local:~/javacode$ /Library/Java/JavaVirtualMachines/jdk1.8.0_111.jdk/Contents/Home/bin/java -cp vipin.jar com.vipin.exp.A
Inside java8 version
```

With output it is clear, version of java class is executed as per jdk version!
