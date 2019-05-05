---
layout:     post
title:      "JDK 12 Switch Expressions"
subtitle:   "JDK 12 introduced new features Switch expression with JEP 325"
date:       2019-05-04 12:00:00
author:     "Vipin Sharma"
header-img: "img/posts/jekyll-bg.jpg"
comments: true
tags: [java, JDK12]
---


JDK 12 Switch Expressions:

Switch expression was introduced recently with JDK12, it is in preview mode for now and JDK team is looking for [feedback](https://mail.openjdk.java.net/pipermail/jdk-dev/2019-April/002770.html) on this.  
To use preview feature we need to pass --enable-preview parameter, sample command is:

    /usr/lib/jvm/java-1.12.0-openjdk-amd64/bin/java --enable-preview com.vip.jdk12.example.ExperimentSwitchJDK12

For summary of Switch Expression best place is [JEP-325](https://openjdk.java.net/jeps/325).
> Extend the `switch` statement so that it can be used as either a
> statement or an expression, and that both forms can use either a
> "traditional" or "simplified" scoping and control flow behavior. These
> changes will simplify everyday coding, and also prepare the way for
> the use of [pattern matching (JEP
> 305)](https://openjdk.java.net/jeps/305) in `switch`. This will be a
> [preview language feature](https://openjdk.java.net/jeps/12).


Some features to highlight here are:

 1. Multiple comma separated labels supported. 
 2. No fallthrough with the arrow (->) syntax
 3. Switch expressions are exhaustive for Enum, in case we miss some case for
    Enum there is compilation error.
    

Before we start looking at Switch expression it is good recall what is basic java expression,as we read this post it will will help us to understand difference between switch expression and switch statement. 
Java [expression](https://docs.oracle.com/javase/tutorial/java/nutsandbolts/expressions.html) is:

    a construct made up of variables, operators, and method invocations, which are constructed according to the syntax of the language, that evaluates to a single value.


Before JDK 12 we had switch statement with colon (:) syntax, now JDK 12 introduced switch expression and arrow (->) syntax, that gives us 4 combinations of code we can write :

 1. Switch statement with colon syntax
 2. Switch statement with arrow syntax 
 3. Switch expression with arrow syntax
 4. Switch expression with colon syntax


We will go through all above 4 cases and some additional ones as well, all java code is available at [github](https://github.com/Vipin-Sharma/JDK12Examples).
Here we are calculating yearly bonus percentage for all 4 combinations, same work in all 4 different ways.
For this we are using Employee class.

```java
public class Employee {
    private int id;
    private String name;
    private String designation;
    ...
}
```

Below is way we are calling these functions.

```java
public class SwitchStatementColonSyntax {

    public static void main(String[] args) {
        Employee vipin = new Employee(1, "Vipin", "MD", 10);
        Employee nitin = new Employee(2, "Nitin", "ED", 10);
        Employee ekta = new Employee(3, "Ekta", "VP", 7);
        Employee mudita = new Employee(3, "Mudita", "VP", 5);
        Employee teena = new Employee(3, "Teena", "VP", 16);
        Employee tanay = new Employee(4, "Tanay", "SeniorAssociate", 11);
        Employee mini = new Employee(4, "Mini", "Associate", 5);

        ArrayList<Employee> employees = new ArrayList();
        employees.add(vipin);
        employees.add(nitin);
        employees.add(ekta);
        employees.add(mudita);
        employees.add(teena);
        employees.add(tanay);
        employees.add(mini);

        System.out.println("");
        employees.forEach(employee ->
                System.out.println(employee.getName() + " gets Bonus " + getYearlyBonus_statement_colonSyntax(employee.getDesignation()) + " %"));


    }
}
```
<br/><br/>
Now we will look at only method code dealing with switch statement/expression, rest of the codes is same as above just method name we call is different and complete code is available at [github](https://github.com/Vipin-Sharma/JDK12Examples).

##### <ins>Switch statement with colon syntax</ins>

Important points to highlight in below code are:
1.  This is typical example of Old Switch statement using break, works in older JDK versions as well.
2.  We have a block scope variable temp.

```java
private static double getYearlyBonus_statement_colonSyntax(String designation) {
    double bonus;
    switch (designation) {
        case "MD": {
            double temp = 1.;
            bonus = 50.0 + temp;
            break;
        }
        case "ED": {
            double temp = 1.;
            bonus = 25.0 + temp;
            break;
        }
        case "VP", "SeniorAssociate" :
            bonus= 20.0;
            break;
        case "Manager": throw new RuntimeException("I dont know what is Manager designation");
        default : {
            bonus = 10.0;
            break;
        }
    }
    return bonus;
}
```
<br/><br/>

##### <ins>Switch statement with arrow syntax</ins>

In this section we are converting previous example into arrow syntax, with this it becomes fallthrough safe, see no use of break here.
Even if we use break, my IDE shows this is redundant. 
In this only change required is : replace : with ->, it is not golden rule but for simplicity taken this example.

Important points to notice are:
1.  This is switch statement using arrow syntax, JDK12 specific.
2.  Code right to arrow -> can be expression, a block, or a throw statement. Here we have taken example of block and Exception.
3.  Block effectively provides scoping as well, we can define variable in block scope here temp is such example.
4.  We can see use of Multi label in case "VP", "SeniorAssociate"
5.  Arrow syntax makes it fallthrough safe.


```java
private static double getYearlyBonus_statement_arrowSyntax(String designation) {
    double bonus;
    switch (designation) {
        case "MD" -> {
            double temp = 1.;
            bonus = 50.0 + temp;
        }
        case "ED" -> {
            double temp = 1.;
            bonus = 25.0 + temp;
        }
        case "VP", "SeniorAssociate" ->
            bonus= 20.0;
        case "Manager" -> throw new RuntimeException("I dont know what is Manager designation");
        default -> {
            bonus = 10.0;
        }
    }
    return bonus;
}
``` 
<br/><br/>


##### <ins>Switch expression with arrow syntax</ins>

If we want to change previous example in switch expression with arrow syntax then below will be new code, in this example changes are:
1.  Switch expression evaluates to value and we are saving it in bonus.
We have also removed block from case "MD" but that is just to show one more clean way to write this.

important points to notice in this code are:

1.  This is switch expression using arrow syntax, JDK12 specific.
2.  Arrow (->) points to returned value.
3.  Code right to arrow -> can be expression, a block, or a throw statement. All 3 cases are covered in this example.
    If case "MD" -> 50.0 looks confusing for an expression you can write 50.0 + 0.0 it may make sense now.
    case "ED" has a block and case "Manager" has Exception. 
4.  temp variable has no use but to show a block using temporary variable.
5.  Again using arrow syntax (->) we get assurance of no fallthrough.
6.  Multiple comma separated labels supported like here:   case "VP", "SeniorAssociate" -> 40;
7.  case "ED" is using break, which is way to return value.


```java
private static double getYearlyBonus_expresion_arrowSyntax(String designation) {
    double bonus = switch (designation) {
        case "MD" -> 50.0 + 1.;
        case "ED" -> {
            double temp = 1.;
            break 25.0 + temp;
        }
        case "VP", "SeniorAssociate" -> 20.0;
        case "Manager" -> throw new RuntimeException("I dont know what is Manager designation");
        default -> 10.0;
    };
    return bonus;
}
```
<br/><br/>

##### <ins>Switch expression with colon syntax</ins>

To convert previous example in Switch expression with colon syntax we need to replace -> with colon 
and add break where we want expression to return value.  

important points to notice in this code are:

1.  This is switch expression using colon syntax, JDK12 specific.
2.  It has same advantages of switch expression, fallthrough safe and Multiple comma separated labels.
3.  We are using  


```java
private static double getYearlyBonus_expresion_colonSyntax(String designation) {
    double bonus = switch (designation) {
        case "MD": {
            break 50.0 + 1.;
        }
        case "ED": {
            double temp = 1.;
            break 25.0 + temp;
        }
        case "VP", "SeniorAssociate": break 20.0;
        case "Manager": throw new RuntimeException("I dont know what is Manager designation");
        default: break 10.0;
    };
    return bonus;
}
```
<br/><br/>

##### Exhaustive 
Switch expressions are exhaustive for Enum, in case we miss any case for Enum there is compilation error.

This code has compilation error, saying "Switch expression does not cover all possible input values" 
```java
private static double getYearlyBonus_expression_arrow_enum_compilationerror(Designation designation) {
    return switch(designation){
        case MD -> 50.0 + 1.0;
        case ED -> 24.0 + 1.0;
        case VP, SeniorAssociate ->  20.0;
    };
}
```
After adding default all inputs are covered and it works fine.

```java
private static double getYearlyBonus_expression_arrow_enum(Designation designation) {
    return switch(designation){
        case MD -> 50.0 + 1.0;
        case ED -> 24.0 + 1.0;
        case VP, SeniorAssociate ->  20.0;
        default -> 10;
    };
}
```
<br/><br/>
##### At the end one more important point to be highlighted is :
Arrow syntax doesnt always mean it is switch expression, similarly colon syntax doesnt always mean it is switch statement.  
 
