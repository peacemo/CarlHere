---
layout: post
title: "Android全局共享数据的创建方法"
date:   2021-03-12
tags: [Android]
comments: false
toc: false
author: shikamaru
---



​	最近在 Android 开发中遇到一个比较棘手的问题，就是同一个对象在不同 Activity 之间的共享访问问题，一开始我的解决方法是在 Intent 中调用 `putExtra()`方法或者使用`Buddle`达到 Activity 之间数据共享的效果，但是这种效果效率不高，而且在传递参数或者对象后一般要重新声明一个对象，对内存空间造成了极大的浪费，而且代码的冗余度也非常高。

​	考虑到我现在的项目中对于“此类对象”的需求就是需要它们作为全局的变量来使用，不同的 Activity 在创建之后，对其的访问不受影响，因此想到了用一个`static`对象来存储数据，而且这个对象必须不属于任何一个 Activity，在 Activity 中访问数据时，只需要调用基本的`getter()`就能访问。下面是创建过程。

## 创建Utils实用类

​	Utils 是 utilities [ 实用程序 ] 的缩写，我们需要创建任何对项目有用的“实用工具”时，都可以在这个类中进行实现，提高代码的整洁度。

​	首先这个类在使用时一定不能为空，所以在创建时就需要检测其内容: 

```java
public class Utils {

    //成员属性 instance: Utils，确保对象不为空
    private static Utils instance;

    private Utils() {
    }


    /**
    * 每次调用 Utils 中的其他方法前调用此方法避免出现空指针异常
    */
    public static Utils getInstance() {

        if (null == instance) {
            instance = new Utils();
        }
        return instance;

    }
    
}

```

之后在类中声明一个上文提到的全局都需要用到的`static`对象，以及其`getter()`

```java
private static ArrayList<Book> books;
public static ArrayList<Book> getBooks() {
        return books;
    }
```

**并且在Utils的构造方法中确保这个对象不为 null**

```java
private Utils() {

        if (books == null) {
            books = new ArrayList<>();
            initData();
        }

    }

private void initData() {

        //TODO: Add data from database.

    }
```

到这里一个Utils工具类就构建完成了。

## Utils类的使用

​	创建完Utils类之后，只需要在需要用到的地方导入包，然后调用即可

```java
BooksRecViewAdapter adapter = new BooksRecViewAdapter(this);

//调用Utils中的数据获取方法，注意先调用getInstance(),避免空指针，除了这一行其他行可以忽略
adapter.setBooks(Utils.getInstance().getBooks()); 

booksRecView.setAdapter(adapter);
booksRecView.setLayoutManager(new LinearLayoutManager(this));
```

