---
title: 'git指令的初步学习'
date: '2023-9-10 23:24'
updated:
type:
comments: 
description: '初学版本管理 谁还没有被git折磨过'
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
###                                                          面向OO的git指令

​		**在秋季学期开设的OOpre课程中，我首次接触到使用Gitlab对代码进行版本管理，在第一次OOpre作业中我就受到了提交库的拷打，现在是2023年9月10日晚上22:46,开始编写面向OO的git指令**

0. **windows命令行操作经典指令**

   1. cd:change dictionary
      * cd D:    #只有转移磁盘需要加冒号:
      * cd OO #进一步转移到目录下文件夹
      * cd ..     #回退一层目录

   ​     2.pwd:print working dictionary 打印当前工作目录

   ​     3. git 指令 git是一个终端

1. 提交注意

   ​     在每一次作业的发布中，分为作业发布区和个人仓库区，常见的操作是从作业发布区进行repository的clone,在本地完成作业后push到个人仓库区，需要注意的是公共发布区是没有push权限的，下面以第一次作业为例

2. **完成作业的常见步骤**

   1. **从公共发布区clone**

      ​		**需要注意的是这里的每一个步骤都可以通过IDEA编译器操作或是通过git终端操作，这里我们主要介绍通过git的操作方式(我更加喜欢)。**

      ​       进行源的clone,通过以下代码实现

      ```c
      git clone url path
      ```

      ​       其中url代表远程仓库的地址如SSH密钥，path代表路径，绝对路径或相对路径均可以，如果指定的路径不存在则会创建该目录，笔者经过验证，在路径不存在的情况下，确实会进行生成。

 **需要注意的是在clone后已经存在.git文件**

   2. 对代码进行修改后的一系列

      1. **常用指令git init**

         git init指令用于生成本地的.git跟踪版本文件，是万恶的开端

      2. **git status**

         git status用于查看当前文件的情况，是否被跟踪(tracked 用于代码管理)，是否有修改等等

      3. **git add**

         git add 指令是很常用的操作，用于将文件添加到tracked,常见的有这几种用法

         ```c
         //将.git目录下全体文件加入tracked
         git add .
         //添加单个文件进行版本管理
         git add <filename>//其中如果filename中包含空格的话需要对文件名进行双引号处理 "filename"
         ```

         通常对文件进行修改后或新建文件后需要进行git add 这时可以通过git status 进行文件状态的查询

 这时可以通过git add 对改变的文件进行track。

3. **git commit**

   git commit用于将代码提交到本地代码库，常用的命令行有:

   ```c
   git commit -m  "message"  //参数-m代表提交的同时进行消息说明 在"message"中写出对于此次提交的注释，便于以后git log查看
   git commit -am "message"  //与之前相比多了参数-a 这表示进行了add操作，这一条指令就相当于git add+commit
   ```

   4. **git log**

      ​		git log可以查看commit记录，查看每一次commit会发现每一次commit都对应着一个编号，这个编号用于版本的回退，后面会提及。


5. **git push**

   git push是第一次需要联网进行的操作，用于将代码推送到远端服务器,在push之前可以查看已存在的SSH路径。

   ```c
   git remote//显示出所有SSH 
   git remote -v//显示出当前使用SSH的地址
   ```


可以发现我们当前已经存在命名过的SSH origin，并且知晓了他的地址。也就是说我们当前进行的push操作是将代码推送到origin对应的远程仓库，但是我们知道，此时进行push会push到课程公共发布区，而这是不被允许且没有权限的。这时就涉及到新建推送地址SSH(个人代码库)

```c
git remote add <name> <url>//其中<name>的名字应当与想要推送到的仓库一致，不可以乱起名，url为对应仓库的SSH
git remote remove <name>//删除SSH源，比如这里当我们只需要提交代码时就可以删除公共发布区的SSH
```

这时，我们距离push还有很多的细节，

* 课程组提供的个人代码库默认为master分支，需要观察我们的代码分支是否为master,这一点在命令行的蓝色部分有显示

  若我们的代码在master分支，则无许进行修改，否则需要进行分支的修改，因为我们的提交是需要分支对应的，我们本地库的master分支对应提交到远程代码库的master分支。具体步骤如下:

  ```c
  git branch//查看现有的分支 在测验环境下只有main
  git branch <newname>//新建一个分支，这里我们一般是将name设置为master,顺应作业提交规则
  git branch master
  //这时就可以进行代码分支的切换
  git checkout master//切换到master分支
  ```



* 检查目标库中是否有本地没有的文件

  很多代码托管平台如github,gitee在创建仓库的时候会询问是否添加README.md文档(在OO课程组提供的个人代码库中初始时为空的)，这时如果push会进行报错，因为远程仓库中包含本地不存在的工作(README.md),有几种解决方法

  ```c
  //1.同步
  git pull origin master
  git add .
  git commit
  git push origin master
  //2.如果只是因为README.md原因，可以选择在本地生成
  git pull --rebase origin master
  git push origin master
   //强推，用本地代码强制覆盖git仓库内内容，在仓库为空时可以使用
  git push -f origin master//-f force
  ```

  **git push**是我们上传作业最重要的工作，我们应当使用以下代码

  ```c
  git push <sshname> <branch>//仓库名+分支名
  //例如
  git push homework_1 master
  //需要注意的是 这里的branch如果原仓库中没有，此行指令会在仓库中新建branch并将代码提交到branch当中
  ```

  希望我们都可以顺利PUSH!!!

6. **git revert**

   版本回退是一个非常好用的功能，可以调出前几次的commit.这里我们需要先使用git log查看git commit记录,在提交记录中有提交的HASH值和指定标签,这种方式是进行一个新的提交来表示撤销操作，而不会抹除历史记录。需要使用git push重新推送到远程仓库中。

   ```c
   git revert <commit-hash>//回退到指定hash
   git revert <tag-name>//回退到指定标签
   git revert HEAD~n//回退到前N个提交
   ```



7. **还有一些版本管理上的内容需要补充**