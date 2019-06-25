# 概述

本文档描述海鹰翼辉嵌入式操作系统POSIX标准的主要数据类型、功能部件属性数据结构定义以及可裁剪宏定义。

# 数据类型定义

|标准C类型|POSIX类型|解释|
|---|---|---|
|typedef void|VOID|void 类型|
|typedef int|BOOL|布尔变量定义|
|typedef void *|PVOID|void * 类型|
|typedef constvoid *|CPVOID|const void*|
|typedef char|CHAR|8 位字符变量|
|typedef unsigned char|UCHAR|8 位无符号字符变量|
|typedef unsigned char *|PUCHAR|8 位无符号字符变量指针|
|typedef char *|PCHAR|8 位字符指针变量|
|typedef constchar *|CPCHAR|const char*|
|typedef unsigned char|BYTE|8 位字节变量|
|typedef unsigned char *|PBYTE|8 位字节变量指针|
|typedef long|LONG|32/64 位数定义|
|typedef unsigned long|ULONG|32/64 位无符号数定义|
|typedef int|INT|编译器相关 int|
|typedef unsigned int|UINT|编译器相关 unsigned int|
|typedef signed int|SINT|编译器相关 signed int|
|typedef signed char|INT8|char|
|typedef unsigned char|UINT8|8位无符号数定义|
|typedef signed char|SINT8|8位有符号数定义|
|typedef short|INT16|short|
|typedef unsigned short|UINT16|16 位无符号数定义|
|typedef signed short|SINT16|16 位有符号数定义|
|typedef int|INT32|long or int|
|typedef unsigned int|UINT32|32 位无符号数定义|
|typedef signed int|SINT32|32 位有符号数定义|
|typedef long long|INT64|long long|
|typedef unsigned long long|UINT64|64 位无符号数定义|
|typedef signed long long|SINT64|64 位有符号数定义|
|typedef volatile unsigned int|INTREG|定义处理器中断寄存器|
|typedef int|SPINLOCKTYPE|自旋锁类型|
|typedef long unsigned int|size_t|size_t类型|

# 线程

## 裁剪宏定义

HYSylixOS中的线程接口可裁剪，但没有专门用于单个裁剪线程的宏。如果需要裁剪可以将base工程中libsylixos/SylixOS/ config/posix/posix_cfg.h文件中LW_CFG_POSIX_EN置位0、将LW_CFG_POSIXEX_EN置为0进行裁剪或将libsylixos/SylixOS/ config/ gjb/gjb_cfg.h中LW_CFG_GJB7714_EN置为0进行裁剪。

## 数据类型定义

|HYSylixOS类型|线程基础类型|解释|
|---|---|---|
|typedef ULONG|LW_OBJECT_HANDLE|这里的对象句柄和 ID 号等同|
|typedef LW_OBJECT_HANDLE|pthread_t|线程句柄|
|typedef long|pthread_key_t|线程的私有变量键|
|typedef UINT32|uid_t|用户ID|
|typedef UINT32|gid_t|组ID|

## 属性宏定义

1. 任务属性contentionscope的取值：

|PTHREAD_SCOPE_PROCESS|0|定义任务的调度范围：进程级|
|---|---|---|
|PTHREAD_SCOPE_SYSTEM|1|定义任务的调度范围：系统级|

2. 任务属性inheritsched的取值：

|PTHREAD_INHERIT_SCHED|1|任务的调度策略及调度属性从调用任务中继承|
|---|---|---|
|PTHREAD_EXPLICIT_SCHED|2|任务的调度策略及调度属性由任务属性对象设置|

3. 任务属性detachstate的取值：

|PTHREAD_CREATE_DETACHED|0|定义任务创建时与其他任务的关系：分离状态|
|---|---|---|
|PTHREAD_CREATE_JOINABLE|1|定义任务创建时与其他任务的关系：联合状态|

4. 任务属性stack_filled的取值：

|PTHREAD_NO_STACK_FILLED|0|任务不填充任务栈|
|---|---|---|
|PTHREAD_STACK_FILLED|1|任务以0xee填充任务栈|

5. 任务调度策略的取值：

|SCHED_OTHER|0|优先级抢占的调度策略|
|---|---|---|
|SCHED_FIFO|1|先进先出的调度策略|
|SCHED_RR|2|时间片轮转（不支持）|

6. 对象可见范围的取值：

|PTHREAD_PROCESS_PRIVATE|0|对象只能在创建该对象的进程范围内可见|
|---|---|---|
|PTHREAD_PROCESS_SHARED|1|对象在所有可访问资源所占内存的进程范围内可见|

7. 任务取消状态的取值：

|PTHREAD_CANCEL_ENABLE|0|允许任务被取消|
|---|---|---|
|PTHREAD_CANCEL_DISABLE|1|禁止任务被取消|

8. 任务取消类型的取值：

|PTHREAD_CANCEL_DEFERRED|0|定义任务的取消时机：延迟取消|
|---|---|---|
|PTHREAD_CANCEL_ASYNCHRONOUS|1|定义任务的取消时机：异步取消|

9. 任务排队策略的取值：

|PTHREAD_WAITQ_FIFO|0|阻塞等待资源的任务将按照先进先出的排队策略解除阻塞|
|---|---|---|
|PTHREAD_WAITQ_PRIO|1|阻塞等待资源的任务将按照优先级抢占的排队策略解除阻塞|

## 属性定义

所有线程属性都通过一个属性对象表示，该属性定义为结构体 pthread_attr_t。 POSIX定义了一系列函数设置和读取线程属性值。
该结构体成员如下所示：

```C
typedef struct { 
    char *name; /* 名字 */ 
    void *stackaddr; /* 指定堆栈地址 */ 
    size_t stackguard; /* 堆栈警戒区域大小 */ 
    size_t stacksize; /* 堆栈大小 */ 
    int schedpolicy; /* 调度策略 */ 
    int inheritsched; /* 是否继承调度参数 */ 
    unsigned long option; /* 选项 */ 
    struct sched_param schedparam; /* 调度参数 */ 
    ULONG reservepad[8]; /* 保留 */ 
    } pthread_attr_t
```

- name ：该属性设置创建线程的名字，默认为“pthread”；
- stackaddr：该属性设置创建线程新的栈起始地址，初始化默认为LW_NULL，系统自动分配栈空间。重新指定需要先分配好空间；
- stackguard ：该属性设置创建线程的线程栈警戒区大小，初始化默认为1024字节；
- stacksize：该属性设置创建线程栈的大小，初始化默认为0，意味的继承创建者的栈大小（注：设置的栈大小不应该小于128 字，否则将返回 EINVAL 错误值。当小于8*1024字节时，默认8*1024字节）；
- schedpolicy：该属性设置创建线程的调度策略，初始化默认为轮转调度算法；

表2-2 线程调度策略宏

|宏名|解释|
|---|---|
|SCHED_OTHER|抢占式调度算法|
|SCHED_FIFO|先进先出调度算法|
|SCHED_RR|不支持轮转调度算法|
|SCHED_SPORADIC|不支持|

- inheritsched：该属性设置创建线程是否继承调度参数，默认不继承；

表2-2 线程是否继承调度参数宏

|宏名|解释|
|---|---|
|PTHREAD_INHERIT_SCHED|继承调度参数|
|PTHREAD_EXPLICIT_SCHED|明确指定调度参数|

- option：该属性设置创建线程选项，默认为允许对任务堆栈进行检查；

表2-4 线程选项设置宏

