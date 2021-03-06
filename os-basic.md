# OS基础-1

## 运行环境

- 中断：指来自CPU执行指令以外的事情发生，eg设备发出io结束中断

- 异常：也成内中断，指源自CPU执行指令内部的事件，eg地址越界、算术溢出

- 原语：操作系统底层的、具有原子性、运行时间短、调用频繁的程序

- 系统调用：用户程序调用操作系统所提供的一些功能

- 用户态：用户程序工作在用户态

- 核心态：操作系统内核工作在核心态，要执行一些特权指令

- 用户态进入核心态，用户堆栈也要切换为系统堆栈

- 并行性：两个或多个事件同一时刻发生，多处理器并行

- 并发性：两个或多个事件同一时间间隔内发生，每个处理器多程序并发执行

- 访管指令：是一条在用户态下执行的指令，用户程序要求操作系统提供服务而有意识的使用访管指令，从而产生一个中断，将操作系统转为核心态

## 进程管理

### 进程和线程

- 进程是如何解决问题的？

- 为什么要引入进程？多道程序环境下，允许多个程序`并发`执行

- 进程实体（也叫进程映像）由什么组成？`进程控制块PCB`，`程序段`，相关`数据段`三部分

- 进程是什么？进程是进程实体`运行过程`，是系统进行资源分配的一个`独立`单位

- 进程状态：运行状态、就绪状态（缺少处理机）、阻塞状态（等待状态，缺少其他资源或等待某一事件）、创建状态、结束状态

- 进程控制：创建新进程、撤销已有进程、实现进程状态转换

- 新进程创建：1）为新进程分配一个唯一的进程标示号，申请一个空白的PCB；2）为进程分配资源，为新进程的程序和数据以及用户栈分配必要的内存空间；3）初始化PCB，包括初始化标志信息、处理机状态、处理机控制信息，设置进程的优先级；4）如果进程就绪队列能够接纳新进程，就将新进程插入到就绪队列，等待被调度运行

- 进程通信：共享存储（甲乙之间有个大麻袋，甲乙通过大麻袋交换物品）、消息传递（甲要告诉乙某些事情，就要写信，通过邮差送到乙）、管道通信

- 线程：轻量级进程，是一个基本的CPU执行单元，由线程ID、程序计数器、寄存器集合和堆栈组成。它与同属同一进程的其他线程共享进程所拥有的全部资源

- 为什么线程能提高并发？线程切换时，有可能会发生进程切换，也有可能不发生，平均下来每次切换所需要的开销就小了

- 只有在CPU发生中断时才发生进程切换

### 处理器调度
- 为什么要进行处理器调度？

- 调度：进程数目往往要多于处理机数，进程争用处理机的情况在所难免。处理器调度是对处理器进行分配，从就绪队列里，按照一定的算法选择一个进程并将处理机分配给它运行

- 调度层次：1）`作业调度`（从外存处于后备状态的作业中挑选作业，给他们分配内存、输入输出设备等必要的资源，并建立相应的进程，以使它们获得竞争处理机的机会）；2）`内存调度`（暂时不用的进程调入外存，在外存已具备运行条件的进程调入内存。以提高内存利用率和系统吞吐量）；3）`进程调度`，按照一定的策略从就绪队列中选取一个进程，将处理机分配给他

- 调度算法有哪些？各有什么利弊？从长作业、短作业、紧迫性作业？先来先服务调度算法、短作业优先调度算法、优先级调度算法、高响应比优先调度算法、时间片轮转调度算法

- 多级反馈队列算法（集合了前几种算法的优点）：1）设置多个就绪队列，为每个赋予不同的优先级；2）赋予每个队列中进程中执行时间片也各不相同，优先级别越高，时间片越小；3）当一个新的进程进入内存后，首先将它放入第1级队尾，当轮到该进程执行时，如果它能在该时间片内完成，便可准备撤离系统，如果未能完成，调度程序则将该进程转入到第2级队列队尾，以此类推...；4）仅当第1级队列为空时，调度程序才会调度第2队列的进程，以此类推...

### 进程同步
- 临界区：访问临界资源的那段代码，`critical section`

- 临界资源：一次允许一个进程使用的资源

- 临界资源访问过程：`进入区`（检查是否可进入临界区，如果可以，则设置访问临界区标志，阻止其他进程进入）、`临界区`（进程中访问临界资源的那段代码）、`退出区`（将访问临界区的标志清除）、`剩余区`(remainder section)

- 为什么要引入进程同步的概念？进程是并发执行的，不同进程之间存在着不同的制约关系(同步、互斥)

