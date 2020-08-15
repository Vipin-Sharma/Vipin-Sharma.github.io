---
layout:     post
title:      "Readable Java multiline Strings using Text Blocks"
subtitle:   "Text Blocks "
date:       2020-08-01 01:00:00
author:     "Vipin Sharma"
header-img: "img/posts/blog-post-bg2.jpeg"
comments: true
tags: [java, JDK15]
---

Initial draft, work in progress.

<!-- Attention -->
### Do you find Java multiline Strings non readable ?
One of the goal for text blocks is to Simplify the task of writing Java programs 
by making it easy to express strings that span several lines of source code.
It makes code readable and bug free. Let's try to understand its importance using following example.

Can you spot a bug in below multiline String?

```java
String oldStringSQLExample = "select emp_id, emp_name, emp_num_of_kids, emp_active" +
                "from employee_table" +
                "where employee_num_of_kids =1";

System.out.println(oldStringSQLExample);
```
Following is the console output for oldStringSQLExample. 

> select emp_id, emp_name, emp_num_of_kids, ***emp_<ins><span style="color:blue">activefrom</span></ins>***
> ***employee_<ins><span style="color:blue">tablewhere</span></ins>*** employee_num_of_kids =1

In this output notice words underlined in blue, here we don't have spaces in between words of consecutive lines. 
It causes SQLSyntaxErrorException.
Next section we will see how this problem can be solved by new language feature text blocks. 

<br>

<!-- Interest -->
### Java 14 introduced Text Blocks, readable multiline Strings.

Now the same code we are writing using Java Text Blocks.
<!-- todo show this on webpage, maybe ss, because this is clearly visible right now, it all comes in one line -->


    String sqlWithTextBlocks = """
                    select emp_id, emp_name, emp_num_of_kids, emp_active      
                    from employee_table      
                    where employee_num_of_kids =1 
                    """; 
    
Following is console output for text blocks, it is same as we have written in code. Space between words issue is resolved in this.

> select emp_id, emp_name, emp_num_of_kids, emp_active
> from employee_table
> where employee_num_of_kids =1



<br>

<!-- Desire -->
### The deep dive into Text block syntax 

A text block consists of zero or more content characters, enclosed by opening and closing delimiters.