|宏名|解释|
|---|---|
|PTHREAD_CREATE_DETACHED|分离状态|
|PTHREAD_CREATE_JOINABLE|联合状态|
|PTHREAD_SCOPE_PROCESS|进程级调度|
|PTHREAD_SCOPE_SYSTEM|系统级调度|
|PTHREAD_NO_STACK_FILLED|任务不填充任务zhai|
|PTHREAD_STACK_FILLED|堆栈填充|

- schedparam：该属性设置创建线程调度参数，结构定义如下，主要用来设置创建线程的优先级，默认优先级为200；

```C
struct sched_param { 
    int sched_priority; /* POSIX 调度优先级 */ 
    /* SCHED_SPORADIC parameter */ 
    int sched_ss_low_priority; /* Low scheduling priority for */ 
    /* sporadic server. */ 
    struct timespec sched_ss_repl_period; /* Replenishment period for */ 
    /* sporadic server. */ 
    struct timespec sched_ss_init_budget; /* Initial budget for sporadic */ 
    /* server. */ int sched_ss_max_repl; 
    /* Max pending replenishments */ 
    /* for sporadic server. */ 
    ULONG sched_pad[12]; /* 扩展保留 */ 
    };
```

- sched_priority：用来设置创建线程的优先级，最高优先级为0，最低优先级为255，取值应位于两者之间；
- sched_ss_low_priority：
- sched_ss_repl_period：
- sched_ss_init_budget：
- sched_ss_max_repl：

# 信号量

## 裁剪宏定义

HYSylixOS中信号量可裁剪，但没有专门用于单个裁剪信号量的宏。如果需要裁剪可以将base工程中libsylixos/SylixOS/ config/ posix/posix_cfg.h文件中LW_CFG_POSIX_EN置位0。

## 属性宏定义


1. 信号量类型相关宏定义：

|SEM_BINARY|1|信号量类型——二进制信号量|
|---|---|---|
|SEM_COUNTING|2|信号量类型——计数信号量|

2. 信号量排队策略宏定义：

|PTHREAD_WAITQ_FIFO|0|信号量排队策略为先进先出|
|---|---|---|
|PTHREAD_WAITQ_PRIO|1|信号量排队策略为优先级调度策略|

## 属性定义

HYSylixOS信号量有两种类型：匿名信号量和命名信号量。匿名信号量只存在于内存中，这就要求使用信号量的线程必须可以访问内存，因此匿名信号量可以应用于同一进程中线程间的通信，不同的进程间需要映射这段内存到自己的地址空间。命名信号量可以通过名字访问，因此可以应用于进程间的通信。HYSylixOS信号量的本质是计数型信号量。HYSylixOS信号量被定义为sem_t，如下：

```c
typedef struct { 
    PVOID SEM_pvPxSem; /* 信号量内部结构 */ 
    PLW_RESOURCE_RAW SEM_presraw; /* 资源管理节点 */ 
    ULONG SEM_ulPad[5]; 
    } sem_t;
```

应用开发人员根本不需要在意其结构体中具体成员，只需要在创建或打开时将其初始值和一系列属性设置好就可以了。
获取信号量属性用到sem_info_t结构，该结构如下：

```c
typedef struct { 
    ULONG SEMINFO_ulCounter; 
    ULONG SEMINFO_ulOption; 
    ULONG SEMINFO_ulBlockNum; 
    ULONG SEMINFO_ulPad[6]; 
    } sem_info_t;|
```

- SEMINFO_ulCounter：信号量计数值
- SEMINFO_ulOption：信号量选项
- SEMINFO_ulBlockNum：信号量被解锁的线程数量

# 互斥量

## 裁剪宏定义

HYSylixOS中的互斥量可裁剪，但没有专门用于单个裁剪互斥量的宏。如果需要裁剪可以将base工程中libsylixos/SylixOS/ config/posix/posix_cfg.h文件中LW_CFG_POSIX_EN置位0或将LW_CFG_POSIXEX_EN置为0进行裁剪。
修改LW_CFG_POSIXEX_EN不存在问题，但只能注释一部分。

## 属性宏定义

1. 互斥量避免优先级倒置的协议属性宏：

|PTHREAD_PRIO_NONE|0|先进先出调度算法且使用优先级继承算法|
|---|---|---|
|PTHREAD_PRIO_INHERIT|1|优先级等待算法且使用优先级继承算法|
|PTHREAD_PRIO_PROTECT|2|优先级等待算法且使用优先级天花板算法|

2. 互斥量类型属性宏

|PTHREAD_MUTEX_NORMAL|0|重复 lock 产生死锁|
|---|---|---|
|PTHREAD_MUTEX_FAST_NP|0|重复 lock 产生死锁|
|PTHREAD_MUTEX_ERRORCHECK|1|重复 lock 返回错误|
|PTHREAD_MUTEX_ERRORCHECK_NP|1|重复 lock 返回错误|
|PTHREAD_MUTEX_RECURSIVE|2|重复 lock 支持递归|
|PTHREAD_MUTEX_RECURSIVE_NP|3|重复 lock 支持递归|

3. 互斥量的任务取消安全属性

|PTHREAD_CANCEL_SAFE|0|锁定该属性互斥量的任务被禁止随意取消|
|---|---|---|
|PTHREAD_CANCEL_UNSAFE|1|锁定该属性互斥量的任务的取消状态不会受影响|

## 属性定义

所有互斥量属性都通过一个属性对象表示，属性定义为结构体pthread_mutexattr_t。POSIX 定义了一系列函数设置和读取该属性值。该结构体成员如下所示：

```c
typedef struct { 
    int PMUTEXATTR_iIsEnable; /* 此属性块是否有效 */ 
    int PMUTEXATTR_iType; /* 互斥量类型 */ 
    int PMUTEXATTR_iPrioceiling; /* 天花板优先级 */ 
    unsigned long PMUTEXATTR_ulOption; /* 算法类型 */ 
    } pthread_mutexattr_t;|

```

- PMUTEXATTR_iIsEnable：初始化和销毁属性快时对其置位，标记该属性快是否可用；
- PMUTEXATTR_iType：该属性用来设置创建互斥量的类型，默认支持递归；

表4-1 互斥量属性定义宏

|宏名|解释|
|---|---|
|PTHREAD_MUTEX_NORMAL|重复 lock 产生死锁|
|PTHREAD_MUTEX_FAST_NP|重复 lock 产生死锁|
|PTHREAD_MUTEX_ERRORCHECK|重复 lock 返回错误|
|PTHREAD_MUTEX_ERRORCHECK_NP|重复 lock 返回错误|
|PTHREAD_MUTEX_RECURSIVE|重复 lock 支持递归|
|PTHREAD_MUTEX_RECURSIVE_NP|重复 lock 支持递归|

- PMUTEXATTR_iPrioceiling：当算法使用了优先级天花板算法避免优先级翻转时用来设置天花板的优先级；
- PMUTEXATTR_ulOption：该选项用来设置创建互斥量的算法类型，默认使用按优先级顺序等待，使用优先级继承避免优先级翻转

表4-3 互斥量算法类型设置宏

|宏名|解释|
|---|---|
|PTHREAD_PRIO_NONE|先进先出调度算法且使用优先级继承算法|
|PTHREAD_PRIO_INHERIT|优先级等待算法且使用优先级继承算法|
|PTHREAD_PRIO_PROTECT|优先级等待算法且使用优先级天花板算法|

互斥量类型为pthread_mutex_t，使用时需要定义一个pthread_mutex_t类型的变量。

```c
typedef struct { 
    LW_OBJECT_HANDLE PMUTEX_ulMutex; /* 互斥信号量句柄 */ 
    int PMUTEX_iType; /* 互斥量类型 */ 
    } pthread_mutex_t;
```

