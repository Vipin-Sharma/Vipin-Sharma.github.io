---
layout:     post
title:      "jdb: Java Command line debugger"
subtitle:   "Debugging java application without a professional IDE"
date:       2021-02-01 00:00:00
author:     "Vipin Sharma"
header-img: "img/posts/blog-post-bg2.jpeg"
comments: true
tags: [OpenJDK, tools]
---

***This is draft post, work in progress:***

### jdb Utility

Professional java developers use some IDE for debugging/troubleshooting applications. Intellij Idea, Eclipse and NetBeans are some popular IDEs. Do you know JDK also provides command line debugger `jdb`?

If you don't have access to any professional IDE, and you have access to JDK, you can use jdb. The jdb command line utility is included in the JDK. We can connect local or remote JVM using this for inspection or debugging.

jdb is available in jdk/bin directory. It uses the Java Debug Interface (JDI) to launch or connect to the target JVM. The Java Debug Interface (JDI) provides a Java programming language interface for debugging Java programming language applications. JDI is a part of the [Java Platform Debugger Architecture](https://docs.oracle.com/en/java/javase/15/docs/specs/jpda/architecture.html).

<br>

### Troubleshoot java application with the jdb Utility

This section we will see how to attach jdb to java application and start debugging and monitoring.


#### jdb command

This is format of the jdb command:

    jdb [options] [classname] [arguments]

    options:    This represents the jdb command-line options (e.g. attach, launch).
    classname:  This represents the name of the main class to debug.
    arguments:  This represents the arguments that are passed to the main() method of the class.

<br>

#### Java program we will be debugging in this post

Following is sample class we are going to debug and try to understand different features available. It is important to compile this class with -g option (javac -g Test.java) in order to see local variables while debugging.

```java
public class Test
{
	public static void main(String[] args)
	{
		System.out.println("First Line of main function");
        System.out.println("Second Line of main function");
        System.out.println("Third Line of main function");
		
		int i=0;
		System.out.println("i: " + i);
		i = 2;
		System.out.println("i: " + i);

		while(true)
		{
		}
	}
}
```
<br>

#### Attach jdb to Java application

Below command is the most common way to start application with jdb debugger, here we are not passing any jdb option, only class name Test is passed, and class Test doesn't require any argument.

    jdb Test

In this way we start executing main class Test in the similar way we start in professional IDE eclipse or intellij. jdb stops the JVM before executing that class's first instruction.

Another way to use the jdb command is by attaching it to a JVM that's already running. Syntax for starting JVM with debugger port is: 

    java -agentlib:jdwp=transport=dt_socket,server=y,suspend=n,address=*:5005 Test

To attach jdb with this remote jvm use below syntax:

    jdb -attach 5005

<br>

#### debugging and monitoring

For this post we are not using remote debugging, using following command to attach with jvm:

    /jdk/bin/jdb Test
    Initializing jdb ...

Setting a break point at line number 5 using `stop`:

    > stop at Test:5
    Deferring breakpoint Test:5.
    It will be set after the class is loaded.

Start execution of application's main class using `run`:

    > run Test
    run  Test
    Set uncaught java.lang.Throwable
    Set deferred uncaught java.lang.Throwable
    >
    VM Started: Set deferred breakpoint Test:5
    
    Breakpoint hit: "thread=main", Test.main(), line=5 bci=0
    5    		System.out.println("First Line of main function");

execute current line using `step`:

    main[1] step

    > First Line of main function
    
    Step completed: "thread=main", Test.main(), line=6 bci=8
    6            System.out.println("Second Line of main function");

execute current line using `step`:

    main[1] step

    > Second Line of main function
    
    Step completed: "thread=main", Test.main(), line=7 bci=16
    7            System.out.println("Thrid Line of main function");

execute current line using `step`:, this is also an example of messed up console print by 2 threads.

    main[1] step
    > Third Line of main fu
    nStep completed: ction
    "thread=main", Test.main(), line=9 bci=24
    9    		int i=0;

execute current line using `step`:

    main[1] step
    >
    Step completed: "thread=main", Test.main(), line=10 bci=26
    10    		System.out.println("i: " + i);

printing local variable i using `print`:

    main[1] print i
    i = 0

printing all local variables in current stack frame using `locals`:

    main[1] locals
    Method arguments:
    args = instance of java.lang.String[0] (id=841)
    Local variables:
    i = 0

dump a thread's stack using `where`:

    main[1] where
    [1] Test.main (Test.java:10)

list threads in running application using `threads`:

    main[1] threads
    Group system:
    (java.lang.ref.Reference$ReferenceHandler)804 Reference Handler   running
    (java.lang.ref.Finalizer$FinalizerThread)805  Finalizer           cond. waiting
    (java.lang.Thread)806                         Signal Dispatcher   running
    (java.lang.Thread)803                         Notification Thread running
    Group main:
    (java.lang.Thread)1                           main                running
    Group InnocuousThreadGroup:
    (jdk.internal.misc.InnocuousThread)807        Common-Cleaner      cond. waiting

continue execution from the breakpoint using `cont`:

    main[1] cont
    > i: 0
    i: 2

coming out of jdb:

    quit


### Conclusion

Knowing features like this helps you get the best java jobs, that's why to help you I wrote ebook [5 steps to Best Java Jobs](https://jfeatures.com/). Download this step by step guide for free!

[<img src="../img/ebook_upd.png" width="200" height="200">](https://jfeatures.com/)
