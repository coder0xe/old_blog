---
layout: mips
title: translate C recursive function into MIPS
date: 2023-10-22 16:32:30
updated:
type:
comments: 
description: 'MIPS编写递归函数'
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

## Translate  C recursive function into MIPS

​	如何规范合理地把C语言中的递归函数翻译成```MIPS assembly```?这个问题令我头疼了一天，翻了网上很多教程总是感觉说的很浅，或者是重复着用MIPS编写计算阶乘的例子，找不到合适的教程对于一个迷茫的新手来说是一件非常绝望的事，经过我求助身边的大佬们，大佬们的一些分享，让我逐渐明白编写中的一些要点，并总结出一些编写规则。

### 一.C中的递归函数

​	对于编写MIPS程序，我们一般是先写出对应的C代码，再一句句翻译成MIPS语言。递归函数也是函数，从函数类型上看，应该有无返回值和有返回值这两种粗浅的大类，其中无返回值是一种值得注意的类型。

#### 1.无返回值类型

​	在无返回值的递归函数中，我们往往只能看见递归层次中的``return``，但是实际上无返回值类型的返回语句可以省略，即程序运行结束时的```return;```可以省略，在翻译时，题目可能就会使用省略这一种写法，需要注意这个“隐藏”的```return```并在MIPS中自行编写。例如如i下C程序

```C
#include<stdio.h>
void print()
{
    printf("hello world!");
    (return;)
}
int main()
{
    print();
}
```

#### 2.有返回值类型

​	有返回值类型常见的为int等，有返回值类型的函数必须“显式“地说明出返回值，如果有分支，则在每个分支中都需要进行返回值的说明，这一类函数可以明显地看出哪里需要```return```对于编写比较友好，例如计算阶乘的示例

```c
#include<stdio.h>
int calculate(int n)
{
    if(n == 1) {
        return 1;
    } else {
        return n*calculate(n-1);
    }
}
int main()
{
    int n;
    scanf("%d",&n);
    printf("%d",calculate(n));
    return 0;
}
```

### 二.举例说明我的翻译规范

#### 0.总体概括

​	无论是有无返回值的递归函数，在翻译时都需要遵循默认的守则，例如使用```$a0~$a3```进行函数参数的传递，如果是有返回值的类型则使用```$v0~$v1```传递返回值，调用者使用s(saved)寄存器,维护t(temporary)寄存器，被调用者随意使用t寄存器，维护s寄存器.......

#### 1.无返回值类型

​	**无返回值类型的函数在翻译时一定要注意补充可能不写出的”隐式“return!**

##### 1.全排列数的生成

###### 1. C code

```C
#include<stdio.h>
#include<stdlib.h>
int symbol[7],array[7];
int n;
void FullArray(int index) {
   int i;
   if(index >= n){
    for(i=0;i<n;i++){
        printf("%d",array[i]);
    }
    printf("\n");
    return;
   }
   for(i=0;i<n;i++){
    if(symbol[i]==0){
        array[index] = i+1;
        symbol[i] = 1;
        FullArray(index+1);
        symbol[i]=0;
    }
   }
}
int main(){
    int i;
    scanf("%d",&n);
    FullArray(0);
    return 0;
}
```

###### 2. MIPS code

```MIPS 
.data
array : .space 1024
symbol : .space 1024
space : .asciiz " "
enter : .asciiz "\n"

.macro end
li $v0,10
syscall
.end_macro

.macro readinteger(%ans)
li $v0,5
syscall
move %ans,$v0
.end_macro

.macro printinteger(%ans)
li $v0,1
move $a0,%ans
syscall
.end_macro

.macro printspace
li $v0,4
la $a0,space
syscall
.end_macro

.macro printenter
li $v0,4
la $a0,enter
syscall
.end_macro

.macro push(%src)  
sw %src,0($sp)
subi $sp,$sp,4
.end_macro

.macro pop(%des)
addi $sp,$sp,4
lw %des,0($sp)
.end_macro


.text
readinteger($s0)  # n
move $a0,$zero
jal FullArray
end


FullArray:     
bge	$a0, $s0, print_init
move $t0,$zero
loop:
beq $t0,$s0,end_loop
sll $t1,$t0,2
lw $t2,symbol($t1)
bne $t2,$zero,else
addi $t3,$t0,1
sll $s2,$a0,2
sw $t3,array($s2)
li $t4,1
sw $t4,symbol($t1)

push($ra)
push($t0)   
push($a0) 

addi $a0,$a0,1  
jal FullArray

pop($a0)
pop($t0)
pop($ra)

sll $t1,$t0,2
sw $zero,symbol($t1)

else:
addi $t0,$t0,1
j loop

end_loop:   //这一段即对应函数末尾隐式的return;!!!!!!!!!!!!!!!!
jr $ra


print_init:
move $t0,$zero
print_loop:
beq $t0,$s0,print_loop_end
sll $t1,$t0,2
lw $t2,array($t1)
printinteger($t2)
printspace
addi $t0,$t0,1
j print_loop

print_loop_end:
printenter
jr $ra
```