应用开发人员根本不需要在意其结构体中具体成员，只需要在创建前设置好互斥量属性块就可以了。
获取互斥量信息用到了pthread_mutex_info_t结构，该结构定义如下：

```c
typedef struct { 
    int value; int inherit; 
    int prioceiling; 
    int wait_type; 
    int cancel_type; 
    int blocknum; 
    pthread_t ownner; 
    ULONG reservepad[6]; 
    } pthread_mutex_info_t;
```

- Value：互斥量计数器值；
- Inherit：避免优先级翻转的调度算法；
- Prioceiling：天花板优先级；
- wait_type：等待互斥量调度算法；
- cancel_type：取消互斥量类型，是否是安全取消；
- blocknum：被阻塞线程的数量；
- ownner：占有互斥量的线程ID。

# 条件变量

## 裁剪宏定义

HYSylixOS中条件变量可裁剪，但没有专门用于单个裁剪条件变量的宏。如果需要裁剪可以将base工程中libsylixos/SylixOS/ config/ posix/posix_cfg.h文件中LW_CFG_POSIX_EN置位0。

## 属性宏定义


1. 条件变量共享属性宏

|PTHREAD_PROCESS_SHARED|1|使条件变量在多个进程共享的内存空间中。|
|---|---|---|
|PTHREAD_PROCESS_PRIVATE|0|该条件变量只能由和初始化该条件变量的任务在同一进程中的任务访问。|

2. 设置条件变量计时时钟属性宏

|CLOCK_REALTIME|0|实时时钟源（唯一支持）|
|---|---|---|


## 属性定义

所有条件变量属性都通过一个属性对象表示，属性定义为pthread_condattr_t。 POSIX定义了一系列函数设置和读取该属性值。
该属性定义如下所示：

表5-1 条件变量属性定义

|类型|属性定义|解释|
|---|---|---|
|ULONG|pthread_condattr_t|条件变量属性|

条件变量类型为pthread_cond_t，使用时需要定义一个pthread_cond_t的变量。

```c
typedef struct { 
    LW_OBJECT_HANDLE TCD_ulSignal; /* 等待信号量句柄 */ 
    LW_OBJECT_HANDLE TCD_ulMutex; /* 互斥信号量 */ 
    ULONG TCD_ulCounter; /* 引用计数器 */ 
    } LW_THREAD_COND; 
    typedef LW_THREAD_COND *PLW_THREAD_COND; 
    typedef LW_THREAD_COND pthread_cond_t;
```

应用开发人员根本不需要在意其结构体中具体成员，只需要在创建前设置好条件变量属性块就可以了。

# 读写锁

## 裁剪宏定义

HYSylixOS中读写锁接口支持可裁剪，但没有专门用于单个裁剪读写锁的宏。如果需要裁剪可以将base工程中libsylixos/SylixOS/ config/posix/posix_cfg.h文件中LW_CFG_POSIX_EN置位0或将LW_CFG_POSIXEX_EN置为0进行裁剪。

## 属性宏定义

1. 读写锁共享属性宏

|PTHREAD_PROCESS_SHARED|1|使读写锁在多个进程共享的内存空间中。|
|---|---|---|
|PTHREAD_PROCESS_PRIVATE|0|该读写锁只能由和初始化该条件变量的任务在同一进程中的任务访问。|

2. 读写锁优先读或者写属性设置宏

|PTHREAD_RWLOCK_PREFER_READER_NP|读优先|
|---|---|
|PTHREAD_RWLOCK_PREFER_WRITER_NP|写优先|

## 属性定义

所有读写锁属性都通过一个属性对象表示，属性定义为pthread_rwlockattr_t。 POSIX
定义了一系列函数设置和读取该属性值。该属性定义如下所示：

表6-1读写锁属性定义

|类型|属性定义|解释|
|---|---|---|
|int|pthread_rwlockattr_t|读写锁属性|

读写锁类型为pthread_rwlock_t，使用时需要定义一个pthread_rwlock_t的变量。

```c
typedef struct { 
    LW_OBJECT_HANDLE PRWLOCK_ulRwLock; /* 内核读写锁 */ 
    LW_OBJECT_HANDLE PRWLOCK_ulReserved[3]; 
    unsigned int PRWLOCK_uiReserved[4]; 
    } pthread_rwlock_t;
```

应用开发人员根本不需要在意其结构体中具体成员，只需要在创建前设置好读写锁属性块就可以了。

# 消息队列

## 裁剪宏定义

HYSylixOS中消息队列可裁剪，但没有专门用于单个裁剪消息队列的宏。如果需要裁剪可以将base工程中libsylixos/SylixOS/ config/ posix/posix_cfg.h文件中LW_CFG_POSIX_EN置位0或将libsylixos/SylixOS/ config/ gjb/gjb_cfg.h中LW_CFG_GJB7714_EN置为0进行裁剪。

## 属性宏定义

1. 消息队列排队策略宏定义：

|PTHREAD_WAITQ_FIFO|0|消息队列排队策略为先进先出|
|---|---|---|
|PTHREAD_WAITQ_PRIO|1|消息队列排队策略为优先级调度策略|

2. 消息队列对象的标志位

|O_NONBLOCK|0x4000|使消息队列满时写或空时读不阻塞|
|---|---|---|
|0|0|使消息队列满时写或空时读阻塞|

3. 打开命名消息队列的标志宏

|O_RDONLY|0x0000|打开的消息队列用来接收消息|
|---|---|---|
|O_WRONLY|0x0001|打开的消息队列用来发送消息|
|O_RDWR|0x0002|打开的消息队列用来接收和发送消息|
|O_CREAT|0x0200|创建一个消息队列|
|O_EXCL|0x0800|如果同时设置 O_EXCL 和 O_CREAT，那么该接口只在所指定name的消息队列不存在时才创建新的消息队列；如果 name指定的消息队列已存在，该接口将会失败。|
|O_NONBLOCK|0x4000|该标志使得一个消息队列在队列为空时的读和队列填满时的写不被阻塞|

4. 消息的最高优先级

|MQ_PRIO_MAX|32|消息的最高优先级|
|---|---|---|


## 属性定义

所有消息队列属性都通过一个属性对象表示，属性定义为mq_attr_t。创建消息队列时需要提供下列的属性。mq_attr_t如下所示：

```c
typedef struct mq_attr { 
    long mq_flags; /* message queue flags */ 
    long mq_maxmsg; /* max number of messages */ 
    long mq_msgsize; /* max message size */ 
    long mq_curmsgs; /* number of messages currently */ 
    } mq_attr_t;
```

- falg：消息队列对象的标志位。

表7-1 消息队列标准位设置宏

|宏名|解释|
|---|---|
|O_NONBLOCK|使消息队列满时写或空时读不阻塞|
|0|使消息队列满时写或空时读阻塞|

- mq_maxmsg：消息队列的最大消息数。
- mq_msgsize：每条消息的最大长度。
- mq_curmsgs：排队类型

表7-2 消息队列排队类型设置宏

|宏名|解释|
|---|---|
|PTHREAD_WAITQ_PRIO|优先级策略解除任务的阻塞|
|PTHREAD_WAITQ_FIFO|先进先出解除任务的阻塞|

消息队列类型位mqd_t，使用时需要定义一个mqd_t的变量。

表7-3 消息队列类型定义

|类型|属性定义|解释|
|---|---|---|
|long|mqd_t|消息队列类型|

应用开发人员根本不需要在意其类型，只需要在创建前设置好消息队列属性块就可以了。

获取消息队列信息用到了mq_info_t结构，该结构定义如下：

