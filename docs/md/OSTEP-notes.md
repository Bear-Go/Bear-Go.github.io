[TOC]

##### Preface

操作系统三大专题：虚拟性(virtualization)，并发性(concurrency)，持久性(persistence)

##### 1 Dialogue

> I Hear And I Forget. I See And I Remember. I Do And I Understand.
>
> 不闻不若闻之，闻之不若见之，见之不若知之，知之不若行之1。
>
> ——荀子

##### 2 Introduction

当一段程序运行的时候，究竟发生了什么：

取指—译码—执行(fetch-decode-execute)

OS的目的是为了更好的运行程序

为了让用户可以告诉操作系统做什么，操作系统一般会提供一些接口interfaces(APIs)来让用户调用，这些接口也叫做system calls，这些接口统称为一个标准库

虚拟性使得很多程序得以在cpu上运行，并且可以并发地获取它们相应的指令与数据，以及共用设备等，OS有时也被叫做资源管理器。cpu/memory/disk是系统的资源，因此操作系统的作用就是去管理这些资源。

虚拟cpu使得多个程序同时运行。

虚拟mem使得程序之间不会互相干扰。操作系统将虚拟地址(virtual address space)映射到物理地址。(linux and macos都会采用虚拟地址随机化来防止攻击，具体见ostep)

多线程在并发情况下的问题

设计和实现操作系统的目的：高性能，提供保护...

os历史：

早期操作系统：只是一些库

超越库：保护，系统调用与内核

多道程序时代：内存保护，同时载入很多程序

当今：unix

##### 25 Dialogue on Concurrency

例子：拿桃子的过程可以简述为：判断一个东西是否为桃子，如果是那么拿桃子。当一堆人拿桌上的桃子时，显然会出现这样的问题：可能存在两个以上的人选中了同一个桃子，那么就会出现冲突。一个简单的解决办法是：让这堆人排成一队，依次拿桃子，这样冲突就得以解决，但是速度显著降低。并发就是为了解决这种问题而生的，并发需要满足解决冲突加高效率。

##### 26 Concurrency and Threads

经典观点是一个程序只有一个执行点(pc)，但是多线程程序会有多个执行点。

换而言之，每个线程类似于独立的进程，只有一点区别：它们共享地址空间，从而能访问相同的数据。

切换线程时需要上下文切换context switch，类似于进程的上下文切换。对于进程我们需要将状态保存到PCB，而对于线程我们需要将状态保存到TCB(Thread Control Block)，区别在于地址空间不变

传统进程也可以称之为单线程进程

线程运行的复杂：

​	1.执行顺序未知

​	2.共享数据

使用工具帮助研究：objdump valgrind purify

根据样例代码，我们可以得知不同的线程之间是可能存在冲突的，这种情况叫做race condition，而会发生race conditon的代码段，我们称为临界区critical section，临界区是访问共享资源的代码片段，一定不能由多个线程同时执行。

于是我们真正想要的代码就是所谓的互斥mutual exclusion，这个属性保证如果一个线程在临界区执行，其他线程将被阻止进入临界区。于是我们希望有一种原子操作，要么根本没运行，要么运行完成，没有中间状态。

显然一个合理的指令集不能提供构建一个并发程序所需的全部原子指令，于是我们希望硬件能够提供一些有用的指令，然后我们基于这些指令构建一个通用的集合，即所谓的同步语言synchronization primitive

问题：为什么要在操作系统研究并发？

因为“历史”，操作系统是第一个并发程序，且需要技术都是在操作系统内部使用的。

##### 27 Thread API

pthread.h 

线程创建

```c
int pthread_create(
    	pthread_t 			* thread,
	const pthread_attr_t	* attr,
		void 				* (*start_routine)(void*),
		void 				* arg);
```

第一个参数用于此后与该线程的交互

第二个参数用来指定该线程属性，先通过pthread_attr_init()等函数对attr指针初始化，然后再传入当前函数

