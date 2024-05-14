---
title: OOpre_HW2
date: 2023-9-15 12:56:53
updated:
type:
comments: 
description: '增量开发第一次'
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
### 			OOpre_HW2 第一次进行类的编写（冒险者故事的开端）

#### 1.什么是面向对象(Object Oriented)

​	对象能够直接反映现实生活中的事物，例如人、车、小鸟等，将其表示为程序中的对象，每个对象都有各自的状态特征（属性）以及行为特征（方法），除了可以存储数据外还可以对自身进行操作，相当于结构体与函数的封装。

​	面向对象就是把构成问题的事物分解成一个一个的对象，建立对象不是为了实现一个步骤，而是描述某个事物在解决问题中的行为。

​	类是面向对象中的一个很重要的概念，类是很多个具有相同属性和行为特征的对象所抽象出来的，**对象是类的一个实例**。

#### 2. OO三大特征

* 封装
* 继承
* 多态

#### 3.类与对象

​	类表示一个共性的产物，是一个综合的产物，而对象是一个个性的产物，**类必须通过对象才可以使用，对象的所有操作都在类中定义**

##### 类由属性和方法组成

* 属性：特征
* 方法：行为

​	一个类想真正地进行操作则必须依靠对象

```java
//对象的定义
classname objectname = new classname();//所有类的对象都是通过new关键字创建
//访问类中的属性或方法
objectname.id //访问属性
objectname.func(parameter1,parameter2)//调用方法
```

##### 类的编写规则

* 类必须编写在.java文件中
* 一个.java文件中可以存在多个类，但只能存在一个public修饰的类
* .java文件名必须与public修饰的类名相同
* 同一个包中不能有重名的类

#### 4.构造方法

​	**在创建对象时，调用构造方法，所有的JAVA类中都至少存在一个构造方法（除了主类）**，如果一个类中没有明确的编写构造方法，编译器会自动生成一个无参的构造方法，构造方法中没有任何的代码！如果自行编写了构造方法，则编译器不会生成无参的构造方法。

##### 构造方法的定义格式

* 构造方法名称必须与类名相同
* 没有返回值类型的声明

```java
//创建一个对象就要调用构造方法
//一个自定义构造方法的例子
public person(String name, int age)
{
    this.name = name;
    this.age = age;
}
```

##### this关键字

* this指当前对象
* 程序中非静态方法可以使用this关键字
* 指向当前代码运行时所处于的对象空间
* 引用当前对象的实例变量
* 目前只在构造方法中接触this关键字

##### static关键字

* static修饰变量为静态变量，也成称为类变量，静态变量属于类本身，而不是属于对象实例。该类的所有对象共享同一个静态变量的值，不会开辟出多块内存空间，可以通过<类名>.<变量名>来访问静态变量，但此时的变量需要被public修饰而不是private

  ```java
  public static int variablename;
  classname.variablename
  ```

  静态变量在程序运行期间只会被初始化一次，在内存中常驻不被销毁

* static修饰的成员方法是静态方法，也成为类方法。静态方法属于类本身，不依赖于对应的对象实例。可以通过<类名>.<方法名>来调用静态方法,方法需要被public修饰

  ```java
  public static int method{
      //?
  }
  className.methodName
  ```

* 静态方法只能访问静态属性，非静态方法可以访问静态属性和非静态属性

* 静态方法不能调用非静态方法，非静态方法可以调用静态方法

#### 5.类成员的可见性

* public:任意外部对象都能访问
* protected:本类或子类对象可以访问
* private:只有本类对象才能访问

**注意：所有的作业中对于类中属性的定义都应为private!**

### 						第一次作业内容，增量开发的基础

### 背景

在接下来的若干次作业中，同学们将进行以本次作业为基础的迭代开发，因此在具体的代码实现中，希望同学们可以考虑到每一次所写代码的可扩展性和可维护性，从而减少下一次的工作量。

在接下来的几次作业中，请想象你是一个穿越到魔法大陆上的冒险者，在旅途中，你需要收集各种道具，使用各种装备，招募其他冒险者加入队伍，提升自己的等级并体验各种战斗。


在本次作业中，你要做的是：

- 实现冒险者类 `Adventurer` 、药水瓶类 `Bottle` 、装备类 `Equipment`

- 利用容器，管理所有冒险者，并管理每一个冒险者所拥有的药水瓶和装备

  


