---
title: 'MIPS中自动存入地址的指令'
date: '2023-9-14 23:24'
updated:
type:
comments: 
description: '保存地址的指令'
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
### 				向```$ra```寄存器中自动存入地址的指令

​	**在进行编写MIPS部分矩阵转化一题时，我误以为```beq```等分支指令也会将下一条指令的地址存入```$ra```寄存器**,这导致出现访存bug.

​	在MIPS架构的汇编指令中，只有

* **```jal```**: 会将当前指令的地址存入```$ra```中，并跳转到目标地址执行
* ```jalr```:**会将要跳转的地址存入目标寄存器**，**并将当前指令的地址存入```$ra```**.
* ```beq```与```bne```等条件分支指令则不会有将当前地址存入```$ra```寄存器的行为。
* 4560