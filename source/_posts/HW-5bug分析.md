---
title: OOpre_HW_5:常见bug分析
date: 2023-10-18 15:08:44
updated:
type:
comments: 
description: '学习常见的bug类型'
keywords: '面向对象先导 debug'
mathjax:
katex:
aside:
aplayer:
categories: "面向对象先导"
highlight_shrink:
random:
tags: "Oct"
---

## OOpre_HW_5:常见bug分析

​	本次作业的任务比较简单，对课程组给出的代码进行debug,只有中测，让我这种挂了强测的鼠鼠好欣慰。

#### 一.输入解析类错误

​	常见的有```scanner```一类的函数，我们需要注意的无非以下几个函数的功能

```java
1. scanner.next(); //读取下一个字符串
2. scanner.nextint();//读取下一个数字
3. scanner.nextLine();//读取下一行字符串
```

​	作业中出现了几次使用```scanner.nextLine()```方法读取下一个字符串的错误，这种错误还是比较明显的。

#### 二.深克隆与浅克隆

​	在我的作业代码中，对于深克隆和浅克隆共出现了一个功能中的两次错误，即克隆小队的操作和克隆士兵的操作。

##### 1.浅克隆

​	浅克隆即只对对象的引用进行克隆，换句话说是创建出来新的一个指针，与原对象指向相同的一块内存空间。本质上两个引用指向的是同一个实例，一个指针对对象进行修改，另一个指针进行访问时就会体现出这种修改。例如:

```java
Team team1 = new Team(123456,"dqr");
Team team2 = team1;
```

##### 2.深克隆

​	深克隆不仅要创建出新的引用，还要开辟出新的内存空间，本质上就是一个新的变量，只不过变量的构造方法中传入参数是需要被克隆的对象的参数。

```java
Team team1 = new Team(123456,"dqr");
Team team2 = new Team(team1.getID(),team1.getName());
```

#### 三.相等比较 ==/equals ?

1. ==是比较两个引用是否是同一个对象
2. equals为内容比较，比如名字，咒语等等

#### 四.遍历容器：迭代器删除

​	对于容器中的元素删除，我们可能经常会用到“边遍历边删除的操作”

```java
for (Dog dog:arrayList){
     arrayList.remove(dog);
}
```

​	这种操作会报错如下:

```java
Exception in thread "main" java.util.ConcurrentModificationException
at java.util.ArrayList$Itr.checkForComodification(ArrayList.java:911)
at java.util.ArrayList$Itr.next(ArrayList.java:861)
```

​	正确的做法是删除的时候不应该使用for循环，而应该使用迭代器遍历删除

```java
Iterator<Dog>iterator= arrayList.iterator();
while (iterator.hasNext()){
       Dog dog=iterator.next();
       iterator.remove();
       System.out.println(dog.name);
}
```