你可能需要实现的类和它们要拥有的属性

- Adventure ：ID，名字，药水瓶和装备各自的容器
- Bottle：ID，名字，容量(capacity)
- Equipment：ID，名字，星级(star)

**请注意，在作业中，可能会存在ID不同但名字相同的情况，请同学们在设计代码的时候考虑这一点**

其中，Bottle的容量属性在本次作业中不会被测试，但是却是后续作业的重要部分，请同学们不要忽略。

在本次作业中，初始时，你没有需要管理的冒险者，我们通过若干条操作指令来修改当前的状态：

1. 加入一个需要管理的冒险者（新加入的冒险者不携带任何药水瓶和装备）

2. 给某个冒险者增加一个药水瓶

3. 删除某个冒险者的某个药水瓶

4. 给某个冒险者增加一个装备

5. 删除某个冒险者的某个装备

6. 给某个冒险者的某个装备提升一个星级

   

其中，提升星级的意思是，新星级=原有星级+1

### 输入格式

第一行一个整数 *n*，表示操作的个数。

接下来的 n 行，每行一个形如 `{type} {attribute}` 的操作，`{type}` 和 `{attribute}` 间、若干个 `{attribute}` 间使用**若干**个空格分割，操作输入形式及其含义如下。同时，为了方便测评，我们需要在需要执行一些指令后进行相关输出。具体要求也在下面的表中列出：

| type | attribute                             | 意义                                                         | 输出格式（每条对应的占一行）                                 |
| ---- | ------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 1    | `{adv_id} {name}`                     | 加入一个 ID 为 `{adv_id}`、名字为 `{name}` 的冒险者          | 无                                                           |
| 2    | `{adv_id} {bot_id} {name} {capacity}` | 给 ID 为 `{adv_id}` 的冒险者增加一个药水瓶，药水瓶的 ID、名字、容量分别为 `{bot_id}`、`{name}`、`{capacity}` | 无                                                           |
| 3    | `{adv_id} {bot_id}`                   | 将 ID 为 `{adv_id}` 的冒险者的 id 为 `{bot_id}` 的药水瓶删除 | `{一个整数} {一个字符串}`（解释：整数为删除后冒险者药水瓶数目，字符串为删除的药水瓶的name） |
| 4    | `{adv_id} {equ_id} {name} {star}`     | 给 ID 为 `{adv_id}` 的冒险者增加一个装备，装备的 ID、名字、星级分别为 `{equ_id}`、`{name}`、`{star}` | 无                                                           |
| 5    | `{adv_id} {equ_id}`                   | 将 ID 为 `{adv_id}` 的冒险者的 id 为 `{equ_id}` 的装备删除   | `{一个整数} {一个字符串}`（解释：整数为删除后冒险者装备数目，字符串为删除的装备的name） |
| 6    | `{adv_id} {equ_id}`                   | 将 ID 为 `{adv_id}` 的冒险者的 id 为 `{equ_id}` 的装备提升一个星级 | `{一个字符串} {一个整数}`（解释：字符串为装备的name，整数为装备升星后的星级） |

输出数值时，你的输出数值需要和正确数值相等。

#### 输入输出样例

##### 输入1

```
4
1 700917 i$KdS=1n
4 700917 829431 ?TE/G1 3 
6 700917 829431
5 700917 829431
```

##### 输出1

```
?TE/G1 4
0 ?TE/G1
```

##### 输入2

```
3
1 700917 i$KdS=1n
2 700917 829431 ?TE/G1 3 
3 700917 829431
```

##### 输出2

```
0 ?TE/G1
```

### 数据限制

##### 变量约束

| 变量       | 类型   | 说明                                   |
| ---------- | ------ | -------------------------------------- |
| `id `      | 整数   | 取值范围：0 - 2147483647               |
| `name`     | 字符串 | 保证不会出现空白字符，长度区间: (0,40) |
| `capacity` | 整数   | 取值范围：0 - 2147483647               |
| `star`     | 整数   | 取值范围：0 - 2147483647               |

##### 操作约束

1. **保证所有的冒险者、药水瓶、装备 id 均不相同**
2. 保证删除了的药水瓶/装备的 id 不会再次出现
3. 2-6保证所有冒险者均已存在
4. 3/5/6保证该冒险者拥有操作中提到 id 的药水瓶/装备
5. 保证增加的装备和药水瓶原本不存在
6. 操作数满足1≤*n*≤2000

