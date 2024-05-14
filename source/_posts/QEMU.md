---
title: QEMU
date: 2024-03-01 14:26:15
updated:
type:
comments: 
description: 'OS模拟器'
keywords: 'QEMU模拟器'
mathjax:
katex:
aside:
aplayer:
categories: "操作系统"
highlight_shrink:
random:
tags: "Feb"
---

# <center>QEMU</center>

​	为了开发和运行我们的MOS操作系统，必须要有配套的支持操作系统运行的硬件系统，我们使用硬件模拟器实现。模拟器能够模拟计算机硬件的行为和特性。我们使用QEMU(```quick enulator```)

## 1. QEMU的使用

* 实验中已经将所有需要用到的QEMU操作写到了```Makefile```中

* 我们的实验基于MIPS架构

​	使用QEMU提供的MIPS环境编译运行代码的指令

```
// minimal_hello_world.c
void printch(char ch) { *((volatile char *)(0xB80003f8U)) = ch; }

void print(char *str) {
    while (*str != '\0') {
        printch(*str);
        str++;
    }
}

void __start() {
    print("Hello, world!\n");
    while (1) {
    }
}
```

* **编译** 编译需要使用交叉编译器```mips-linux-gnu-gcc```

  ```
  $ mips-linux-gnu-gcc \
          -EL \
          -nostdlib \
          -o hello_world.elf \
          minimal_hello_world.c
  ```

  ​	生成目标文件为```hello_world.elf```

* **运行**

  * 所有的QEMU指令都是```qemu-```的形式，对于某一体系架构下的模拟，使用

    ```
    qemu-system-*
    ```

    对于小端序的mips架构，对应命令为

    ```
    qemu-system-mipsel
    ```

  * 运行以上示例代码

    ```
    $ qemu-system-mipsel \
            -m 64 \
            -nographic \
            -M malta \
            -no-reboot \
            -kernel hello_world.elf
    Hello, world!
    ```

    * ```qemu-system-mipsel```：指定小端序mips架构
    * ```-nographic```模拟中不使用图形界面，使用串口输出
    * ```-M``` 制定模拟的目标机器，这里模拟的是```MIPS melta```开发板
    * ```-no-reboot```虚拟机直接退出而不是重启
    * ```-kernel```指定要启动的内核

* 退出QEMU

  * ```ctrl + A```+```x```

## 2. 在QEMU中使用GDB调试

* QEMU原生支持GDB调试

* 编译指令中添加```-g```选项生成可供调试的```debug```版本

  ```
  $ mips-linux-gnu-gcc \
          -g \
          -EL \
          -nostdlib \
          -o hello_world.elf \
          minimal_hello_world.c
  ```

* 运行指令

  ```
  $ qemu-system-mipsel \
          -s \
          -S \
          -m 64 \
          -nographic \
          -M malta \
          -no-reboot \
          -kernel hello_world.elf \
          < /dev/null \
          &
  ```

  * ```-S```让模拟器不要再一开始就启动处理器
  * ```-s```**等待GDB连接到1234端口**（GDB 和 QEMU 的协作是通过远程连接进行）
  * 我们要在同一个终端中运行 QEMU 和 GDB。所以 QEMU 需要在**后台运行**。这时我们必须将其标准输入重定向为 `/dev/null`

* ```ps```指令查看当前终端下运行的进程

* **为了适应我们的MIPS架构，需要使用```gdb-multiarch```进行调试**

* 进入```gdb-multiarch```后进行架构设置

  ```
  (gdb) set architecture mips
  ```

  (set architecture + ```tab```可以查看所有架构)

* 连接QEMU的1234端口

  ```
  (gdb) target remote localhost:1234
  ```

* QEMU不支持```run```或```start```

* 退出前杀死QEMU进程（防止占用1234端口）

  * ps查看所有进程，或配合使用grep正则匹配指令

    ```
    ps | grep qemu
    ```

  * kill杀死进程

    ```
    kill -9 <id>
    ```

  * 或者直接

    ```
    pkill -9 qemu
    ```

    
