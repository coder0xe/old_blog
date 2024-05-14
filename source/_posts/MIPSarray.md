---
title: 'MIPS中的一维数组和二维数组'
date: '2023-9-11 23:24'
updated:
type:
comments: 
description: 'MIPS表示数组'
keywords: '面向对象先导'
mathjax:
katex:
aside:
aplayer:
categories: "计组"
highlight_shrink:
random:
tags: "Sep"
---
### 							MIPS中使用数组

#### 1.一维数组

​	**数组存储需要申请内存空间**

```MIPS
.data
array: .space 40           # 存储这些数需要用到数组，数组需要使用 10 * 4 = 40 字节
                           # 一个 int 整数需要占用 4 个字节，需要存储 10 个 int 整数
                           # 因此，array[0] 的地址为 0x00，array[1] 的地址为 0x04
                           # array[2] 的地址为 0x08，以此类推。

str:   .asciiz "The numbers are:\n"
space: .asciiz " "

.text
li $v0,5
syscall                    # 输入一个整数
move $s0, $v0              # $s0 is n
li $t0, 0                  # $t0 循环变量

loop_in:
beq $t0, $s0, loop_in_end  # $t0 == $s0 的时候跳出循环
li $v0, 5
syscall                    # 输入一个整数
sll $t1, $t0, 2            # $t1 = $t0 << 2，即 $t1 = $t0 * 4
sw $v0, array($t1)         # 把输入的数存入地址为 array + $t1 的内存中
addi $t0, $t0, 1           # $t0 = $t0 + 1
j loop_in                  # 跳转到 loop_in

loop_in_end:
la $a0, str
li $v0, 4
syscall                    # 输出提示信息
li $t0, 0
loop_out:
beq $t0, $s0, loop_out_end
sll $t1, $t0, 2            # $t1 = $t0 << 2，即 $t1 = $t0 * 4
lw $a0, array($t1)         # 把内存中地址为 array + $t1 的数取出到 $a0 中
li $v0, 1
syscall                    # 输出 $a0
la $a0, space
li $v0, 4
syscall                    # 输出一个空格
addi $t0, $t0, 1
j loop_out

loop_out_end:
li $v0, 10
syscall                    # 结束程序

```

#### 2.二维数组

```MIPS
.data
matrix: .space  256             # int matrix[8][8]   8*8*4 字节
                                # matrix[0][0] 的地址为 0x00，matrix[0][1] 的地址为 0x04，……
                                # matrix[1][0] 的地址为 0x20，matrix[1][1] 的地址为 0x24，……
                                # ……
str_enter:  .asciiz "\n"
str_space:  .asciiz " "

# 这里使用了宏，%i 为存储当前行数的寄存器，%j 为存储当前列数的寄存器
# 把 (%i * 8 + %j) * 4 存入 %ans 寄存器中
.macro  getindex(%ans, %i, %j)
    sll %ans, %i, 3             # %ans = %i * 8
    add %ans, %ans, %j          # %ans = %ans + %j
    sll %ans, %ans, 2           # %ans = %ans * 4
.end_macro

.text
li  $v0, 5
syscall
move $s0, $v0                   # 行数
li  $v0, 5
syscall
move $s1, $v0                   # 列数
# 这里使用了循环嵌套
li  $t0, 0                      # $t0 是一个循环变量

in_i:                           # 这是外层循环
beq $t0, $s0, in_i_end
li  $t1, 0                      # $t1 是另一个循环变量
in_j:                           # 这是内层循环
beq $t1, $s1, in_j_end
li  $v0, 5
syscall                         # 注意一下下面几行，在 Execute 页面中 Basic 列变成了什么
getindex($t2, $t0, $t1)         # 这里使用了宏，就不用写那么多行来算 ($t0 * 8 + $t1) * 4 了
sw  $v0, matrix($t2)            # matrix[$t0][$t1] = $v0
addi $t1, $t1, 1
j   in_j
in_j_end:
addi $t0, $t0, 1
j   in_i
in_i_end:
# 这里使用了循环嵌套，和输入的时候同理
li  $t0, 0

out_i:
beq $t0, $s0, out_i_end
li  $t1, 0
out_j:
beq $t1, $s1, out_j_end
getindex($t2, $t0, $t1)
lw  $a0, matrix($t2)            # $a0 = matrix[$t0][$t1]
li  $v0, 1
syscall
la  $a0, str_space
li  $v0, 4
syscall                         # 输出一个空格
addi $t1, $t1, 1
j   out_j
out_j_end:
la  $a0, str_enter
li  $v0, 4
syscall                         # 输出一个回车
addi $t0, $t0, 1
j   out_i

out_i_end:
li  $v0, 10
syscall

```

**事实上**，在做矩阵转化一题时，我用了利用一维数组模拟二维数组

```MIPS
.data
array: .space 10000
space: .asciiz " "
enter: .asciiz "\n"

.text
li $v0,5  # n
syscall
move $t0,$v0
addu $a1,$t0,$zero  # a1 is row_counter

li $v0,5 # m
syscall
move $t1,$v0
addu $a2,$t1,$zero # a2 is col_counter

add $a3,$a2,$zero           # calcultate row and col
li $t2,0  # i
mult $t0, $t1
mflo $t3  # m*n

input:
beq $t2, $t3, output

li $v0,5
syscall

sll $t4, $t2, 2
sw $v0, array($t4)
addi $t2, $t2, 1

j input


output:
beq $t3, $zero, output_end
beq $a3,$zero,if_1_else  # equals to 1 col ; row--
sll $t4,$t3,2
subi $t4,$t4,4
lw $t5, array($t4)
beq $t5,$zero,if_2_else


add $a0,$a1,$zero
li $v0,1
syscall

la $a0,space
li $v0,4
syscall

add $a0,$a3,$zero
li $v0,1
syscall

la $a0,space
li $v0,4
syscall

add $a0,$t5,$zero
li $v0,1
syscall

la $a0,enter
li $v0,4
syscall


subi $a3,$a3,1
subi $t3,$t3,1
j output


if_1_else:    # row--  col_reset
subi $a1,$a1,1
add $a3,$zero,$a2
j output


if_2_else:
subi $a3,$a3,1
subi $t3,$t3,1
j output



output_end:
li $v0,10
syscall
```