### ArrayList,HashMap与容器

​	**容器是一种用于存储和管理数据的类或接口的集合，最常用的容器包括集合框架与映射框架**

集合框架：

* List:用于存储有序的元素集合，例如ArrayList与LinkedList
* Set:用于存储独一无二的元素集合，例如HashSet与TreeSET
* Queue:用于存储按照特定顺序进行哈如何访问的元素集合

 映射框架：

* Map:用于存储<键-值>对的集合，其中每个键都是唯一的，例如HashMap和TreeMap

#### ArrayList

ArrayList是一个可以动态修改的数组，但是他没有固定大小的限制，其中数组下标即为存入顺序。

```java
//ArrayList类位于java.util包中，使用前需要进行引入
import java.util.ArrayList;

public class ArrayListSample
{
    public void sample()//创建ArrayList 
    {
        ArrayList<Bottles> bottles = new ArrayList<>();
      //ArrayList<className> ArrayName = new ArrayList<>();
      //可以看出数组中的元素为类的实例化对象
        Bottle bottle1 = new Bottle(/*parameters*/);
        Bottle bottle2 = new Bottle(/*parameters*/);
       //增加一个元素 数组名.add(对象名)
        bottles.add(bottle1);
        bottles.add(bottle2);
        //访问数组中下标为i的元素 ArrayName.get(i)
        Bottle bottle = bottles.get(0);//取出第一个元素
       //判断元素是否在容器中
        if(bottles.contains(bottle)){
            System.out.println("yep");
        }
        //数组大小
        int size = bottles.size();
        //遍历元素
        for(Bottle item : bottles)
        {
            System.out.println(item.getName());
        }
        //或者是
        for(int i=0;i<bottles.size();i++)
        {
            System.out.println(bottles.get(i).getName());
        }
        
        //删除元素
        bottles.remove(bottle1);//对象名
        bottles.remove(0);//按照下标
    }
    
}

```

#### HashMap

**散列表不会记录存入的顺序，存储内容是键值对(key-value)的映射**

```java
import java.util.HashMap;

public class HasnMapSample
{
    public void sample()
    {
        //创建散列表 bottle.id -> bottle
        HashMap<Integer,Bottle> bottles = new HashMap<>();
        Bottle bottle1 = new Bottle(/*parameters*/);
        Bottle bottle2 = new Bottle(/*parameters*/);
        //散列表中加入元素 mapname.put(key,value)
        bottles.put(12345,botttle1);
        bottles.put(bottle2.getID(),bottle2);
        //访问key值对应的value
        Bottle bottle = bottles.get(12345);
        //检查是否存在指定的key对应的映射关系
        if(bottles.containsKey(12345))
        {
            System.out.println("yep");
        }
        //检测是否存在指定的value对应的映射关系
        if(bottles.containsValue(bottle2))
        {
            System.out.println("yep");
        }
        //散列表大小
        int size = bottles.size();
        
        //遍历所有元素
        for(int key : bottles.keySet())
        {
            System.out.println(bottles.get(key).getName());
        }
        for(Bottle value : bottles.values())
        {
            System.out.println(value.getName());
        }
        
        //删除一个映射关系 name.remove(key)
        // bottles.containsKey(12345) == true
        bottles.remove(12345);
        // bottles.containsKey(12345) == false
        
        //删除一个键值对
        bottles.remove(bottle2.getID(),bottle2);
    }
}
```

### 代码实现

#### main.java