```c
typedef struct { 
    mq_attr_t attr; 
    mode_t mode; 
    uint32_t priomap; 
    ULONG reservepad[12]; 
    } mq_info_t;
```


- attr ：消息队列属性块，具体参见mq_attr_t属性定义；
- mode：创建mode；
- priomap：位图表 (与优先级算法相同)；

# 信号

## 裁剪宏定义

HYSylixOS中信号支持可裁剪，如果需要裁剪可以将base工程libsylixos/SylixOS/config/system/system_cfg.h文件中LW_CFG_SIGNAL_EN置位0。

## 属性宏定义


1. 信号定义

|SIGHUP|挂断控制终端或进程。通常用此通知守护进程再次读取它们的配置文件，因 为守护进程不会有控制终端，通常决不会接收到这种信号|
|---|---|
|SIGINT|来自键盘的中断。一般采用 Ctrl + C 来产生此信号。当一个进程在运行时失 控，特别是他正在屏幕上产生大量不需要的输出时，常用此信号终止|
|SIGQUIT|来自键盘的退出|
|SIGILL|非法指令|
|SIGTRAP|跟踪断点|
|SIGABRT|异常结束|
|SIGUNUSED|未使用|
|SIGFPE|协处理出错，如除以 0、浮点溢出等|
|SIGKILL|强迫进程结束。|
|SIGBUS|总线错误，通常是指示一个实现定义的硬件故障|
|SIGSEGV|无效内存引用|
|SIGUNUSED2|未使用 2|
|SIGPIPE|管道写错误，无读者|
|SIGALRM|实时定时器报警|
|SIGTERM|进程终止。这是 kill 命令的默认动作，由于这个信号是由应用程序捕获的， 使用 SIGTERM 也让程序有机会在退出之前做好清理工作，从而优雅的终止|
|SIGCNCL|线程取消|
|SIGSTOP|停止进程执行。此信号不能被捕获和忽略|
|SIGTSTP|tty 发出停止进程|
|SIGCONT|恢复进程继续执行|
|SIGCHLD|子进程停止或者被终止。系统默认是忽略此信号|
|SIGTTIN|后台进程请求输入|
|SIGTTOU|后台进程请求输出|
|SIGCANCEL|同 SIGTERM 相同|
|SIGIO|异步 I/O 事件|
|SIGXCPU|进程超出了软 CPU 事件限制|
|SIGXFSZ|进程超出了软文件长度限制|
|SIGVTALRM|函数 setitimer 设置的虚拟间隔定时器已经超时|
|SIGPROF|函数 setitimer 设置的梗概间隔定时器已经超时|
|SIGWINCH|更改了窗口的大小|
|SIGINFO|信息请求|
|SIGPOLL|同 SIGIO 相同|
|SIGUSR1|用户定义信号 1|
|SIGUSR2|用户定义信号 2|
|SIGPWR|电源失败重新开始|
|SIGSYS|错误的系统调用|
|SIGURG|网络连接上接到带外的数据时，可选择地产生此信号|
|SIGRTMIN-SIGRTMAX|HYSylixOS 中 SIGRTMIN = 48、SIGRTMAX = 63，系统没有指定明确的含义， 由用户自定义，并且不应该使用某数值|

2. 信号处理方式

|SIG_ERR|错误|
|---|---|
|SIG_DFL|使用默认方式处理|
|SIG_IGN|忽略信号|
|SIG_CATCH|捕捉信号|
|SIG_HOLD|继续|
|VOID (\*PSIGNAL_HANDLE)(INT)函数|自定义的方式处理信号|

3. 信号集处理方式

|SIG_BLOCK|1|在阻塞信号集上给定的信号集|
|---|---|---|
|SIG_UNBLOCK|2|从阻塞信号集上删除的信号集|
|SIG_SETMASK|3|设置信号阻塞集|

## 属性定义

对于产生的信号进行处理时用到了信号处理方式，其中HYSylixOS对于信号的处理方式宏和方式见表8-2：

表8-2 信号处理方式

|处理宏|解释|
|---|---|
|SIG_ERR|错误|
|SIG_DFL|使用默认方式处理|
|SIG_IGN|忽略信号|
|SIG_CATCH|捕捉信号|
|SIG_HOLD|继续|
|VOID (\*PSIGNAL_HANDLE)(INT)函数|自定义的方式处理信号|

注：

不能忽略和捕捉的信号有：SIGSTOP。

不能屏蔽的信号有：SIGILL、SIGABRT、SIGFPE、SIGKILL、SIGBUS、SIGSEGV、SIGSTOP。

在 HYSylixOS中，需要一个能表示多个信号的信号集，以便告诉内核不允许递送该信号集中的信号。信号集类型见表8-3。

表8-3 信号集类型

|类型|属性定义|解释|
|---|---|---|
|UINT64|sigset_t|信号集类型|

应用开发人员根本不需要在意其类型，只需要在使用相应的接口对其进行设置。
HYSylixOS还对处理结构进行了定义，在sigaction等接口使用，定义为：structsigaction结构。

```c
typedef VOID (\*PSIGNAL_HANDLE)(INT); /* 信号处理句柄函数类型 */ 
typedef VOID (\*PSIGNAL_HANDLE_ACT)(INT, struct siginfo \*, PVOID); 
struct sigaction { 
                    union{ 
                            PSIGNAL_HANDLE \_sa_handler; 
                            PSIGNAL_HANDLE_ACT \_sa_sigaction; 
                         } _u; /* 信号服务函数句柄 */ 
                    sigset_t sa_mask; /* 执行时的信号屏蔽码 */ 
                    INT sa_flags; /* 该句柄处理标志 */ 
                    PSIGNAL_HANDLE sa_restorer; /* 恢复处理函数指针 */ 
                };
#define sa_handler _u._sa_handler 
#define sa_sigaction _u._sa_sigaction
```


- _u：信号处理函数句柄；
- sa_mask：
- sa_flags：句柄的处理标志；

表8-4 句柄的处理标志宏

|选项|说明|
|---|---|
|SA_NOCLDSTOP|子进程被删除时不要产生信号|
|SA_NOCLDWAIT|不产生僵尸进程|
|SA_SIGINFO|信号句柄需要 siginfo 参数|
|SA_ONSTACK|自定义堆栈|
|SA_RESTART|执行信号句柄后, 重启调用|
|SA_INTERRUPT|执行信号句柄后不重启调用|
|SA_NOMASK|不阻止在指定信号处理句柄中再收到信号|
|SA_NODEFER|不阻止在指定信号处理句柄中再收到信号|
|SA_ONESHOT|信号句柄一旦被调用就恢复到默认状态|
|SA_RESETHAND|执行句柄后, 将信号句柄设置为忽略|

- sa_restorer：已被废弃，不应该使用。

接下来对 sa_flags进行解释：如果包含 SA_NOCLDSTOP标志，父进程不接收子进程的暂停信号，SIGCHLD 信号被忽略。
指定 SA_NOCLDWAIT 标志，将由系统接管回收子进程的资源，因此不会产生僵尸进程。
指定 SA_NOMASK标志，在执行信号处理函数时，如果收到相同信号则会被中断，如此将会形成递归。
指定 SA_RESETHAND 标志，执行一次信号处理函数后，信号动作将设置为默认动作，此标志兼容了不可靠的信号机制。
sa_sigaction 成 员 是 一 个 替 代 的 信 号 处 理 程 序 ， 在 sigaction 结 构 中如 果 使 用 了 SA_SIGINFO 标志，则使用该信号处理程序。在 HYSylixOS中，sa_sigaction 成员和 sa_handler成员使用了同一存储区，所有应用程序只能一次使用这两个成员中的一个。
通常，如果使用 sa_handler 成员，按下面方式调用信号处理程序：