第三个参数指示线程运行的函数入口

第四个参数用来传入相应的函数参数

线程完成

```c
int pthread_join(
    pthread_t,	thread，
    	void,	**value_ptr);
```

第一个参数用来指定等待的线程，即pthread_create()时创建的

第二个参数用来接收该线程运行函数的返回值

注意不要返回一个指向线程调用栈上分配的东西，因为线程结束后，栈就被自动释放了

锁

```c
int pthread_mutex_lock(pthread_mutex_t *mutex)
int pthread_mutex_unlock(pthread_mutex_t *mutex)
```

通过锁来提供互斥进入临界区的那些函数，使用前需要初始化lock，使用时要检查是否返回错误

条件变量

```c
int pthread_cond_wait(pthread_cond_t *cond, pthread_mutex_t *mutex);
int pthread_cond_signal(pthread_cond_t *cond);
```

#没有太明白条件变量的使用场景

##### 28 锁

```c
lock_t mutex;
...
lock(&mutex);
...
unlock(&mutex);
```

注：POSIX 库将锁称为互斥量（mutex），因为它被用来提供线程之间的互斥。

```c
pthread_mutex_t lock = PTHREAD_MUTEX_INITIALIZER;
...
Pthread_mutex_lock(&lock); // wrapper for pthread_mutex_lock()
...
Pthread_mutex_unlock(&lock);
```

对锁的评价：一是互斥性，二是公平性，三是性能

硬件提供的基本操作：原子交换（test & set），比较并交换（compare & exchange），条件式存储（store-conditional），获取并增加（fetch & add）

1.自旋锁 spin lock：一直自旋，利用CPU周期，直到锁可用。在单处理器上，需要抢占式的调度器（即不断通过时钟中断一个线程，运行其他线程），才能使用自旋锁。

基于原子交换的锁：

```c
void lock(lock_t *lock) {
	while (TestAndSet(&lock->flag, 1) == 1)
	; // spin-wait (do nothing)
}
```

基于比较并交换的锁：

```c
void lock(lock_t *lock) {
	while (CompareAndSwap(&lock->flag, 0, 1) == 1)
	; // spin
}
```

基于条件式存储的锁：

```c
void lock(lock_t *lock) {
	while (LoadLinked(&lock->flag)||!StoreConditional(&lock->flag, 1))
	; // spin
}
```

基于获取并增加的锁：ticket  锁

```c
void lock(lock_t *lock) {
	int myturn = FetchAndAdd(&lock->ticket);
	while (lock->turn != myturn)
	; // spin
}
```

线程可以处于 3 种状态之一（运行、就绪和阻塞）

2.使用睡眠、唤醒的操作，用队列的方式来调度线程

在 linux 上：

```c
int futex(int *uaddr, int futex_op, int val, ...)
```

3.两阶段锁，先自旋一会儿，再睡眠

##### 29 基于锁的并发数据结构

并发计数器

并发链表：一把大锁，或者对每个节点上锁，这里有一个balance

注意：有一个通用建议，对并发代码和其他代码都有用，即注意控制流的变化导致函数返回和退出，或其他错误情况导致函数停止执行

并发队列：有两个锁，一个负责队列头，另一个负责队列尾

并发散列表

##### 30 条件变量

锁并不是并发程序设计所需要的唯一原语

在多线程程序中，一个线程等待某些条件是很常见的，那么如何高效地等待一个条件？

```c
pthread_cond_wait(pthread_cond_t *c, pthread_mutex_t *m);
pthread_cond_signal(pthread_cond_t *c);
```

提示：发信号时总是持有锁

生产者/消费者（有界缓冲区）问题

由于Mesa语义，我们要记住一条关于条件变量地简单规则：总是使用while循环。虽然有时候不需要重新检查条件，但这样做总是安全的。

使用多个条件变量以决定正确地唤醒目标线程

```c
pthread_cond_broadcast() // 覆盖条件
```

