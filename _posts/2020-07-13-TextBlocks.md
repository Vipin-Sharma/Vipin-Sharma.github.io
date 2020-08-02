---
layout:     post
title:      "Readable Java multiline Strings using Text Blocks"
subtitle:   "Text Blocks "
date:       2020-09-12 01:00:00
author:     "Vipin Sharma"
header-img: "img/posts/blog-post-bg2.jpeg"
comments: true
tags: [java, JDK15]
---

Initial draft, work in progress.

<!-- Attention -->
### Do you find Java multiline Strings non readable ?

Can you spot a bug in below multiline String? 

```java
String oldStringSQLExample = "select emp_id, emp_name, emp_num_of_kids, emp_active" +
                "from employee_table" +
                "where employee_num_of_kids =1";

System.out.println(oldStringSQLExample);
```
Following is the console output for oldStringSQLExample. 
See words in bold, here we don't have spaces in between words of consecutive lines. 
It causes SQLSyntaxErrorException.

> select emp_id, emp_name, emp_num_of_kids, ***emp_activefrom***
> ***employee_tablewhere*** employee_num_of_kids =1

<br>

<!-- Interest -->
### Java 14 introduced Text Blocks, readable multiline Strings. 

Now same example we are writing using Java Text Blocks.
<!-- todo show this on webpage, maybe ss, because this is clearly visible right now, it all comes in one line -->

```java  
String sqlWithTextBlocks = """      
                select emp_id, emp_name, emp_num_of_kids, emp_active      
                from employee_table      
		where employee_num_of_kids =1 """; 
```   
Following is console output for text blocks, it is same as we have written in code, no spaces issue now.

> select emp_id, emp_name, emp_num_of_kids, emp_active
> from employee_table
> where employee_num_of_kids =1



<br>

<!-- Desire -->
### The deep dive into Text block syntax 

A text block consists of zero or more content characters, enclosed by opening and closing delimiters.

1. ***The opening delimiter*** is a sequence of three double quote characters (""") followed by zero or more white spaces followed by a line terminator.
2. ***The closing delimiter*** is a sequence of three double quote characters.
3. ***The content*** begins at the first character after the line terminator of the opening delimiter and ends before closing delimiter.

>     """
>     line 1    
>     line 2    
>     line 3    
>     """

Above text block is equivalent to the string literal:

> line 1\nline 2\nline 3\n

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

3 important parts to study Text blocks in depth are:

 1. Line terminators
 2. White spaces removal
 3. Escape sequence processing


Details

#### Line terminators
Different operating systems have their [Line terminators](https://en.wikipedia.org/wiki/Newline).
All line terminators (CR/LF/CRLF) in the content are translated to LF (\u000A). 
     
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
    
There has to be one line terminator immediately after initial opening delimiter.
Now remaining are content and closing delimiter. if we move any line of content or closing delimiter to left
it reduces common whitespace prefix. In other words starting character of all lines in a text block is decided
on basis of left most character in content or end delimiter.


```java
public static void printInitialCharacterPositionDecidedByLeftMostCharacterOfLines() {
    String textBlock = """
            Hello
        test block
            """;
    System.out.println("stextBlock Experiment printInitialCharacterPositionDecidedByLeftMostCharacterOfLines");
    System.out.println(textBlock);
}

```

Another example:

```java
public static void printInitialCharacterPositionDecidedByEndDelimiter() {
    String textBlock = """
    ........Hello         
    ........test block      
    ....""";
    System.out.println("textBlock Experiment printInitialCharacterPositionDecidedByEndDelimiter");
    System.out.println(textBlock);
}

```
 
public static void printTextBlockMovingEndDelimiterToRightOfContentHasNoEffect()
{
    String textBlock = """
            Hello         
            test block      
                """;
    System.out.println("textBlock Experiment printTextBlockMovingEndDelimiterToRightOfContentHasNoEffect");
    System.out.println(textBlock);
}

    
    
 3. After the content is re-indented, any escape sequences in the content are interpreted. 
    Performing interpretation as the final step means developers can write escape
    sequences such as \n without them being modified or deleted by
    earlier steps.

<!-- Developers will have access to escape processing via String::translateEscapes, a new instance method. 
     Show use of this method -->



<br>

### At the end

To learn the best java language features download my ebook [5 steps to Best Java Jobs](https://jfeatures.com/) for Free.

Follow me on twitter [@vipinbit](https://twitter.com/vipinbit) to get daily tips like this on Java Language Features.