###### 3. analyse

1. 补全```return```

​	全排列数的生成是一个典型的无返回值递归函数，在课程组提供的C代码中并没有写”显式“的```return```，我们将其补全之后可以得到如下更为完整的C代码

```C
void FullArray(int index) {
   int i;
   if(index >= n){
    for(i=0;i<n;i++){
        printf("%d",array[i]);
    }
    printf("\n");
    return;
   }
   for(i=0;i<n;i++){
    if(symbol[i]==0){
        array[index] = i+1;
        symbol[i] = 1;
        FullArray(index+1);
        symbol[i]=0;
    }
   }
    (return;)
}
```

​	这样我们就可以更加清楚的指导有两处需要进行```jr $ra```，一处为递归终止条件处向上递归，另一处为函数运行结束。

2. 对于寄存器的维护（push,pop）

​	我们知道在递归函数中需要维护跳转地址寄存器```$ra```等，在此题目中，我们分析进入递归的部分

```C
for(i=0;i<n;i++){
    if(symbol[i]==0){
        array[index] = i+1;
        symbol[i] = 1;
        FullArray(index+1);
        symbol[i]=0;
    }
   }
```

​	显然，传递给函数的参数，存储index的变量也应当被维护，比较不明显（第一次写没注意）的是对于循环变量i的维护，想想就很容易知道，i是需要进行维护的，在一次递归从下至顶结束之后，还要进行对symbol的赋值以及继续进行循环。

```MIPS
push($ra)
push($t0)   
push($a0) 

addi $a0,$a0,1  
jal FullArray

pop($a0)
pop($t0)
pop($ra)
```

​	我的处理是只在进行递归调用的部分进行寄存器的push&&pop,这样相比于教程中的在函数开始时进行push,跳出时pop我认为有一定先进性：

1. 维护位置集中，如果需要维护循环变量，优势更加明显
2. 跳转```return```时只需要一句话```jr $ra```，若采用在函数开始时push，则每一次跳转都需要pop，这种方式更加简洁

##### 2.哈密顿回路

###### 1. C code

```C
#include <stdio.h>
int G[8][8];    // 采用邻接矩阵存储图中的边
int book[8];    // 用于记录每个点是否已经走过
int m, n, ans;

void dfs(int x) {
    book[x] = 1;
    int flag = 1, i;
    // 判断是否经过了所有的点
    for (i = 0; i < n; i++) {
        flag &= book[i];
    }
    // 判断是否形成一条哈密顿回路
    if (flag && G[x][0]) {
        ans = 1;
        return;
    }
    // 搜索与之相邻且未经过的边
    for (i = 0; i < n; i++) {
        if (!book[i] && G[x][i]) {
            dfs(i);
        }
    }
    book[x] = 0;
}

int main() {
    scanf("%d%d", &n, &m);
    int i, x, y;
    for (i = 0; i < m; i++) {
        scanf("%d%d", &x, &y);
        G[x - 1][y - 1] = 1;
        G[y - 1][x - 1] = 1;
    }
    // 从第0个点（编号为1）开始深搜
    dfs(0);
    printf("%d", ans);
    return 0;
}

//输出0 代表不具有回路 输出1 代表具有回路
```

###### 2. MIPS code

