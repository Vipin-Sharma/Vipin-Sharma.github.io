---
layout:     post
title:      "Immutable Records"
subtitle:   "Helpful immutable data carrier classes"
date:       2021-04-13 12:00:00
author:     "Vipin Sharma"
header-img: "img/posts/blog-post-bg2.jpeg"
comments: true
tags: [java, JDK16]
---

**This is a draft post, work in progress:**

Synchronizing your code is not easy in all cases. When a Java object is not immutable, any thread in the application can change its state. Without synchronizing code, some threads might not see the updated state of the object. This is one of the biggest problem professional java developers deal with every day. The immutable nature of the record eliminates this problem completely.

**[Part 1](https://jfeatures.com/blog/records)** of the post we saw record provides a way to create data carrier-classes without writing a lot of boilerplate code. As part of this post, we will focus on the Immutability feature of the record. Immutability is one of the best features provided by records. We can use record objects without worrying about any other thread changing its state.


### Java language features making it immutable

Following are few ways the state of a record class can be changed if it behaves like the java class. Along with this we are also discussing what are record features that prevent it from making record immutable.

| Index   | Way to update record | record features preventing it |
|---------|------------|------------|
|    1    | Create a record sub-class and change the state of the sub-class. | Record classes are implicitly final and can not be abstract, this way we can not create a sub-class of record class. |
|    2    | Extend some other class(superclass) and change the state of the superclass.  | All record classes extend java.lang.Record by default so can not extend any other class. |
|    3    | Create instance fields that can be modified. | Record classes cannot declare instance fields. Only record components carry the state of the record object which are final and assigned values while initializing record. |
|    4    | Update record components. | Record components are implicitly final so can not be assigned a new value once initialized.  |
|    5    | Update record components in the constructor. | Only the canonical constructor is allowed to do this, for other constructors assigning any of the record components in the constructor body became a compile-time error. |
|    6    | Update record components using reflection. | Reflection [Field](https://docs.oracle.com/en/java/javase/16/docs/api/java.base/java/lang/reflect/Field.html#set(java.lang.Object,java.lang.Object)) API has been updated for specific handling of non-static fields which are part of record class. This treatment is similar to hidden classes. I have a separate post covering [Hidden classes](https://jfeatures.com/blog/HiddenClass) in detail. |



<!--
1. Create a record sub-class and change the state of the sub-class. Record classes are implicitly final and can not be abstract, this way we can not create a sub-class of record class.
2. Extend some other class(superclass) and change the state of the superclass. All record classes extend java.lang.Record by default so can not extend any other class.
3. Create instance fields that can be modified. Record classes cannot declare instance fields. Only record components carry the state of the record object which are final and assigned values while initializing record.
4. Update record components. Record components are implicitly final so can not be assigned a new value once initialized.
5. Update record components in the constructor. Only the canonical constructor is allowed to do this, for other constructors assigning any of the record components in the constructor body became a compile-time error.
6. Update record components using reflection. Reflection [Field](https://docs.oracle.com/en/java/javase/16/docs/api/java.base/java/lang/reflect/Field.html#set(java.lang.Object,java.lang.Object)) API has been updated for specific handling of non-static fields which are part of record class. This treatment is similar to hidden classes. I have a separate post covering [Hidden classes](https://jfeatures.com/blog/HiddenClass) in detail.
-->
<br>

### Shallowly immutable

Record components are final, which means you can not change the field once assigned. Although fields of record components can be changed, there is no restriction on that, it makes record shallowly immutable. Let's see this with an example.

```java
public class EmployeeTest {

    public static void main(String[] args) {
        List<Integer> integerList = new ArrayList<>();
        integerList.add(1);
        IntegerListRecord integerListRecord = new IntegerListRecord(integerList);

        System.out.println(integerListRecord.getListSize());

        integerList.add(2);
        System.out.println(integerListRecord.getListSize());
    }
}

    record IntegerListRecord(List<Integer> integerList) {
        int getListSize()
        {
            return integerList.size();
        }
    }
```

Here we created a list of integers(integerList), added one element into it, and initialized the record class with this. Calling method getListSize of record class results into 1. Now we add one more element in integerList and calling getListSize results into 2. Here we did not change record component (integerList) but updated the fields of the record component, which does not have any restriction. This is the reason we call the record shallowly immutable.

<br>

### Conclusion

Records help you remove repetitive and error-prone code, and increases developer productivity. The immutability feature keeps it away from concurrency bugs. Using language features like this is going to make you a great developer everyone wants to hire.

If you want to get amazing Java jobs, I wrote an ebook [5 steps to Best Java Jobs](https://jfeatures.com/). You can download this step-by-step guide for free!

[<img src="../img/ebook_upd.png" width="200" height="200">](https://jfeatures.com/)

<br>

### Resources

1.  https://openjdk.java.net/jeps/395
2.  https://cr.openjdk.java.net/~briangoetz/amber/datum.html