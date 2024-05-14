---
title: OOpre_HW4
date: 2023-10-10 12:56:53
updated:
type:
comments: 
description: '利用正则表达式进行输入解析'
keywords: '面向对象先导'
mathjax:
katex:
aside:
aplayer:
categories: "面向对象先导"
highlight_shrink:
random:
tags: "Oct"
---
### OOpre_HW4 : 正则表达式

#### 1.实现思路

​	这次作业实现思路上没有特别大难度(只新增了四条指令)，但实际上作业体验下来相当于新增了一条指令，很多功能可以顺带着实现。即在我的做法中```OP14()```是进行战斗日志存储的方法，```OP15()```,```OP16()```,```OP17()```,只是将存好的战斗日志输出出来。

​	沿用“二维数组”的输入解析法，特判操作数为14时进行多行输入，引用变量```row```代表实际的行数(因为战斗日志不算在指令条数n内)，利用正则表达式对输入的战斗日志进行解析，下面附上我的冗长的正则表达式

```java
Pattern p = Pattern.compile("(\\d{4}/\\d{2})-([^\\s@#-]+)-([^\\s@#-]+)");
Pattern p1 = Pattern.compile("(\\d{4}/\\d{2})-([^\\s^@#-]+)@([^\\s@#-]+)-([^\\s@#-]+)");
Pattern p2 = Pattern.compile("(.*/.*)-(.*)@#-(.*)"); //这一条是助教改进的，还没太理解
```

​	之后按照题目叙述按部就班从二维数组中取出元素操作即可。这里我将战斗日志分为三个部分：

​	**注意：战斗日志的存储只能使用```ArrayList```只有这样才满有序性！**

1. 总表，在```inputhandler```中设置，在```OP14()```中读出后就将其加入总表，这样相当于沿着完整的时间线存入了战斗日志，对于```OP15()```的完成比较简单，只需要使用正则表达式从中提取出来，下面附上我的正则表达式（其实只需要对日期进行匹配）

   ```java
   Pattern p = Pattern.compile(date + ".+");
   ```

2. 下设在```Adventure```类中的```attacklog```和```attackedlog```分别记录这个人作为攻击者和被攻击的战斗记录，需要注意的是在实际操作中攻击者增加```attacklog```同时被攻击者要增加```attackedlog```。

​	沿着这个思路实现就好，但是助教说不够“面向对象”。(查我代码库```QAQ```)。

#### 2.BUGS

​	这次作业遇到的bug是我de时间最长的一次```WWW```.有很多粗心，也有一些逻辑上的不周到(第一遍写的时候没有反应过来)，甚至还有笔误。这次作业我遇到的bug大部分都是输出错误，虽然要来回找很繁琐但是不值得记录，只有一个逻辑上的错误比较烦心，整整看了三个小时才通过比较AC输出调试出来，心态很崩

**下面是错误代码**

```java
 public boolean useequ(Adventure man, Adventure man1,String name) {
        Equipment equipment = null;
        for (Equipment item : man.equipments) {
            if (item.getName(item).equals(name)) {
                    equipment = item;
                    break;
            }
        }
        if (equipment != null) {
            if(equipment.getBecarried(equipment)){
            	int level = man.level;
            	man1.hitpoint = man1.hitpoint - equipment.getStar() * level;
            	System.out.println(man1.getId(man1) + " " + man1.gethitpoint(man1));
            	return true;
            }
        }
        return false;
    }
```

​	这种实现思路的错误之处在于：在我之前的迭代思路中，“背包”是一个概念而不是一个实体，在总库```equipments```中进行查找时，完全可能找到名字符合但是并没有携带的```equipment```（即但从名字找```equipment```不具有唯一性，可能会找错），这样就会使得永远也加不进去战斗日志，之前的迭代作业我们知道，一个人同名的装备只能有一件状态为```carried```，对于名字和是否携带的双重判断才是正确的逻辑。

**下面是正确代码**

```java
 public boolean useequ(Adventure man, Adventure man1,String name) {
        Equipment equipment = null;
        for (Equipment item : man.equipments) {
            if (item.getName(item).equals(name)) {
                if (item.getBecarried(item)) {
                    equipment = item;
                    break;
                }
            }
        }
        if (equipment != null) {
            int level = man.level;
            man1.hitpoint = man1.hitpoint - equipment.getStar() * level;
            System.out.println(man1.getId(man1) + " " + man1.gethitpoint(man1));
            return true;
        }
        return false;
    }
```



