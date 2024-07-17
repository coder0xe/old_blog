---
title: learn-python-the-smart-way
date: 2024-07-13 12:25:54
updated:
type:
comments: 
description: '一天速通python语法'
keywords: 'python语法'
mathjax:
katex:
aside:
aplayer:
highlight_shrink:
random:
categories: "AI"
tags: "July"
---

# learn-python-the-smart-way

> 一日速通python语法 参加第二天夏令营

## 1. 变量与常量

### 1.1 常量

* 固定的量，值不能改变
  * 数字：整数/浮点
  * 字符串：单引号/双引号/三引号
  * 逻辑值：True/False
* ``type(*)``查看``*``的类型

### 1.2 变量

* python中无需声明变量类型，直接赋值，python来确定变量类型

  ```python
  b = "hello"
  type(b)
  ```

* 变量命名：数字/字母/下划线

## 2. 运算符与函数

### 2.1 运算符

* 算数运算符

  * ``+ - * /``
  * 乘方``**``
  * 整除``//``
  * 取余``%``

* 逻辑运算符

  ```python
  > >= < <= == != and or not
  ```

* 位运算符

  ```python
  >> << & | ^ ~
  ```

### 2.2 函数

* 函数通过``def``关键字声明，函数的输入由函数名后的括号内参数定义，函数的结构由``return``关键字定义

* python的一大语法特点是**缩进敏感**，在函数体中需要使用``tab``缩进来表示这是函数的内容

  ```python
  def judge_equal(a,b):
      return a==b
  print(judge_equal(1,1))
  ```

* 在函数体中使用全局变量时，用``global``关键字进行标注

  ```python
  def judge_equal(a,b):
      global v
      return (a+b == v)
  ```

## 3. Python控制流

> * if-else
> * while
> * for-in
> * break
> * continue

### 3.1 while

```python
while *** :
    statement
```

* 同样需要使用``tab``表示循环体内内容

```python
a = 0
while a < 5:
    a=a+1
    print(a)
```

### 3.2 for

* ``for-in``根据某个序列进行循环迭代，直到迭代完整个序列

  ```python
  for * in *** :
      statement
  ```

* 一个例子

  ```python
  sum = 10
  def judge_equal(a,b):
      global sum
      return a+b==sum
  
  for i in [2,3,5,6,7]:
      for j in [2,3,5,6,7]:
          if judge_equal(i,j):
              print("sum is equal to 10:",i,j)
  ```

### 3.3 if-else

```python
if ***:
    statement1
else:
    starement2
```

* 一个例子

  ```python
  if judge_equal(i,j):
      print("sum is equal to 10:",i,j)
  else :
      pass
  ```

### 3.4 break & continue

* ``break``关键字只用于停止当前循环，如果需要跳出外层循环，可以增加一个标记变量，用来逐层跳出循环

  ```python
  flag = False
  for i in [2,3,5,6,7]:
      if flag :
          break
      for j in [2,3,5,6,7]:
          if judge_equal(i,j):
              print("sum is equal to 10:",i,j)
              flag = True
              break
          else :
              pass
  ```

* ``continue``关键字用于停止当前循环，执行循环中下一个迭代

> ``Excercise 1``
>
> ​	按规定，某种电子元件使用寿命超过 1000 小时为一级品。已知某一大批产品的一级品率为 0.2，现在从中随机地抽查 20 只。使用 Python 计算 20 只元件中恰好有 k 只 (k=0,1,...,20) 为一级品的概率为？
>
> ```python
> def calculate_P(num):
>     i = 1
>     sum = 1
>     while i < num :
>         i=i+1;
>         sum = sum * i;
>     return sum
> 
> def calculate_C(n,k):
>     return calculate_P(n)/(calculate_P(n-k)*calculate_P(k))
> 
> def calculate_X(n,k):
>     return (0.2 ** k) * (0.8 ** (n - k))    
> 
> k = 0
> while k != 21:
>     print('P{ X =',k,'} = ', calculate_C(20,k)*calculate_X(20,k))
>     k = k + 1
> ```

## 4. Python数据结构

> * 列表 list : 保存有序项集合的**变量**，以方括号标识
> * 元组 tuple : 保存有序项集合的**常量**，以圆括号标识
> * 字典 dict : 保存无序``(key, value)``，以花括号标识
> * 集合 set : 保存无序项集合的变量  

### 4.1 列表

* 保存有序项：**列表中可以容纳不同的元素类型**

* 方括号创建

  ```python
  list = [1,2,3,4]
  ```

* 列表操作

  * 增：``append``

    ```python
    list.append('dqr')
    ```

  * 删：``del``

    ```python
    del list[0]
    ```

  * 查：``[]``查找某个位置元素

    ```python
    print(list[0])
    ```

    * 0表示第一个元素
    * 支持倒序查找：-1表示倒数第一个元素

  * 改：赋值符号``=``

* 有关``[]``关键字

  * 表示列表中元素位置
  * 切片形式获取子列表，左闭右开

* `append(x)` 把元素 x 放在入列表尾部

* `count(x)` 统计元素 x 在列表中出现次数

* `extent(seq)` 把新列表 seq 合并到列表尾部

* `index(x)` 返回元素 x 在列表第一次出现的位置

* `insert(index, x)` 把元素 x 插入到 index 位置

* `pop(index)` 删除并返回 index 所在位置的元素

* `remove(x)` 删除出现的第一个 x 元素

* `reverse()` 颠倒列表顺序

* `sort()` 对列表进行排序

