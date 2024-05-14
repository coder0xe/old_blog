---
title: 'Leetcode01'
date: '2023-7-15 13:24'
updated:
type:
comments: 
description: '所谓万丈深渊，走下去也是前程万里'
keywords: 'hash'
mathjax:
katex:
aside:
aplayer:
categories: "leetcode"
highlight_shrink:
random:
tags: "July"
---
# 用hash解决leetcode01两数之和

***题意分析：在一个数组中找到和为target的两个元素，并返回下标数组***

## 1. 做法一：双层暴力循环

   ```c
   int *twoSum(int *nums,int numsSize,int target,int *returnSize)
   {
       static int ret[10]={0};
      for(int i=0;i<numsSize;i++){
          for(int j=i+1;j<numsSize;j++){
              if(nums[i]+nums[j]==target){
                //int* ret=(int *)malloc(sizeof(int)*10);
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

 ##  2. 做法二：hash

      ​       **利用hash的做法即为涉及到查找，我们设置一个集合，初始状态为空，在遍历原数组的过程中，就在这个集合中查找target-a[i],如果有则停止查找，如果没有则将该元素加入集合**

      ```c
      //leetcode支持ut_hash函数库
      typedef struct {
          int key;
          int value;
          UT_hash_handle hh;//make this structure hashable
      }map;
      map* hashMAP=NULL;
      void hashMapAdd(int key,int value)
      {
          map*s;
          HASH_FIND_INT(hashMap,&key,s);
          if(s==NULL){
              s=(map*)malloc(sizeof(map));
              s->key=key;
              HASH_ADD_INT(hashMap,key,s);
          }
          s->value=value;
      }
      map* hashMapFind(int key)
      {
          map*s;
         HASH_FIND_INT(hashMap,&key,s);
          return s;
      }
      void hashMapCleanup()
      {
          map*cur,*tmp;
          HASH_ITER(hh,hashMap,cur,tmp){
              HASH_DEL(hashMap,cur);
              free(cur);
          }
      }
      void hashPrint(){
          map*s;
          for(s=hashMap;s!=NULL;s=(map*)(s->hh.next))
          {
              print("key %d ,value %d\n",s->key,s->value);
          }
      }
      int *twoSum(int *nums,int numsSize,int target,int *returnSize)
      {
          int i,*ans;
          map* hashMapRes;
          hashMap=NULL;
          ans=(int*)malloc(sizeof(int)*2);
          for(i=0;i<numsSize;i++){
              hashMapAdd(nums[i],i);
          }
          hashPrint();//经典打印检查
          for(i=0;i<numsSize;i++){
              hashMapRes=hashMapFind(target-nums[i]);
              if(hashMapRes&&hashMapRes->value!=i){
                  ans[0]=i;
                  ans[1]=hashMapRes->value;
                  *returnSize=2;
                  return ans;
              }
          }
          hashMapCleanup();
          *returnSize=0;
          return NULL;
      }
      ```

      ​     此种做法中涉及到很多使用头文件函数库uthash.h中的用法，当然我们也可以将这些封装好的函数进行手搓，关于函数库uthash.h会在下一篇中介绍

      **手搓hash**

      ```c
      //每个元素的关键是值和下标
      struct node{
          int key;
          int value;
          struct node*link;
      };
      struct node *hash[10000]={NULL};
      //这里的整数值都比较小，可以考虑直接取余法(质数)，但是会处理一些冲突
      int HASH(int key)
      {
         return key%13;
      }
      int *twoSum(int *nums,int numsSize,int target,int *returnSize)
      {
          int*a=(int*)malloc(sizeof(int)*2);
          for(int i=0;i<numsSize;i++){
              //以下代码为target>nums[i]
              if(hash[HASH(target-nums[i])]==NULL){
                  struct node *p=(struct node*)malloc(sizeof(struct node));  
                  p->key=nums[i];p->value=i;p->link=NULL;
                  hash[HASH(nums[i])]=p;                                                                                                                          
              }else{
                  struct node *q=hash[HASH(target-nums[i])];
                  while(q!=NULL){
                      if(q->value!=i)//不同下标
                      {
                          a[0]=q->value,a[1]=i;
                          *returnSize=2;
                          return a;
                      }
                      q=q->link;
                  }
              }
          }
          *returnSize=0;
          return NULL;
      }
      ```

      ​      **但是测试用例中会出现target<nums[i]的情况，即可能出现hash数组下标为负数的情况，以上版本不能通过**

      

      