```MIPS
.data
G : .space 1024
book : .space 32

.macro readinteger(%des)
li $v0,5
syscall
move %des,$v0
.end_macro

.macro matrixGetIndex(%des,%i,%j)
multu %i,$s1
mflo %des
add %des,%des,%j
sll %des,%des,2
.end_macro

.macro vectorGetIndex(%des,%i)
sll %des,%i,2
.end_macro

.macro printinteger(%src)
li $v0,1
move $a0,%src
syscall
.end_macro

.macro  end
li $v0,10
syscall
.end_macro


.macro push(%src)
sw %src,0($sp)
subi $sp,$sp,4
.end_macro

.macro pop(%des)
addi $sp,$sp,4
lw %des,0($sp)
.end_macro

.text
main:
readinteger($s0) # n
readinteger($s1) # m
move $t0,$zero
loop:
beq $t0,$s1,loop_end
readinteger($t2)  #x
readinteger($t3)  #y
subi $t2,$t2,1
subi $t3,$t3,1
li $t4,1
matrixGetIndex($t5,$t2,$t3)
sw $t4,G($t5)
matrixGetIndex($t5,$t3,$t2)
sw $t4,G($t5)
addi $t0,$t0,1
j loop

loop_end:
move $a0,$zero  
jal dfs
printinteger($v1)
end

# $a0 is x
# $t1 is 1
dfs:
vectorGetIndex($t0,$a0)
li $t1,1
sw $t1,book($t0)
move $t2,$t1 #  $t2 is flag
move $t0,$zero
dfs_loop:
beq $t0,$s0,end_dfs_loop
vectorGetIndex($t3,$t0)
lw $t4,book($t3)
and $t2,$t2,$t4
addi $t0,$t0,1
j dfs_loop

end_dfs_loop:
matrixGetIndex($t3,$a0,$zero)
lw $t4,G($t3)
and $t5,$t2,$t4
beq $t5,$t1,exit
move $t0,$zero
dfs_loop_2:
beq $t0,$s0,dfs_loop_2_end
vectorGetIndex($t2,$t0)
lw $t3,book($t2)
matrixGetIndex($t2,$a0,$t0)
lw $t4,G($t2)
seq $t3,$t3,$zero
and $t5,$t3,$t4
beq $t5,$t1,recursive
addi $t0,$t0,1
j dfs_loop_2

dfs_loop_2_end:
sll $t8,$a0,2
sw $zero,book($t8)
jr $ra

recursive:
push($a0)
push($ra)
push($t0)
move $a0,$t0
jal dfs
pop($t0)
pop($ra)
pop($a0)
addi $t0,$t0,1
j dfs_loop_2

exit:
li $v1,1
jr $ra
```

###### 3. analyse

​	这个题目也是一个典型的void类型recursive function，我们需要将他隐式的return补全

```C
void dfs(int x) {
    book[x] = 1;
    int flag = 1, i;
    // 判断是否经过了所有的点
    for (i = 0; i < n; i++) {
        flag &= book[i];
    }
    // 判断是否形成一条哈密顿回路
    if (flag && G[x][0]) {
        ans = 1;
        return;
    }
    // 搜索与之相邻且未经过的边
    for (i = 0; i < n; i++) {
        if (!book[i] && G[x][i]) {
            dfs(i);
        }
    }
    book[x] = 0;
    (return;)
}
```

​	对于寄存器的push&&pop不再赘述

#### 2.有返回值类型

##### 计算阶乘

##### 1. C code

```c
#include<stdio.h>
int calculate(int n)
{
    if(n == 1) {
        return 1;
    } else {
        return n*calculate(n-1);
    }
}
int main()
{
    int n;
    scanf("%d",&n);
    printf("%d",calculate(n));
    return 0;
}
```

##### 2. MIPS code

```MIPS
.macro end
    li      $v0, 10
    syscall
.end_macro

.macro getInt(%des)
    li      $v0, 5
    syscall
    move    %des, $v0
.end_macro

.macro printInt(%src)
    move    $a0, %src
    li      $v0, 1
    syscall
.end_macro

.macro push(%src)
    sw      %src, 0($sp)
    subi    $sp, $sp, 4
.end_macro

.macro pop(%des)
    addi    $sp, $sp, 4
    lw      %des, 0($sp) 
.end_macro

.text
main:
    getInt($s0)
    
    move $a0, $s0
    jal  factorial
    move $s1, $v0
    printInt($s1)
    end

factorial:
    bne  $a0, 1, else
    li  $v0, 1
    j   exit  
    else:
    push($ra)
    push($a0)
    subi $a0,$a0,1
    jal  factorial
    pop($a0)    ###########先恢复原值n
    pop($ra)
    mul  $v0,$a0,$v0
    exit:
    jr  $ra

```

##### 3.analyse

​	有返回值类型的一道典型题目，使用$v0接收返回值，需要注意的是计算```n*f(n-1)```时需要先将```$a0```恢复原值，即先pop后运算!!!

### 三.概括总结

​	以上内容是我在P2课下总结的一些经验，还是那句老话：**只有在机组上机之前学计组才效率最高！（虽然今天是周日）**

​	概括地说为以下几点:

1. 常见的在递归中需要维护的寄存器有```$ra```，对函数的传参``$a0``，如果在循环中进行递归则还需要维护循环变量i
2. 对于void型的递归函数要懂得把隐式表达的return翻译出来！
3. 在递归步骤集中进行push&&pop，这样在实现跳转时只需要```jr $ra```，对于要进行算术运算的，尤其要注意先pop后运算。
4. ```jal function   ——> jr $ra```
