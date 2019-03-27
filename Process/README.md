[toc]
# 进程相关

## 原子操作

>"原子操作(atomic operation)是不需要synchronized"，这是多线程编程的老生常谈了。所谓原子操作是指不会被线程调度机制打断的操作；这种操作一旦开始，就一直运行到结束，中间不会有任何 context switch （切换到另一个线程）

原子操作是不可分割的，在执行完毕之前不会被任何其它任务或事件中断

## 进程

## 进程间通信（Linux）
[参考原文](http://www.cnblogs.com/CheeseZH/p/5264465.html)
### 1.管道（无名管道）

+ 数据只能在`一个方向`上流动，有固定的读端和写端
+ 只能用于具有`亲缘关系`的进程之间的通信（父子进程或者兄弟进程之间）
+ 可以看成是一种`特殊的文件`，对于它的读写也可以使用普通的read、write 等函数；但是它不是普通的文件，并不属于其他任何文件系统，并且只存在于内存中

当一个管道建立时，它会创建两个文件描述符：fd[0]为读而打开，fd[1]为写而打开，如下图：

![6bae19b8fce7aab37da98861eb63b687.png](en-resource://database/6407:0)

原型：
```c
1 #include <unistd.h>
2 int pipe(int fd[2]);    // 返回值：若成功返回0，失败返回-1
```

若要数据流从父进程流向子进程，则关闭父进程的读端（fd[0]）与子进程的写端（fd[1]）；反之，则可以使数据流从子进程流向父进程

![dfe753633d96a69e54eed43eb14f79c0.png](en-resource://database/6411:0)

> 两组单行不就和串口通信的RX、TX单行线一样啦


用一组管道也可以实现半双工：

![6bae19b8fce7aab37da98861eb63b687.png](en-resource://database/6407:1)


### 2.FIFO（命名管道）

+ FIFO可以在`无关的`进程之间交换数据，与无名管道不同
+ FIFO有`路径名`与之相关联，`它以一种特殊设备文件形式存在于文件系统中`

FIFO的通信方式类似于在进程中`使用文件来传输数据`，只不过FIFO类型文件同时具有管道的特性。在数据读出时，FIFO管道中同时清除数据，并且“先进先出”

![84e2fbd3f4cd7745eed0b7be08093807.png](en-resource://database/6403:0)

原型：
```c
1 #include <sys/stat.h>
2 // 返回值：成功返回0，出错返回-1
3 int mkfifo(const char *pathname, mode_t mode);
```

> 管道形式的”文件“，用路径名找到它，大家都能读写

### 3.消息队列

+ 消息队列，是消息的链接表，存放在内核中；一个消息队列由一个标识符（即`队列ID`）来标识
+ 面向记录，其中的消息具有`特定的格式`以及`特定的优先级`
+ `消息队列独立于发送与接收进程；进程终止时，消息队列及其内容并不会被删除`
+ 可以实现消息的`随机查询`，消息不一定要以先进先出的次序读取,也`可以按消息的类型读取`

原型：
```c
1 #include <sys/msg.h>
2 // 创建或打开消息队列：成功返回队列ID，失败返回-1
3 int msgget(key_t key, int flag);
4 // 添加消息：成功返回0，失败返回-1
5 int msgsnd(int msqid, const void *ptr, size_t size, int flag);
6 // 读取消息：成功返回消息数据的长度，失败返回-1
7 int msgrcv(int msqid, void *ptr, size_t size, long type,int flag);
8 // 控制消息队列：成功返回0，失败返回-1
9 int msgctl(int msqid, int cmd, struct msqid_ds *buf);
```

> 公用，数据存在内核中，可以按消息的type值查询需要的内容

### 4.信号量

信号量（semaphore）是一个计数器，`用于实现进程间的互斥与同步，而不是用于存储进程间通信数据`


+ 信号量`用于进程间同步`，若要在进程间传递数据需要结合共享内存
+ 信号量基于操作系统的 PV 操作，程序对信号量的操作都是`原子操作`
+ 每次对信号量的 PV 操作不仅限于对信号量值加 1 或减 1，而且可以加减任意正整数
+ 支持信号量组

**二值信号量（Binary Semaphore）**：最简单的信号量，只能取 0 和 1 的变量，是信号量最常见的一种形式
**通用信号量**：可以取多个正整数的信号量

Linux 下的信号量函数都是在`通用信号量`数组上进行操作，而不是在一个单一的二值信号量上进行操作

原型：
```c
1 #include <sys/sem.h>
2 // 创建或获取一个信号量组：若成功返回信号量集ID，失败返回-1
3 int semget(key_t key, int num_sems, int sem_flags);
4 // 对信号量组进行操作，改变信号量的值（通过simbuf）：成功返回0，失败返回-1
5 int semop(int semid, struct sembuf semoparray[], size_t numops);  
6 // 控制信号量的相关信息
7 int semctl(int semid, int sem_num, int cmd, ...);
```

如果不删除信号量，它将继续在系统中存在，即使程序已经退出，它可能在你下次运行此程序时引发问题，而且信号量是一种有限的资源

> sem_flg 没有指定IPC_NOWAIT时，二值信号量和mutex的lock和unlock作用一样呀：信号量=0占用，信号量=1可用；P操作：信号量不为0时-1加锁，为0时等待，V操作：信号量+1解锁；比如：
```
P（lock）
....执行代码
V（unlock）
```
### 5.共享内存

共享内存（Shared Memory）指两个或多个进程共享一个给定的存储区

+ 共享内存是`最快`的一种 IPC，因为进程是直接对内存进行存取
+ 因为多个进程可以同时操作，所以`需要进行同步`
+ `信号量+共享内存通常结合在一起使用`，信号量用来同步对共享内存的访问

原型：

```c
1 #include <sys/shm.h>
2 // 创建或获取一个共享内存：成功返回共享内存ID，失败返回-1
3 int shmget(key_t key, size_t size, int flag);
4 // 连接共享内存到当前进程的地址空间：成功返回指向共享内存的指针，失败返回-1
5 void *shmat(int shm_id, const void *addr, int flag);
6 // 断开与共享内存的连接：成功返回0，失败返回-1
7 int shmdt(void *addr); 
8 // 控制共享内存的相关信息：成功返回0，失败返回-1
9 int shmctl(int shm_id, int cmd, struct shmid_ds *buf);
```

创建一段共享内存时，必须指定其 size；而如果引用一个已存在的共享内存，则将 size 指定为0 ；当一段共享内存被创建以后，它并不能被任何进程访问，必须使用shmat函数连接该共享内存到当前进程的地址空间，连接成功后把共享内存区对象映射到调用进程的地址空间，随后可像本地空间一样访问

eg：
```c
struct shmid_ds buf1;  /*用于删除共享内存*/
// 创建共享内存
shmid = shmget(key, 1024, IPC_CREAT|0666)
 // 连接共享内存
shm = (char*)shmat(shmid, 0, 0);
// 创建信号量
semid = creat_sem(key);

...

//信号量P操作（lock）
sem_p(semid);
//读或写共享内存
printf("%s\n",shm);
//V操作（unlock）
sem_v(semid);

...
//删除共享内存、信号量
shmctl(shmid, IPC_RMID, &buf1);
del_sem(semid);
```

> 本质是一块内存空间，进程通过key找到这块空间的地址进行读写，用信号量完成读写互斥

### 前五种进程间通信方式总结
1. 管道：速度慢，容量有限，只有父子进程能通讯 
2. FIFO：任何进程间都能通讯，但速度慢
3. 消息队列：容量受到系统限制，且要注意第一次读的时候，要考虑上一次没有读完残留在内核里的数据
4. 信号量：不能传递复杂消息，只能用来同步
5. 共享内存区：能够很容易控制容量，速度快，但要保持同步（类比线程中的线程安全）
### 6.Socket
### 7.Streams