```c
void handler (int signo);
```

- 如果使用 sa_sigaction 成员，也就是设置了 SA_SIGINFO 标志，那么按下面方式调用信号处理程序：

```c
void handler (int signo, siginfo_t \*siginfo, void \*arg);
```


- siginfo_t 结构包含了信号产生原因的有关信息，在HYSylixOS 中该结构如下定义：

```c
typedef struct siginfo 
{ 
    INT si_signo; 
    INT si_errno; 
    INT si_code; 
    union { 
            struct { 
                    INT _si_pid; 
                    INT _si_uid; 
                    } _kill; 
            struct { 
                    INT _si_tid; 
                    INT _si_overrun; 
                    } _timer; 
            struct { 
                    INT _si_pid; 
                    INT _si_uid; 
                    } _rt; 
            struct { 
                    INT _si_pid; 
                    INT _si_uid; 
                    INT _si_status; 
                    clock_t _si_utime; 
                    clock_t _si_stime; 
                    } _sigchld; 
            struct { 
                    INT _si_band; 
                    INT _si_fd; 
                    } _sigpoll; 
        } _sifields; 
#define si_pid _sifields._kill._si_pid 
#define si_uid _sifields._kill._si_uid 
#define si_timerid _sifields._timer._si_tid 
#define si_overrun _sifields._timer._si_overrun 
#define si_status _sifields._sigchld._si_status 
#define si_utime _sifields._sigchld._si_utime 
#define si_stime _sifields._sigchld._si_stime 
#define si_band _sifields._sigpoll._si_band 
#define si_fd _sifields._sigpoll._si_fd 
    
    union sigval si_value; 

#define si_addr si_value.sival_ptr /* Faulting insn/memory ref */ 
#define si_int si_value.sival_int 
#define si_ptr si_value.sival_ptr 

    ULONG si_pad[4]; 
    
} siginfo_t;

```

- 成员 si_code 指示了信号的产生原因，如表 8-5 所示 HYSylixOS 中各种信号的si_code 值定义。

表8-5 si_code值定义

|信号|代码|说明|
|---|---|---|
|ANY|SI_KILL|使用 kill()发送的信号|
||SI_USER|同 SI_KILL|
||SI_QUEUE|使用 sigqueue 发送的信号|
||SI_TIMER|POSIX 定时器发送的信号|
||SI_ASYNCIO|异步 I/O 系统完成发送的信号|
||SI_MESGQ|接收到一条消息产生的信号|
||SI_KERNEL|HYSylixOS 内核内部使用|
|SIGILL|ILL_ILLOPC|非法操作码|
||ILL_ILLOPN|非法操作数|
||ILL_ILLADR|非法地址模式|
||ILL_ILLADR|非法陷入|
||ILL_ILLTRP|特权操作码|
||ILL_PRVOPC|特权操作码|
||ILL_PRVREG|权寄存器|
||ILL_COPROC|协处理器出错|
||ILL_BADSTK|内部栈出错|
|SIGFPE|FPE_INTDIV|整数除以 0|
||FPE_INTOVF|整数溢出|
||FPE_FLTDIV|浮点除以 0|
||FPE_FLTOVF|浮点向上溢出|
||FPE_FLTUND|浮点向下溢出|
||FPE_FLTRES|浮点不精确结果|
||FPE_FLTINV|无效浮点操作|
||FPE_FLTSUB|下标超出范围|
|SIGSEGV|SEGV_MAPERR|地址不映射至对象|
||SEGV_ACCERR|对于映射对象的无效权限|
|SIGBUS|BUS_ADRALN|无效地址对齐|
||BUS_ADRERR|不存在的物理地址|
||BUS_OBJERR|对象特定硬件错误|
|SIGTRAP|TRAP_BRKPT|进程断点陷入|
||TRAP_TRACE|进程跟踪陷入|
|SIGCHLD|CLD_EXITED|子进程终止|
||CLD_KILLED|子进程已异常终止（无 core）|
||CLD_DUMPED|子进程已异常终止（有 core，目前 SylixOS 不支持 core 文件）|
||CLD_TRAPPED|被跟踪子进程已陷入|
||CLD_STOPPED|子进程已停止|
||CLD_CONTINUED|停止的子进程已继续|
|SIGPOLL|POLL_IN|数据输入可用|
||POLL_OUT|输出缓冲区可用|
||POLL_MSG|输入消息可用|
||POLL_ERR|I/O 错误|
||POLL_PRI|高优先级输入可用|
||POLL_HUP|设备断开|

# 中断、异常


## 裁剪宏定义

HYSylixOS中断异常接口可裁剪，但没有专门用于单个裁剪中断异常的宏。如果需要裁剪可以将base工程中libsylixos/SylixOS/ config/ posix/posix_cfg.h文件中LW_CFG_POSIX_EN置位0或将libsylixos/SylixOS/ config/ gjb/gjb_cfg.h中LW_CFG_GJB7714_EN置为0进行裁剪。

## 属性宏定义


1. 中断向量定义（mini2440bsp）

|VIC_CHANNEL_EINT0|0|外部中断0|
|---|---|---|
|VIC_CHANNEL_EINT1|1|外部中断1|
|VIC_CHANNEL_EINT2|2|外部中都2|
|VIC_CHANNEL_EINT3|3|外部中断3|
|VIC_CHANNEL_EINT4_7|4|外部中断4到7|
|VIC_CHANNEL_EINT8_23|5|外部中都8到23|
|VIC_CHANNEL_CAM|6|摄像头中断|
|VIC_CHANNEL_BAT_FLT|7|电池故障|
|VIC_CHANNEL_TICK|8|RTCTick中断|
|VIC_CHANNEL_WDT_AC97|9|看门狗或AC97声卡接口中断|
|VIC_CHANNEL_TIMER0|10|定时器0|
|VIC_CHANNEL_TIMER1|11|定时器1|
|VIC_CHANNEL_TIMER2|12|定时器2|
|VIC_CHANNEL_TIMER3|13|定时器3|
|VIC_CHANNEL_TIMER4|14|定时器4|
|VIC_CHANNEL_UART2|15|串口2|
|VIC_CHANNEL_LCD|16|LCD控制器|
|VIC_CHANNEL_DMA0|17|DMA通道0|
|VIC_CHANNEL_DMA1|18|DMA通道1|
|VIC_CHANNEL_DMA2|19|DMA通道2|
|VIC_CHANNEL_DMA3|20|DMA通道3|
|VIC_CHANNEL_SDI|21|SDI中断|
|VIC_CHANNEL_SPI0|22|SPI0中断|
|VIC_CHANNEL_UART1|23|串口1|
|VIC_CHANNEL_NFCON|24|NANDFLASH控制器中断|
|VIC_CHANNEL_USBD|25|USB设备中断|
|VIC_CHANNEL_USBH|26|USB控制器中断|
|VIC_CHANNEL_IIC|27|IIC中断|
|VIC_CHANNEL_UART0|28|串口0|
|VIC_CHANNEL_SPI1|29|SPI1中断|
|VIC_CHANNEL_RTC|30|RTC报警中断|
|VIC_CHANNEL_ADC|31|ADC中断|

2. ARM 模式定义（mini2440bsp）

|MODE_USR32|0x10|用户状态|
|---|---|---|
|MODE_FIQ32|0x11|快速中断状态|
|MODE_IRQ32|0x12|中断状态|
|MODE_SVC32|0x13|管理状态|
|MODE_ABT32|0x17|中止状态|
|MODE_UND32|0x1B|未定义状态|
|MODE_SYS32|0x1F|系统状态|
|MODE_DISFIQ|0x40|关闭FIQ中断|
|MODE_DISIRQ|0x80|关闭IRQ中断|

