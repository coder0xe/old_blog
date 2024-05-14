---
title: OS第四次理论作业
date: 2024-05-06 15:54:56
type:
comments: 
description: 'OS第四次理论作业'
keywords: ‘homework4'
mathjax:
katex:
aside:
aplayer:
categories: "操作系统"
highlight_shrink:
random:
tags: "May"
---

# OS第四次理论作业

### 1.读者写者问题（写者优先）: 1）共享读; 2）互斥写、读写互斥; 3）写者优先于读者（一 旦有写者，则后续读者必须等待，唤醒时优先考虑写者）。

```c
int readcount = 0;
int writecount = 0;
semaphore mutex = 1;//信号量为1相当于互斥锁
semaphore write = 1;
semaphore read = 1;
semaphore r_wait = 1;

writer() {
    P(write);   // 保护 writecount
    writecount++;
    if(writecount == 1) {
        P(r_wait);      // 有写入的，暂停读 
    }
    V(write);

    P(mutex);   // 互斥写 
    write_data();
    V(mutex);

    P(write);
    writecount--;
    if(writecount == 0) {   // 没有写入的，恢复读 
        V(r_wait);
    }
    V(write);
}

reader() {
    P(r_wait);  // 如果有写入的或等待写入的，暂停读，实现写优先于读
    P(read);    // 保护 readcount
    if(readcount == 0) {
        P(mutex);   // 第一个读的，加锁 
    }
    readcount++;
    V(read);
    V(r_wait);

    read_data();

    P(read);
    readcount--;
    if (readcount == 0) {   // 最后一个读的，解锁 
        V(mutex);
    }
    V(read);
}
```

### 2.寿司店问题。假设一个寿司店有 5 个座位，如果你到达的时候有一个空座位，你可以立 刻就坐。但是如果你到达的时候 5 个座位都是满的有人已经就坐，这就意味着这些人都是一 起来吃饭的，那么你需要等待所有的人一起离开才能就坐。编写同步原语，实现这个场景的约束。

```c
int eating = 0, waiting = 0;
Semaphore mutex = 1, queue = 1;
bool must_wait = false;

Customer(){
    P(mutex);
    if (must_wait){
        waiting++;
        V(mutex); // 对 waiting 变量的保护可以释放 
        P(queue); // 被阻塞，坐着等待排队，等待被唤醒 
    }
    else {
        eating++;
        must_wait = (eating == 5);
        V(mutex); // 对 eating 变量的保护可以释放 
    }
    // 上一部分已经解决了进店后是等待还是吃的问题 
    Eat_sushi();// else 的人和被唤醒的排队者成功进入这一步 
    P(mutex);   // 开启对 eating, waiting 变量保护 
    eating--;  // 吃的人 -1, 如果 5 个没全吃完，不可以换下一批人吃 
    if (eating == 0){ // 最后一个吃完的人离开才可以进顾客 
        int n = min(5, waiting); // 放顾客进来的数量，不超过 5 个 
        waiting -= n;
        eating +=n;
        must_wait = (eating == 5);
        for(int i = 0; i<n; i++){
            V(queue);  // 唤醒排队的 n 个人继续进程 
        }
        V(mutex); // 允许下一个吃完的人对变量和队列进行操作 
    }
}
```

### 3.进门问题。（1）请给出P、V操作和信号量的物理意义。（2）一个软件公司有5名员工， 每人刷卡上班。员工刷卡后需要等待，直到所有员工都刷卡后才能进入公司。为了避免拥挤， 公司要求员工一个一个通过大门。所有员工都进入后，最后进入的员工负责关门。请用 P、 V 操作实现员工之间的同步关系

* P操作：请求一个资源，V操作：释放一个资源

* 第二问

  ```c
   int N;
   Semaphore empty = N;  // 假设初始条件下缓冲区有 N 个空位 
   Semaphore mutex = 1;
   Semaphore odd = 0;
   Semaphore even = 0;
  
   void P1(){
       int integer;
       while(true){
           integer = produce();  // 生成一个整数 
           P(empty);       // 若 empty 为 0 则会被阻塞（等待别人拿走）
           P(mutex);       // 开始互斥，保护缓冲区 
           put();          // 放入缓冲区 
           V(mutex);       // 访问临界区结束 
           if(integer %2 == 0){
               V(even);      // 是偶数 
           } else {
               V(odd);       // 是奇数 
           }
       }
   }
  
   void P2(){
       while(true){
           P(odd);       // 请求一个奇数 
           P(mutex);      // 互斥 
           getodd();
           V(mutex);
           V(empty);      // 缓冲区多一个位置 
           countodd();
       }
   }
  
   void P3(){
       while(true){
           P(even);      // 请求一个偶数 
           P(mutex);      // 互斥 
           geteven();
           V(mutex);
           V(empty);      // 缓冲区多一个位置  
           counteven();
       }
  }
  ```

### 4.搜索-插入-删除问题。三个线程对一个单链表进行并发的访问，分别进行搜索、插入和删 除。搜索线程仅仅读取链表，因此多个搜索线程可以并发。插入线程把数据项插入到链表最 后的位置；多个插入线程必须互斥防止同时执行插入操作。但是，一个插入线程可以和多个 搜索线程并发执行。最后，删除线程可以从链表中任何一个位置删除数据。一次只能有一个 删除线程执行；删除线程之间，删除线程和搜索线程，删除线程和插入线程都不能同时执行。  请编写三类线程的同步互斥代码，描述这种三路的分类互斥问题。

```c
Semaphore insertMutex =1, searchMutex = 1; // 保护 int 变量 
Semaphore No_search = 1; // 顾名思义，为 1 时没有搜索进程访问 
Semaphore No_insert = 1; // 为 1 时没有插入进程访问 
// 当上述两个信号量同时为 1，删除者才可以进行删除操作 

int searcher = 0, inserter = 0;
void Search(){
    P(searchMutex);     // 保护 searcher
        searcher++;
        if (searcher == 1) // 第一个进来的搜索者加锁 
        P(No_search)
    V(searchMutex);
    Searching();
    P(searchMutex);
    searcher--;
    if (searcher == 0)
        V(No_search);  // 表示此时没有搜索线程在进行，解锁 
    V(searchMutex);
}

void Insert(){
    P(insertMutex);
    inserter++;
    if (inserter == 1)
        P(No_insert)
    V(insertMutex);
    
    P(insertMutex); // 既然可以和搜索线程并行，那么不用管 Searcher
    Inserting(); // 访问临界区，多个插入者要互斥访问，一次一个 insert
    V(insertMutex);
    
    P(insertMutex);
    inserter--;
    if (inserter == 0)
        V(No_insert); // 解锁，可唤醒删除者 
    V(insertMutex);
}

void Delete(){   // 删除线程与其他任何线程互斥 
    P(No_search);
    P(No_insert); // 若为 1 则可进入，这个信号量顺便也可以当作删除者的互斥保护 
    Deleting(); // 搜索和插入线程都没，成功进入临界区 
    V(No_insert);
    V(No_search); 
}
```