```java
import java.util.Scanner;
import java.util.HashMap;

public class Main {
    public static void main(String [] args) {
        Scanner scanner = new Scanner(System.in); //读取指令条数
        int opCount = Integer.parseInt(scanner.nextLine());
        HashMap<Integer,Adventure> adventurers = new HashMap<>(); //构造id与对应adventure的映射
        for (int i = 0;i < opCount;i++) {
            String opLine = scanner.nextLine();//整行读取字符串
            Scanner opLineScannner = new Scanner(opLine);
            int op = opLineScannner.nextInt();//nextInt()方法读取字符串中第一个整数
            int id = opLineScannner.nextInt();//读取第二个整数
            if (op == 1) { //增加冒险者
                String name = opLineScannner.next();//读取名字
                Adventure adventurer = new Adventure(id,name);
                adventurers.put(id,adventurer);
            } else if (op == 2) { //增加药水瓶
                int botId = opLineScannner.nextInt();
                String botName = opLineScannner.next();
                int botCapacity = opLineScannner.nextInt();
                Bottle bottle = new Bottle(botId,botName,botCapacity);
                Adventure man = adventurers.get(id);
                man.addBottle(bottle);
            } else if (op == 3) { //删除药水瓶
                int botid = opLineScannner.nextInt();
                Adventure man = adventurers.get(id);
                Bottle bottle = man.getBottle(botid);
                String bottlename = bottle.getName(bottle);
                man.removeBottle(bottle);
                int bottlesnum = man.BottlesNumber(man);
                System.out.println(bottlesnum + " " + bottlename);
            } else if (op == 4) { //增加装备
                int equId = opLineScannner.nextInt();
                String equName = opLineScannner.next();
                int equStar = opLineScannner.nextInt();
                Adventure man = adventurers.get(id);
                Equipment equipment = new Equipment(equId,equName,equStar);
                man.addEquipment(equipment);
            } else if (op == 5) { //删除装备
                int equId = opLineScannner.nextInt();
                Adventure man = adventurers.get(id);
                Equipment equipment = man.getEquipment(equId);
                String equipmentName = equipment.getName(equipment);
                man.removeEquipment(equipment);
                int equipmentNumber = man.EquipmentNumber(man);
                System.out.println(equipmentNumber + " " + equipmentName);
            } else if (op == 6) { //装备升级
                int equId = opLineScannner.nextInt();
                Adventure man = adventurers.get(id);
                Equipment equipment = man.getEquipment(equId);
                equipment.setStar(equipment.getStar() + 1);
                String equipmentName = equipment.getName(equipment);
                int star = equipment.getStar();
                System.out.println(equipmentName + " " + star);
            }
        }
    }
}

```

#### Adventure.java

```java
import java.util.ArrayList;

public class Adventure {
    private int id;
    private String name;
    private ArrayList<Bottle> bottles;
    private ArrayList<Equipment> equipments;

    public Adventure(int id,String name)//构造方法
    {
        this.id = id;
        this.name = name;
        this.bottles = new ArrayList<>();
        this.equipments = new ArrayList<>();
    }

    public void addBottle(Bottle bottle)
    {
        bottles.add(bottle);
    }

    public Bottle getBottle(int id)
    {
        for (Bottle item : bottles) {
            if (item.getID(item) == id)
            {
                return item;
            }
        }
        return null;
    }

    public void removeBottle(Bottle bottle)
    {
        bottles.remove(bottle);
    }

    public void addEquipment(Equipment equipment)
    {
        equipments.add(equipment);
    }

    public void removeEquipment(Equipment equipment)
    {
        equipments.remove(equipment);
    }

    public void addStar(Equipment equipment)
    {
        if (equipment != null) {
            equipment.setStar(equipment.getStar() + 1);
        }
    }

    public int BottlesNumber(Adventure adventure)
    {
        return adventure.bottles.size();
    }

    public Equipment getEquipment(int id)
    {
        for (Equipment item : equipments)
        {
            if (item.getID(item) == id)
            {
                return item;
            }
        }
        return  null;
    }

    public int EquipmentNumber(Adventure adventure)
    {
        return adventure.equipments.size();
    }
}
```

#### Bottle.java

```java
public class Bottle {
    private int id;
    private String name;
    private int capacity;

    public Bottle(int id,String name,int capacity)//构造方法
    {
        this.id = id;
        this.name = name;
        this.capacity = capacity;
    }

    public int getID(Bottle bottle)
    {
        return bottle.id;
    }

    public String getName(Bottle bottle)
    {
        return bottle.name;
    }
}
```

#### Equipment.class

```java
public class Equipment {
    private int id;
    private String name;
    private int star;

    public Equipment(int id,String name,int star)//构造方法
    {
        this.id = id;
        this.name = name;
        this.star = star;
    }

    public int getStar()
    {
        return star;
    }

    public void setStar(int star)
    {
        this.star = star;
    }

    public int getID(Equipment equipment)
    {
        return equipment.id;
    }

    public String getName(Equipment equipment)
    {
        return equipment.name;
    }
}
```









 