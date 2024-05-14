---
title: 'MIPS中的函数调用'
date: '2023-9-13 23:24'
updated:
type:
comments: 
description: 'MIPS函数调用'
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
### 								 																	函数调用

#### 一.调用初印象

​	最早接触到函数调用是在选择排序程序中，**教学视频中代码块来换回拼接导致我看了好几遍视频！** 下面附上源码

```c
.data 
array: .space 400 // 申请数组空间
message_input_n: .asciiz "please input an integer as the length of the sequence\n"
message_input_array: .asciiz "please input an integer followed with a line breaker\n"
message_output_array: .asciiz "the sorted sequence is:\n"
space: .asciiz " "
stack: .space 100 // 申请栈空间

.globl main  // 在代码段起始位置声明main为全局符号
.text 

input:
    la $a0,message_input_n
    li $v0,4
    syscall

    li $v0,5   // number of integers to be sorted  
    syscall    // the number is stored in $v0
    move $t0, $v0 // set $t0 to the contents of $v0
        
    li $t1, 0  // 循环变量
    for_1_begin:
    slt $t2, $t1, $t0  // t2=1 if t1 < t0
    beq $t2, $zero, for_1_end
    nop                // 目标指令紧跟分支指令 增加延时槽防止并行性引起错误
        
 //下面这段代码实际上是在计算存入地址
 // 存入地址 = 首地址 + 循环变量 * 4
    la $t2, array  / 将数组首地址存入t2
    li $t3, 4
    mult $t3, $t1  // t3 * t1 高位存入 hi，低位存入lo 事实上一般的乘法，只要结果不超过32位，lo中的值就是完整的答案
    mflo $t3       // 将lo寄存器中移动到t3（所有的move指令都相当于赋值语句，复制后原值不会改变，而不是“移动”）
    addu $t2, $t2, $t3  //在首地址t2基础上加上偏移量t3

    la $a0,message_input_array //输出前导字符串
    li $v0,4
    syscall

    li $v0, 5          //输入数字
    syscall

    sw $v0, 0($t2)     // 从寄存器存入数组中对应的地址 这里的t2就是刚刚计算过的地址

    addi $t1, $t1, 1	// 循环变量++
    j for_1_begin
    nop
        
    for_1_end:
    move $v0, $t0
    jr $ra             //跳回到主程序，跳转语句的下一条语句
    nop

//与input同理 无需多言！
output:
    move $t0, $a0
    li $t1,0
    la $a0,message_output_array
    li $v0,4
    syscall

    for_2_begin:

    slt $t2, $t1, $t0
    beq $t2, $zero, for_2_end
    nop

    la $t2, array
    li $t3, 4
    mult $t3, $t1
    mflo $t3
    addu $t2, $t2, $t3

    lw $a0,0($t2)
    li $v0,1
    syscall

    la $a0,space
    li $v0,4
    syscall


    addi $t1, $t1, 1	
    j for_2_begin
    nop
    for_2_end:
    jr $ra
    nop
    
    
sort:
    addiu $sp,$sp,-32 //向低地址移动32字节
    move $t0,$a0   //此时a0值即为元素个数
    
    li $t1,0  //循环变量
    for_4_begin:
    slt $t2, $t1, $t0   //选择排序外层循环n-1趟
    beq $t2, $zero, for_4_end
    nop
    //计算地址
    la $t2, array
    li $t3, 4
    mult $t1, $t3
    mflo $t3
    addu $t2, $t2, $t3

    move $a0, $t0
    move $a1, $t1
        
    //父函数维护t寄存器 入栈  需要注意的是多层调用时$ra的维护
    sw $t2, 28($sp)
    sw $t1, 24($sp)
    sw $t0, 20($sp)
    sw $ra, 16($sp) //这时的ra值需要保存 这里的ra值记录的是返回到主函数的指令地址 经过调用findmin后会变为返回到sort的地址!

    jal findmin   //调用子函数
    nop
        
    //出栈
    lw $ra, 16($sp)
    lw $t0, 20($sp)
    lw $t1, 24($sp)
    lw $t2, 28($sp)
    
     //交换值   v0地址与t2地址处存储的值,两个寄存器分别存储
    lw $t3, 0($v0)
    lw $t4, 0($t2)
    sw $t3, 0($t2)
    sw $t4, 0($v0)

    addi $t1,$t1,1 //更新循环变量
    j for_4_begin
    nop

    for_4_end:
    addiu $sp,$sp,32  //将申请的栈空间退回，栈指针回到高地址
    jr $ra  //回到主程序
    nop



findmin:  //从sort中传入 a0值为n, a1值为外层循环变量i
    la $t0,array
    sll $a0,$a0,2
    subi $a0,$a0,4   //需要注意的是在 * 4的基础上需要减去4，
    addu $t0,$t0,$a0  //当前的地址是数组中最后一个元素的地址

    lw $t1, 0($t0)    // t1=a[n-1]
    move $t2,$t0
    move $t3,$t0      // t3 = t2 = t0 = 最后一个元素地址
        
    // a[i+1]
    la $t0,array
    sll $a1,$a1,2
    addu $t0,$t0,$a1// t0此时为a[i+1]地址

    for_3_begin:
    sge $t4,$t3,$t0 // t4 = 1 if t3 >= t0
    beq $t4,$zero,for_3_end
    nop

    lw $t5,0($t3)  // t5=a[n-1]
        
    // 进入查找时 t3为最后一个元素地址 ,t0为a[i+1]地址
    //这里寻找最小值的操作实际上是从末尾开始的，先记t1=a[n-1]为最小值，之后由哨兵 t5=遍历到的值 从n-1逐步向前遍历直到 i+1
    //不断更新t1的值作为新的最小值，并在t2中保存最小值地址 t3
    //这里t1被设置为保存最小值 如果当前遍历的元素小于t1最小值则进入下方更新最小值操作，否则进入if_1_else,顺序进入if_1_end遍历     //下一个元素
        
    slt $t6,$t5,$t1   // t6 = 1 if t5 < t1   第一次运行时 t5 = t1 直接跳转到 if_1_else
    beq $t6,$zero,if_1_else
    nop
    move $t1,$t5   //更新最小值
    move $t2,$t3   //保存最小值在数组中的的地址
    j if_1_end
    nop

    if_1_else:// if_1_else后误操作则直接执行 if_1_end  在标签之间无跳转时按照顺序执行
    
    if_1_end:
    subi $t3,$t3,4 //t3从末尾向前移动一个元素，更新遍历元素
    j for_3_begin
    nop

    for_3_end:
    move $v0,$t2
    jr $ra //回到主程序
    nop


main:
    la $sp, stack   
    addiu $sp,$sp,100  //此处sp+100是将指针向高地址移动
    addiu $sp,$sp,-20  //-20向低地址移动

    jal input   //跳转到input
    nop
        
    move $t0,$v0 //回到位置
    move $a0,$t0
    sw $t0, 16($sp) //向高地址偏移量为16  调用者维护t寄存器，将t0入栈
    
    jal sort
    nop
    
    lw $t0,16($sp) // t0出栈
    move $a0,$t0
    
    jal output
    nop

    addiu $sp,$sp,20
    
    li $v0,10  //程序结束
    syscall

```