## 数据类型定义

HYSylixOS系统中默认支持的中断向量表大小为256，不过可以通过LW_CFG_MAX_INTER_SRC进行设置。
中断处理函数类型定义为EXC_HANDLER，用来进行中断到来时进行相应的操作处理。

```c
typedef void (\*EXC_HANDLER)(int type);
```


# 时钟

## 裁剪宏定义

HYSylixOS中时钟可裁剪，但没有专门用于单个裁剪时钟接口的宏。如果需要裁剪可以将base工程中libsylixos/SylixOS/ config/ posix/posix_cfg.h文件中LW_CFG_POSIX_EN置位0或将libsylixos/SylixOS/ config/ gjb/gjb_cfg.h中LW_CFG_GJB7714_EN置为0进行裁剪。

## 属性宏定义


1. 时钟源宏定义：

|CLOCK_REALTIME0|实时时钟源|
|---|---|
|CLOCK_MONOTONIC1|单调增长时间|
|CLOCK_PROCESS_CPUTIME_ID2|进程从启动开始所消耗的 CPU 时间|
|CLOCK_THREAD_CPUTIME_ID3|线程从启动开始所消耗的 CPU 时间|

## 属性定义

UTC（Universal Time Coordinated），即世界协调时间，在实际使用中，它等同于GMT（Greenwich Mean Time），即格林威治标准时间。UTC 时间以 1970 年 1 月 1 日 0时 0 分 0秒为基准时间，并以秒为最小计数单位。以英国伦敦格林威治（本初子午线）为中时区，将地球分为东西各 12 个时区，各时区之间相差 1小时，这就是各个时区的本地时间。由于地球自西向东旋转，因此东时区时间早于中时区时间，西时区时间晚于中时区时间。
HYSylixOS中使用time_t表示UTC时间，类型定义为：

表9-1 time_t类型定义

|类型|属性定义|解释|
|---|---|---|
|INT64|time_t|时间秒定义|

使用timespec可以获取更高精度的时间

```c
struct timespec { 
    time_t tv_sec; /* seconds */ 
    LONG tv_nsec; /* nanoseconds */ 
    /* (0 - 1,000,000,000) */ 
    };
```


tv_nsec的值为0\~999999999，即不超过1秒，与tv_sec组成了当前的时间，注意此时间时时UTC时间。
时区信息结构定义为timezone如下

```c
struct timezone { 
    int tz_minuteswest; /* 是格林威治时间往西方的时差 */ 
    /* 以分钟为单位 (东8区) -60*8 */ 
    int tz_dsttime; /* 时间的修正方式 必须为0 */ 
    };
```


tz_minuteswest 的定义与前面讲的不同，这里的定义为相对于格林威治时间向西方的时差，因此东时区的时区值为负数。
上述时间类型都是单一地以秒来表示，不符合正常使用习惯，因此有以下类型表示我们通常熟悉的时间格式。该类型定义为struct
tm：

```c
struct tm { 
    INT tm_sec; /* seconds after the minute - [0, 59] */ 
    INT tm_min; /* minutes after the hour - [0, 59] */ 
    INT tm_hour; /* hours after midnight - [0, 23] */ 
    INT tm_mday; /* day of the month - [1, 31] */ 
    INT tm_mon; /* months since January - [0, 11] */ 
    INT tm_year; /* years since 1900 */ 
    INT tm_wday; /* days since Sunday - [0, 6] */ 
    INT tm_yday; /* days since January 1 - [0, 365] */ 
    #define tm_day tm_yday INT tm_isdst; 
    /* Daylight Saving Time flag must zero*/ 
    };

```

获取系统启动后到目前为止经过的时钟计数，该计数类型为clock_t，该类型是指时钟节拍。

表9-2 clock_t类型定义

|类型|属性定义|解释|
|---|---|---|
|ULONG|clock_t|时钟节拍定义|

HYSylixOS系统还提供了获取不同时钟源的时间接口，其中用到了clockid_t类型，该类型用来指明需要获取时间的时钟源。

表9-3 clockid_t时钟源定义

|类型|属性定义|解释|
|---|---|---|
|INT|clockid_t|时钟源定义|

时钟源主要分为：实际的物理时间，单调增长时间，进程从启动开始所消耗的 CPU时间，线程从启动开始所消耗的 CPU 时间。具体定义见表9-4。

表9-4 时钟源定义

|时钟源名称|说明|
|---|---|
|CLOCK_REALTIME0|实时时钟源|
|CLOCK_MONOTONIC1|单调增长时间|
|CLOCK_PROCESS_CPUTIME_ID2|进程从启动开始所消耗的 CPU 时间|
|CLOCK_THREAD_CPUTIME_ID3|线程从启动开始所消耗的 CPU 时间|

HYSylixOS创建定时器用到了定时器句柄，定义为wdg_t。

表9-4 wdg_t类型定义

|实际类型|属性定义|解释|
|---|---|---|
|LW_OBJECT_HANDLE|wdg_t|定时器句柄|

wdg_info_t类型用来获取定时器的信息。

```c
typedef struct { 
    int run; 
    ULONG cnt; 
    } wdg_info_t;
```


struct sigevent类型在创建定时器时设置定时器时间到时发出的信号。

```c
typedef struct sigevent { 
    INT sigev_signo; 
    union sigval sigev_value; 
    INT sigev_notify; 
    void (*sigev_notify_function)(union sigval); 
    #if LW_CFG_POSIX_EN > 0 
    pthread_attr_t *sigev_notify_attributes; 
    #else PVOID sigev_notify_attributes;
    #endif /* LW_CFG_POSIX_EN > 0 */ 
    LW_OBJECT_HANDLE sigev_notify_thread_id;
    /*Linux-specific */
    /* equ pthread_t */ 
    ULONG sigev_pad[8]; 
    } sigevent_t;
```


# 内存管理

## 裁剪宏定义


HYSylixOS中内存管理可裁剪，但没有专门用于单个裁剪内存管理的宏。如果需要裁剪可以将base工程中libsylixosSylixOS/ config/ posix/posix_cfg.h文件中LW_CFG_POSIX_EN置位0或将libsylixos/SylixOS/ config/ gjb/gjb_cfg.h中LW_CFG_GJB7714_EN置为0进行裁剪。

## 属性宏定义

1. 用户堆内存管理算法

|FIRST_FIT_ALLOCATION|Frist-Fit分配算法|
|---|---|
|BUDDY_ALLOCATION|Buddy分配算法|

## 属性定义

创建内存分区时用到内存分区标识，该表示类型定义为mpart_id。

表10-2 内存分区标识

|类型|属性定义|说明|
|---|---|---|
|LW_OBJECT_HANDLE|mpart_id|内存分区标识|

应用开发人员根本不需要在意其类型。
获取内存使用信息需要使用存放内存信息的结构体，定义为struct meminfo。

```c
struct partinfo { 
    ULONG segment; 
    size_t used; size_t maxused; 
    size_t free; 
    };
```


- segment：分段数量
- used：使用的字节数
- maxused：使用峰值字节数
- free：空闲字节数

# 文件

## 裁剪宏定义

HYSylixOS中文件操作支持可裁剪。如果需要裁剪文件打开关闭等一系列操作可以将libsylixos/SylixOS/config/system/system_cfg.h文件中的LW_CFG_DEVICE_EN宏允许系统设备管理置为0。

