---
title: OOpre_HW3
date: 2023-9-22 12:56:53
updated:
type:
comments: 
description: '新增背包概念'
keywords: '面向对象先导'
mathjax:
katex:
aside:
aplayer:
categories: "面向对象先导"
highlight_shrink:
random:
tags: "Sep"
---
### 						```OOpre_HW3 and JUnit```

#### 一.关于```OO_checkstyle```的新发现

1. 只能采用驼峰命名法命名变量
2. 方法行数不超过60行(后续重构代码将操作与处理输入分离的主要原理，虽然只有两分)
3. 每行字数不超过100（方法传参时发现）
4. 其余关于空格的问题省略

#### 二.增量开发的思路

* 在此次作业中，新增了“食物”、“背包”等概念。```food```作为与```equipment```和```bottle```同级物品，背包则负责容纳这些物品。
* 新增操作：
  1. 尝试携带（放入背包）某物品（保证尝试携带的物品冒险者已经拥有）
  2. 尝试使用某物品（该物品必须被携带才能够使用）

​	**实现逻辑**：我们需要明白“携带”与“使用”的业务逻辑。

1. 我的第一版代码实现思路

​	我第一版代码中，按照题目描述，将```food```与```package```作为新建类处理，冒险者与背包之间的关系使用哈希表处理，建立起```<advid,package>```的映射，在背包中建立三个容器分别存储瓶子，装备和食物。对于加入背包，我的理解是，为冒险者增加物品是将物品放在冒险者对应的类```adventure.java```中对应的总库三个容器中，加入背包需要将物品从总库移动到与冒险者对应的背包，从物理角度来看是对物品进行了移动。这导致实现起来非常麻烦，例如统计数量等都需要考虑两个部分。这与题意不符，具体体现在中测最后一个数据点不过。

2. 第二版代码

​		经过与助教的沟通，我理解到：

1. **放入背包是一个概念的问题，而不是一个物理上的问题**。放入背包并不需要将物品从总库中删除，只需要加入背包。

2. 一开始处理中建立冒险者与背包对应哈希表的想法并不符合面向对象的逻辑，这是面向过程的思路，如果想要具体实现背包应该建立在冒险者类中

3. 既然放入背包是一个概念问题，那么我们完全可以不去实现背包实体，而只需要进行概念上的判断。例如给每个物品增加一个属性

   ```java
   private boolean becarried ;
   ```

   初始时设置为```false```即不在背包中，放入背包即建立方法将属性设置为```true```.这个思路在实现代码上是十分简便的，具体体验到的优势如下：

   1. 不需要新建数据结构存储放在背包中的物品
   2. 判断该物品是否在背包中只需要获取属性```becarried```
   3. 删除物品只需要在```adventure```类中的总库删除，实现简洁
   4. 获取物品数量是需要获取总库中的数量

#### 三.代码架构与重构

​	经过```checkstyle```与```JUnit```对于代码架构的步步限制，我经历了三次代码重构，第一次是在编写过程中发现方法的行数不能超过60行，第二次是在传参时受到限制，选择将定义的静态方法从```operation.java```移动到```inputhandler.java```,第三次是编写```JUnit```过程中由于不能进行输入输出重定向等测评机认为的违法操作，这样只能将输入集中到一个类中，后续在方法中进行读取已经存储好的输入。

​	对于输入的类，原码如下

```java
import java.util.Arrays;
import java.util.Scanner;
import java.util.ArrayList;

public class Main {
    public static void main(String [] args) {
        ArrayList<ArrayList<String>> inputInfo = new ArrayList<>(); // 解析后的输入将会存进该容器中, 类似于c语言的二维数组
        Scanner scanner = new Scanner(System.in);
        int n = Integer.parseInt(scanner.nextLine().trim()); // 读取行数
        for (int i = 0; i < n; ++i) {
            String nextLine = scanner.nextLine(); // 读取本行指令
            String[] strings = nextLine.trim().split(" +"); // 按空格对行进行分割
            inputInfo.add(new ArrayList<>(Arrays.asList(strings))); // 将指令分割后的各个部分存进容器中
        }
        InputHandler inputHandler = new InputHandler(inputInfo);
        inputHandler.solve(n);
    }
}

```

​	这样所有的操作指令被以分割的字符串的方式存入```inputinfo```,后续将```inputinfo```传入```inputhandler```类进行处理，所有的变量从这个形式上的二维数组中读取。这样可以避免在编写```JUnit```时无法控制台输入导致无法测试方法导致覆盖率不够，第二部分任务寄掉的问题。

​	**详解输入解析**

```java
//定义的数组类型为 ArrayList<Arraylist<String>> 处理完每一行输入后的示意图如下
//行数
0  "1" "123456" "dqr"
1  "2" "123456" "111" "ok" "50"
//需要注意的是排列近似于二维数组，里面的每一个元素以字符串的形式存储
//读取二维数组中的元素
String name = inputinfo.get(0).get(0);//读取第一行中的第一个元素
int id = Integer.parseInt(inputinfo.get(0).get(1));//将字符串类型转化为整数类型
//存入方式 我这里选择模仿
String nextLine = "1 123456 dqr"; // 本行指令
String[] strings = nextLine.trim().split(" +"); // 按空格对行进行分割
inputInfo.add(new ArrayList<>(Arrays.asList(strings))); // 将指令分割后的各个部分存进容器中
```

#### 四.```JUnit```

1. 编写```JUnit```时由于导入头文件错误，且测试方法前没有写```@Test```导致测评机无法识别，此处提供```JUnit```模板

```java
//只需要导入这两个头文件
import org.junit.Test;
import static org.junit.Assert.*;

public class FoodTest  {

    @Test //每个测试方法前必须要有
    public void test() {
     
    }
}
```

2. 编写时常用到的```JUnit4```标准断言

| 方法                                                | 介绍                                 |
| --------------------------------------------------- | ------------------------------------ |
| ```assertEquals(expected, actual)```                | 检查两个值是否相等                   |
| ```assertTrue(condition)```                         | 检查条件是否为真                     |
| ```assertFalse(condition)```                        | 检查条件是否为假                     |
| ```assertNotNull(object)```                         | 检查是否不为空                       |
| ```assertNull(object)```                            | 检查是否为空                         |
| ```assertNotSame(expected, actual)```               | 检查两个相关对象是否不指向同一个对象 |
| ```assertSame(expected, actual)```                  | 检查两个相关对象是否指向同一个对象   |
| ```assertArrayEquals(expectedArray, resultArray)``` | 检查两个数组是否相等                 |

**注：使用assert()断言是测评机不识别的，会导致本地覆盖率与测评结果差距较大**

3. 运行测试代码报错空指针

   ​	编写```inputhandler.java```中的测试方法时，由于有删除，携带等操作，前提是必须有对应的冒险者，对应的物品，所以想要测试这个方法需要连带调用前提方法，如果不建立前提就会出现空指针。这里的测评方法同样是采用建```ArrayList<ArrayList<String>>```类型并进行赋值。相对来说对```inputhandler```的测试是最为复杂的。

#### 五.特别致谢助教