1. ***The opening delimiter*** is a sequence of three double quote characters (\"\"\") followed by zero or more white spaces followed by a line terminator.
2. ***The closing delimiter*** is a sequence of three double quote characters.
3. ***The content*** begins at the first character after the line terminator of the opening delimiter and ends before closing delimiter.

<!--
>     """
>     line 1    
>     line 2    
>     line 3    
>     """

Above text block is equivalent to the string literal:

> line 1\nline 2\nline 3\n
-->

Compile-time processing

The content of a text block is processed by the Java compiler in three steps in the same sequence as given below:

1.    ***Line terminators***:               Line terminators in the content are translated to LF (\u000A).
    
2.    ***Common white spaces removal***:           Incidental white space surrounding the content, introduced to match the indentation of Java source code, is removed.
    
3.    ***Escape sequence processing***:     Escape sequences in the content are interpreted. Performing interpretation as the final step means developers can write escape sequences such as \n without them being modified or deleted by earlier steps.


The following sections discuss compile-time processing in more detail.

#### Line terminators
Different operating systems have their [Line terminators](https://en.wikipedia.org/wiki/Newline).
All line terminators (CR/LF/CRLF) in the content are translated to LF (\u000A). 
It makes same java code work across platforms.

#### White space removal
<!--
    Incidental white space surrounding the content, introduced to match
    the indentation of Java source code, is removed.
    
    Example 1:
    
    Here is the HTML example using dots to visualize the 14 spaces per line that is added for indentation:
    
    String html = """
    ..............<html>
    ..............    <body>
    ..............        <p>Hello, world</p>
    ..............    </body>
    ..............</html>
    ..............""";
    
    In this example 14 initial spaces are removed by compiler, and output will be:
    
    |<html>|
    |    <body>|
    |        <p>Hello, world</p>|
    |    </body>|
    |</html>|
    
    Example 2:  Trailing space
    
    Here is the HTML example reimagined with some trailing white space, again using dots to visualize spaces:
    
    String html = """
    ..............<html>...
    ..............    <body>
    ..............        <p>Hello, world</p>....
    ..............    </body>.
    ..............</html>...
    ..............""";
    
    Following is output, here we are using | to visualize margins, we can see trailing white space is removed.
    
    |<html>|
    |    <body>|
    |        <p>Hello, world</p>|
    |    </body>|
    |</html>|
    
    @todo There are 3 ways to keep trailing whitespaces, suggested by JDK team.
    
    Example 3: Significant trailing line policy
    Moving position of closing delimiter can affect white space stripping. 
    For example when closing delimeter is moved all the way to the left, it completely avoids white space stripping.
    We will look at examples to understand it better.
    
-->
Following two things help us understand whitespace removal.      
1.  There has to be one line terminator immediately after initial opening delimiter.
2.  Now we have content and closing delimiter. if we move any line of content or closing delimiter to left it reduces common whitespace prefix. In other words left most character in content or end delimiter decides starting character of all lines in text block.

Let's check some examples to understand how it works in practice.

***This is first example*** having now whitespaces in the output.
In all the examples dots show spaces in code.

```
public static void printTextBlock() {
String textBlock = """
........First line of test block
........Second line of test block
........""";
System.out.println(textBlock);
}
```
Following is the output, showing all incidental white spaces removed.
In all the examples we are using \| to visualize the left margin

```
|First line of test block
|Second line of test block
|
```


***This is second example*** showing initial character position in text block is decided by start of second line in text block content,
    which has leftmost character out of content and end delimiter.

```
public static void printInitialCharacterPositionDecidedByLeftMostCharacterOfLines() {
String textBlock = """
........First line of test block
....Second line of test block
........""";
System.out.println(textBlock);
}

```

Following is the output of second example. 
We can see initial 4 spaces which are common are removed in output. 

```
|    First line of test block
|Second line of test block
|
```

***This is third example*** showing effect of moving end delimiter to left.  


```
public static void printInitialCharacterPositionDecidedByEndDelimiter() {
String textBlock = """
........First line of test block........
........Second line of test block........
....""";
System.out.println(textBlock);
}
```

In following output of third example. We can see spaces at the end of line are removed.
Moving end delimiter 4 spaces to left adds 4 spaces in all the lines.

```
|    First line of test block
|    Second line of test block
|
```

***This is fourth example*** showing effect of moving end delimiter to right.
Here we see moving end delimiter to right of content has no effect.

```
public static void printTextBlockMovingEndDelimiterToRightOfContentHasNoEffect()
{
....String textBlock = """
............First line of test block         
............Second line of test block      
................""";
....System.out.println("textBlock Experiment printTextBlockMovingEndDelimiterToRightOfContentHasNoEffect");
....System.out.println(textBlock);
}
```
Following is the output of the fourth example, it shows no effect of moving end delimiter to left, all common white spaces are removed. 
```
|First line of test block
|Second line of test block
|
```

 <!--
    After the content is re-indented, any escape sequences in the content are interpreted. 
    Performing interpretation as the final step means developers can write escape
    sequences such as \n without them being modified or deleted by
    earlier steps.
 -->
<!-- Developers will have access to escape processing via String::translateEscapes, a new instance method. 
     Show use of this method -->

#### Escape processing
We have seen first line terminators are interpreted, then next step is white space removal and at the end
escape processing. When we use \n in text block content, it will not be modified by initial 2 steps and will be interpreted
at end.

Let's learn different language features in escape processing via following code example.   

```
private static void printEscapeProcessing() {
....String textBlock = """
............"Hello\n
............Text Block " ' \\ \t "
............experiment another opening/closing delimiter type of 3 consecutive quotes \"""
............without newline concatenation of Strings \
............spaces \s\sat end in this way trailing spaces are not removed\s\s
............\"""";
....System.out.println("printEscapeProcessing");
....System.out.println(textBlock);
}
```

Following is the output of above escape processing example.

```
|printEscapeProcessing
|"Hello
|
|Text Block " ' \ 	 "
|experiment another opening/closing delimiter type of 3 consecutive quotes """
|without newline concatenation of Strings spaces ..at end in this way trailing spaces are not removed..  
|"
```

In above output few things to observe are:
1. \n is allowed in text block, and it generates additional new line in output.
2. " and ' can be used freely in text blocks like Strings.
3. to use \ we need to use \\\\.
4. Sequences of three " characters require the escaping of at least one " to avoid mimicking the closing delimiter.
5. To allow finer control of the processing of newlines and white space, two new escape sequences are introduced in java 15.  
	1.    ***\\*** at the end acts like concatenation of 2 Strings, in other words it avoids line terminator between  
   consecutive lines.  
	2.    ***\\s*** adds space



### Common mistakes in text blocks
Here are some examples of ill-formed text blocks:

```java
String a = """""";   // no line terminator after opening delimiter
String b = """ """;  // no line terminator after opening delimiter
String c = """
           ";        // no closing delimiter (text block continues to EOF)
String d = """
           abc \ def
           """;      // unescaped backslash (escape processing discussed below)

```
<br>

### At the end

To learn the best java language features download my ebook [5 steps to Best Java Jobs](https://jfeatures.com/) for Free.

Follow me on twitter [@vipinbit](https://twitter.com/vipinbit) to get daily tips like this on Java Language Features.

### Resources
1. https://openjdk.java.net/jeps/378