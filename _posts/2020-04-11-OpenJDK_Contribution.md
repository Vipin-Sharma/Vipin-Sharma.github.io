---
layout:     post
title:      "How to start contribution in OpenJDK"
subtitle:   "How to start contribution in OpenJDK "
date:       2021-04-11 01:00:00
author:     "Vipin Sharma"
header-img: "img/posts/OpenJDK_SS.jpeg"
comments: true
tags: [java, OpenJDK]
---

## How to start contribution in OpenJDK

First link to read: https://openjdk.java.net/contribute/ , in this "Become a Contributor" is first step, get OCA signed it might take couple of days time, meanwhile you can prepare your development env ready for contribution.

follow this link to prepare your development env
https://hg.openjdk.java.net/jdk/jdk11/raw-file/tip/doc/building.html#tldr-instructions-for-the-impatient



1. Get the complete source code:
hg clone http://hg.openjdk.java.net/jdk/jdk

2. Run configure:
bash configure

  If configure fails due to missing dependencies (to either the toolchain, build tools, external libraries or the boot JDK), most of the time it prints a suggestion on how to resolve the situation on your platform. Follow the instructions, and try running bash configure again.

3. Run make:
  make images

4. Verify your newly built JDK:
./build/*/images/jdk/bin/java -version

5. Run basic tests:
make run-test-tier1


In case you stuck anywhere in above steps, you can find solution on [this page](https://hg.openjdk.java.net/jdk/jdk11/raw-file/tip/doc/building.html)


First time when I run configure command, it failed due to some external libraries missing, but I got message saying install this library and it worked after that.

<!-- todo That reminds me, one of the place library name had problem in Small/Upper case, I should submit patch for that. -->

I got problem while running tests and found below on the page:

"Most of the JDK tests are using the JTReg test framework. Make sure that your configuration knows where to find your installation of JTReg. If this is not picked up automatically, use the --with-jtreg=<path to jtreg home> option to point to the JTReg framework. Note that this option should point to the JTReg home, i.e. the top directory, containing lib/jtreg.jar etc."


I have jtreg source code as well so in my case this option was something like this:
--with-jtreg=/home/vipin/IdeaProjects/jtreg/build/images/jtreg

Once your setup is successful and you execute tests, result will be something like this:

At end of test you will see below output:

```
==============================
Test summary
==============================
   TEST                                              TOTAL  PASS  FAIL ERROR   
   jtreg:test/hotspot/jtreg:tier1                     1481  1481     0     0   
   jtreg:test/jdk:tier1                               1894  1894     0     0   
   jtreg:test/langtools:tier1                         4027  4027     0     0   
   jtreg:test/nashorn:tier1                              0     0     0     0   
   jtreg:test/jaxp:tier1                                 0     0     0     0   
==============================
TEST SUCCESS

Formatted output:

==============================
Test summary
==============================
   TEST                                              	TOTAL  PASS  FAIL ERROR   
   jtreg:test/hotspot/jtreg:tier1                     1481  	1481     0     0   
   jtreg:test/jdk:tier1                               1894  	1894     0     0   
   jtreg:test/langtools:tier1                         4027  	4027     0     0   
   jtreg:test/nashorn:tier1                             0     0     0     0   
   jtreg:test/jaxp:tier1                                 0     0     0     0   
==============================
TEST SUCCESS
```

After this you may want to setup jdk project in IDE. I use Intellij and we have a shell script idea.sh comes along with jdk to setup project for you. I wanted to setup java.base only so my command was:
idea.sh java.base

In case you want to setup all modules then just run idea.sh.

Once you identify what you would contribute and then you need to prepare patch using webrev.sh

Sample command is:

<!-- todo write command-->

After creating patch you need to create bug id, send email to corresponding email list (add email list link). Initially someone should sponsor your fix and create bug Id on your behalf.

Once you have bug id, submit review request. The review request should be clearly marked as such: "RFR <bug-id>: <synopsis>"

e.g.
RFR 8240524: Removed warnings from test classes

You can not attach anything and send to email list, if this is small patch then you can add patch text in email or talk to sponsor how do they want to accept it.

### Resources:    

1.

<br>

### At the end : --
To learn more Java language features you can join [mailing list](https://jfeatures.com/) and follow me on twitter [@vipinbit](https://twitter.com/vipinbit).