唤醒所有在等待区的线程，应用场景：分配内存时，会唤醒所有申请内存的线程，然后重新检查条件。

##### 31 信号量

信号量是一个整数值的对象，可以用两个函数来操作它。在 POSIX 标准中，是 sem_wait() （also called P()）和 sem_port()（also called V()）。

```c
#include <semaphore.h>
sem_t s;
sem_init(&s, 0, 1);
```

信号量是编写并发程序的强大而灵活的原语。有程序员会因为简单实用，只用信号量，不用锁和条件变量。

##### 32 常见并发问题

有哪些类型的缺陷？

- 非死锁缺陷（大多数）
- 死锁缺陷

非死锁缺陷

这里主要讨论其中两种

- 违反原子性缺陷（atomicity violation）
- 错误顺序缺陷（order  violation）

```
Thread 1:: 
if (thd->proc_info) { 
	... 
	fputs(thd->proc_info, ...); 
	... 
} 
 
Thread 2:: 
thd->proc_info = NULL; 
```

第一种类型的问题叫做违反原子性（即代码段本意是原子的，但在执行中并没有强制实现原子性）

这类问题的修复通常很简单，只要给共享变量的访问加锁，确保每个线程访问proc_info字段时，都持有锁(proc_info_lock)

```
Thread 1:: 
	void init() { 
        ... 
        mThread = PR_CreateThread(mMain, ...); 
        ... 
} 
 
	Thread 2:: 
	void mMain(...) { 
		... 
		mState = mThread->State; 
		... 
}
```

第二种类型的问题叫做违反顺序（两个内存访问的预期顺序被打破了（即A应该在B之前执行，但是实际运行中却不是这个顺序）

通过强制顺序来修复这种缺陷，例如通过条件变量同步

死锁（Deadlock）缺陷

为什么发生死锁？哲学家吃饭问题/组件间复杂的依赖/封装

产生死锁的条件？互斥/持有并等待/非抢占/循环等待

1.预防死锁

- 获取锁时提供一个全序（trick:根据锁的地址作为获取锁的顺序）

- 设置全局的prevention锁
- 使用trylock()函数尝试获得锁
- 完全避免互斥？

2.通过调度避免死锁

- 适用场景很局限，不是广泛使用的通用方案

3.检查和恢复

- 允许死锁偶尔发生，检查到死锁时再采取行动（不要总是完美）

##### 33 基于事件的并发（进阶）

基本想法：事件循环

```
while (1) { 
	events = getEvents(); 
	for (e in events) 
	processEvent(e); 
}
```

重要API：select()或poll()

下面以 select()为例，手册页（在 macOS X 上）以这种方式描述 API： 

```
int select(	int nfds,
			fd_set *restrict readfds, 
			fd_set *restrict writefds, 
			fd_set *restrict errorfds,  
			struct timeval *restrict timeout);
```

> 补充：阻塞与非阻塞接口 
>
> 阻塞（或同步，synchronous）接口在返回给调用者之前完成所有工作。非阻塞（或异步，asynchronous）接口开始一些工作，但立即返回，从而让所有需要完成的工作都在后台完成。
>
> 通常阻塞调用的主犯是某种 I/O。例如，如果一个调用必须从磁盘读取才能完成，它可能会阻塞，等待发送到磁盘的 I / O 请求返回。 
>
> 非阻塞接口可用于任何类型的编程（例如，使用线程），但在基于事件的方法中非常重要，因为阻塞的调用会阻止所有进展。

注：有点协程的感觉

一个问题：阻塞系统调用？

- 在基于事件的系统中必须遵守一条规则：不允许阻塞调用。

解决方案：异步I/O

- 轮询与中断的问题

另一个问题：状态管理

- 这种代码通常比传统的基于线程的代码更复杂

什么事情仍然很困难？

- 多核系统上，锁不可避免
- 不能很好地与某些类型的系统活动集成，如分页（paging）

- 随着时间的推移，基于事件的代码可能很难管理
