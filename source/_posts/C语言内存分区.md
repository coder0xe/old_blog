---
title: 'C语言内存分区'
date: '2023-7-14 15:19'
updated:
type:
comments: 
description: '守得云开见月明'
keywords: '内存申请'
mathjax:
katex:
aside:
aplayer:
categories: 'C语言'
highlight_shrink:
random:
tags: 'July'
---
## C语言内存分区

5大分区

1. 栈区

   + 向下生长

   * 编译器自动分配释放
   * 存储：局部变量 形参 返回值

2. 堆区

   * 向上生长
   * 程序员调用和分配
   * malloc free

3. 全局（静态）区      

   + 全局变量 静态变量 

4. 常量区 

   + 字符串 数字

5. 代码区   

   + 程序代码

下面来看一段代码

```c
int *twoSum(int *nums,int numsSize,int target,int *returnSize)
{
   int ret[10]={0};
   for(int i=0;i<numsSize;i++){
       for(int j=i+1;j<numsSize;j++){
           if(nums[i]+nums[j]==target){
              ret[0]=i,ret[1]=j;
              *returnSize=2;
               return ret;
           }
       }
   }
   *returnSize=0;
   return NULL;
}
```

​       这是leetcode第一题 ，函数要求返回存储原数组下标的新数组，出现过错误在于在函数中去建立临时变量数组，再返回数组地址，

ret的内存被分配在栈区，然而栈区的内存会随着函数运行结束而被释放 ，因此返回的是无意义的地址，产生报错。

​       解决方法有两种 

1. 用malloc在堆区申请空间 

   malloc在堆区申请空间并不会随着函数运行结束而被释放

   ```c
   int *twoSum(int *nums,int numsSize,int target,int *returnSize)
   {
      for(int i=0;i<numsSize;i++){
          for(int j=i+1;j<numsSize;j++){
              if(nums[i]+nums[j]==target){
                int* ret=(int *)malloc(sizeof(int)*10);//malloc申请空间
                 ret[0]=i,ret[1]=j;
                 *returnSize=2;
                  return ret;
              }
          }
      }
      *returnSize=0;
      return NULL;
   }
   ```

   

2. 改变为静态变量  全局区

```c
int *twoSum(int *nums,int numsSize,int target,int *returnSize)
{
    static int ret[10]={0};//改为静态变量 全局区
   for(int i=0;i<numsSize;i++){
       for(int j=i+1;j<numsSize;j++){
           if(nums[i]+nums[j]==target){
              ret[0]=i,ret[1]=j;
              *returnSize=2;
               return ret;
           }
       }
   }
   *returnSize=0;
   return NULL;
}
```