​	**上述选择排序算法较为复杂，主体结构为```main```调用```input```,```sort```,```output```函数，在```sort```中又调用```findmin```子函数**

 **在MARS中的运行配置：**

* delayed branching
* initial program counter to global "main" if defined
* address configuration: compact ,data at address 0

​	**需要注意的是多层函数调用时除了经典的父函数维护t寄存器，子函数维护s寄存器，还要对```ra```寄存器进行维护**,经典的例子是在```sort```调用```findmin```过程中，```$ra```一开始存储的值是```sort```函数返回到主函数```main```下一条指令的地址，经过调用```findmin```,寄存器```$ra```中的值会被自动更新为```findmin```跳回到```sort```的指令地址，故需要保存跳回到```main```的指令地址，对```$ra```进行维护！！！

```c
//选择排序的C语言实现
void selectSort(int k[],int n)
{
    int i,j,d;
    int temp;
    for(i=0;i<n-1;i++){
        d=i;
        for(j=i+1;j<n;j++){//从后n-i+1中选取
            if(k[j]<k[d])d=j; //找到后面最小的元素
        }
        if(d!=i)//放在排好队的序列最后面
        {
            temp=k[d];
            k[d]=k[i];
            k[i]=temp;
        }
    }
}
```

#### 二.函数调用

