---
layout: posts
title: 管程实现读者优先and写者优先
date: 2022-10-28 19:56:52
description: "这是文章开头，显示在主页面，详情请点击此处。"
categories: 
- "操作系统学习"
tags:
- "管程"
- "读者优先"
- "写者优先"
---


前提条件 <!--more-->

1. 允许多个读者可以同时对文件执行读操作；
2. 只允许一个写者往文件中写信息；
3. 任一写者在完成写操作之前不允许其他读者或写者工作；

### 信号量的实现

```c
class Semaphore{
	int sem
  WaitQueue q 
}
Semaphore::P(){
	sem--
  if (sem < 0 ){
    Add this thread t to q
    block(t)
  }
}
Semaphore::V(){
  sem++
  if (sem <= 0 ){
    Remove a thread t from q
    wakeup(t)
  }
}
```



# 《读者优先》读者可以插队

- `semaphore rw = 1;`　　　 实现读、写进程对文件的**互斥**访问
- `semaphore mutex = 1;`　　 实现**互斥**修改变量
- `int readcnt = 0;`　　　　 记录当前有几个读者在读

### 写者进程

```c
while(true):
	P(rw) // 写之前占用临界区资源，实现写操作的互斥。
    /** 临界区写文件 **/
  V(rw) // 写完后释放临界区资源

```

### 读者进程

```c
while(true):
	P(mutex)
    	if(readcnt==0) // 第一个读者进程负责占用临界区资源，第一个到最后一个读操作被框定在一个锁的空间内，可以实现多个读者一起执行。
            P(rw)
      readcnt+=1 // 访问临界区的读者进程数+1
  V(mutex)
    /** 临界区读文件 **/  //注意这里读操作是不互斥的，可以多个读者一起执行。/
	P(mutex)
      readcnt-=1 // 访问临界区的读者进程数-1
    	if(readcnt==0) // 最后一个读者进程负责释放临界区资源
            V(rw)
  V(mutex)

```

分析：

第一个读者进程 就锁住了 之后所有的写操作，直到最后一个读者进程执行完毕才释放这个锁rw，此时才能进行写的操作。



# 《读写公平》

semaphore rw = 1;　　实现读、写进程对文件的互斥访问
semaphore w = 1;　　 实现写进程之间对文件的互斥访问
semaphore mutex1 = 1, mutex2 = 1;　实现互斥修改变量
int readcnt = 0, writecnt = 0;　 分别记录当前有几个读、写进程想访问临界区

### 写者进程

```c
while(true):
    P(mutex1)
        if(writecnt==0) // 第一个写者进程负责占用临界区资源
            P(rw)
        writecnt+=1 // 访问临界区的写者进程数+1
    V(mutex1)
    P(w) // 写之前占用临界区资源，实现写操作的互斥。
    /** 临界区写文件 **/
    V(w) // 写完后释放临界区资源
    P(mutex1)
        writecnt-=1 // 访问临界区的写者进程数-1
        if(writecnt==0) // 最后一个写者进程负责释放临界区资源
            V(rw)
    V(mutex1) 

```

### 读者进程

```c
while(true):
    P(mutex2)
        if(readcnt==0) // 第一个读者进程负责占用临界区资源
            P(rw)
        readcnt+=1 // 访问临界区的读者进程数+1
    V(mutex2)
    /** 临界区读文件 **/
    P(mutex2)
        readcnt-=1 // 访问临界区的读者进程数-1
        if(readcnt==0) // 最后一个读者进程负责释放临界区资源
            V(rw)
    V(mutex2)

```

分析：

当写者拿到rw锁时，后来的写者都可以直接等待在P(w)处（在临界区外排队等待进入），而后来的读者中有一个会阻塞在P(rw)，剩余的阻塞在P(mutex2)，这样一来读者也需要等待多个写者了，而方案二中读者最多只需要等一个写者。

这时的情况像是读、写进程是两个派别，谁先第一个拿到rw锁，其派别的 “兄弟们” 都能跟着他一起上。没拿到锁的一方只能等对方所有人完成后才能执行。

因此有多个读者访问临界区时，写者也只能等待，而我们希望的是写者能够抢占读者进程，不需要等后来的读者。所以这种方案也只能说是再一步提高了写进程的优先级。


# 《写者优先》写者可以插队

思路：

在保证写者互斥、读写互斥、可多个读者同时执行的前提下，额外加一个变量记录执行中写者的数量，再加一个信号量实现只要【读读写写读读读写写读】中有写者还没执行完，读者就要等待。

semaphore rw = 1;　 　                         实现对文件的互斥访问
semaphore mutex = 1, mutex1 = 1, mutex2 = 1;　实现互斥修改变量
semaphore mutex3 = 1;　　　　　保证只有一个写者，读者进程会被卡在 P( read )
semaphore read = 0;　 　　　　　 　 同步锁：读进程必须在没有写进程时才能访问临界区
int readcnt = 0, writecnt = 0;　　 分别记录当前有几个读进程在读、有几个写进程想写
bool waiting = false;　　　　　 　 表示当有写进程在写文件时，有没有读进程来到