> ``Exercise 2 ``
>
> Task 1. 在上一次期末考试中，XiaoHu 考了数学 65 分，语文 55 分；XiaoMing 考了数学 80 分，语文92 分；XiaoWei 考了数学 95 分，语文 98 分，以此建立学生成绩管理系统。
>
> Task 2. 在本次期末考试中，XiaoHu 考了数学 95 分，语文 85 分；XiaoMing 考了数学 75 分，语文 71 分；XiaoWei 考了数学 92 分，语文 93 分，以此对之前的成绩进行更新。
>
> Task 3. 由于 XiaoMing 的成绩出现了大幅度下滑，家长决定要 XiaoMing 转学到另一所高中，以此在系统中删除 XiaoMing 的信息。
>
> Task 4. 学校新转来的学生 Cryin 本次考试成绩为 数学 87 分，语文 88 分，在系统中录入 Cryin 的成绩。
>
> ```python
> # Exercise 2 建立简单层级化管理
> XiaoHu = [65,55]
> XiaoMing = [80,92]
> XiaoWei = [95,98]
> students = [XiaoHu,XiaoMing,XiaoWei]
> print(students)
> students[0] = [95,85]
> students[1] = [75,71]
> students[2] = [92,93]
> print(students)
> del students[1]
> Cryin = [87,88]
> students.append(Cryin)
> print(students)
> ```

### 4.2 元组

* **元组是常量，无法修改的列表**

  ```python
  tuple = (1,2,3,4)
  ```

### 4.3 字典

* 字典就相当于其他语言中的哈希表，以``key->value``形式组织

* 通过``{}``创建，通过``:``区分键与值，通过``,``分隔键值对

  ```python
  dict = {
      'dqr': 1,
      'xzq': 2,
      'lzq': 3,
  }
  ```

* 字典操作：由于哈希是无序的，方括号内为键值

  * 增：通过关键字``[]``

    ```python
    dict['hxr'] = 4
    ```

  * 删：通过关键字``del``

    ```python
    del dict['dqr']
    ```

  * 查

    ```python
    print(dict['xzq'])
    ```

  * 改

    ```python
    dict['xzq'] = 5
    ```

* `clear()` 清除字典内所有元素

* `copy()` 返回字典的一个复制

* `has_key(key)` 检查 key 是否在字典中

* `items()` 返回一个含由 (key, value) 格式元组构成的列表

* `keys()` 返回由键构成列表

* `values()` 返回由值构成的列表

* `setdefault(key, default)` 为键 key 添加默认值 default

* `pop(key)` 删除 key 并返回对应的值

>Exercise 3
>
>使用字典优化两数之和为target算法，大大降低时间复杂度
>
>```python
>def judge_target(nums,target):
>    hash = {}
>    for i in nums:
>        if i in hash:
>            return [i,hash[i]]
>        else :
>            hash[target - i] = i
>```

### 4.4 集合

* 存储无序的元素集合，不考虑元素的顺序和出现次数

* 通过``{}``创建，用``,``分隔集合元素

  ```python
  set = (1,2,3,4)
  ```

* 集合操作

  * 增：``add``

    ```python
    set.add(4)
    ```

  * 删：``remove``

    ```python
    set.remove(1)
    ```

  * 查：``in``

    ```python
    print(1 in set)
    ```

* **集合支持数学操作：求集合的交集、并集、异或**

  * 并集

    ```python
    s1 | s2
    ```

  * 交集

    ```python
    s1 & s2
    ```

  * 异或

    ```python
    s1 ^ s2
    ```

* `add(x)` 向集合中添加元素 x
* `clear()` 清空集合
* `copy()` 返回集合的一个复制
* `difference(set)` 返回集合与另一个集合的差集
* `discard(x)` 删除元素 x
* `isdisjoint(set)` 判断两个集合是否有交集
* `issubset(set)` 判断新集合 set 是否是集合的子集
* `issuperset()` 判断新集合 set 是否是集合的超集

## 5. python面向对象

> 面向对象思想oo中早已熟悉qwq

* python使用``class``关键字定义类

  ```python
  class student():
      name = 'undefined'
      math_score = None
      chinese_score = None
  
  xiaohu = student();
  xiaohu.name = 'xiaohu'
  print(xiaohu.name)
  ```

* 在类中定义属性和方法，对象通过``.``符号进行引用

* 在类中定义方法(函数)

  ```python
  class student():
  	def change_name():
  	#
  ```

* **构造方法(初始化对象): ___init__**_

  ```python
  class student():
      # attributes
      name = 'undefined'
      math_score = None
      chinese_score = None
      # operation
      def __init__(self,name, math_score, chinese_score) :
          self.name = name
          self.math_score = math_score
          self.chinese_score = chinese_score
  
  xiaohu = student('xiaohu', 99, 100);
  print(xiaohu.name)
  print(xiaohu.math_score)
  ```

## 6. python 文件与模块

* 打开文件``open(file, mode)``

  ```python
  file = open("test.txt", 'w')
  content  = "hello world\n"
  file.write(content)
  file.close()
  file = open("test.txt", 'r')
  content = file.readlines()
  print(content)
  file.close()
  ```

  * ``r / rb``
    * ``read()``：读取整个文件
    * ``readlines()``：读取文件的所有行
    * ``readline()``：读取文件的一行
  * ``w / a``
    * ``write()``
    * ``writelines()``

* ``open``函数返回的时一个指针，对文件的读写操作会移动文件指针的``offset``，只有重新定位到文件头或``close()``后才能重新定位指针

* 模块

  * 使用``import``关键字来将一个模块引入到一个程序

    ```python
    import math
    ```

  * 当我们只需要模块中的几个函数或类时，使用

    ```python
    from model_name import xxx
    ```