​	对于函数调用，可以看成在函数调用的这条语句，程序跳转到函数的内容处开始执行，执行完整个函数后再跳转回到调用处向下执行，这个跳转过程可以用汇编语言中的跳转指令和标签实现。

```MIPS
# function call
jal function_name



# function
function_name:
    <function-content>
    jr $ra
```

​	在这里我们的跳转指令选择```jal```而非```j```, ```jal = jump and link```,相比j指令，```jal```多了将```PC+4 ```写入```$ra```的过程,即记录跳转语句下一条语句的地址。当函数结束时，会返回到之前调用它的位置，并执行下一条指令。故我们常常搭配使用```jal```和```j```.

​        **例如简单的C语言代码，计算两数相加**

```c
#include<stdio.h>

int sum(int a, int b)
{
    int tmp = a + b;
    return tmp;
}

int main()
{
    int a = 2;
    int b = 3;

    int ans = sum(a, b);

    printf("%d\n", ans);

    return 0;
}

```

​        **翻译成汇编语言**

```c
.macro end
    li $v0,10
    syscall
.end_macro

.text
main:
li $s0, 2
li $s1, 3
    
jal sum

move $a0, $s2
li $v0,1
syscall

end  //marco
    
sum:
add $s2,$s0,$s1
jr $ra
```

#### 三.复用代码

```c
int sum(int a, int b)
{
    int tmp = a + b;
    return tmp;
}

int main()
{
    int a = 2;
    int b = 3;
    int c = 4;
    int d = 5;

    int sum1 = sum(a, b);
    printf("%d", sum1);
    sum2 = sum(c, d);
    printf("%d", sum2);
    return 0;
}
```

​	上面的C程序，sum代码是可以进行复用的，但是在我们原来的汇编代码中，操作的寄存器是固定的，即```$s0,$s1,$s2```,为了复用代码（可以对其他寄存器进行操作），就必须要让一些特定寄存器作为“接收器”，对于不同的参数，都采用同一组寄存器来存储它们的值，也就是我们说的函数传参寄存器```$a0.$a1,$a2,$a3```,同样，对于返回值，也需要指定特定的寄存器。

```MIPS
//利用宏进行代码复用
.macro end
   li $v0,10
   syscall
.end_macro

.macro printStr(%Str)
    la $a0,%Str
    li $v0,4
    syscall
.end_macro

.data
   space: .asciiz " "
   
.text

li $t0,1
li $t1,2
li $t2,3
li $t3,4

#传参 $a0,$a1作为桥梁作用
move $a0,$s0
move $a1,$s1
jal sum
move $s4,$v0

li $v0,1
move $a0,$s4
syscall

printStr(space)

move $a0,$s2
move $a1,$s3
jal sum
move $s5,$v0

li $v0,1
move $a0,$s5
syscall

end

sum:
#传参
move $t0,$a0
move $t1,$a1
add $v0,$t0,$t1
jr $ra
```

​	**如果传参过程中```$a0,$a1,$a2,$a3```不够用（参数超过四个），可以利用栈```$sp```,将多余的参数存入内存中**。

#### 四.避免对外界造成影响

​	函数还有一个重要的功能是不对函数体外的变量造成不必要的影响

```c
int sum(int a, int b)
{
    int tmp = a + b;
    return tmp;
}

int main()
{
    int a = 2;
    int b = 3;
    int c = 4;
    int sum1 = sum(a, b);
    int sum2 = sum(sum1, c);
    printf("%d", sum2);
    return 0;
}

```

​	汇编

```MIPS
.macro end
    li      $v0, 10
    syscall
.end_macro

.text
li      $s0, 2
li      $s1, 3
li      $t0, 4

move    $a0, $s0 #传参
move    $a1, $s1
jal     sum
move    $s4, $v0 #获得返回值

move    $a0, $s4
move    $a1, $t0
jal     sum
move    $s5, $v0

li      $v0, 1
move    $a0, $s5
syscall

end

sum:
#传参过程
move    $t0, $a0 #t0被修改了
move    $t1, $a1
#函数过程
add     $v0 $t0, $t1
jr      $ra

```