## 属性宏定义


1. 文件打开模式宏定义

|O_RDONLY|以只读的方式打开|
|---|---|
|O_WRONLY|以只写的方式打开|
|O_RDWR|以读写的方式打开|
|O_ACCMODE|用于检查文件的打开方式|
|O_SHLOCK|读锁|
|O_EXLOCK|写锁|
|O_CREAT|打开的文件不存在则创建文件|
|O_TRUNC|若文件存在并且以可写的方式打开时，此标识会令文件长度清为0，原文件资料丢失|
|O_APPEND|以附加的方式对文件操作|
|O_EXCL|检查文件是否存在|
|O_NONBLOCK|以不可阻断的方式打开文件|
|O_NDELAY|以不可阻断的方式打开文件|
|O_SYNC|以同步的方式打开文件|
|O_FSYNC|以同步的方式打开文件|
|O_DSYNC|数据同步|
|O_ASYNC|数据准备号发出信号|
|O_NOCTTY|如果欲打开的文件为终端设备文件，则不会将该终端机当成进程控制终端机|
|O_BINARY|以二进制方式打开|
|O_NOFOLLOW|如果参数pathname所指的文件为符号连接，则会导致文件打开失败|

2. 文件自身权限宏定义

|S_IRUSR|文件所有者可读|
|---|---|
|S_IWUSR|文件所有者可写|
|S_IXUSR|文件所有者可执行|
|S_IRWXU|文件所有者可读可写可执行|
|S_IRGRP|文件所有者同组用户可读|
|S_IWGRP|文件所有者同组用户可写|
|S_IXGRP|文件所有者同组用户可执行|
|S_IRWXG|文件所有者同组用户可读可写可执行|
|S_IROTH|其他用户可读|
|S_IWOTH|其他用户可写|
|S_IXOTH|其他用户可执行|
|S_IRWXO|其他用户可读可写可执行|

3. 文件ioctl操作宏定义

|FIONBIO|set non-blocking I/O;|
|---|---|
|FIONMSGS|return num msgs in pipe|
|FIOGETNAME|return file name in arg|
|FIOGETOPTIONS|get options|
|FIOSETOPTIONS|set options|
|FIOISATTY|is a tty|
|FIOSYNC|sync to disk|
|FIOPROTOHOOK|specify protocol hook routine|
|FIOPROTOARG|specify protocol argument|
|FIORBUFSET|alter the size of read buffer|
|FIOWBUFSET|alter the size of write buffer|
|FIORFLUSH|flush any chars in read buffers|
|FIOWFLUSH|flush any chars in write buffers|
|FIOSELECT|wake up process in select on I/O|
|FIOUNSELECT|wake up process in select on I/O|
|FIONFREE|get free byte count on device|
|FIOTRIM|ATA TRIM command|
|FIOSYNCMETA|sync range sector to disk|
|FIOLABELGET|get volume label|
|FIOLABELSET|set volume label|
|FIOATTRIBSET|set file attribute|
|FIOCONTIG|allocate contiguous space|
|FIOREADDIR|read a directory entry (POSIX)|
|FIOFSTATGET|get file status info|
|FIOUNMOUNT|unmount disk volume|
|FIONCONTIG|get size of max contig area on dev|
|FIOTRUNC|truncate file to specified length|
|FIOGETFL|get file mode, like fcntl(F_GETFL)|
|FIOTIMESET|change times on a file for utime()|
|FIOFSTATFSGET|get file status info 64bit|
|FIOFSTATGET64|move file, ala mv, (mv not rename)|
|FIODATASYNC|sync data to disk|
|FIOSETFL|set file mode, like fcntl(F_SETFL)|
|FIOGETCC|get tty ctl char table|
|FIOSETCC|set tty ctl char table|
|FIOGETLK|get a lockf|
|FIOSETLK|set a lockf|
|FIOSETLKW|set a lockf (with blocking)|

4. 文件fcntl操作宏定义

|F_DUPFD|0|Duplicate fildes|
|---|---|---|
|F_GETFD|1|Get fildes flags (close on exec)|
|F_SETFD|2|Set fildes flags (close on exec)|
|F_GETFL|3|Get file flags|
|F_SETFL|4|Set file flags|
|F_GETOWN|5|Get owner - for ASYNC|
|F_SETOWN|6|Set owner - for ASYNC|
|F_GETLK|7|Get record-locking information|
|F_SETLK|8|Set or Clear a record-lock(Non-Blocking)|
|F_SETLKW|9|Set or Clear a record-lock(Blocking)|
|F_RGETLK|10|Test a remote lock to see ifit is blocked|
|F_RSETLK|11|Set or unlock a remote lock|
|F_CNVT|12|Convert a fhandle to an openfd|
|F_RSETLKW|13|Set or Clear remoterecord-lock(Blocking)|

## 属性定义


打开文件返回的文件描述符是int整形，用户并不需要关心，只需要判断是否大于0，大于0表示打开成功。

# 设备管理

## 裁剪宏定义


HYSylixOS中设备管理支持可裁剪，但没有专门用于单个裁剪设备管理的宏。
如果需要裁剪驱动注册和设备安装相关接口可以将base工程中libsylixos / SylixOS /
config/posix/posix_cfg.h文件中LW_CFG_POSIX_EN置位0或将libsylixos/SylixOS/config/gjb/gjb_cfg.h中LW_CFG_GJB7714_EN置为0进行对网络裁剪。
如果需要裁剪设备打开关闭等操作可以将libsylixos/ SylixOS/ config/
system/system_cfg.h文件中的LW_CFG_DEVICE_EN宏允许系统设备管理置为0。

## 属性宏定义


1. 设备打开模式宏定义

|O_RDONLY|以只读的方式打开|
|---|---|
|O_WRONLY|以只写的方式打开|
|O_RDWR|以读写的方式打开|
|O_ACCMODE|用于检查文件的打开方式|
|O_SHLOCK|读锁|
|O_EXLOCK|写锁|
|O_CREAT|打开的文件不存在则创建文件|
|O_TRUNC|若文件存在并且以可写的方式打开时，此标识会令文件长度清为0，原文件资料丢失|
|O_APPEND|以附加的方式对文件操作|
|O_EXCL|检查文件是否存在|
|O_NONBLOCK|以不可阻断的方式打开文件|
|O_NDELAY|以不可阻断的方式打开文件|
|O_SYNC|以同步的方式打开文件|
|O_FSYNC|以同步的方式打开文件|
|O_DSYNC|数据同步|
|O_ASYNC|数据准备号发出信号|
|O_NOCTTY|如果欲打开的文件为终端设备文件，则不会将该终端机当成进程控制终端机|
|O_BINARY|以二进制方式打开|
|O_NOFOLLOW|如果参数pathname所指的文件为符号连接，则会导致文件打开失败|

## 属性定义


在进行驱动注册时使用到了驱动表，主要将写的驱动函数与系统函数指针进行对应，该结构定义为struct
file_operations。

