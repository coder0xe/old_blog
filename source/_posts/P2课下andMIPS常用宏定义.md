---
title: P2课下&&MIPS常用宏定义
date: 2023-10-20 17:06:31
updated:
type:
comments: 
description: '用宏定义使MIPS简洁起来'
keywords: '计组'
mathjax:
katex:
aside:
aplayer:
categories: "计组"
highlight_shrink:
random:
tags: "Oct"
---

## P2课下&&常用MIPS宏定义

​	MIPS是一门非常灵活的语言，在编写过程中可以非常直观地感受到对内存的操作（虽然我编写MIPS都是照着C语言一句一句翻译），但也正因为是这样，使得MIPS编码看起来```有一点繁琐```，我们可以使用自己定义的宏```macro```进行代码风格的改善，将可以复用的代码抽离出来，类似于C语言中的函数。

### 1. MIPS——macro

​	MIPS中宏定义的方法是

```MIPS
//无参数宏定义
.macro  name

//statement

.end_macro

//有参数宏定义
.macro name(%parameter1,%parameter2,...)

//statement

.end_macro
```

#### 1. end

```MIPS
.macro end
li $v0,10
syscall
.end_macro
```

#### 2. readinteger

```MIPS
.macro readinteger(%des)
li $v0,5
syscall
move %des,$v0
.end_macro
```

#### 3. printinteger

```MIPS
.macro printinterger(%src)
li $v0,1
move $a0,%src
syscall
.end_macro
```

#### 4. printstr

```MIPS
.macro printstr(%src)
li $v0,4
la $v0,%src
syscall
.end_macro
```

#### 5. 函数调用push

```MIPS
.macro push(%src)
sw %src,0($sp)
subi ge$sp,$sp,4
.end_macro
```

#### 6. 函数调用pop

```MIPS
.macro pop(%des)
addi $sp,$sp,4
lw %des,0($sp)
.end_macro
```

#### 7.一维数组计算地址偏移量

```MIPS
.macro getVectorAddress(%des,%col)
sll %des,%col,2
.end_macro
```

#### 8.二维数组计算地址偏移量

```MIPS
.macro getMatrixAddress(%des,%i,%j)
multu %i,col（矩阵的列数）
mflo %des
add %des,%des,%j
sll %des,%des,2
.end_macro
```

**注意：一定是乘上矩阵的列数！```QAQ```之前乘了矩阵的行数还是看内存发现的bug**

#### 9.直接打印字符串

```MIPS
.macro printStr(%str)
    .data 
        tmpLabel:   .asciiz %str
    .text
        li  $v0, 4
        la  $a0, tmpLabel
        syscall
.end_macro
```

### 3.典型的易错点

#### 1.调用子函数中的跳转应当用jal

​	```jal```即```jump and link```在进行跳转的同时，将```PC+4```存入```$ra```寄存器，通过```j $ra```返回到跳转前的下一条语句。写递归的时候递归调用用了```j function```之后就寄了......

#### 2.循环等普通标签跳转用j

​	```j```即```jump```，跳到标签位置，在循环的编写中经常使用到，例如

```MIPS
#一维情况
loop:
beq $t0,$s0,loop_end

//statement

addi $t0,$t0,1
j loop

loop_end:

//statement

#二维情况
loop_1:
beq $t0,$s0,loop_1_end
move $t1,$zero #第二层循环变量清0
loop_2:
beq $t1,$s1,loop_2_end

//statement

loop_2_end:
addi $t0,$t0,1
j loop_1

loop_1_end:

//statement
```

#### 3.对变量的初始化要写在循环外

​	我知道这个问题很弱智，可是弱智的我很爱出错；例如

```MIPS
loop_1:
beq $t0,$s0,loop_1_end
loop_2:
beq $t1,$s1,loop_2_end
move $t1,$zero   #error!!!!!!!!!

//statement

loop_2_end:
addi $t0,$t0,1
j loop_1

loop_1_end:

```

​	这样写这个循环就永远出不来力！！！！！

​	跳转到循环时，初始化步骤只有第一次跳转到才进行，应该在循环外进行变量初始化，代码结构类似于这样

```MIPS
calculate:
move $t0,$zero
loop_1:
...
move $t1,$zero
loop_2:
...
```

#### 4.存储问题

​	为什么会经常弄混```lw```与```sw```啊！一定要注意了！另外一定要小心，有的时候想着存字符的话只需要一个字节，使用```sb```操作，但是地址偏移量用了四个字节......需要格外细心！

​	另外，在申请数组空间上，切记要申请出4的倍数(1 word = 4 bytes)，否则在运行过程中可能会报出字没有对齐的错误，为字符串申请空间写在最后更好，也是为了防止没有字对齐！

### 4.MIPS中运算

1. 乘法``` mult``` :  hi寄存器中存储乘法结果的高32位，lo寄存器中存储乘法结果的低32位
2. 除法 ```div```  :  hi寄存器中保存除法结果的余数，lo寄存器保存除法结果的商
3. 注意：MIPS中不提供求模运算，但是可以利用除法完成，即做除法之后取hi寄存器中的余数结果```mfhi```
4. 加法 ```add  addi```
5. 减法 ```sub   subi```
6. 与 ```and```
7. 或 ```or```
8. 异或 ```xor```