​	**这里的问题在于，```$t0```在主函数中存储变量值,而在sum函数中用于接受参数，改变了原来的变量值**，即函数对外部产生了影响！

​	所以我们需要保证函数不会对外部造成影响，方法就是应用栈，利用栈来保存和恢复函数中所使用的寄存器。

在哪里维护寄存器：

* **t寄存器**：在调用者中进行维护‘
* **s寄存器**：在被调用者中进行维护

##### 1.在调用者中进行维护

```MIPS
.macro end
    li      $v0, 10
    syscall
.end_macro

.text
li      $s0, 2
li      $s1, 3
li      $t0, 4

move    $a0, $s0 #传参
move    $a1, $s1

#进行维护 入栈
sw      $t0, 0($sp)
addi    $sp, $sp, -4

jal     sum

#出栈
addi    $sp, $sp, 4
lw      $t0, 0($sp) 

move    $s4, $v0 #获得返回值

move    $a0, $s4
move    $a1, $t0

sw      $t0, 0($sp) #入栈
addi    $sp, $sp, -4

jal     sum

addi    $sp, $sp, 4 #出栈
lw      $t0, 0($sp)

move    $s5, $v0

li      $v0, 1
move    $a0, $s5
syscall

end

sum:
#传参过程
move    $t0, $a0
move    $t1, $a1
#函数过程
add     $v0 $t0, $t1
jr      $ra
```

