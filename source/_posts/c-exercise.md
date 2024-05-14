---
title: c-exercise
date: 2024-03-01 19:12:35
updated:
type:
comments: 
description: 'OSpre预习作业c-exercise'
keywords: ‘OS预习作业'
mathjax:
katex:
aside:
aplayer:
categories: "操作系统"
highlight_shrink:
random:
tags: "Feb"
---

# <center>OS预习题1：c-exercise</center>

​	OS预习教程中第一道练习题，要求自行实现函数库中几个对于字符串进行操作的函数，为之后的实验打好基础(~~恢复已经遗忘的C语言记忆~~)，在做完之后，我搜索了标准库函数中他们的实现，更加简洁优雅。

## 1. strlen

**我的实现**

```
size_t strlen(const char *s) {
    size_t i  = 0;
    while(s[i] != '\0') {
        i++;
    }
    return i;
}
```

* 关于```size_t```类型：size_t 类型表示**C中任何对象所能达到的最大长度**，它是无符号整数。它是为了方便系统之间的移植而定义的，不同的系统上，定义size_t 可能不一样。**size_t在32位系统上定义为 unsigned int，也就是32位无符号整型。在64位系统上定义为 unsigned long ，也就是64位无符号整形。**size_t 的目的是提供一种可移植的方法来声明与系统中可寻址的内存区域一致的长度。

**标准库中的实现**

```
int strlen(const char *str)
{
    const char *p = str;
    while(*p != '\0')
        ++p;
    return p - str;
}
```

* 可以看出，标准库中的实现优雅之处在于使用了指针间的减法来计算长度。

### C语言中的指针运算

​	这里参考绿皮书对C语言中的指针运算进行简单回顾。

​	***若n是一个整形量，p是一个指针，则p+n和p-n是合法的指针运算表达式，指针类型不变，表达式的值由下式确定***
$$
value(p \pm n) \space = value(p) \space \pm n*sizeof(T)
$$
​	即我们知道**指针的移动是以指针变量类型的元素为步长移动的而不是以字节为单位进行移动的**（这个bug在后面的strsep中我犯过）

​	

### C语言中其他关于指针的基本概念

* 指针: 指针即地址，变量类型后加*代表指针变量类型

  ```
  int *p = &a
  ```

* 指针的解引用：代表指针指向的内容

  ```
  int a = 5;
  int * p = &a;
  *p = 5
  ```

* 关于数组名：数组名即为数组首地址，也是数组第一个元素的地址，可以作为指针使用

* 常量指针和指针常量

  * 常量指针：指向常量的指针(这个在strsep中用过，~~当时以为是指针常量，很奇怪~~)

    ```
    const char* p
    ```

  * 指针常量：固定指向的指针

    ``` 
    char* const p
    ```

  * 这两个总是分不清楚 首先记住指针常量吧，从英文字面上理解

    ```
    char*(指针) const(常量) p
    ```

* 指针与下标
  $$
  p[n] \space = \space *(p+n)
  $$

  $$
  \&p[n] = p + n
  $$

  **如果数组index<0说明向当前指针负向移动，否则正向**

## 2. strcat && strncat

### 1. strcat

**我的实现**

```
char * strcat(char *dst, const char *src) {
    char *temp = dst;
    while(*temp != '\0'){
        temp++;
    }
    while(*src != '\0'){
        *temp = *src;
        temp++;
        src++;
    }
    *temp = '\0';
    return dst;
}

```

**标准实现**

```
char * strcat (char * dst, const char * src)
{
    char * cp = dst;
 
    while( *cp )
        ++cp;           /* Find end of dst */
    while( *cp++ = *src++ )
       ;               /* Copy src to end of dst */
    return( dst );
}

```

### 2. strncat

**我的实现**

```
char *strncat(char *dst, const char *src, size_t n){
    size_t oplength = n;
    if(strlen(src) < n) {
        oplength = strlen(src);
    }
    char * temp = dst;
    while(*temp != '\0') {
        temp++;
    }
    for(size_t i = 0;i < oplength;i++) {
        *temp++ = *src++;
    }
    *temp = '\0';
    return dst;
}
```

**标准实现**

```
char *strncat (char *front, const char *back, unsigned count)
{
    char *start = front;
 
    while (*front++)
        ;
    front--;
 
    while (count--)
        if ((*front++ = *back++) == '\0')
            return(start);
    *front = '\0';
    return(start);
}
```

## 3. strsep

* 这个函数之前没遇到过，先简单解释一下概念，**概括起来就是进行字符串分割**

* 这个函数定义为
  $$
  char* strsep(char** stringp, const char* delim)
  $$

  * ```stringp```是指针的指针(~~虽然不知道为什么这么设计~~)，他解引用得到的字符串就是我们要处理的字符串
  * ```delim```是一个分割符字符串，其中的每个字符都是可以用来分隔字符串的分隔符
  * 该函数的主要功能是在字符串中找到第一个出现的分隔符，用 ‘\0’ 替换它，并返回指向原始分隔符之前的子字符串的指针。然后，通过更新传递给函数的指针来指向原始字符串的下一个位置（这个是教程中的叙述，我认为读起来非常拗口，他的大致意思就是这个函数只会对字符串中从左到右出现的第一个分隔符进行分割，把该位置的字符替换为\0，之后调整```*stringp```的位置为该分隔符之后，返回值为原始的```*stringp```）

  - 如果 `strsep` 的第一个参数所指向字符串的指针为 `NULL`（即 `*strsep == NULL`），那么函数直接返回 `NULL`，而不会执行任何分割操作。
  - 如果 `*stringp` 字符串中没有找到任一分隔符， `*stringp` 会被设置为 `NULL`。

**我的实现**

```
char* strsep(char** stringp, const char* delim){
    char *temp = *stringp;
    char *preStringp = *stringp;
    if(temp == NULL) {
        return NULL;
    } else {
        while(*temp != '\0') {
            const char* delete = delim;
            while(*delete != '\0') {
                if(*delete == *temp) {
                    *temp = '\0';
                    *stringp = temp + 1;
                    return preStringp;
                }
                delete++;
            }
            temp++;
        }
        if(*temp == '\0'){
            *stringp = NULL;
        }
        return preStringp;
    }
}
```

* 我的代码中出现过一处Bug就是前文提到的指针运算问题，未修改前，最外层while中我使用```*stringp```作为循环变量，但这个指针类型是字符串指针，一次移动当前字符串的字节大小，出现bug

**标准库实现**

```
char *strsep(char **stringp, const char *delim)
{
    char *s;
    const char *spanp;
    int c, sc;
    char *tok;
    if ((s = *stringp)== NULL)
        return (NULL);
    for (tok = s;;) {
        c = *s++;
        spanp = delim;
        do {
            if ((sc =*spanp++) == c) {
                if (c == 0)
                    s = NULL;
                else
                    s[-1] = 0;
                *stringp = s;
                return (tok);
            }
        } while (sc != 0);
    }
}
```

* 涉及到```char```和```int```形之间的转换，用```char```形值赋给```int```形会默认复制为他的```ascii```值，这里```\0```的```ascii```值为0