- 不同的进程之间会存在什么关系？1）`同步`，为完成某个任务而建立两个或多个进程，这些进程需要在某些位置上协调它们的工作次序而等待；2）`互斥`，当一个进程进入临界区访问临界资源时， 另一个必须等待

- 临界区互斥的原则：空闲让进、忙则等待、有限等待、让权等待

- 临界区互斥的基本方法：1）软件实现，在进入区和检查一些标志来表明是否有进程在临界区

	```
	# 单标志法
	# 进程1，标志turn为0，允许进入临界区
	while(turn != 0); 
	critical section; # 临界区
	turn = 1;
	remainder section; # 剩余区
	
	# 进程2，标志turn为1，允许进入临界区
	while(turn != 1);
	critical section;
	turn = 0;
	remainder section;
	
	# 如果进程1离开临界区，进程2并无进入临界区的打算，而进程1则无法再次进入临界区
	```

	```
	# 双标志法先检查
	# 进程i
	while(flag[j]); 1
	flag[i] = TRUE; 3
	critical section;
	flag[i] = FALSE;
	remainder section;
	
	# 进程j
	while(flag[i]); 2
	flag[j] = TRUE; 4
	critical section;
	flag[j] = FALSE;
	remainder section;
	# 如果按照上面1，2，3，4步骤执行的话进程i和进程j可能同时进入临界区
	```

	```
	# 双标志法后检查
	# 进程i
	flag[i] = TRUE; 1
	while(flag[j]); 3
	critical section;
	flag[i] = FALSE;
	remainder section;
	
	# 进程j
	flag[j] = TRUE; 2
	while(flag[i]); 4
	critical section;
	flag[j] = FALSE;
	remainder section;
	# 如果按照上面1，2，3，4步骤执行的话进程i和进程j可能都无法进入临界区
	```

	```
	# Peterson algorithm
	# 进程i
	flag[i] = TRUE; turn = j;
	while(flag[j] && turn == j);
	critical section;
	flag[i] = FALSE;
	remainder section;

	# 进程j
	flag[j] = TRUE; turn = i;
	while(flag[i] && turn == i);
	critical section;
	flag[j] = FALSE;
	remainder section;
	
	# 同时检测另一个进程状态标志flag和不允许进入标志turn，perfect
	```
	2）硬件实现方法：

	```
	# 中断屏蔽法
	关中断；
	临界区；
	开中断；
	# 只有在发生中断的时候才会进行进程切换
	```
	
	```
	TestAndSet指令, 原子操作，执行该代码时候，不允许被中断
	Swap指令
	```

- 信号量组成：一个整型变量加两个操作P()和V()，变量只能由这两个操作修改。eg类别车站，当车站有空站台时，车辆驶入资源信号-1，驶离资源信号+1

	```
	# 由操作系统管理，执行不会中断
	S = 1; # 资源数目，假设为1
	P(S) { # 保证互斥访问临界资源
		while(S <= 0); # 资源为空，等待
		S = S - 1;
	}
	
	V(s) { # 使用后释放临界资源
		S = S + 1;
	}
	
	# PV成对出现，不能顺序颠倒、遗漏
	```

- 管程：由一组数据及定义在这组数据之上的对这组数据的操作组成的软件模块

- 使用信号量解决实际问题

	1. [生产者-消费者问题](https://zh.wikipedia.org/wiki/%E7%94%9F%E4%BA%A7%E8%80%85%E6%B6%88%E8%B4%B9%E8%80%85%E9%97%AE%E9%A2%98)

	2. [读写问题](https://zh.wikipedia.org/wiki/%E8%AF%BB%E5%86%99%E9%94%81)

	3. [哲学家进餐问题](https://zh.wikipedia.org/zh-hans/%E5%93%B2%E5%AD%A6%E5%AE%B6%E5%B0%B1%E9%A4%90%E9%97%AE%E9%A2%98)

- 为什么会产生死锁？当多个的进程，双方都在等待对方停止运行，以获取系统资源，但是没有一方提前退出时

- 产生死锁有什么条件？`同时`满足四条条件：互斥条件（某资源在一段时间内仅为一个进程所有）、不剥夺条件（资源不能被强制夺走）、请求和保持条件（进程至少保持了一个资源，又提出了新的资源的请求，而该资源已被其他人所有）、循环等待条件（循环等待链：每个进程已获得的资源同时被链中的下一个进程所请求）

- 有什么办法解决死锁？1）预防死锁：设置一些限制条件，使死锁无法发生；2）死锁避免：动态分配资源过程中，用一些算法防治系统进入不安全状态；3）死锁检测和解除：死锁产生前不采取任何措施，只检测当前系统有么有发生死锁，如果有则采取一些措施解除死锁

- 饥饿：进程在信号量内无穷等待的情况
