### 写者进程

```c
while(true):
    P(mutex)
        writecnt+=1 // 只要有读进程来了，writecnt+1
    V(mutex)
    P(rw) // 写之前占用临界区资源
    /** 临界区进行写 **/
    V(rw) // 写完后释放临界区资源
    P(mutex1)
        writecnt-=1 // 写完了writecnt-1
        // 没有写者想写 且 有读者正阻塞在P(read) 就释放read让读者读文件
        if(writecnt==0 && waiting):
            V(read)           
            waiting = false   // 考虑到第二批要进入临界区的【读写读写读读】，要及时复原。
            // 如果没有waiting，可能导致没有读者时写者会V(read)导致read资源变多
    V(mutex1)

```

### 读者进行

```c
 while(true):
    P(mutex3)  // 如果没有mutex3，可能造成多个读进程阻塞在P(read)，而p(read)的锁需要 写者 的v(read)来释放，可能多个阻塞在 P(read)的读操作，只被写者释放了一次。 但是有了 P(mutex3) 就可以把这个主动权抓在 读者手中了，只要写着释放完一次 锁，读者可以自发 释放后续的锁。
        if(writecnt!=0): // 一旦有写者来了，后来的读者就要等待写者写完
            waiting = true // 表明现在有读者在等写者
            P(read) // 要等写者写完才可以继续, 注意：这里read初始值为0，第一个读就是p(0)，直接给锁了。
    V(mutex3)
    P(mutex2)
        if(readcnt==0): // 第一个读者进程负责占用临界区资源
            p(rw)
        readcnt+=1
    V(mutex2)
    /** 临界区进行读 **/
    P(mutex2)
        readcnt-=1
        if(readcnt==0): // 最后一个读者进程负责释放临界区资源
            V(rw)
    V(mutex2)

```

分析：

读者1 → 写者 → 读者2：假设读者1占有rw资源，写者来到阻塞在P(rw)处，此时 writecnt已经不是 0。所以读者2 再来时会被阻塞在P(read)处。当读者1 完成读操作释放rw锁，然后写者立即会占有rw，此时一直有写者来的话都是可以直接在临界区外排队的。等所有写操作完成了，writecnt 就变为 0，读者才能获得read锁进行读。

在读者先于写者来到的情况下，写者总是会获得rw锁，因为当有写者在等待rw时，其它后来的读者中的第一个会阻塞在P(read)，剩余的阻塞在P(mutex3)。而前面已经在临界区的读者，写完后就会有readcnt==0然后释放rw锁，写者立刻就可以访问临界区了。但是在上一个方案的话，因为后来的读者不会进行 “检查”，只要它们中有一个读者持有rw锁，即使已经有写者正在等待，读者们依然可以直接进入临界区，但在本方案中却会被阻塞在P(mutex3)或P(read)。

```c
//（自己重写一下）
//记录写者数量，当写者还有的时候，读者就得等着。读者的钥匙要拿在写者手中。写者全部完成后才会给读者们释放锁，但是要注意不能让读者全部阻塞在一个锁上，因为写者只释放一次，所以为了避免多个阻塞进程和一个释放操作，我们引入信号量mutex3来让 原阻塞只阻塞一个进程，后续的阻塞由读者自行完成，也由读者自行释放。这样在写者全部执行完后，多个阻塞的读者能不依赖写者的释放锁 全部执行完。
//另外注意一下，read == 0 这个信号量的初始值是0，第一个进来p-- 就直接被锁住了，不同于 平时初始值是 1的信号量，第二个进入的进程才被锁住。

//信号量read == 0 ;readcnt ==0;writecnt==0;信号量(mutex.)初始值均为1;wait==false;
// 修改的变量只要不同，就要用不同的信号量来锁住修改变量的操作。

//读者
while(1):
	P(mutex3)
    if (writecnt != 0):
      wait = true
      P(read)
  V(mutex3)
      
  P(mutex4)
  	if (readcnt == 0):
      P(rw)
    readcnt++
  V(mutex4)
  	//** 临界区 **//
  P(mutex4)
    readcnt--
  	if (readcnt == 0):
      V(rw)
  V(mutex4)
      
//写者
while(1):
	P(mutex1)
    writecnt++
  V(mutex1)
  P(rw)
    //** 临界区 **//
  V(rw)
  P(mutex2)
    writecnt--
    if (writecnt==0&&wait==true):  //没有wait 会让写者结尾的情况，无意义释放一个V(read)锁。
      V(read)
      wait = false     //下一轮时复原状态。
  V(mutex2)
```











参考原文：https://blog.csdn.net/qq_43280818/article/details/105788767