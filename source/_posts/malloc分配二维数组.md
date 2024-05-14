---
title: 'malloc分配二维数组'
date: '2023-7-16 16:02'
updated:
type:
comments: 
description: 'malloc分配两次'
keywords: 'malloc'
mathjax:
katex:
aside:
aplayer:
highlight_shrink:
random:
categories: "C语言"
tags: "July"
---

## 利用malloc分配二维数组

先利用malloc分配出连续的行，再分别对每行分配内存

```c
int row,col;//二维数组的行数和列数
int** a=(int**)malloc(sizeof(int)*row);//分配出连续的行头
for(int i=0;i<row;i++){
    a[i]=(int* )malloc(sizeof(int)*col);//分配每一行的内存
}
```

注：每一行的内存是连续的，相邻两行的内存不一定连续