​	**在以上代码中，只涉及到对t寄存器的维护，故只需要在主函数中使用栈，在子函数中无需使用栈，**在发觉这一点之前，我对于主函数中对于栈指针```$sp```移动的操作表示迷惑，认为完全可以删去这两行代码。**事实上，如果在一个参数的入栈与出栈之间没有对```$sp```进行任何操作，确实可以不移动```$sp```**,但事实上，**考虑到我们习惯上第一个参数入栈表达为 ```lw $s0 0($sp)```,即相对于栈指针地址没有偏移，而如果我们在子函数中这样存入，在主函数中又没有移动```$sp```,无疑会覆盖掉我们在那个位置上保存的参数，从而发生bug**,(杠精当然可以说我会写```lw $s0, 4($sp)```），但这样写在主函数中维护寄存器数量很多时很费笔墨，不如移动栈指针来的简洁。**当然，我们在子函数中同样需要注意栈指针的移动，在子函数结束时即使释放栈空间，防止覆写主函数中维护的参数。**

##### 2.在被调用者中维护

```MIPS
.macro end
    li      $v0, 10
    syscall
.end_macro

.text
li      $s0, 2
li      $s1, 3
li      $t0, 4

move    $a0, $s0 #传参
move    $a1, $s1
jal     sum
move    $s4, $v0 #获得返回值

move    $a0, $s4
move    $a1, $t0
jal     sum
move    $s5, $v0

li      $v0, 1
move    $a0, $s5
syscall

end

sum:
#入栈过程
sw      $t0, 0($sp)
addi    $sp, $sp, -4
#传参过程
move    $t0, $a0
move    $t1, $a1
#函数过程
add     $v0 $t0, $t1
#出栈过程
addi    $sp, $sp, 4
lw      $t0, 0($sp)
#return
jr      $ra
```

#### 五.嵌套函数调用

​	**嵌套函数调用的重要意识是用栈保存```$ra```，以保存向外层函数的跳转，这一点我在sort程序中就有发现，还是有一定理解能力(```bushi```).**

​	嵌套函数的C语言例子

```c
int sum(int a, int b)
{
    return a + b;
}
int cal(int a, int b)
{
    return a - sum(b, a);
}

int main()
{
    int a = 2;
    int b = 3;
    int ans = cal(2, 3);
    printf("%d", ans);
}
```

​	其中cal函数嵌套调用sum函数。

```MIPS
.macro end
    li $v0, 10
    syscall
.end_macro

.text
li      $s0, 2
li      $s1, 3

move    $a0, $s0
move    $a1, $s1
jal     cal
move    $s5, $v0

li      $v0, 1
move    $a0, $s5
syscall

end

sum:
#传参过程
move    $t0, $a0
move    $t1, $a1
#函数过程
add     $v0, $t0, $t1
#return
jr      $ra

cal:
#传参过程
move    $t0, $a0
move    $t1, $a1
#调用sum的过程
move    $a0, $t1
move    $a1, $t0
jal     sum
move    $t2, $v0
#运算a-sum(b, a)
sub     $v0, $t0, $t2 
#return
jr      $ra  #错误的地址值 造成死循环
```

​	**这段代码会陷入死循环！在cal调用sum时，```$ra```中的值由cal跳回主函数的地址由```jal```指令更改为sum跳回cal的地址，因此会陷入死循环。**

​	**所以我们可以总结出：在嵌套函数调用中，一旦一个函数不是叶子函数（调用逻辑的最低端），就需要保存和恢复```$ra```,以能够正常一层一层向上返回。**

​	以下为在父一级函数中对```$ra```进行维护的正确代码

```MIPS
.macro end
    li $v0, 10
    syscall
.end_macro

.text
li      $s0, 2
li      $s1, 3

move    $a0, $s0
move    $a1, $s1
jal     cal
move    $s5, $v0

li      $v0, 1
move    $a0, $s5
syscall

end

sum:
#将 $t0 和 $t1 入栈
sw      $t0, 0($sp)
addi    $sp, $sp, -4
sw      $t1, 0($sp)
addi    $sp, $sp, -4
#传参过程
move    $t0, $a0
move    $t1, $a1
#函数过程
add     $v0 $t0, $t1
#将 $t0 和 $t1 出栈
addi    $sp, $sp, 4
lw      $t1, 0($sp) 
addi    $sp, $sp, 4
lw      $t0, 0($sp) 
#return
jr      $ra

cal:
#将 $ra 入栈
sw      $ra, 0($sp)
addi    $sp, $sp, -4
#传参过程
move    $t0, $a0
move    $t1, $a1
#调用 sum 的过程
move    $a0, $t1
move    $a1, $t0
jal     sum
move    $t2, $v0
#运算a-sum(b, a)
sub     $v0, $t0, $t2
#将ra出栈
addi    $sp, $sp, 4
lw      $ra, 0($sp) 
#return
jr      $ra
```

#### 六.递归函数调用

​	最后的部分：递归函数的汇编翻译，**递归函数的本质是一个在函数体内调用自身的嵌套函数**。

​	C语言阶乘

```c
#include <stdio.h>

int factorial(int n)
{
    if (n == 1)
    {
        return 1;
    }
    else
    {
        return n * factorial(n - 1);
    }
}

int main()
{
    printf("%d\n", factorial(5));

    return 0;
}
```

​	汇编版本

```MIPS
# 程序结束
.macro end
    li      $v0, 10
    syscall
.end_macro

# 从标准输入处得到一个整型变量，并存储到 %des 寄存器中
.macro getInt(%des)
    li      $v0, 5
    syscall
    move    %des, $v0
.end_macro

# 向标准输出中写入一个数据，这个数据保存在 %src 寄存器中
.macro printInt(%src)
    move    $a0, %src
    li      $v0, 1
    syscall
.end_macro

# 将寄存器 %src 中的数据入栈
.macro push(%src)
    sw      %src, 0($sp)
    subi    $sp, $sp, 4  #入栈栈指针向低地址移动
.end_macro

# 将栈顶数据出栈，并保存在 %des 寄存器中
.macro pop(%des)
    addi    $sp, $sp, 4  # 出栈栈指针向高地址移动
    lw      %des, 0($sp) 
.end_macro

.text
main:
    getInt($s0)

    move    $a0, $s0
    jal     factorial   #最终结果存储在$v0中
    move    $s1, $v0

    printInt($s1)
    end


factorial:
    # 入栈
    push($ra)
    push($t0)
    # 传参
    move    $t0, $a0
    #函数过程
    bne     $t0, 1, else
    # 基准情况
    if:
        li      $v0, 1
        j       if_end  
    # 递归情况  
    else:
        subi    $t1, $t0, 1
        move    $a0, $t1
        jal     factorial
        mult    $t0, $v0
        mflo    $v0
    if_end:
    # 出栈
    pop($t0)
    pop($ra)
    # 返回
    jr   $ra
```

​	**我们可以预想到，调用过递归函数的栈中，一定是整整齐齐的存储了一排```$ra```值**

