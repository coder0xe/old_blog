---
title: 'MIPS基本语法'
date: '2023-9-12 23:24'
updated:
type:
comments: 
description: 'MIPS语法'
keywords: '计组'
mathjax:
katex:
aside:
aplayer:
categories: "计组"
highlight_shrink:
random:
tags: "Sep"
---
##  									MIPS代码的基本语法

#### 1. .data数据段

```c
.data
fibs: .space 48
size: .word 12
```

​	**在数据段进行变量的定义,<变量名> :  .<伪指令>  <变量内容>**

#### 2. .test代码段

​	**定义程序的代码段，指令后为代码**

```c
.test
 add $t0,&t1,$t2
```

#### 3. .macro

​	**使用宏可以避免相同代码多次复用**

1. 不带参数定义的宏

```
macro  <macro_name>
#
#
#
.end_macro
```

常用的场景是可以替换程序结束的两行代码

```c
li $v0,10
syscall
```

如果我们定义出代表这两行定义的宏

```c
macro DONE
li $v0,10
syscall
.end_macro
```

这样我们在需要调用这两行代码的时候只需一行

```
DONE  #而且这样对于代码的含义理解还更加清晰
```

2. 带参数定义的宏

**这样的宏适用于一段结构相似的代码，只有变量名需要变动的情形**,其中参数的形式为 **%<parameter_name>**.例如斐波那契中的例子

```c
.macro <macro_name> (%parameter1, %parameter2, ...)
 #
 #
 #
 .end_macro
```

例如矩阵乘法

```c
.macro getindex(%ans, %i, %j)
   sll %ans, %i, 3 
   add %ans, %ans, %j 
   sll %ans, %ans, 2 
.end_macro
```

#### 4. .eqv

**用于定义常量数字**

```c
.eqv <name> <value>
```