```c
typedef struct file_operations { 
    struct module *owner; 
    long (*fo_create)(); /* DEVENTRY_pfuncDevCreate */ 
    int (*fo_release)(); /* DEVENTRY_pfuncDevDelete */ 
    long (*fo_open)(); /* DEVENTRY_pfuncDevOpen */ 
    int (*fo_close)(); /* DEVENTRY_pfuncDevClose */ 
    size_t (*fo_read)(); /* DEVENTRY_pfuncDevRead */ 
    ssize_t (*fo_read_ex)(); /* DEVENTRY_pfuncDevReadEx */ 
    ssize_t (*fo_write)(); /* DEVENTRY_pfuncDevWrite */ 
    ssize_t (*fo_write_ex)(); /* DEVENTRY_pfuncDevWriteEx */ 
    int (*fo_ioctl)(); /* DEVENTRY_pfuncDevIoctl */ 
    int (*fo_select)(); /* DEVENTRY_pfuncDevSelect */ 
    int (*fo_lock)(); /* not support now */ 
    off_t (*fo_lseek)(); /* DEVENTRY_pfuncDevLseek */ 
    int (*fo_fstat)(); /* DEVENTRY_pfuncDevFstat */ 
    int (*fo_lstat)(); /* DEVENTRY_pfuncDevLstat */ 
    int (*fo_symlink)(); /* DEVENTRY_pfuncDevSymlink */ 
    ssize_t (*fo_readlink)(); /* DEVENTRY_pfuncDevReadlink */ 
    int (*fo_mmap)(); /* DEVENTRY_pfuncDevMmap */ 
    int (*fo_unmap)(); /* DEVENTRY_pfuncDevUnmmap */ 
    ULONG fo_pad[16]; /* reserve */ 
    } FILE_OPERATIONS; 
    typedef FILE_OPERATIONS \*PFILE_OPERATIONS;
```


- owner ：拥有该结构模块的指针；
- fo_create：对设备进行创建操作；
- fo_release：删除设备；
- fo_open：对设备进行打开操作；
- fo_close：对设备进行关闭操作；
- fo_read：从设备中获取数据；
- fo_read_ex：读设备函数扩展函数；
- fo_write：向设备中写数据；
- fo_write_ex：读设备函数扩展函数；
- fo_ioctl：设备执行特点的命令；
- fo_select：select功能函数；
- fo_lock：暂时不支持；
- fo_lseek：改变设备当前读写位置；
- fo_fstat：fstat函数；
- fo_lstat：lstat函数；
- fo_symlink：建立链接文件；
- fo_readlink：读取链接文件；
- fo_mmap：文件映射；
- fo_unmap：取消设备内存映射到进程的地址空间的映射
- fo_pad：保留

每次驱动创建成功都会返回一个驱动号，是驱动的唯一标识，类型定义为dev_t。

表11-1 驱动号类型定义

|类型|属性定义|说明|
|---|---|---|
|ULONG|dev_t|驱动号定义|

对文件集操作用到了文件类型，定义为fd_set;

```c
typedef ULONG fd_mask; /* 单位掩码 */ 
typedef struct fd_set { 
    fd_mask fds_bits[__HOWMANY(FD_SETSIZE, NFDBITS)]; /* 建立掩码表 */ 
    } fd_set;
```


对文件集的操作有：

- FD_ZERO(p) ：对文件集进行清零操作；
- FD_SET(n, p) ：向文件集中加入文件描述符；
- FD_CLR(n, p) ：将某个文件从文件集中删除；
- FD_ISSET(n, p)：判断某个文件是否在文件中；

# 网络通信

## 裁剪宏定义

HYSylixOS网络部分支持可裁剪，如需要裁剪可以将base工程中libsylixos/ SylixOS/config/ net/net_cfg.h文件中LW_CFG_NET_EN置位0，进行对网络裁剪。

若单单裁剪IPv4，则需要将base工程中的libsylixos/ SylixOS/include/network/lwip/opt.h中的LWIP_IPV4置位0进行裁剪。

## 属性宏定义


1. Socket协议域类型宏

|AF_UNSPEC|未指定|
|---|---|
|AF_INET|IPv4 因特网域|
|AF_INET6|IPv6 因特网域|
|AF_UNIX|UNIX 域|
|AF_PACKET|PACKET 域|

2. Socket套接字类型宏

|SOCK_DGRAM|数据报套接口，固定长度，无连接的，不可靠的报文传递|
|---|---|
|SOCK_RAW|原始套接口，IP 协议的数据报接口|
|SOCK_SEQPACKET|有序分组套接口，固定长度的、有序的、可靠的、面向连接的报文传递|
|SOCK_STREAM|字节流套接口，有序的、可靠的、双向的、面向连接的字节流|

3. 协议类型宏

|IPPROTO_IP|IPv4 网络协议|
|---|---|
|IPPROTO_ICMP|因特网控制报文协议|
|IPPROTO_TCP|传输控制协议|
|IPPROTO_UDP|用户数据报协议|
|IPPROTO_IPV6|IPv6 网络协议|
|IPPROTO_RAW|原始 IP 数据包协议|

4. 数据发送类型宏

|MSG_PEEK|数据预读但不删除数据|
|---|---|
|MSG_WAITALL|等待所有数据到达后才返回|
|MSG_OOB|带外数据|
|MSG_DONTWAIT|不阻塞的接收数据|
|MSG_MORE|有更多的数据需要发送|

## 属性定义

socket API 适用于各种底层网络协议，如 IPv4、IPv6 以及后面要讲的 UNIX Domainsocket。然而，各种网络协议的地址格式并不相同，为使不同格式地址能够传入到套接字函数，地址会被强制转换成一个通用的地址结构 sockaddr。

下面是 HYSylixOS 系统下的实现：

```c
typedef u8_t sa_family_t; 
struct sockaddr { 
    u8_t sa_len; sa_family_t sa_family; 
    char sa_data[26]; /* sylixos add same size with in6 */ 
    };

```

IPv4（AF_INET）地址 in_addr 结构：

```c
typedef u32_t in_addr_t; 
struct in_addr 
    { 
    in_addr_t s_addr; 
    };

```

IPv4（AF_INET）地址 sockaddr_in 结构：

```c
struct sockaddr_in 
{ 
    u8_t sin_len; 
    sa_family_t sin_family; 
    in_port_t sin_port; 
    struct in_addr sin_addr; 
    #define SIN_ZERO_LEN 8 
    char sin_zero[SIN_ZERO_LEN]; 
    };
```


- sin_len：数据结构长度，是为增加 OSI 协议支持而增加的，有了长度成员后简化了变长套接口地址结构的处理；
- sin_family：地址族（AF_INET）；
- sin_port：网络协议端口号；
- sin_addr：网路字节序的 IPv4 地址；
- sin_zero：8 字节的数据填充。

IPv6（AF_INET6）地址 in6_addr 结构：

```c
struct in6_addr { 
    union { 
        u8_t u8_addr[16]; /* SylixOS change u8 in front */ 
        u32_t u32_addr[4]; 
        } un; 
        #define s6_addr un.u8_addr
         };
```


IPv6（AF_INET6）地址 sockaddr_in6 结构：

```c
struct sockaddr_in6 { 
    u8_t sin6_len; /* length of this structure */ 
    sa_family_t sin6_family; /* AF_INET6 */ 
    in_port_t sin6_port; /* Transport layer port # */ 
    u32_t sin6_flowinfo; /* IPv6 flow information */ 
    struct in6_addr sin6_addr; /* IPv6 address */ 
    u32_t sin6_scope_id; /* Set of interfaces for scope */ 
    };
```


- sin6_len：数据结构长度；
- sin6_family：地址族（AF_INET6）；
- sin6_port ：传输层端口号；
- sin6_flowinfo：流信息，低序 20 位是流标签，高序 12 位保留；
- sin6_addr：IPv6 地址；
- sin6_scope_id：范围 ID。

```c
struct sockaddr_un { 
    uint8_t sun_len; /* sockaddr len including null */ 
    uint8_t sun_family; /* AF_UNIX */ 
    char sun_path[104]; /* path name (gag) */ 
    };|
```
- sun_len：数据结构长度；
- sun_family：地址族（AF_UNIX）；
- sun_path：路径名；
