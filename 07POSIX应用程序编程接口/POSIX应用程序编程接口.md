# 概述
  
POSIX是Portable Operating System Interface（可移植操作系统接口）的缩写。

从1970第一款现代操作系统——UNIX诞生至今，出现了多款现代操作系统，例如：Windows、Linux、BSD、Solaris等，为了方便应用程序以及中间件的移植，大多数操作系统都采用与UNIX兼容的API（Windows除外）。为了保证操作系统API的相互兼容性制定了POSIX标准。

POSIX是IEEE（Institue of Electrical and Electronics Engineers，电气和电子工程师学会）为了规范各种UNIX操作系统提供的API接口而定义的一系列互相关联的标准的总称，其正式称呼为IEEE 1003，国际标准名称为ISO/IEC 9945。此标准源于一个大约开始于1985年的项目。其中POSIX标准对实时操作系统定义了一个称作1003.1b的子协议，该协议定义了标准实时操作系统的基本行为，HYSylixOS符合此协议的要求。

当前的POSIX主要分为四个部分：Base Definitions（基本功能定义）、System Interfaces（系统接口）、Shell and Utilities（shell与相关工具）和Rationale（基本原理）。HYSylixOS兼容这四部分的大多数规范。由于HYSylixOS对POSIX的支持，其他兼容POSIX系统上的应用程序可以非常方便的移植到HYSylixOS操作系统之上。  
  
下表中列出了HYSylixOS支持的POSIX标准头文件。 
  
|头文件名|说明|头文件名|说明|  
|---|---|---|---|  
|<dirent.h>|目录项|<dlfcn.h>|动态链接库操作函数|  
|<fcntl.h>|文件控制|<fmtmsg.h>|消息显示结构|  
|<fnmatch.h>|文件名匹配类型|<ftw.h>|文件树漫游|  
|<grp.h>|组文件|<iconv.h>|代码集转换实用程序|  
|<netdb.h>|网络数据库操作|<langinfo.h>|语言信息常量|  
|<pwd.h>|口令文件|<libgen.h>|模式匹配函数定义|  
|<regex.h>|正则表达式|<monetary.h>|货币类型|  
|<tar.h>|TAR归档值|<ndbm.h>|数据库操作|  
|<termios.h>|终端I/O|<nl_types.h>|消息类别|  
|<unistd.h>|符合常量|<poll.h>|轮询函数|  
|<arpa/inet.h>|Internet定义|<search.h>|搜索表|  
|<netinet/in.h>|Internet地址族|<strings.h>|字符串操作|  
|<netinet/tcp.h>|传输控制协议定义|<syslog.h>|系统出错日志记录|  
|<sys/mman.h>|内存管理声明|<ulimit.h>|用户限制|  
|<sys/select.h>|select函数|<utmpx.h>|用户账户数据库|  
|<sys/socket.h>|套接字接口|<sys/ipc.h>|IPC机制|  
|<sys/stat.h>|文件状态|<sys/msg.h>|消息队列|  
|<sys/times.h>|进程时间|<sys/resource.h>|资源操作|  
|<sys/types.h>|基本系统数据类型|<sys/sem.h>|信号量|  
|<sys/un.h>|UNIX域套接字定义|<sys/shm.h>|共享内存|  
|<sys/utsname.h>|系统名|<sys/statvfs.h>|文件系统信息|  
|<sys/wait.h>|进程控制|<sys/time.h>|时间类型|  
|<cpio.h>|cpio归档值|<sys/timeb.h>|附加的日期和时间定义|  
|<sys/uio.h>|矢量I/O操作|<aio.h>|异步I/O|  
|<mqueue.h>|消息队列|<pthread.h>|线程|  
|<sched.h>|执行调度|<semaphore.h>|信号量|  
|<spawn.h>|实时spawn接口|<stropts.h>|XSI STREAMS接口|  
|<trace.h>|时间跟踪|||  
  
本文档描述了HYSylixOS中符合POSIX标准的内核应用程序编程接口。  
  
# 线程
  
## pthread_attr_init() ：初始化任务属性对象
  
### 函数原型：
  
```c  
int pthread_attr_init   
(   
    pthread_attr_t*pattr   
)  
```  
  
### 描述：
  
该接口使用默认值初始化任务属性对象pattr，初始化默认值时由系统确定的，pthread_create()接口使用该接口初始化的属性对象来定义被创建任务的属性，可以在多个同时调用的pthread_create()中使用同一个任务属性对象。对应应根据任务进行分别设置的属性，如任务堆栈地址，系统会自动进行分配，保证不会发生重叠。调用设置属性值的接口可以修改已初始化的属性对象。任务属性对象用于指定任务创建时的属性信息，其初始化默认为：  
  
|属性对象|默认设置为|说明|  
|---|---|---|  
|name|”pthread”|线程名字|  
|stackaddr|LW_NULL|自动分配堆栈内存|  
|stackguard|LW_CFG_THREAD_DEFAULT_GUARD_SIZE|堆栈预警大小为1KB|  
|stacksize|0|堆栈大小继承创建者|  
|schedpolicy|LW_OPTION_SCHED_RR|调度策略使用调度RR|  
|inheritsched|PTHREAD_EXPLICIT_SCHED|不继承创建者调度参数|  
|option|LW_OPTION_THREAD_STK_CHK|允许对任务堆栈检查|  
|schedparam|PX_PRIORITY_CONVERT(LW_PRIO_NORMAL)|调度参数优先级为55|  
  
### 参数：
  
- pattr ：需要初始化的线程属性块指针。  
  
### 返回值：
  
成功返回0，失败返回相应的错误码。  
  
### 错误码：
  
- EINVAL ：参数无效。  
  
### 备注：
  
无。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <unistd.h>  
int main (int argc, char **argv)  
{  
	int ret  ;  
	pthread_attr_t attr;  
  
	ret = pthread_attr_init(&attr);  
  
	if(ret != 0)  
	{  
		perror("func pthread_attr_init error");  
		return -1;	  
	}  
	......  
  
    return  (0);  
}  
```  
  
## pthread_attr_destroy() ：删除任务属性对象
  
  
### 函数原型：
  
```c  
int  pthread_attr_destroy  
(  
	pthread_attr_t  *pattr  
);  
```  
  
  
### 描述：
  
该接口用来删除线程属性对象，将pattr引用的对象设定为无效值。删除后attr属性对象可以使用pthread_attr_init()重新初始化。删除后不能对其引用。  
  
### 参数：
  
- pattr ：需要销毁的线程属性快指针。  
  
### 返回值：
  
成功返回0，失败返回相应的错误码。  
  
### 错误码：
  
- EINVAL ：参数pattr指定的对象不是一个已初始化的任务属性对象。  
  
### 备注：
  
无。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <unistd.h>  
int main (int argc, char **argv)  
{  
	int ret  ;  
	pthread_attr_t attr;  
  
	ret = pthread_attr_init(&attr);  
	if(ret != 0)  
	{  
		perror("func pthread_attr_init error");  
		return -1;  
	}  
  
	......  
  
	pthread_attr_destroy(&attr);  
  
    return  (0);  
}  
```  
  
## pthread_attr_getstackaddr() ：获取一个线程属性块的堆栈地址
  
### 函数原型：
  
```c  
int   pthread_attr_getstackaddr  
(  
	const pthread_attr_t  *pattr,  
	void  **ppvStackAddr  
);  
```  
  
### 描述：
  
该函数用来获取指定任务属性块的stackaddr属性,并存放在参数ppvStackAddr指定的地址中。任务的stackaddr属性指定了任务栈的存储位置，存储区域的大小最小为PTHREAD_STATCK_MIN。  
  
### 参数：
  
- pattr ：需要获取的任务属性块指针。  
- ppvStackAddr ：保存堆栈地址的指针。  
  
### 返回值：
  
成功返回0，失败返回相应的错误码。  
  
### 错误码：
  
- EINVAL ： 参数无效。  
  
### 备注：
  
无。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <unistd.h>  
int main (int argc, char **argv)  
{  
	int ret;  
	pthread_attr_t attr;  
	void *paddr = NULL;  
	......  
  
	ret = pthread_attr_getstackaddr(&attr, &paddr);  
	if(ret != 0 )  
	{  
		perror("func pthread_attr_getstackaddr error\n");  
		return -1;  
	}  
	printf("attr stack addr :%p\n", paddr);  
  
	......  
  
    return  (0);  
}  
```  
  
## pthread_attr_setstackaddr() ：指定一个线程属性块的堆栈地址
  
### 函数原型：
  
```c  
int  pthread_attr_setstackaddr  
(  
	pthread_attr_t  *pattr,   
	void  *pvStackAddr  
);  
```  
  
  
### 描述：
  
该接口用来设置任务属性对象attr中的stackaddr属性。Stackaddr属性指定了任务栈的存储位置。在设置好地址时必须设置好堆栈的大小。一个堆栈地址对应一个线程。  
  
### 参数：
  
- pattr ：需要设置的任务属性块指针。  
- pvStackAddr ：堆栈地址。  
  
### 返回值：
  
成功返回0，失败返回相应的错误码。  
  
### 错误码：
  
- EINVAL ： 参数无效。  
  
### 备注：
  
无。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <unistd.h>  
int main (int argc, char **argv)  
{  
	int          ret ;  
	pthread_attr_t attr;  
	static char addr[1024 * 10] = {0};  
  
	ret = pthread_attr_setstackaddr(&attr,addr);   
    if(ret != 0)  
    {  
        perror("func pthread_attr_setstackaddr error\n");  
        return -1;  
    }   
	......  
  
    return  (0);  
}  
```  
  
  
## pthread_attr_getstacksize() ：获取任务的stacksize属性
  
### 函数原型：
  
```c  
int  pthread_attr_getstacksize   
(  
	const pthread_attr_t  *pattr,  
	size_t  *pstSize  
);  
```  
  
### 描述：
  
该函数用来获取指定任务属性块的stacksize属性。stacksize属性指定了任务栈的堆栈大小。  
  
### 参数：
  
- pattr ：需要获取的任务属性块指针。  
- pstSize ：任务栈的堆栈大小。  
  
|宏定义|宏值|解释|  
|---|---|---|  
|PTHREAD_STACK_MIN|8*1024|堆栈最小大小|  
  
### 返回值：
  
成功返回0，失败返回相应的错误码。  
  
### 错误码：
  
- EINVAL ： 参数无效。  
  
### 备注：
  
无。  
  
### 样例：
  
```c  
int main (int argc, char **argv)  
{  
	int          ret;  
	pthread_attr_t attr;  
	size_t len = 0;  
  
  
	ret = pthread_attr_getstacksize(&attr, &len);  
	if(ret != 0)  
	{  
		perror("func pthread_attr_getstacksize error\n");  
		return -1;  
	}  
	printf("attr stacksize :%lu\n", len);  
	......  
    return  (0);  
}  
```  
  
  
## pthread_attr_setstacksize() ：设置任务的stacksize属性
  
### 函数原型：
  
```c  
int  pthread_attr_setstacksize  
(  
	pthread_attr_t  *pattr,   
	size_t  stSize  
);  
```  
  
  
### 描述：
  
该接口用来设置指定任务属性块的stacksize属性。stacksize属性指定了任务栈的堆栈大小。  
  
### 参数：
  
- pattr ：需要设置的任务属性块指针。  
- stSize ：堆栈大小。  
  
|宏定义|宏值|解释|  
|---|---|---|  
|PTHREAD_STACK_MIN|8*1024|堆栈最小大小|  
  
### 返回值：
  
成功返回0，失败返回相应的错误码。  
  
### 错误码：
  
- EINVAL ： 参数无效。  
  
### 备注：
  
无。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <unistd.h>  
int main (int argc, char **argv)  
{  
	int ret  ;  
	pthread_attr_t attr;  
  
	ret = pthread_attr_setstacksize(&attr, 1024 * 10);  
	if(ret != 0)  
	{  
		perror("func pthread_attr_setstacksize error\n");  
		return -1;  
	}  
	......  
	return  (0);  
  
}  
```  
  
## pthread_attr_getstackfilled() ：获得线程属性块栈填充特性
  
### 函数原型：
  
```c  
int  pthread_attr_getstackfilled  
(  
	const pthread_attr_t *pattr,  
	int *stackfilled  
);  
```  
  
  
### 描述：
  
该接口用来获取指定任务属性块的stackfilled属性。stackfilled属性指定了任务栈的填充属性。  
  
### 参数：
  
- pattr ：指向任务属性块的指针。  
- stackfilled ：堆栈填充设置。  
  
|宏定义|宏值|解释|  
|---|---|---|  
|PTHREAD_NO_STACK_FILLED|0|任务不填充任务栈|  
|PTHREAD_STACK_FILLED|1|任务以0xee填充任务栈|  
  
### 返回值：
  
成功返回0，失败返回相应的错误码。  
  
### 错误码：
  
- EINVAL ： 参数无效。  
  
### 备注：
  
无。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <unistd.h>  
int main (int argc, char **argv)  
{  
	int            ret ;  
	pthread_attr_t  attr;  
	int stackfilled;  
  
	ret = pthread_attr_init(&attr);  
	if(ret != 0)  
	{  
		perror("func pthread_attr_init error");  
		return -1;  
	}  
	......  
	ret = pthread_attr_getstackfilled(&attr, &stackfilled);  
	if(ret != 0)  
	{  
		perror("func pthread_attr_getstackfilled error");  
		return -1;  
	}  
	printf("attr stackfilled %d\n", stackfilled);  
	......  
    return  (0);  
}  
```  
  
## pthread_attr_setstackfilled() ：设置线程属性块栈填充特性
  
### 函数原型：
  
```c  
int  pthread_attr_setstackfilled  
(  
	pthread_attr_t *pattr,   
	int stackfilled  
);  
```  
  
### 描述：
  
该接口用来设置指定任务属性块的stackfilled属性。stackfilled属性指定了任务栈的填充属性。  
  
### 参数：
  
- pattr ：指向任务属性块的指针。  
- stackfilled ：任务栈填充设置。  
  
|宏定义|宏值|解释|  
|---|---|---|  
|PTHREAD_NO_STACK_FILLED|0|任务不填充任务栈|  
|PTHREAD_STACK_FILLED|1|任务以0xee填充任务栈|  
  
### 返回值：
  
成功返回0，失败返回相应的错误码。  
  
### 错误码：
  
- EINVAL ： 参数无效。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <unistd.h>  
int main (int argc, char **argv)  
{  
	int ret  ;  
	pthread_attr_t attr;  
  
	ret = pthread_attr_init(&attr);  
	if(ret != 0)  
	{  
		perror("func pthread_attr_init error");  
		return -1;  
	}  
	......  
	ret = pthread_attr_setstackfilled(&attr, PTHREAD_STACK_FILLED);  
	if(ret != 0)  
	{  
		perror("func pthread_attr_setstackfilled error");  
		return -1;  
	}  
	......  
	return  (0);  
}  
```  
  
  
## pthread_attr_getname() ：获取任务的name属性
  
### 函数原型：
  
```c  
int  pthread_attr_getname   
(  
	const pthread_attr_t  *pattr,   
	char  **ppcName  
);  
```  
  
### 描述：
  
该接口用于获取任务属性对象pattr中的name属性。name属性指定了创建任务的名字。默认创建名为”pthread”。  
  
### 参数：
  
- pattr ：指向任务属性块的指针。  
- ppcName ：存放名字属性的指针  
  
### 返回值：
  
成功返回0，失败返回相应的错误码。  
  
### 错误码：
  
- EINVAL ： 参数无效。  
  
### 备注：
  
无。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <unistd.h>  
int main (int argc, char **argv)  
{  
	int ret  ;  
	pthread_attr_t attr;  
	char *name = malloc(128);  
  
	ret = pthread_attr_init(&attr);  
	if(ret != 0)  
	{  
		perror("func pthread_attr_init error");  
		return -1;  
	}  
  
	......  
  
	ret = pthread_attr_getname(&attr, &name);  
	if(ret != 0)  
	{  
		perror("func pthread_attr_getname error");  
		return -1;  
	}  
	printf("attr name: %s\n", name);  
	......  
   return  (0);  
}  
```  
  
  
## pthread_attr_setname() ：设置任务的name属性
  
### 函数原型：
  
```c  
int  pthread_attr_setname  
(  
	pthread_attr_t  *pattr,   
	const char  *pcName  
);  
```  
  
  
### 描述：
  
该接口用来设置任务属性对象pattr中的name属性。name属性指定了创建任务的名字。默认创建名为pthread。  
  
### 参数：
  
- pattr ：指向任务属性块的指针。  
- pcName ：需要设置的任务名称。  
  
### 返回值：
  
成功返回0，失败返回相应的错误码。  
  
### 错误码：
  
- EINVAL ： 参数无效。  
  
### 备注：
  
无。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <unistd.h>  
int main (int argc, char **argv)  
{  
	int          ret ;  
	pthread_attr_t attr;  
  
	ret = pthread_attr_init(&attr);  
	if(ret != 0)  
	{  
		perror("func pthread_attr_init error");  
		return -1;  
	}  
	......  
	ret = pthread_attr_setname(&attr, "SylixOS pthread");  
	if(ret != 0)  
	{  
		perror("func pthread_attr_setname error");  
		return -1;  
	}  
  
	......  
  
   return  (0);  
}  
```  
  
  
## pthread_attr_getbreakallowed() ：获得线程属性块是否允许断点
  
### 函数原型：
  
```c  
int  pthread_attr_getbreakallowed   
(  
	const pthread_attr_t *pattr,  
	int *breakallowed  
);  
```  
  
### 描述：
  
该接口用于获取任务属性块对象breakallowed属性。breakallowed属性指定了任务是否忽略断点状态。  
  
### 参数：
  
- pattr ：指向属性块的指针。  
- breakallowed ：存放是否忽略断点的指针。  
  
|宏定义|宏值|解释|  
|---|---|---|  
|PTHREAD_BREAK_ALLOWED|1|不忽略断点|  
|PTHREAD_BREAK_DISALLOWED|0|忽略断点|  
  
默认值为PTHREAD_BREAK_ALLOWED（1）。  
  
### 返回值：
  
成功返回0，失败返回相应的错误码。  
  
### 错误码：
  
- EINVAL ： 参数无效。  
  
### 备注：
  
无。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <unistd.h>  
int main (int argc, char **argv)  
{  
	Int          ret ;  
	pthread_attr_t attr;  
	int breakallowed;  
  
	ret = pthread_attr_init(&attr);  
	if(ret != 0)  
	{  
		perror("func pthread_attr_init error");  
		return -1;  
	}  
	......  
	ret = pthread_attr_getbreakallowed(&attr, &breakallowed);  
	if(ret != 0)  
	{  
		perror("func pthread_attr_getbreakallowed error");  
		return -1;  
	}  
	printf("attr breakallowed %d\n", breakallowed);  
	......  
   return  (0);  
  
```  
  
## pthread_attr_setbreakallowed() ：设置任务的breakallowed属性
  
### 函数原型：
  
```c  
int  pthread_attr_setbreakallowed   
(  
	pthread_attr_t *pattr,   
	int breakallowed  
);  
```  
  
  
### 描述：
  
该接口用于设置任务属性块对象breakallowed属性。breakallowed属性指定了任务是否忽略断点状态。  
  
### 参数：
  
- pattr ：指向属性块的指针。  
- breakallowed ：设置是否忽略断点的值。  
  
|宏定义|宏值|解释|  
|---|---|---|  
|PTHREAD_BREAK_ALLOWED|1|不忽略断点|  
|PTHREAD_BREAK_DISALLOWED|0|忽略断点|  
  
默认值为PTHREAD_BREAK_ALLOWED（1）。这里只允许取值为PTHREAD_BREAK_ALLOWED（1）。  
  
### 返回值：
  
成功返回0，失败返回相应的错误码。  
  
### 错误码：
  
- EINVAL ： 参数无效。  
  
### 备注：
  
无。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <unistd.h>  
int main (int argc, char **argv)  
{  
	int            ret ;  
	pthread_attr_t  attr;  
  
	ret = pthread_attr_init(&attr);  
	if(ret != 0)  
	{  
		perror("func pthread_attr_init error");  
		return -1;  
	}  
	......  
	ret = pthread_attr_setbreakallowed(&attr, PTHREAD_BREAK_ALLOWED);  
	if(ret != 0)  
	{  
		perror("func pthread_attr_setbreakallowed error");  
		return -1;  
	}  
	......  
    return  (0);  
}  
```  
  
## pthread_attr_getfpallowed() ：获得线程属性块是否允许浮点
  
### 函数原型：
  
```c  
int  pthread_attr_getfpallowed   
(  
	const pthread_attr_t *pattr,  
	int *fpallowed  
);  
```  
  
### 描述：
  
该接口用于获取任务属性块对象的pallowed属性。pallowed属性指定了是否允许浮点。  
  
### 参数：
  
- pattr ：指向线程属性的指针。  
- fpallowed ：存放是否允许浮点的值。  
  
|宏定义|宏值|解释|  
|---|---|---|  
|PTHREAD_FP_ALLOWED|1|允许浮点|  
|PTHREAD_FP_DISALLOWED|0|不允许浮点|  
  
### 返回值：
  
成功返回0，失败返回相应的错误码。  
  
### 错误码：
  
- EINVAL ： 参数无效。  
  
### 备注：
  
无。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <unistd.h>  
int main (int argc, char **argv)  
{  
	int ret ;  
	int fpallowed;   
	pthread_attr_t attr;  
  
	ret = pthread_attr_init(&attr);  
	if(ret != 0)  
	{  
		perror("func pthread_attr_init error");  
		return -1;  
	}  
	......  
	ret =  pthread_attr_getfpallowed(&attr, &fpallowed);  
	if(ret != 0)  
	{  
		perror("func  pthread_attr_getfpallowed error");  
		return -1;  
	}  
	printf("attr fpallowed %d\n", fpallowed);  
	......  
   return  (0);  
}  
```  
  
## pthread_attr_setfpallowed() ：设置任务的pallowed属性
  
  
### 函数原型：
  
```c  
int  pthread_attr_setfpallowed   
(  
	pthread_attr_t *pattr,   
	int fpallowed  
);	  
```  
  
### 描述：
  
该接口用于设置任务属性块对象的pallowed属性。pallowed属性指定了是否允许浮点。  
  
### 参数：
  
- pattr ：指向任务属性块对象的指针。  
- fpallowed ：设置是否允许浮点的值。  
  
|宏定义|宏值|解释|  
|---|---|---|  
|PTHREAD_FP_ALLOWED|1|允许浮点|  
|PTHREAD_FP_DISALLOWED|0|不允许浮点|  
  
### 返回值：
  
成功返回0，失败返回相应的错误码。  
  
### 错误码：
  
- EINVAL ： 参数无效。  
  
### 备注：
  
无。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <unistd.h>  
int main (int argc, char **argv)  
{  
	int ret  ;  
	pthread_attr_t attr;  
  
	ret = pthread_attr_init(&attr);  
	if(ret != 0)  
	{  
		perror("func pthread_attr_init error");  
		return -1;  
	}  
	......  
	ret =  pthread_attr_setfpallowed(&attr, PTHREAD_FP_ALLOWED);  
	if(ret != 0)  
	{  
		perror("func  pthread_attr_setfpallowed error");  
		return -1;  
	}  
	......  
    return  (0);  
}  
```  
  
## pthread_attr_getinheritsched() ：获取属性块继承调度策略的方式
  
### 函数原型：
  
```c  
int  pthread_attr_getinheritsched   
(  
	const pthread_attr_t  * pattr,  
	int  * piInherit  
);  
```  
  
### 描述：
  
该接口用于获取任务属性块的inheritsched属性。inheritsched属性指定了继承调度策略的方式。调度策略默认值为PTHREAD_EXPLICIT_SCHED（1）。PTHREAD_INHERIT_SCHED表示新任务的调度属性继承父任务的调度属性，而attr中的调度属性被忽略；PTHREAD_EXPLICIT_SCHED表示新任务的调度属性应设置成任务属性对象中对应的属性值。  
  
### 参数：
  
- pattr ：指向任务属性块的指针。  
- piInherit ：获取创建线程的继承属性。  
  
|宏定义|宏值|解释|  
|---|---|---|  
|PTHREAD_INHERIT_SCHED|1|任务的调度策略及调度属性从调用任务中继承|  
|PTHREAD_EXPLICIT_SCHED|2|任务的调度策略及调度属性由任务属性对象设置|  
  
### 返回值：
  
成功返回0，失败返回相应的错误码。  
  
### 错误码：
  
- EINVAL ： 参数无效。  
  
### 备注：
  
无。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <unistd.h>  
int main (int argc, char **argv)  
{  
	int ret  ;  
	pthread_attr_t attr;  
	int iInherit;  
  
	ret = pthread_attr_init(&attr);  
	if(ret != 0)  
	{  
		perror("func pthread_attr_init error");  
		return -1;  
	}  
	......  
	ret = pthread_attr_getinheritsched(&attr, &iInherit);  
	if(ret != 0)  
	{  
		perror("func pthread_attr_getinheritsched error\n");  
		return -1;  
	}  
	printf("attr iInherit %d\n", iInherit);  
	......  
    return  (0);  
}  
```  
  
## pthread_attr_setinheritsched() ：设置属性块继承调度策略的方式
  
  
### 函数原型：
  
```c  
int  pthread_attr_setinheritsched   
(  
	pthread_attr_t  *pattr,  
	int  iInherit  
);  
```  
  
### 描述：
  
该接口用于设置任务属性块的inheritsched属性。inheritsched属性指定了继承调度策略的方式。  
  
### 参数：
  
- pattr ：指向任务属性块的指针。  
- iInherit ：设置创建线程的继承属性。  
  
|宏名|解释|  
|---|---|  
|PTHREAD_INHERIT_SCHED|继承调度参数|  
|PTHREAD_EXPLICIT_SCHED|明确指定调度参数|  
  
### 返回值：
  
成功返回0，失败返回相应的错误码。  
  
### 错误码：
  
- EINVAL ： 参数无效。  
- ENOTSUP ：系统不支持的操作。  
  
### 备注：
  
GJB 7714-2012错误码返回只有：  
- EINVAL ： 参数无效。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <unistd.h>  
int main (int argc, char **argv)  
{  
	int ret;  
	pthread_attr_t attr;  
  
	ret = pthread_attr_init(&attr);  
	if(ret != 0)  
	{  
		perror("func pthread_attr_init error");  
		return -1;  
	}  
	......  
	ret = pthread_attr_setinheritsched(&attr, PTHREAD_EXPLICIT_SCHED);  
	if(ret != 0)  
	{  
		perror("func pthread_attr_setinheritsched error\n");  
		return -1;  
	}  
  
	......  
  
    return  (0);  
}  
```  
  
## pthread_attr_getschedpolicy() ：获取一个线程属性块的调度策略
  
### 函数原型：
  
```c  
int  pthread_attr_getschedpolicy   
(  
	const pthread_attr_t  *pattr,   
	int  *piPolicy  
);  
```  
  
  
### 描述：
  
该接口用来获取任务属性块的schedpolicy属性。schedpolicy属性指定了任务的调度策略。  
  
### 参数：
  
- pattr ：指向任务属性块的指针。  
- piPolicy ：存放调度策略的指针。  
  
|宏定义|宏值|解释|  
|---|---|---|  
|SCHED_OTHER|0|优先级抢占的调度策略|  
|SCHED_FIFO|1|先进先出的调度策略|  
  
### 返回值：
  
成功返回0，失败返回相应的错误码。  
  
### 错误码：
  
- EINVAL ： 参数无效。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <unistd.h>  
int main (int argc, char **argv)  
{  
	int ret  ;  
	pthread_attr_t attr;  
	int ipolicy ;  
  
	ret = pthread_attr_init(&attr);  
	if(ret != 0)  
	{  
		perror("func pthread_attr_init error");  
		return -1;  
	}  
	......  
	ret = pthread_attr_getschedpolicy(&attr, &ipolicy);  
	if(ret != 0)  
	{  
		perror("func pthread_attr_getschedpolicy error");  
		return -1;  
	}  
	printf("attr ipolicy is :%d\n", ipolicy);  
	......  
    return  (0);  
}  
```  
  
## pthread_attr_setschedpolicy() ：设置一个线程属性块的调度策略
  
### 函数原型：
  
```c  
int  pthread_attr_setschedpolicy   
(  
	pthread_attr_t  *pattr,   
	int  iPolicy  
);  
```  
  
  
### 描述：
  
该接口用来设置任务属性块的schedpolicy属性。schedpolicy属性指定了任务的调度策略。  
  
### 参数：
  
- pattr ：指向任务属性块的指针。  
- iPolicy ：设置的调度策略。  
  
|宏名|解释|  
|---|---|  
|SCHED_OTHER|抢占式调度算法|  
|SCHED_FIFO|先进先出调度算法|  
|SCHED_RR|不支持轮转调度算法|  
|SCHED_SPORADIC|不支持|  
  
### 返回值：
  
成功返回0，失败返回相应的错误码。  
  
### 错误码：
  
- EINVAL ： 参数无效。  
- ENOTSUP ：系统不支持的操作。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <unistd.h>  
int main (int argc, char **argv)  
{  
	int ret  ;  
	pthread_attr_t attr;  
	struct sched_param param;  
  
	ret = pthread_attr_init(&attr);  
	if(ret != 0)  
	{  
		perror("func pthread_attr_init error");  
		return -1;  
	}  
  
	......  
  
	ret = pthread_attr_setschedpolicy(&attr,SCHED_FIFO);  
    if(ret != 0)  
    {  
        perror("func pthread_attr_setschedpolicy error");  
        return -1;  
    }  
	......  
    return  (0);  
}  
```  
  
## pthread_attr_getdetachstate() ：获取线程属性块的分离联合状态
  
  
### 函数原型：
  
```c  
int  pthread_attr_getdetachstate   
(  
	const pthread_attr_t  *pattr,   
	int  *piDetachState  
);   
```  
### 描述：
  
该接口用于获取任务属性对象pattr的detachstate属性。detachstate属性指定了任务的分离联合状态。处于分离状态的任务，调用pthread_detach接口和pthread_join接口会失败。初始化默认为PTHREAD_CREATE_JOINABLE（1）联合状态。  
  
### 参数：
  
- pattr ：需要获取的任务属性块指针。  
- piDetachState ：存放任务分离状态属性的指针。  
  
|宏定义|宏值|解释|  
|---|---|---|  
|PTHREAD_CREATE_DETACHED|0|定义任务创建时与其他任务的关系：分离状态|  
|PTHREAD_CREATE_JOINABLE|1|定义任务创建时与其他任务的关系：联合状态|  
  
### 返回值： 
  
成功返回0，失败返回相应的错误码。  
  
### 错误码：
  
- EINVAL ：参数无效。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <unistd.h>  
int main (int argc, char **argv)  
{  
	int ret  ;  
	pthread_attr_t attr;  
	int DetachState = 0;  
  
	......  
	ret = pthread_attr_init(&attr);  
	if(ret != 0)  
	{  
		perror("func pthread_attr_init error");  
		return -1;  
	}  
  
	ret = pthread_attr_getdetachstate(&attr, &DetachState);  
	if(ret != 0)  
	{  
		perror("func pthread_attr_getdetachstate error\n");  
		return -1;  
	}  
	printf("attr detachstate is :%d\n", DetachState);  
	......  
   return  (0);  
}  
```  
  
  
## pthread_attr_setdetachstate() ：设置任务的分离联合状态属性
  
  
### 函数原型：
  
```c  
int  pthread_attr_setdetachstate   
(  
	pthread_attr_t  *pattr,   
	int  iDetachState  
);  
```  
  
### 描述：
  
该接口用于设置任务属性对象pattr的detachstate属性。detachstate属性指定了任务的分离联合状态。处于分离状态的任务，调用pthread_detach接口和pthread_join接口会失败。  
  
### 参数： 
  
- pattr ：指向任务属性对象的指针。  
- iDetachState ：需要设置的任务分离状态属性。  
  
|宏定义|宏值|解释|  
|---|---|---|  
|PTHREAD_CREATE_DETACHED|0|定义任务创建时与其他任务的关系：分离状态|  
|PTHREAD_CREATE_JOINABLE|1|定义任务创建时与其他任务的关系：联合状态|  
  
### 返回值：
  
成功返回0，失败返回相应的错误码。  
  
### 错误码：
  
- EINVAL ： 参数无效。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <unistd.h>  
int main (int argc, char **argv)  
{  
	int ret  ;  
	pthread_attr_t attr;  
  
	ret = pthread_attr_init(&attr);  
	if(ret != 0)  
	{  
		perror("func pthread_attr_init error");  
		return -1;  
	}  
	......  
	ret = pthread_attr_setdetachstate(&attr, PTHREAD_CREATE_JOINABLE);  
	if(ret != 0)  
	{  
		perror("func pthread_attr_setdetachstate error\n");  
		return -1;  
	}  
	......  
    return  (0);  
}  
```  
  
  
## pthread_create() ：创建任务
  
  
### 函数原型：
  
```c  
int  pthread_create  
(  
	pthread_t    *pthread,   
	const pthread_attr_t  *pattr,   
	void   *(*start_routine)(void *),  
	void   *arg  
);  
```  
  
### 描述：
  
该接口使用指定的属性pattr创建一个新的任务，如果pattr为NULL，则使用默认的任务属性。任务创建成功，pthread_create()接口通过参数thread返回创建任务的ID。被创建的任务使用参数arg作为start_routine执行的唯一参数。Star_routine返回效果相当于使用start_routine的返回值作为退出状态调用pthread_exit().  
  
### 参数：
  
- pthread ：存放任务id的指针  
- pattr ：指向任务属性对象的指针  
- start_routine ：返回值和参数都是void*的任务执行函数指针  
- arg ：任务执行函数的参数  
  
### 返回值：
  
成功返回0，失败返回相应的错误码。  
  
### 错误码：
  
- EINVAL ：参数无效。  
- EAGAIN ：系统资源不足。  
- ENOMEM ：内存不足。  
  
### 备注：
  
GJB 7714-2012错误码返回只有：  
- EINVAL ：参数无效。  
- EAGAIN ：系统资源不足。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <unistd.h>  
static pthread_t tid1, tid2;  
int main (int argc, char **argv)  
{  
    int  ret;  
     
// ……  
  
    ret = pthread_create(&tid1, &attr, Thread1, NULL);  
    if(ret != 0)  
    {  
        perror("func pthread_create error");  
        return -1;  
	}  
// ……  
  return 0;  
}  
```  
  
  
## pthread_cancel() ：取消任务
  
### 函数原型：
  
```c  
int  pthread_cancel   
(  
	pthread_t  thread  
);  
```  
  
  
### 描述：
  
该接口请求取消thread指定的任务。任务的取消状态和类型决定了何时执行取消操作。如果任务的取消操作被触发，任务的取消处理函数就会被调用。  
  
### 参数：
  
- thread ：需要取消的任务的ID。  
  
### 返回值：
  
成功返回0，失败返回相应的错误码。  
  
### 错误码：
  
- ERROR_KERNEL_HANDLE_NULL ：句柄出错。  
- ERROR_THREAD_NULL ：线程句柄无效。  
- ERROR_THREAD_DISCANCEL ：线程设置了 DISCANCEL 标志。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <unistd.h>  
static void * Thread1(void *arg)  
{  
	int ret = 0;  
  
	// ……  
	ret = pthread_cancel(tid2);  
	if(ret != 0)  
	{  
		perror("func pthread_cancel error\n");  
		return (PVOID)-1;  
	}  
	return NULL;  
}  
```  
  
  
## pthread_exit() ：退出当前任务
  
  
### 函数原型：
  
```c  
void pthread_exit   
(  
	void *status  
);  
```  
  
  
### 描述：
  
该接口终止当前任务，同时是status的值对于任意等待该任务终止的其他任务可用。任务终止过程中，那些已经压栈但还没出栈的取消处理函数都会以入栈相反的顺序出栈并执行。如果任务有私有数据，那么在所有的取消处理函数被执行后，任务会调用这些私有数据的析构函数。任务终止会释放一些系统资源，如互斥信号量和文件描述符等。任务终止后，访问任务的本地变量的结果时不确定的。因此，任务的本地变量不能用作接口pthread_exit()的参数value_ptr的值。  
  
### 参数：
  
- status ：任务返回值。  
  
### 返回值：
  
无。  
  
### 错误码：
  
- ERROR_KERNEL_IN_ISR ：系统处于中断中。  
- ERROR_KERNEL_NOT_RUNNING ：系统没有运行。  
- ERROR_KERNEL_HANDLE_NULL ：句柄出错。  
- ERROR_THREAD_NULL ：线程句柄无效。  
- ERROR_THREAD_OTHER_DELETE ：已经有其他线程在等待删除。  
  
### 备注：
  
GJB 7714-2012错误码为无。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <unistd.h>  
static void * Thread1(void *arg)  
{  
	// ……  
  
 	pthread_exit (NULL);  
	return NULL;  
}  
```  
  
  
## pthread_getid() ：获取任务标识符（ID）
  
### 函数原型：
  
```c  
int  pthread_getid  
(  
	const char *name,  
	pthread_t *pthread  
);  
```  
  
### 描述：
  
该接口用于获取与指定的任务名相匹配的任务ID。  
  
### 参数：
  
- name ：任务名  
- pthread ：存放任务ID的指针  
  
### 返回值：
  
成功返回0，失败返回相应的错误码。  
  
### 错误码：
  
- EINVAL ：参数无效。  
- ESRCH ：线程不存在。  
  
### 备注：
  
GJB 7714-2012错误码为：  
- ESRCH ：线程不存在。  
- EINVAL ：参数无效。  
- RTYPENOTMATCH ：任务类型不匹配。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <unistd.h>  
static void * Thread1(void *arg)  
{  
	pthread_t tid;  
	int ret;  
  
	// ……  
  
	ret = pthread_getid("pthread", &tid);  
	if(ret != 0)  
	{  
		printf("func pthread_getid error\n");  
		return (PVOID)-1;  
	}  
	printf("name is t_tshell pthread id:%d\n", tid);  
	// ……  
	return NULL;  
}  
```  
  
## pthread_getname() ：获取任务名字
  
### 函数原型：
  
```c  
int  pthread_getname  
(  
	pthread_t thread,  
	char *name  
);  
```  
  
  
### 描述：
  
该接口用于获取指定ID任务的线程名。  
  
### 参数：
  
- thread ：指定线程ID指针。  
- name ：用户获取线程的名字。  
  
### 返回值：
  
成功返回0，失败返回相应的错误码。  
  
### 错误码：
  
- EINVAL ：参数无效。  
- ESRCH ：线程不存在  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <unistd.h>  
static void * Thread1(void *arg)  
{  
	int ret = 0;  
 	char name[32];  
	// ……  
	ret = pthread_getname(tid1, name);                                       
	if(ret != 0)  
   {  
        perror("func pthread_getname error\n");  
        return (PVOID)-1;  
   }  
	printf("name is %s \n",name);	  
// ……  
	return NULL;  
}  
```  
  
## pthread_self() ：获取调用任务自身的标识符（ID）
  
### 函数原型：
  
```c  
pthread_t  pthread_self  
(  
	void  
);  
```  
  
  
### 描述：
  
该接口用来获取调用任务的ID。  
  
### 参数：
  
无。  
  
### 返回值：
  
成功返回当前线程句柄；失败返回0，并置错误码。  
  
### 错误码：
  
- ERROR_KERNEL_IN_ISR ：系统处于中断中。  
  
### 备注：
  
GJB 7714-2012错误码为：无。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <unistd.h>  
static void * Thread2(void *arg)  
{  
	pthread_t tid;  
  
	// ……  
    tid = pthread_self();  
    if(tid == 0)  
    {  
        perror("func pthread_self error\n");  
        return (PVOID)-1;  
    }  
    printf("pthread_self is %d \n",(int)tid);  
	// ……  
  
	return NULL;  
}  
```  
  
  
## pthread_suspend() ：挂起任务
  
### 函数原型：
  
```c  
int  pthread_suspend  
(  
	pthread_t thread  
);  
```  
  
  
### 描述：
  
该接口用来挂起一个指定的任务，如果thread取值为零，则调用任务自身被挂起。任务的挂起状态是附加的，即任务可以是睡眠状态和挂起状态共存，或阻塞状态和挂起状态并存。  
  
### 参数：
  
- thread ：要挂起线程的ID。  
  
### 返回值：
  
成功返回0，失败返回相应的错误码。  
  
### 错误码：
  
- EINVAL ：参数无效。  
- ERROR_KERNEL_HANDLE_NULL ：句柄出错。  
- ERROR_THREAD_NULL ：线程句柄无效。  
- ERROR_KERNEL_IN_ISR ：系统处于中断中。  
  
### 备注：
  
GJB 7714-2012错误码为：  
- ESRCH ：线程不存在。  
- EINVAL ：参数无效。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <unistd.h>  
static void * Thread1(void *arg)  
{  
	int ret = 0;  
  
	// ……  
	ret = pthread_suspend(pthread_self());  
	if(ret != 0)  
	{  
		printf("func pthread_suspend error\n");  
		return (PVOID)-1;  
	}  
	// ……  
  
	return NULL;  
}  
```  
  
## pthread_resume() ：唤醒任务
  
### 函数原型：
  
```c  
int  pthread_resume  
(  
	pthread_t thread  
);  
```  
  
### 描述：
  
该接口恢复指定的任务。挂起状态解除，任务恢复到被挂起前的状态。  
  
### 参数：
  
- thread ：要唤醒任务的ID。  
  
### 返回值：
  
成功返回0，失败返回相应的错误码。  
  
### 错误码：
  
- EINVAL ：参数无效。  
- ERROR_KERNEL_HANDLE_NULL ：句柄出错。  
- ERROR_THREAD_NULL ：线程句柄无效。  
- ERROR_KERNEL_IN_ISR ：系统处于中断中。  
  
### 备注：
  
GJB 7714-2012错误码为：  
- ESRCH ：线程不存在。  
- EINVAL ：参数无效。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <unistd.h>  
static void * Thread2(void *arg)  
{  
	int ret = 0;  
  
	// ……  
	ret = pthread_resume(tid1);  
	if(ret != 0)  
	{  
		printf("func pthread_resume error\n");  
		return (PVOID)-1;  
	}  
	// ……  
  
	return NULL;  
}  
```  
  
  
## pthread_delay() ：任务延迟
  
### 函数原型：
  
```c  
int  pthread_delay   
(  
	int  ticks  
);  
```  
  
  
### 描述：
  
该接口是调用任务在指定时间（以tick为单位）内放弃CPU。  
  
### 参数：
  
- ticks ：任务延迟的tick数。值应为正值或零，为0时任务主动将CPU让给同等优先级的任务运行，负值不延时。  
  
### 返回值：
  
成功返回0，失败返回相应的错误码。  
  
### 错误码：
  
- ENOTSUP ：系统不支持的操作。  
- ERROR_KERNEL_IN_ISR ：系统处于中断中。  
- EINTR ：线程收到一个非阻塞或忽略的信号。  
  
### 备注：
  
GJB 7714-2012错误码为：  
- ENOTSUP ：系统不支持设置。  
- EINTR ：线程收到一个非阻塞或忽略的信号。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <unistd.h>  
static void * Thread2(void *arg)  
{  
	int ret = 0;  
  
	// ……  
	ret = pthread_delay(100);  
	if(ret != 0)  
{  
		printf("func pthread_delay error\n");  
		return (PVOID)-1;  
	}  
	// ……  
  
	return NULL;  
}  
```  
  
## thread_lock() ：禁止任务调度
  
### 函数原型：
  
```c  
int  pthread_lock   
(  
	void  
)  
```  
  
### 描述：
  
该接口用于禁止上下文切换。调用该接口的任务不允许被抢占，除非调用任务使自己不处于就绪状态而主动放弃CPU。  
  
### 参数：
  
无。  
  
### 返回值：
  
成功返回0，失败返回相应的错误码。  
  
### 错误码：
  
- ECALLEDINISR ：系统处于中断中。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <unistd.h>  
static void * Thread2(void *arg)  
{  
	int ret = 0;  
  
	// ……  
  
	ret = pthread_lock();  
	if(ret != 0){  
		printf("func pthread_lock error\n");  
		return (PVOID)-1;  
	}  
	// ……  
	ret = pthread_unlock();  
	if(ret != 0)  
	{  
		printf("func pthread_unlock error\n");  
		return (PVOID)-1;  
	}  
	// ……  
  
	return NULL;  
}  
```  
  
## pthread_unlock() ：解除任务调度
  
### 函数原型：
  
```c  
int  pthread_unlock   
(  
	void  
);  
```  
  
  
### 描述：
  
解锁任务调度，允许任务调度进程或线程，减少任务锁定的次数。该接口必须与pthread_lock接口配对使用。  
  
### 参数：
  
无。  
  
### 返回值： 
  
成功返回0，失败返回相应的错误码。  
  
### 错误码：
  
- ECALLEDINISR ：系统处于中断中。  
  
### 备注：
  
无。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <unistd.h>  
static void * Thread2(void *arg)  
{  
	int ret = 0;  
  
	// ……  
	ret = pthread_lock();  
	if(ret != 0)  
	{  
		printf("func pthread_lock error\n");  
		return (PVOID)-1;  
	}  
	// ……  
	ret = pthread_unlock();  
	if(ret != 0)  
	{  
		printf("func pthread_unlock error\n");  
		return (PVOID)-1;  
	}  
	// ……  
  
	return NULL;  
}  
```  
  
## pthread_is_ready() ：判断一个任务的状态是否就绪
  
### 函数原型：
  
```c  
boolean  pthread_is_ready  
(  
	pthread_t thread  
);  
```  
  
  
### 描述：
  
该接口用于判断一个任务的状态是否为就绪状态。  
  
### 参数：
  
- thread ：任务ID。  
  
### 返回值：
  
就绪返回1，没有就绪返回0；失败返回-1，并置错误码。  
  
### 错误码：
  
- ESRCH ：线程不存在。  
- ERROR_KERNEL_HANDLE_NULL ：句柄出错。  
- ERROR_THREAD_NULL ：线程句柄无效。  
- ERROR_KERNEL_IN_ISR ：系统处于中断中。  
  
### 备注：
  
GJB 7714-2012错误码为：  
- ESRCH ：线程不存在。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <unistd.h>  
static void * Thread1(void *arg)  
{  
	int ret = 0;  
  
	// ……  
	ret = pthread_is_ready(tid2);  
	if(ret == 1)  
	{  
		......  
	}  
	// ……  
	return NULL;  
}  
```  
  
## pthread_is_suspend() :判断一个任务是否被挂起
  
### 函数原型：
  
```c  
boolean  pthread_is_suspend   
(  
	pthread_t thread  
)  
```  
  
  
### 描述：
  
该接口用于判断一个任务的状态是否为挂起状态。  
  
### 参数：
  
- thread ：任务ID。  
  
### 返回值：
  
挂起返回1， 没挂起返回0；失败返回-1，并置错误码。  
  
### 备注：
  
无。  
  
### 错误码：
  
- ESRCH ：线程不存在。  
- ERROR_KERNEL_HANDLE_NULL ：句柄出错。  
- ERROR_THREAD_NULL ：线程句柄无效。  
- ERROR_KERNEL_IN_ISR ：系统处于中断中。  
  
### 样例：
  
```c  
static void * Thread1(void *arg)  
{  
	int ret = 0;  
  
	// ……  
	ret = pthread_is_suspend(tid2);  
	if(ret == 1)  
	{  
		......  
	}  
	// ……  
	return NULL;  
}  
```  
  
## pthread_testcancel() ：检测线程是否有请求取消标志
  
### 函数原型：
  
```c  
void  pthread_ testcancel  
(  
	void  
);  
```  
  
  
### 描述：
  
该接口在调用任务中创建一个取消点。如果任务不允许被取消，则该接口对任务无影响。  
  
### 参数：
  
无。  
  
### 返回值：
  
无。  
  
### 错误码：
  
- ERROR_KERNEL_IN_ISR ：系统处于中断中。  
- ERROR_KERNEL_NOT_RUNNING ：系统没有运行。  
- ERROR_KERNEL_HANDLE_NULL ：句柄出错。  
- ERROR_THREAD_NULL ：线程句柄无效。  
- ERROR_THREAD_OTHER_DELETE ：已经有其他线程在等待删除。  
  
### 备注：
  
无。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <unistd.h>  
static void * Thread1(void *arg)  
{  
	// ……  
 	pthread_ testcancel ();  
// ……  
	return NULL;  
}  
```  
  
## pthread_setcancelstate() ：设置任务取消状态是否使能
  
### 函数原型：
  
```c  
int  pthread_setcancelstate   
(	  
	int  newstate,   
	int  * poldstate  
);  
```  
  
### 描述：
  
该接口用于设定调用任务的取消状态为指定的newstate,并将旧状态通过poldstate返回。  
  
### 参数：
  
- newstate ：新的任务取消状态值  
- poldstate ：存放旧的任务取消状态的值  
  
|取消类型|说明|  
|---|---|  
|LW_THREAD_CANCEL_ASYNCHRONOUS|异步取消|  
|LW_THREAD_CANCEL_DEFERRED|延迟取消|  
  
### 返回值：
  
成功返回0，失败返回相应的错误码。  
  
### 错误码：
  
- EINVAL ：参数无效。  
  
### 备注：
  
无。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <unistd.h>  
static void * Thread1(void *arg)  
{  
	int ret = 0;  
  
	// ……  
	ret = pthread_setcancelstate(LW_THREAD_CANCEL_ENABLE, LW_NULL);  
	if(ret != 0)  
	{  
		perror("func pthread_setcancelstate error");  
		return (PVOID)-1;  
	}  
	// ……  
	return NULL;  
}  
```  
  
## pthread_setcanceltype() ：设置当前线程被动取消时的动作
  
### 函数原型：
  
```c  
int  pthread_setcanceltype   
(  
	int  newtype,  
	int  *poldtype  
);  
```  
  
### 描述：
  
该接口用于设定调用任务的取消类型为指定的newstate,并将旧状态通过poldstate返回。newstate的合法取值为  
  
### 参数：
  
- newstate ：新的任务取消类型值  
  
|取消类型|说明|  
|---|---|  
|LW_THREAD_CANCEL_ASYNCHRONOUS|异步取消|  
|LW_THREAD_CANCEL_DEFERRED|延迟取消|  
  
- poldstate ：存放旧的任务取消类型的值  
  
### 返回值：
  
成功返回0，失败返回相应的错误码。  
  
### 错误码：
  
- EINVAL ：参数无效。  
  
### 备注：
  
无。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <unistd.h>  
static void * Thread1(void *arg)  
{  
	int ret = 0;  
  
	// ……  
	ret = pthread_setcanceltype(LW_THREAD_CANCEL_DEFERRED, LW_NULL);  
	if(ret != 0)  
	{  
		perror("func pthread_setcanceltype error");  
		return (PVOID)-1;  
	}  
	// ……  
  
	return NULL;  
}  
```  
  
## pthread_cleanup_push() ：将一个清除函数压入函数堆栈
  
### 函数原型：
  
```c  
void  pthread_cleanup_push   
(  
	void (*pfunc)(void *),   
	void *arg  
);  
```  
  
  
### 描述：
  
该接口把指定的取消处理函数以堆栈的方式（FILO）存入任务控制块中，在以下时刻用arg作为参数，调用最近存放的函数。压入到任务的取消处理栈中，并会在：  
任务退出（即调用pthread_exit()）；  
任务触发了一个取消请求；  
任务用一个非零的execute参数调用pthread_cleanup_pop()；  
  
### 参数：
  
- pfunc ：需要处理的函数指针。  
- arg ：取消处理函数的参数。  
  
### 返回值：
  
无。  
  
### 错误码：
  
- ERROR_KERNEL_HOOK_NULL ：内核钩子出错。  
- ERROR_KERNEL_LOW_MEMORY ：缺少内存。  
  
### 备注：
  
GJB 7714-2012错误码为无。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <unistd.h>  
static void fun(void * arg)  
{  
	......  
}  
  
static void * Thread1(void *arg)  
{  
	int ret = 0;  
  
	......  
	pthread_cleanup_push(fun, NULL);  
 	......  
  
	return NULL;  
}  
```  
  
  
## pthread_cleanup_pop() ：将一个压栈函数运行并释放
  
### 函数原型：
  
```c  
void  pthread_cleanup_pop   
(  
	int  iNeedRun  
);  
```  
  
  
### 描述：
  
该接口以堆栈的移除方式取出保存的处理函数，并根据参数iNeedRun决定调用/忽略它。  
  
### 参数：
  
- iNeedRun ：标示是否执行取消处理函数。  
  
### 返回值：
  
无。  
  
### 错误码：
  
无。  
  
### 备注：
  
无。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <unistd.h>  
static void fun(void * arg)  
{  
	......  
}  
  
static void * Thread1(void *arg)  
{  
	int ret = 0;  
  
	......  
	pthread_cleanup_push(fun, NULL);  
 	......  
	pthread_cleanup_pop(1);     
	return NULL;  
}  
```  
  
  
## pthread_getschedprio() ：动态获取任务的优先级
  
### 函数原型：
  
```c  
int  pthread_getschedprio   
(  
	pthread_t  thread,   
	int  * prio  
);  
```  
  
  
### 描述：
  
该接口用于获取thread指定任务的调度优先级。并保存在参数prio中。pthread_create()创建任务时默认线程的调度优先级为55。  
  
### 参数：
  
- thread ：指定任务ID。  
- prio ：用于存放任务调度优先级的指针。  
  
### 返回值：
  
成功返回0，失败返回相应的错误码。  
  
### 错误码： 
  
- ESRCH ：线程不存在。  
- ERROR_KERNEL_HANDLE_NULL ：句柄出错。  
- ERROR_THREAD_NULL ：线程句柄无效。  
  
### 备注：
  
GJB 7714-2012错误码为：  
- ESRCH ：线程不存在。  
- EINVAL ：参数无效。  
- ENOTSUP ：系统不支持设置。  
- RTYPENOTMATCH ：任务类型不匹配。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <unistd.h>  
static void * Thread1(void *arg)  
{  
	int ret = 0;  
	int prio = 0;  
  
	// ……  
	ret = pthread_getschedprio(tid2, &prio);  
	if(ret != 0)  
	{  
		perror("func pthread_getschedprio error");  
		return (PVOID)-1;  
	}  
	// ……  
  
	return NULL;  
}  
```  
  
## pthread_setschedprio() ：动态设置线程优先级
  
  
### 函数原型：
  
```c  
int  pthread_setschedprio   
(  
	pthread_t  thread,   
	int  prio  
);  
```  
  
### 描述：
  
该接口用于设置thread指定任务的调度优先级。如果pthread_setschedprio接口失败，目标线程的调度优先级不会改变。优先级从0至255，依次增大。  
  
### 参数：
  
- thread ：指定任务ID。  
- prio ：任务优先级。  
  
### 返回值：
  
成功返回0，失败返回相应的错误码。  
  
### 错误码：
  
- EINVAL ：参数无效。  
- ESRCH ：线程不存在。  
- ERROR_KERNEL_IN_ISR ：系统处于中断中。  
- ERROR_KERNEL_HANDLE_NULL ：句柄出错。  
- ERROR_THREAD_PRIORITY_WRONG ：优先级错误。  
- ERROR_THREAD_NULL ：线程句柄无效。  
  
### 备注：
  
无。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <unistd.h>  
static void * Thread1(void *arg)  
{  
	int ret = 0;  
 	// ……  
	ret = pthread_setschedprio(tid2, 60);  
	if(ret != 0)  
	{  
		perror("func pthread_setschedprio error");  
		return (PVOID)-1;  
	}  
	// ……  
	return NULL;  
}  
```  
  
## pthread_equal() ：判断两个 posix 线程是否相同
  
### 函数原型：
  
```c  
int  pthread_equal   
(  
	pthread_t  thread1,   
	pthread_t  thread2  
);  
```  
  
  
### 描述：
  
用来比较两个任务ID，相等返回非0，不等返回0。  
  
### 参数：
  
- thread1 ：任务1的ID。  
- thread2 ：任务2的ID。  
  
### 返回值：
  
两个ID相等返回1，不等返回0。  
  
### 错误码：
  
无。  
  
### 备注：
  
GJB 7714-2012返回值为：  
  
- 不等返回0，相等返回且是合法的任务ID返回1，不是合法任务ID返回-1。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <unistd.h>  
static void * Thread1(void *arg)  
{  
	int ret = 0;  
  
	// ……  
	ret = pthread_equal(pthread_self(), tid1);  
	if(ret == 1)  
	{  
		printf("Current pthread is tid1\n");  
	}  
	else  
	{  
		printf("Current pthread is not tid1\n");  
	}  
	// ……  
  
	return NULL;  
}  
```  
  
## ched_yield()：主动让出一次 CPU 调度
  
  
### 函数原型：
  
```c  
int  pthread_yield   
(  
	void  
)；  
```  
  
### 描述：
  
将当前任务插入到同优先级调度器链表的最后，主动让出一次CPU调度，直到该任务再次被调度。注意，必须同优先级。  
  
### 参数：
  
无。  
  
### 返回值：
  
函数返回0。  
  
### 错误码：
  
- ERROR_KERNEL_IN_ISR ：系统处于中断中。  
- ERROR_KERNEL_HANDLE_NULL ：句柄出错。  
- ERROR_THREAD_NULL ：线程句柄无效。  
- ERROR_THREAD_NOT_READY ：线程没有就绪。  
  
### 备注：
  
GJB 7714-2012返回值为：成功返回1，失败返回-1。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <unistd.h>static   
void * Thread2(void *arg)  
{  
	pthread_t tid;  
  
	// ……  
	ret = pthread_yield();                                                  
    if(ret != 0)  
    {  
          perror("pthread_yield is error\n");  
          pthread_exit(NULL);  
    }  
	// ……  
  
	return NULL;  
}  
```  
  
## sched_get_priority_max() ：获取任务优先级最大值
  
### 函数原型：
  
```c  
int  sched_get_priority_max  
(  
	int  iPolicy  
);  
```  
  
  
### 描述：
  
该接口用于获取调度策略iPolicy对应的调度优先级的最大值。  
  
### 参数：
  
- iPolicy ：调度策略。  
  
### 返回值：
  
成功返回最大优先级，失败返回-1，并置错误码。  
  
### 错误码：
  
- EINVAL ：参数无效。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <unistd.h>  
static void * Thread1(void *arg)  
{  
  
	int maxprio;  
	maxprio = sched_get_priority_max(LW_OPTION_SCHED_FIFO);  
	if(maxprio == -1)  
	{  
		perror("func sched_get_priority_max error\n");  
		return (PVOID) -1;  
	}  
    printf("maxprio is %d\n",maxprio);  
	// ……  
	return NULL;  
}  
```  
  
  
## sched_get_priority_min() ：获取任务优先级最小值
  
### 函数原型：
  
```c  
int  sched_get_priority_min  
(  
	int  iPolicy  
);  
```  
  
### 描述：
  
该接口用于获取调度策略iPolicy对应的调度优先级的最小值。  
  
### 参数：
  
- iPolicy ：调度策略。  
  
### 返回值：
  
成功返回最大优先级，失败返回-1，并置错误码。  
  
### 错误码：
  
- EINVAL ：参数无效。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <unistd.h>  
static void * Thread1(void *arg)  
{  
	int minprio;  
	minprio = sched_get_priority_min(LW_OPTION_SCHED_FIFO);  
    if(minprio == -1)  
    {  
        perror("func sched_get_priority_max error\n");  
        return (PVOID) -1;  
	}  
	printf("minprio is %d\n",minprio);  
	// ……  
  
	return NULL;  
}  
  
```  
  
## thread_verifyid()：检验任务是否存在
  
### 函数原型：
  
```c  
int  pthread_verifyid   
(  
	pthread_t thread  
);  
```  
  
### 描述：
  
检测指定的任务是否存在。  
  
### 参数：
  
- thread ：任务ID。  
  
### 返回值：
  
存在返回0，不存在返回-1。  
  
### 错误码：
  
无。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <unistd.h>  
static void * Thread1(void *arg)  
{  
	int ret = 0;  
  
	// ……  
	ret = pthread_verifyid(tid2);                                         
	if(ret == 0)   
	{           
		// ……      
	}  
	else  
	{  
        // ……  
	}  
	// ……  
	return NULL;  
}  
  
```  
  
## pthread_cancelforce() ：强制取消任务
  
  
### 函数原型：
  
```c  
int  pthread_cancelforce   
(  
	pthread_t thread  
);  
```  
  
### 描述：
  
该接口将强制取消指定的任务，忽略任务的取消状态和类型的设置。  
  
### 参数：
  
- thread ：任务ID。  
  
### 返回值：
  
成功返回0，失败返回相应的错误码。  
  
### 错误码：
  
- ESRCH ：线程不存在。  
- ERROR_KERNEL_IN_ISR ：系统处于中断中。  
- ERROR_KERNEL_NOT_RUNNING ：系统没有运行。  
- ERROR_KERNEL_HANDLE_NULL ：句柄无效。  
- ERROR_THREAD_NULL ：线程句柄无效。  
  
### 备注：
  
GJB 7714-2012错误码为：  
- ESRCH ：线程不存在。  
- RTYPENOTMATCH ：任务类型不匹配。  
  
### 样例：
  
```c  
static void * Thread1(void *arg)  
{  
	int ret = 0;  
	// ……  
	ret = pthread_cancelforce(tid2);  
	if(ret != 0)  
	{  
		perror("func pthread_cancelforce error\n");  
		return (PVOID)-1;  
	}  
	// ……  
  
}  
```  
  
  
## pthread_join() ：等待任务终止
  
  
### 函数原型：
  
```c  
int  pthread_join   
(  
	pthread_t  thread,   
	void ** ppstatus  
);  
```  
  
  
### 描述：
  
该接口将挂起调用任务，直到参数thread指定的任务终止。(当创建多个任务并且进行多个任务的回收时，只有第一个回收任务可以正常回收，其余的会出现“No such prograss”的错误)。  
  
### 参数：
  
- thread ：需要等待的任务ID。  
- ppstatus ：存放指定任务结束时返回值的指针。  
  
### 返回值：
  
成功返回0，失败返回相应的错误码。  
  
### 错误码：
  
- ESRCH ：线程不存在。  
- EDEADLK ：检测到死锁。  
- ERROR_KERNEL_IN_ISR ：系统处于中断中。  
- ERROR_KERNEL_HANDLE_NULL：句柄无效。  
- ERROR_THREAD_NULL ：线程句柄无效。  
- ERROR_THREAD_JOIN_SELF ：线程合并自己。  
- ERROR_THREAD_DETACHED ：线程已经设定为不可合并。  
  
### 备注：
  
GJB 7714-2012错误码为：  
- ESRCH ：线程不存在。  
- EINVAL ：参数无效。  
- EDEADLK ：检测到死锁。  
  
### 样例：
  
```c  
static pthread_t tid1, tid2;  
  
static void * Thread1(void *arg)  
{  
	......  
  
	return NULL;  
}  
  
static void * Thread2(void *arg)  
{  
	......  
  
	return NULL;  
}  
int main (int argc, char **argv)  
{  
	int ret  ;  
	pthread_attr_t attr;  
  
	ret = pthread_attr_init(&attr);  
	if(ret != 0)  
	{  
		perror("func pthread_attr_init error");  
		return -1;  
	}  
  
	ret = pthread_create(&tid1, &attr, Thread1, NULL);  
	if(ret != 0)  
	{  
		perror("func pthread_create error");  
		return -1;  
	}  
  
	ret = pthread_create(&tid2, &attr, Thread2, NULL);  
	if(ret != 0)  
	{  
		perror("func pthread_create error");  
		return -1;  
	}  
  
	ret = pthread_join(tid1, NULL);  
	if(ret )  
  
	pthread_join(tid2, NULL);  
    return  (0);  
}  
```  
  
## pthread_detach() ：设置任务结束后自动回收资源
  
  
### 函数原型：
  
```c  
int  pthread_detach   
(  
	pthread_t  thread  
);  
```  
  
### 描述：
  
该接口将任务设置为detached状态，处于该状态的任务在终止的时候资源会直接回收。  
  
### 参数：
  
- thread ：需要设置的任务ID。  
  
### 返回值：
  
成功返回0，失败返回相应的错误码。  
  
### 错误码：
  
- ESRCH ：线程不存在。  
- EDEADLK ：检测到死锁。  
- ERROR_KERNEL_HANDLE_NULL ：句柄无效。  
- ERROR_THREAD_NULL ：线程句柄无效。  
- ERROR_THREAD_DETACHED ：线程已经设定为不可合并。  
  
### 备注：
  
GJB 7714-2012错误码为：  
- ESRCH ：线程不存在。  
- EINVAL ：参数无效。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <unistd.h>  
static pthread_t tid1, tid2;  
static void * Thread1(void *arg)  
{  
	......  
	return NULL;  
}  
  
static void * Thread2(void *arg)  
{  
	......  
	return NULL;  
}  
int main (int argc, char **argv)  
{  
	int ret  ;  
	pthread_attr_t attr;  
  
	......  
	ret = pthread_detach(tid1);  
	if(ret != 0)  
	{  
		perror("func pthread_detach error");  
	}  
	......  
  
    return  (0);  
}  
```  
  
  
## pthread_getinfo() ：获取指定任务的信息
  
### 函数原型：
  
```c  
int  pthread_getinfo   
(  
	pthread_t thread,   
	pthread_info_t *info  
);  
```  
  
  
### 描述：
  
该接口用于获取指定任务的信息。  
  
### 参数：
  
- thread ：任务ID。  
- info ：存放任务信息的指针。  
  
### 返回值：
  
成功返回0，失败返回相应的错误码。  
  
### 错误码：
  
- ESRCH ：线程不存在。  
- EINVAL ：参数无效。  
- ERROR_KERNEL_HANDLE_NULL：句柄无效。  
- ERROR_THREAD_NULL ：线程句柄无效。  
  
### 样例：
  
```c  
static void * Thread1(void *arg)  
{  
	int ret = 0;   
	pthread_info_t info;  
	// ……  
	ret = pthread_getinfo(tid1, &info);  
	if(ret != 0)  
	{  
		perror("func pthread_getinfo error\n");  
		return (PVOID)-1;  
	}  
	printf("**********************info**************************\n");  
	printf("tid 			:%ld\n", info.TCBD_ulId);  
	printf("name			:%s\n", info.TCBD_cThreadName);  
	printf("****************************************************\n");  
  
    // ……  
  
	return NULL;  
}  
```  
  
  
## pthread_getregs() ：获取任务的上下文信息
  
### 函数原型：
  
```c  
int  pthread_getregs   
(  
	pthread_t thread,   
	REG_SET * pregs  
);  
```  
  
  
### 描述：
  
该接口用于获取任务的上下文信息，并将这些信息存放在pregs中。对于出于非运行的稳定任务，该接口可以很好的运行。不能对任务自身调用。  
  
### 参数：
  
- thread ：指定任务的ID。  
- pregs ：存放任务上下文的信息的指针。  
  
### 返回值：
  
成功返回0，失败返回相应的错误码。  
  
### 错误码：
  
- ESRCH ：线程不存在。  
- EINVAL ：参数无效。  
- ENOTSUP ：系统不支持的操作。  
- ERROR_KERNEL_IN_ISR ：线程处于中断中。  
  
### 样例：
  
```c  
static pthread_t tid1, tid2;  
static void * Thread1(void *arg)  
{  
	int       ret = 0;  
 	REG_SET  regs;  
  
// ……  
  
    ret = pthread_getregs(tid2, &regs);          
if(ret != 0)   
{  
        perror("func pthread_getregs error\n");  
        return -1;  
    }  
  
    printf("**********************info**************************\n");  
    printf("REG_uiCpsr         :%d\n", regs.REG_uiCpsr);  
    printf("REG_uiR0           :%d\n", regs.REG_uiR0);  
    printf("REG_uiR1           :%d\n", regs.REG_uiR1);  
    printf("REG_uiR2           :%d\n", regs.REG_uiR2);  
    printf("REG_uiR3           :%d\n", regs.REG_uiR3);  
    printf("REG_uiFp           :%d\n", regs.REG_uiFp);  
    printf("REG_uiIp           :%d\n", regs.REG_uiIp);  
    printf("REG_uiLr           :%d\n", regs.REG_uiLr);  
    printf("REG_uiPc           :%d\n", regs.REG_uiPc);  
    printf("****************************************************\n");  
    // ……  
  
	return NULL;  
}	  
```  
  
  
## pthread_show() ：显示所有的线程的信息
  
  
### 函数原型：
  
```c  
int   pthread_show  
(  
	pthread_t thread,   
	int level  
);  
```  
  
  
### 描述：
  
显示所有的线程的信息。  
  
### 参数：
  
- thread ：需要显示的任务的ID（该参数无用）。  
  
- level ：显示信息的详细程度（本系统无效）。  
  
### 返回值：
  
函数返回0。  
  
### 错误码：
  
- ERROR_KERNEL_IN_ISR ：系统处于中断中。  
  
### 备注：
  
GJB 7714-2012错误码为：  
- ESRCH ：线程不存在。  
- EINVAL ：参数无效。  
  
- 返回值为成功返回0，失败返回相应的错误码。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <unistd.h>  
static void * Thread1(void *arg)  
{  
// ……  
	pthread_show(pthread_self(), 1);  
    // ……  
	return NULL;  
}  
```  
  
## pthread_showstack() ：显示CPU使用率
  
  
### 函数原型：
  
```c  
int  pthread_showstack   
(  
	pthread_t thread  
);  
```  
  
### 描述：
  
显示CPU使用率。  
  
### 参数：
  
- thread ：需要显示的任务的ID(该参数未使用)。  
  
### 返回值：
  
函数返回0。  
  
### 错误码：
  
- ERROR_KERNEL_IN_ISR ：系统处于中断中。  
  
### 备注：
  
GJB 7714-2012错误码为：  
- ESRCH ：线程不存在。  
  
- 返回值为无。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <unistd.h>  
static void * Thread1(void *arg)  
{  
	// ……  
	pthread_showstack(pthread_self());  
	// ……  
  
	return NULL;  
}  
```  
  
  
## pthread_showstackframe() ：显示任务的栈回溯信息
  
  
### 函数原型：
  
```c  
int  pthread_showstackframe   
(  
	pthread_t thread  
)  
```  
  
  
### 描述：
  
用于显示任务的栈回溯信息，只能用于显示调用任务自身的栈回溯信息。  
  
### 参数：
  
- thread ：任务ID。  
  
### 返回值：
  
成功返回0，失败返回相应的错误码。  
  
### 错误码：
  
- ENOTSUP ：系统不支持的操作。  
  
### 备注：
  
GJB 7714-2012错误码为：  
- ESRCH ：线程不存在。  
  
- 返回值为无。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <unistd.h>  
static pthread_t tid1, tid2;  
static void * Thread1(void *arg)  
{  
// ……  
	pthread_showstackframe(tid2);  
    // ……  
	return NULL;  
}  
```  
  
  
## sched_rr_get_interval() ：获取任务时间片轮转调度的调度周期
  
  
### 函数原型：
  
```c  
int  sched_rr_get_interval   
(  
	pid_t  pid,   
	struct timespec  *interval  
);  
```  
  
  
### 描述：
  
给接口用于获取时间片轮转调度的调度周期，并存放在interval中。如果pid取值为0，该接口将返回调用任务的时间轮转时间。  
  
### 参数：
  
- pid ：需要获取的任务的ID；  
  
- interval ：存放时间片轮转调度时间的指针。  
  
### 返回值：
  
成功返回0；失败返回-1，并置错误码。  
  
### 错误码： 
  
- EINVAL ：参数无效。  
- ESRCH ：任务不存在。  
  
### 备注：
  
GJB 7714-2012错误码为：  
- ESRCH ：线程不存在。  
  
### 样例：
  
```c  
#include <SylixOS.h>  
#include <module.h>  
#include <pthread.h>  
#include <stdio.h>  
static void * Thread2(void *arg)  
{  
	int ret;   
	struct timespec interval;  
  
	// ……  
  
	ret = sched_rr_get_interval(getpid() , &interval);  
	if(ret != 0)  
	{  
		perror("func sched_rr_get_interval error\n");  
		return (PVOID)-1;  
	}  
  
	// ……  
  
	return NULL;  
}  
```  
  
  
## sched_settimeslice() ：设置系统时间片轮转周期（tick数）
  
### 函数原型：
  
```c  
int  sched_settimeslice   
(  
	UINT32  ticks  
);  
```  
  
  
### 描述：
  
该接口用于设置系统中时间片轮状调度策略的调度周期，设置的时间单位是tick数。如果参数ticks取值为0，将禁止时间片轮转调度。  
  
### 参数：
  
- ticks ：时间片轮转周期tick数。  
  
### 返回值：
  
成功返回0；失败返回-1，并置错误码。  
  
### 错误码： 
  
- EACCES ：权限不足。  
- ERANGE ：超出返回。  
  
### 备注：
  
GJB 7714-2012错误码为：无。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <unistd.h>  
static void * Thread1(void *arg)  
{  
	int ret = 0;  
	// ……  
	ret = sched_settimeslice(50);  
	if(ret != 0)  
	{  
		perror("func sched_settimeslice error\n");  
		return (PVOID) -1;  
	}  
	// ……  
  
	return NULL;  
}  
```  
  
## sched_gettimeslice() ：获取系统时间片轮转调度的调度周期
  
  
### 函数原型：
  
```c  
unsigned int  sched_gettimeslice   
(  
	void  
);  
```  
  
  
### 描述：
  
该接口用于获取系统中时间片轮状调度策略的调度周期，时间单位是tick数。  
  
### 参数：
  
无。  
  
### 返回值：
  
返回时间片轮转调度的调度周期，0表示不允许时间片轮转调度。  
  
### 错误码：
  
无。  
  
### 样例：
  
```c  
#include <SylixOS.h>  
#include <module.h>  
#include <pthread.h>  
#include <stdio.h>  
static void * Thread1(void *arg)  
{  
	int tick;  
	// ……  
	tick = sched_gettimeslice();  
	// ……  
  
	return NULL;  
}  
```  
  
  
## pthread_create_hook_add() ：添加任务创建的钩子函数
  
### 函数原型：
  
```c  
int   pthread_create_hook_add  
(  
	OS_STATUS (*create_hook)(pthread_t)  
);  
```  
  
  
### 描述：
  
该接口用于添加任务创建的钩子函数，指定的函数在任务创建时被调用。需要申明宏#define__SYLIXOS_KERNEL才可以调用。  
  
### 参数：
  
- create_hook ：任务创建的钩子函数。  
  
### 返回值：
  
成功返回0，失败返回相应的错误码。  
  
### 错误码：
  
- EACCES ：没有权限。  
- EINVAL ：参数无效。  
- EAGAIN ：系统资源不足。  
  
### 备注：
  
GJB 7714-2012错误码为：  
- EINVAL ：参数无效。  
- EAGAIN ：系统资源不足。  
  
### 样例：
  
创建一个kernel Module工程，并完成调用函数。  
  
```c  
#include <SylixOS.h>  
#include <module.h>  
#include <pthread.h>  
#include <stdio.h>  
OS_STATUS create_hook(pthread_t tid)  
{  
	// ……  
	return 0;  
}  
  
int module_init (void)  
{  
	int res = 0;  
	res = pthread_create_hook_add(create_hook);  
	if(res != 0)  
	{  
		printk("func pthread_create_hook_add error\n");  
		return -1;  
	}  
  
	// ……  
    return 0;  
}  
```  
  
  
## pthread_create_hook_delete() ：删除任务创建的钩子函数
  
  
### 函数原型：
  
```c  
int  pthread_create_hook_delete   
(  
	OS_STATUS (*create_hook)(pthread_t)  
);  
```  
  
### 描述：
  
该接口用于从任务创建的钩子函数列表删除指定的钩子函数。  
  
### 参数：
  
- create_hook ：要删除的任务创建钩子函数。  
  
### 返回值： 
  
成功返回0，失败返回相应的错误码。  
  
### 错误码：
  
- EACCES ：没有权限。  
- EINVAL ：参数无效。  
  
### 备注：
  
GJB 7714-2012错误码为：  
- EINVAL ：参数无效。  
  
### 样例：
  
```c  
#include <SylixOS.h>  
#include <module.h>  
#include <pthread.h>  
#include <stdio.h>  
OS_STATUS create_hook(pthread_t tid)  
{  
	// ……  
	return 0;  
}  
  
void module_exit (void)  
{  
    int res = 0;  
    res = pthread_create_hook_delete(create_hook);  
    if(res != 0)  
    {  
    	printk("func pthread_create_hook_add error\n");  
}  
	// ……  
  
}  
```  
  
## pthread_switch_hook_add() ：添加任务切换的钩子函数
  
  
### 函数原型：
  
```c  
int  pthread_switch_hook_add   
(  
	OS_STATUS (*switch_hook)(pthread_t, pthread_t)  
);  
```  
  
  
### 描述：
  
该接口用于添加一个指定的钩子函数至任务切换的钩子函数列表。  
  
### 参数：
  
- switch_hook ：任务切换的钩子函数。  
  
### 返回值：
  
成功返回0，失败返回相应的错误码。  
  
### 错误码：
  
- EACCES ：没有权限。  
- EINVAL ：参数无效。  
- EAGAIN ：系统资源不足。  
  
### 备注：
  
GJB 7714-2012错误码为：  
- EINVAL ：参数无效。  
- EAGAIN ：系统资源不足。  
  
### 样例：
  
```c  
#include <SylixOS.h>  
#include <module.h>  
#include <pthread.h>  
#include <stdio.h>  
OS_STATUS switch_hook(pthread_t tid1, pthread_t tid2)  
{  
	// ……  
	return 0;  
}  
  
int module_init (void)  
{  
	int res = 0;  
	res = pthread_switch_hook_add(switch_hook);  
	if(res != 0)   
	{  
		printk("func pthread_switch_hook_add error\n");  
		return -1;  
	}  
  
	// ……  
  
    return 0;  
}  
```  
  
  
## pthread_switch_hook_delete() ：删除任务切换的钩子函数
  
  
### 函数原型：
  
```c  
int  pthread_switch_hook_delete   
(  
	OS_STATUS (*switch_hook)(pthread_t, pthread_t)  
);  
```  
  
### 描述：
  
该接口用于从任务切换数列表删除指定的钩子函数。  
  
### 参数：
  
- switch_hook ：要删除的任务切换钩子函数。  
  
### 返回值：
  
成功返回0，失败返回相应的错误码。  
  
### 错误码：
  
- EACCES ：没有权限。  
- EINVAL ：参数无效。  
  
### 备注：
  
GJB 7714-2012错误码为：  
- EINVAL ：参数无效。  
  
### 样例：
  
```c  
#include <SylixOS.h>  
#include <module.h>  
#include <pthread.h>  
#include <stdio.h>  
OS_STATUS switch_hook(pthread_t tid1, pthread_t tid2)  
{  
	_PrintFormat("%ld switch %ld\n", tid1, tid2);  
	return 0;  
}  
  
void module_exit (void)  
{  
	int res = 0;  
	res = pthread_switch_hook_delete(switch_hook);  
	if(res != 0)   
	{  
		printk("func pthread_switch_hook_delete error\n");  
	}  
  
	// ……  
  
}  
```  
  
## pthread_close_hook_add() ：添加任务删除的钩子函数
  
### 函数原型：
  
```c  
int  pthread_close_hook_add   
(  
	OS_STATUS (*close_hook)(pthread_t)  
);  
```  
  
### 描述：
  
该接口用于添加一个指定的钩子函数至任务删除的钩子函数列表。  
  
### 参数：
  
- close_hook ：任务删除的钩子函数。  
  
### 返回值：
  
成功返回0，失败返回相应的错误码。  
  
### 错误码：
  
- EACCES ：没有权限。  
- EINVAL ：参数无效。  
- EAGAIN ：系统资源不足。  
  
### 备注：
  
GJB 7714-2012错误码为：  
- EINVAL ：参数无效。  
- EAGAIN ：系统资源不足。  
  
### 样例：
  
创建一个kernel Module工程。并完成调用函数。  
  
```c  
#include <SylixOS.h>  
#include <module.h>  
#include <pthread.h>  
#include <stdio.h>  
OS_STATUS close_hook(pthread_t tid)  
{  
	_PrintFormat("pthread %ld close\n", tid);  
	return 0;  
}  
  
int module_init (void)  
{  
	int res = 0;  
  
	res = pthread_close_hook_add(close_hook);  
	if(res != 0)  
	{  
		printk("func pthread_close_hook_add error\n");  
		return -1;  
	}  
	// ……  
    return 0;  
}  
```  
  
  
## pthread_close_hook_delete()：删除任务删除的钩子函数
  
### 函数原型：
  
```c  
int  pthread_close_hook_delete   
(  
	OS_STATUS (*close_hook)(pthread_t)  
)  
```  
  
  
### 描述：
  
该接口用于从任务删除的钩子函数列表删除指定的钩子函数。  
  
### 参数：
  
- close_hook ：要删除的任务删除钩子函数。  
  
### 返回值： 
  
成功返回0，失败返回相应的错误码。  
  
### 错误码：
  
- EACCES ：没有权限。  
- EINVAL ：参数无效。  
  
### 备注：
  
GJB 7714-2012错误码为：  
- EINVAL ：参数无效。  
  
### 样例：
  
```c  
#include <SylixOS.h>  
#include <module.h>  
#include <pthread.h>  
#include <stdio.h>  
#define  __SYLIXOS_KERNEL  
OS_STATUS close_hook(pthread_t tid)  
{  
	// ……  
	return 0;  
}  
  
void module_exit (void)  
{  
	int res = 0;  
    res = pthread_close_hook_delete(close_hook);  
    if(res != 0)  
    {  
    	printk("func pthread_close_hook_delete error\n");  
	}  
	// ……  
  
}  
```  
  
  
## pthread_key_create() ：创建任务私有数据的键
  
### 函数原型：
  
```c  
int  pthread_key_create   
(  
	pthread_key_t  *pkey,  
	void (*destructor)(void *)  
)  
```  
  
  
### 描述：
  
该接口创建所有任务可见的任务私有数据的键。接口调用成功后，任务私有数据的键ID存放在参数pkey中，该关键字用来标识任务私有数据。  
  
### 参数：
  
- pkey ：任务私有数据对应的键值。  
  
- destructor ：任务私有数据相关的删除函数。  
  
### 返回值：
  
成功返回0，失败返回相应的错误码。  
  
### 错误码：
  
- EAGAIN ：系统资源不足。  
- ENOMEM ：内存不足。  
- EINVAL ：参数无效。  
- ERROR_KERNEL_IN_ISR ：线程处于中段中。  
- ERROR_THREAD_PRIORITY_WRONG ：优先级错误。  
- ERROR_KERNEL_PNAME_TOO_LONG ：名字太长。  
- ERROR_EVENT_FULL ：事件控制块已用完。  
  
### 备注：
  
GJB 7714-2012错误码为：  
- EAGAIN ：系统资源不足。  
- ENOMEM ：内存不足。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <unistd.h>  
static pthread_t tid1, tid2;  
static pthread_key_t key;  
static void *Thread1(void *arg)  
{  
	// ……  
  
	return NULL;  
}  
static void *Thread2(void *arg)  
{  
	// ……  
  
	return NULL;  
}  
int main (int argc, char **argv)  
{  
	int ret;  
	ret = pthread_create(&tid1, NULL, Thread1, NULL);  
	if(ret != 0)  
	{  
		perror("func pthread_create error");  
		return -1;  
	}  
  
	ret = pthread_create(&tid2, NULL, Thread2, NULL);  
	if(ret != 0)  
	{  
		perror("func pthread_create error");  
		return -1;  
	}  
  
	ret = pthread_key_create(&key ,NULL);  
	if(ret != 0)  
	{  
		perror("func pthread_key_create error\n");  
		return -1;  
	}  
	// ……  
	return 0;  
}  
```  
  
## pthread_key_delete() ：删除任务私有数据的键
  
  
### 函数原型：
  
```c  
int  pthread_key_delete   
(  
	pthread_key_t  key  
);  
```  
  
  
### 描述：
  
该接口用于删除先前有pthread_key_create()创建的任务私有数据关键字。在pthread_key_delete()被调用时，和key相关的任务私有数据的值可以不是NULL。  
调用该接口时，任务私有数据的析构函数不会被调用，由应用负责释放与key相关的内存或执行相关的清理函数。  
  
### 参数：
  
- key ：任务私有数据的对应的键值。  
  
### 返回值：
  
成功返回0，失败返回对于的错误码。  
  
### 错误码：
  
- EINVAL ：参数无效。  
  
### 备注：
  
无。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <unistd.h>  
static pthread_key_t key;  
int main (int argc, char **argv)  
{  
	int ret;  
  
	.....  
	ret = pthread_key_create(&key ,NULL);  
	if(ret != 0)  
	{  
		perror("func pthread_key_create error\n");  
		return -1;  
	}  
	// ……  
	ret = pthread_key_delete(key);  
	if(ret != 0)  
	{  
		perror("func pthread_setspecific error\n");  
		return -1;  
	}  
	......  
	return 0;  
}  
```  
  
## pthread_setspecific() ：设置任务私有数据
  
  
### 函数原型：
  
```c  
int  pthread_setspecific   
(  
	pthread_key_t  key,  
	const void  *pvalue  
);  
```  
  
  
### 描述：
  
该接口用于把任务私有数据的值与先前调用pthread_key_create()得到的key关联起来。不同的任务可以给相同的关键字绑定不同类型的值。  
  
### 参数：
  
- key ：任务私有数据的键值。  
  
- pvalue ：任务私有数据的值。  
  
### 返回值：
  
成功返回0，失败返回相应的错误码。  
  
### 错误码：
  
- EINVAL ：参数无效。  
- ENOMEM ：内存不足。  
  
### 备注：
  
GJB 7714-2012错误码为：  
- EINVAL ：参数无效。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <unistd.h>  
static pthread_key_t key;  
static void *Thread1(void *arg)  
{  
	int ret =0;  
	ret = pthread_setspecific(key, "SylixOS thread1");  
	if(ret != 0)  
	{  
		perror("func pthread_setspecific error\n");  
		return (PVOID)-1;  
	}  
  
	// ……  
  
	return NULL;  
}  
```  
  
## pthread_getspecific() ：获取任务私有数据
  
  
### 函数原型：
  
```c  
void *pthread_getspecific   
(  
	pthread_key_t  key  
);  
```  
  
  
### 描述：
  
该接口用于获取调用任务中与key绑定的私有数据。  
  
### 参数：
  
- key ：任务私有数据对应的键值。  
  
### 返回值：
  
成功返回对应的值，失败返回NULL，并置错误码。  
  
### 错误码：
  
- EINVAL ：参数无效。  
  
### 备注：
  
GJB 7714-2012错误码为：无。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <unistd.h>  
static pthread_key_t key;  
static void *Thread1(void *arg)  
{  
	int ret =0;  
	ret = pthread_setspecific(key, "SylixOS thread1");  
	if(ret != 0)  
	{  
		perror("func pthread_setspecific error\n");  
		return (PVOID)-1;  
	}  
  
	// ……  
  
	char *p = (char *) pthread_getspecific(key);  
	if(p != NULL)  
	{  
		printf("pthread1 :%s\n", p);  
	}  
  
	// ……  
  
	return NULL;  
}  
```  
  
## pthread_addvar() ：添加任务变量
  
  
### 函数原型：
  
```c  
int  pthread_addvar   
(  
	pthread_t thread, int *pvar  
);  
```  
  
### 描述：
  
该接口用于向任务上下文添加一个指定的变量pvar。调用该接口后，变量将为任务锁私有。该接口只能在单处理器上使用,启用需要将base中mp_cfg.h中的LW_CFG_SMP_EN 设置为0.  
  
### 参数：
  
- thread ：任务ID。  
  
- pvar ：指向任务变量的指针。  
  
### 返回值：
  
成功返回0，失败返回相应的错误码。  
  
### 错误码：
  
- EINVAL ：参数无效。  
- ENOTSUP ：系统不支持的操作。  
- ESRCH ：线程不存在。  
  
### 备注：
  
GJB 7714-2012错误码为：  
- EINVAL ：参数无效。  
- ESRCH ：线程不存在。  
  
### 样例：
  
```c  
static pthread_t tid1, tid2;  
static int var;  
static void *Thread1(void *arg)  
{  
	int ret =0;  
	ret = pthread_addvar(tid1, &var);  
	if(ret != 0)  
	{  
		perror("func pthread_addvar error\n");  
	}  
	ret = pthread_addvar(tid2, &var);  
	if(ret != 0)  
	{  
		perror("func pthread_addvar error\n");  
	}  
  
	// ……  
  
	return NULL;  
}  
```  
  
## pthread_delvar() ：删除任务变量
  
### 函数原型：
  
```c  
int  pthread_delvar   
(  
	pthread_t thread,   
	int *pvar  
)  
```  
  
  
### 描述：
  
该接口用来从指定的任务上下文中删除指定的任务变量pvar，被删除变量的私有值将丢失。该接口只能在单处理器上使用。  
  
### 参数：
  
- thread ：任务ID  
  
- pvar ：指向任务变量的指针。  
  
### 返回值：
  
成功返回0，失败返回相应的错误码。  
  
### 错误码：
  
- EINVAL ：参数无效。  
- ESRCH ：线程不存在。  
  
### 样例：
  
```c  
static void *Thread2(void *arg)  
{  
	int ret ;  
  
	// ……  
  
	ret = pthread_delvar(tid1, &var);  
	if(ret != 0)  
	{  
		perror("func pthread_delvar error\n");  
	}  
  
	ret = pthread_delvar(tid2, &var);  
	if(ret != 0)  
	{  
		perror("func pthread_delvar error\n");  
	}  
	return NULL;  
}  
```  
  
  
## pthread_setvar() ：设置任务变量
  
### 函数原型：
  
```c  
int  pthread_setvar  
(  
	pthread_t thread,   
	int *pvar,  
	int value  
)  
```  
  
### 描述：
  
该接口设置指定任务的数据变量私有值。指定任务通常不是调用任务，因为调用任务可以通过直接修改变量来设置私有值。该接口只能在单处理器上使用。  
  
### 参数：
  
- thread ：任务ID  
  
- pvar ：指向任务变量的指针。  
  
- value ：任务变量值。  
  
### 返回值：
  
成功返回0，失败返回相应的错误码。  
  
### 错误码：
  
- EINVAL ：参数无效。  
- ESRCH ：线程不存在。  
  
### 备注：
  
无。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <unistd.h>  
static void *Thread2(void *arg)  
{  
	int ret ;  
  
	// ……  
	ret = pthread_setvar(tid1, &var, 1);  
	if(ret != 0)  
	{  
		perror("func pthread_setvar error\n");  
	}  
	ret = pthread_setvar(tid2, &var, 2);  
	if(ret != 0)  
	{  
		perror("func pthread_setvar error\n");  
	}  
	// ……  
  
	return NULL;  
}  
```  
  
  
## pthread_getvar() ：获取任务变量
  
  
### 函数原型：
  
```c  
int  pthread_getvar   
(  
	pthread_t thread,  
	int *pvar,  
	int *value  
);  
```  
  
  
### 描述：
  
该接口返回指定任务的任务变量私有值。指定任务通常不是调用任务，因为调用任务可以通过直接得到变量来设置私有值。该接口只能在单处理器上使用。  
  
### 参数：
  
- thread ：需要获取的任务的ID。  
  
- pvar ：指向任务变量的指针。  
  
- value ：指向任务变量值的指针。  
  
### 返回值：
  
成功返回0，失败返回相应的错误码。  
  
### 错误码：
  
- EINVAL ：参数无效。  
- ESRCH ：线程不存在。  
  
### 备注：
  
无。  
  
### 样例：
  
```c  
static pthread_t tid1, tid2;  
static int var;  
static void *Thread1(void *arg)  
{  
	int ret =0;   
	int value;  
  
// ……  
	ret = pthread_getvar(tid1, &var, &value);  
	if(ret == 0)  
	{  
		printf("tid1 value %d\n", value);  
	}  
	ret = pthread_getvar(tid2, &var, &value);  
	if(ret == 0)  
	{  
		printf("tid2 value %d\n", value);  
	}  
	sleep(1);  
	return NULL;  
}  
```  
  
  
## pthread_attr_get_np () ：获取线程属性控制块
  
  
### 函数原型：
  
```c  
int  pthread_attr_get_np  
(  
	pthread_t  thread,  
	pthread_attr_t *pattr  
);  
```  
  
  
### 描述：
  
该接口返回获取线程属性控制块 (FreeBSD 扩展接口)。其中返回的name为空。  
  
### 参数：
  
- thread ：需要获取的任务的ID。  
  
- pattr ：需要设置的任务属性块指针。  
  
### 返回值：
  
成功返回0，失败返回相应的错误码。  
  
### 错误码：
  
- EINVAL ：参数无效。  
- ESRCH ：线程不存在。  
- ERROR_KERNEL_HANDLE_NULL ：句柄出错。  
- ERROR_THREAD_NULL ：线程句柄无效。  
  
### 备注：
  
GJB 7714-2012不存在。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <unistd.h>  
static void * Thread1(void *arg)  
{  
    int            ret = 0;  
pthread_attr_t  attr;  
  
    while(1)  
    {  
        // ……  
        ret = pthread_attr_get_np(tid1,&attr);  
		if(ret != 0)  
        {  
            perror("pthread_attr_get_np is error\n");  
            pthread_exit(NULL);  
        }  
        printf("pthread_attr_t schedpolicy is %d \n",attr.schedpolicy);  
        // ……  
    }  
  
    return NULL;  
}  
```  
  
## pthread_getattr_np () ：获取线程属性控制块
  
  
### 函数原型：
  
```c  
int  pthread_getattr_np  
(  
	pthread_t thread,   
	pthread_attr_t *pattr  
);  
```  
  
  
### 描述：
  
该接口返回获取线程属性控制块 (FreeBSD扩展接口)。其中返回的name为空。其与pthread_attr_get_np ()相同。  
  
### 参数：
  
- thread ：需要获取的任务的ID。  
  
- pattr ：需要设置的任务属性块指针。  
  
### 返回值：
  
成功返回0，失败返回相应的错误码。  
  
### 错误码：
  
- EINVAL ：参数无效。  
- ESRCH ：线程不存在。  
- ERROR_KERNEL_HANDLE_NULL：句柄出错。  
- ERROR_THREAD_NULL ：线程句柄无效。  
  
### 备注：
  
GJB 7714-2012不存在。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <unistd.h>  
static void * Thread1(void *arg)  
{  
    int           ret = 0;  
    pthread_attr_t  attr;  
    while(1)  
    {  
        // ……  
        ret = pthread_getattr_np (tid1,&attr);  
		if(ret != 0)  
        {  
            perror("pthread_getattr_np is error\n");  
            pthread_exit(NULL);  
        }  
        printf("pthread_attr_t schedpolicy is %d \n",attr.schedpolicy);  
        // ……  
    }  
  
    return NULL;  
}  
```  
  
  
## pthread_safe_np () ：线程进入安全模式
  
  
### 函数原型：
  
```c  
void  pthread_safe_np   
(  
	void  
);  
```  
  
  
### 描述：
  
线程进入安全模式, 任何对本线程的删除操作都会推迟到线程退出安全模式时进行。  
  
### 参数：
  
无。  
  
### 返回值：
  
无。  
  
### 错误码：
  
- ERROR_KERNEL_IN_ISR：线程处于中断中  
  
### 备注：
  
GJB 7714-2012不存在。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <unistd.h>  
static void * Thread2(void *arg)  
{  
    while(1)  
{  
    // ……  
  
        pthread_safe_np();                                                
  
// ……  
  
        pthread_unsafe_np();   
                                             
        // ……  
    }  
    return NULL;  
}  
```  
  
  
## pthread_unsafe_np () ：线程退出安全模式
  
  
### 函数原型：
  
```c  
void  pthread_unsafe_np  
(  
	void  
)  
```  
  
### 描述：
  
线程退出安全模式。  
  
### 参数：
  
无。  
  
### 返回值：
  
无。  
  
### 错误码：
  
- ERROR_KERNEL_IN_ISR：线程处于中断中。  
  
### 备注：
  
GJB 7714-2012不存在。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <unistd.h>  
static void * Thread2(void *arg)  
{  
    while(1)  
{  
    // ……  
  
        pthread_safe_np();   
  
	// ……  
  
        pthread_unsafe_np();   
  
        // ……  
    }  
    return NULL;  
}  
```  
  
  
## pthread_setconcurrency()：设置线程新的并行级别
  
  
### 函数原型：
  
```c  
int  pthread_setconcurrency   
(  
	int  newlevel  
)  
```  
  
  
### 描述：
  
设置任务新的并行级别。该函数未实现。  
  
### 参数：
  
Newlevel ：新的并行级别。  
  
### 返回值：
  
成功返回0。  
  
### 错误码：
  
无。  
  
### 备注：
  
该函数未实现。  
  
## pthread_getconcurrency () ：获取线程新的并行级别
  
### 函数原型：
  
```c  
int  pthread_getconcurrency   
(  
	void  
)  
```  
  
  
### 描述：
  
获取线程新的并行级别，实际上该接口返回管理线程的最大数量（65535）。  
  
### 参数：
  
无  
  
### 返回值：
  
LW_CFG_MAX_THREADS ：系统管理的最大线程数量（65535）。  
  
### 错误码：
  
无  
  
### 备注：
  
GJB 7714-2012不存在。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <unistd.h>  
static void * Thread2(void *arg)  
{  
    int ret;  
  
    while(1)  
    {  
        // ……  
  
        ret = pthread_getconcurrency();                
        printf("pthread_getconcurrency is %d",ret);  
  
        // ……;  
  
    }  
    return NULL;  
}  
```  
  
  
## pthread_getcpuclockid () ：获得线程 CPU 时间 clock id
  
### 函数原型：
  
```c  
int  pthread_getcpuclockid   
(  
	pthread_t thread,  
	clockid_t *clock_id  
);  
```  
  
  
### 描述：
  
获得线程 CPU 时间 clock id。  
  
### 参数：
  
- Thread ：需要获取的线程的句柄。  
  
- clock_id ：存放时钟id的指针。  
  
### 返回值：
  
成功返回0，失败返回相应的错误码。  
  
### 错误码：
  
- EINVAL ：参数无效。  
- ESRCH ：线程不存在。  
  
### 备注：
  
GJB 7714-2012不存在。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <unistd.h>  
static void * Thread1(void *arg)  
{  
    int       ret = 0;  
    clockid_t clockid;  
  
    while(1)  
    {  
        // ……  
  
        ret=pthread_getcpuclockid(tid1,&clockid);                                        
        if(ret != 0)  
        {  
            perror("pthread_getcpuclockid is error\n");  
            return(PVOID) -1;  
        }  
        printf("pthread_getcpuclockid is %d\n",clockid);  
        // ……  
    }  
    return NULL;  
}  
```  
  
  
## pthread_setname_np () ：设置线程名字
  
  
### 函数原型：
  
```c  
int  pthread_setname_np  
(  
	pthread_t  thread,  
	const char  *name  
)  
```  
  
  
### 描述：
  
设置指定线程名字  
  
### 参数：
  
- thread ：线程句柄。  
  
- name ：线程名字。  
  
### 返回值：
  
成功返回0，失败返回相应的错误码。  
  
### 错误码：
  
- EINVAL ：参数无效。  
  
### 备注：
  
GJB 7714-2012不存在。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <unistd.h>  
int main (int argc, char **argv)  
{  
    int ret  ;  
    pthread_attr_t attr;  
    ......  
    ret = pthread_setname_np(tid1,"pthreadone");  
    if(ret != 0)  
    {  
        perror("pthread_setname_np is error\n");  
        return -1;  
    }  
    ......  
    return  (0);  
}  
```  
  
  
## pthread_getname_np()：获得线程名字
  
  
### 函数原型：
  
```c  
int  pthread_getname_np  
(  
	pthread_t  thread,  
	char  *name,   
	size_t len  
)  
```  
  
### 描述：
  
获得线程名字。  
  
### 参数：
  
- thread ：线程句柄。  
  
- name ：线程名字。  
  
### 返回值：
  
成功返回0，失败返回相应的错误码。  
  
### 错误码：
  
- EINVAL ：参数无效。  
- ERANGE ：返回值过长。  
  
### 备注：
  
GJB 7714-2012不存在。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <unistd.h>  
static void * Thread1(void *arg)  
{  
    int            ret = 0;  
  
    char *name = malloc(128);  
    while(1)  
    {  
        ......  
        memset(name,0,128);  
        ret = pthread_getname_np(tid1,name,128);  
        if(ret != 0)  
        {  
            perror("func pthread_attr_getname error");  
            pthread_exit(NULL);  
        }  
        printf("attr name: %s\n", name);  
        ......  
        sleep(1);  
    }  
  
    return NULL;  
}  
```  
  
  
# 进程
  
## execl() 执行文件函数
  
  
### 函数原型：
  
```c  
int execl  
(  
	const char *path,   
	const char *argv0,  
	 ...  
);  
```  
  
### 描述：
  
用来执行参数path字符串所代表的文件路径，接下来的参数代表执行该文件时传递过去的argv[0]// ……，最后一个参数必须用空指针NULL作结束。  
本函数只能由当前进程主线程调用，否则会返回失败。其具体实现是由spawnv()函数完成。  
  
### 参数：
  
- path ：可执行文件的路径。  
  
- argv0, ... ：传递给可执行文件的参数。  
  
### 返回值：
  
成功返回0，失败-1以及对应的错误码。  
  
### 错误码： 
  
- ENOMEM ：内存不足。  
- EINVAL ：参数无效。  
  
### 样例：
  
子进程：  
  
```c  
#include <stdio.h>  
#include <stdlib.h>  
#include <process.h>  
#include <unistd.h>  
int main (int argc, char *argv[])  
{  
    char *env_parent = (char*)0;  
    // ……  
    while(1)  
    {  
        printf("this is %s function test\n",argv[1]);  
        env_parent = getenv("PARENT");  
        if (env_parent)   
		{  
            printf("environment variable: PARENT = %s\n", env_parent);  
        }  
        // ……  
	}  
	      
	return  (0);  
}  
```  
  
  
父进程：  
  
```c  
#include <stdio.h>  
#include <stdlib.h>  
#include <process.h>  
#include <unistd.h>  
#define CHILD_PATH  "../HYSylixos_execl_child_demo/HYSylixos_execl_child_demo"  
int main (int argc, char *argv[])  
{  
    int ret = 0;  
  
    // ……  
  
    ret = execl(CHILD_PATH,CHILD_PATH,"execl",NULL);  
    if(ret != 0)  
    {  
        perror("execl");  
        return -1;  
    }  
  
	// ……  
  
    return 0;  
}  
```  
  
  
## execle() 执行文件函数
  
  
### 函数原型：
  
```c  
int execle  
(  
	const char *path,   
	const char *argv0,   
	...  
);  
```  
  
  
### 描述：
  
用来执行参数path字符串所代表的文件路径，接下来的参数代表执行该文件时传递过去的argv[0]// ……，最后一个参数必须用空指针NULL作结束。本函数只能由当前进程主线程调用，否则会返回失败。其具体操作是由spawnve完成。  
  
### 参数：
  
- path ：可执行文件的路径。  
  
- argv0, ... ：传递给可执行文件的参数。  
  
### 返回值：
  
成功返回0，失败-1以及对应的错误码。  
  
### 错误码： 
  
- EINVAL ：参数无效。  
- ENOMEM ：内存不足。  
  
### 样例：
  
子进程：  
  
```c  
#include <stdio.h>  
#include <stdlib.h>  
#include <process.h>  
#include <unistd.h>  
int main (int argc, char *argv[])  
{  
    char *env_parent = (char*)0;  
  
    if (argc != 2)  
    {  
        printf("child process param error!\n");  
        return  (-1);  
    }  
  
    while(1)  
    {  
        printf("this is %s function test\n",argv[1]);  
        env_parent = getenv("PARENT");  
        if (env_parent)   
		{  
            printf("environment variable: PARENT = %s\n", env_parent);  
        }  
  
        sleep(2);  
  
	}  
      
	return  (0);  
}  
```  
  
  
父进程：  
  
```c  
#include <stdio.h>  
#include <stdlib.h>  
#include <process.h>  
#include <unistd.h>  
#define CHILD_PATH  "../HYSylixos_execl_child_demo/HYSylixos_execl_child_demo"  
int main (int argc, char *argv[])  
{  
    int ret = 0;  
  
    char *env[] = { "PARENT=execle_demo", (char *)0 };  
  
    printf("before execle\n");  
    ret = execle(CHILD_PATH, CHILD_PATH, "execle demo", (char *)0, env);  
    if(ret != 0)  
    {  
        perror("execle");  
        return -1;  
    }  
  
    printf("after execle\n");  
  
    return 0;  
}  
```  
  
  
## execlp 执行文件函数
  
  
### 函数原型：
  
```c  
int execlp  
(  
	const char *file,   
	const char *argv0,  
 	...  
);  
```  
  
  
### 描述：
  
用来执行参数path字符串所代表的文件路径，接下来的参数代表执行该文件时传递过去的argv[0]// ……，最后一个参数必须用空指针NULL作结束。  
本函数只能由当前进程主线程调用，否则会返回失败。  
  
### 参数：
  
- file ：可执行文件。  
  
- argv0, ... ：传递给可执行文件的参数。  
  
### 返回值：
  
成功返回0，失败-1以及对应的错误码。  
  
### 错误码： 
  
- EINVAL ：参数无效。  
- ENOMEM ：内存不足。  
  
### 样例：
  
子进程：  
  
```c  
#include <stdio.h>  
#include <stdlib.h>  
#include <process.h>  
#include <unistd.h>  
int main (int argc, char *argv[])  
{  
    char *env_parent = (char*)0;  
  
    if (argc != 2)  
    {  
        printf("child process param error!\n");  
        return  (-1);  
    }  
  
    while(1)  
    {  
        printf("this is %s function test\n",argv[1]);  
        env_parent = getenv("PARENT");  
        if (env_parent)   
		{  
            printf("environment variable: PARENT = %s\n", env_parent);  
        }  
  
        sleep(2);  
}  
      
return  (0);  
}  
```  
  
父进程：  
  
```c  
#include <stdio.h>  
#include <stdlib.h>  
#include <process.h>  
#include <unistd.h>  
#define CHILD_PATH  "../HYSylixos_execl_child_demo/HYSylixos_execl_child_demo"  
int main (int argc, char *argv[])  
{  
    int ret = 0;  
      
    // ……  
      
    ret = execlp(CHILD_PATH, CHILD_PATH,"execlp demo",NULL);  
    if(ret != 0)  
    {  
        perror("execle");  
        return -1;  
    }   
      
    // ……  
  
    return 0;  
}  
```  
  
  
## execv 执行文件函数
  
### 函数原型：
  
```c  
int execv  
(  
	const char *path,   
	char * const *argv  
);  
```  
  
  
### 描述：
  
用来执行参数path字符串所代表的文件路径，execv函数会覆盖本进程环境。  
  
### 参数：
  
- path ：可执行文件的路径。  
  
- argv0, ... ：传递给可执行文件的参数。  
  
### 返回值：
  
成功返回0，失败-1以及对应的错误码。  
  
### 错误码： 
  
- EINVAL ：参数无效。  
- ENOMEM ：内存不足。  
  
### 样例：
  
子进程：  
  
```c  
#include <stdio.h>  
#include <stdlib.h>  
#include <process.h>  
#include <unistd.h>  
int main (int argc, char *argv[])  
{  
    char *env_parent = (char*)0;  
  
    if (argc != 2)  
    {  
        printf("child process param error!\n");  
        return  (-1);  
    }  
  
    while(1)  
    {  
        printf("this is %s function test\n",argv[1]);  
        env_parent = getenv("PARENT");  
        if (env_parent)   
		{  
            printf("environment variable: PARENT = %s\n", env_parent);  
        }  
        sleep(2);  
	}  
      
	return  (0);  
}  
```  
  
  
父进程：  
  
```c  
#include <stdio.h>  
#include <stdlib.h>  
#include <process.h>  
#include <unistd.h>  
#define CHILD_PATH  "../HYSylixos_execl_child_demo/HYSylixos_execl_child_demo"  
int main (int argc, char *argv[])  
{  
    int ret = 0;  
    char *arg[3] = {CHILD_PATH,"execv",NULL};  
  
// ……  
  
    ret = execv(CHILD_PATH,arg);  
    if(ret != 0)  
    {  
        perror("execl");  
        return -1;  
	}  
	// ……  
    return 0;  
}  
  
```  
  
  
## execve 执行文件函数
  
  
### 函数原型：
  
```c  
int execve  
(  
	const char *path,   
	char * const *argv,   
	char * const *envp  
);  
```  
  
  
### 描述：
  
用来执行参数path字符串所代表的文件路径。  
  
### 参数：
  
- Path ：可执行文件的路径。  
  
- argv ：传递给可执行文件的参数。  
  
- envp ：环境变量。  
  
### 返回值：
  
成功返回0，失败-1以及对应的错误码。  
  
### 错误码： 
  
- EINVAL ：参数无效。  
- ENOMEM ：内存不足。  
  
### 样例：
  
子进程：  
  
```c  
#include <stdio.h>  
#include <stdlib.h>  
#include <process.h>  
#include <unistd.h>  
int main (int argc, char *argv[])  
{  
    char *env_parent = (char*)0;  
  
    if (argc != 2)  
    {  
        printf("child process param error!\n");  
        return  (-1);  
    }  
  
    while(1)  
    {  
        printf("this is %s function test\n",argv[1]);  
        env_parent = getenv("PARENT");  
        if (env_parent)   
		{  
            printf("environment variable: PARENT = %s\n", env_parent);  
        }  
  
        sleep(2);  
  
	}  
      
	return  (0);  
}  
```  
  
  
父进程：  
  
```c  
#include <stdio.h>  
#include <stdlib.h>  
#include <process.h>  
#include <unistd.h>  
#define CHILD_PATH  "../HYSylixos_execl_child_demo/HYSylixos_execl_child_demo"  
int main (int argc, char *argv[])  
{  
    int ret = 0;  
    char *arg[3] = {CHILD_PATH,"execve",NULL};  
    char *env[] = { "PARENT=execle_demo", (char *)0 };  
  
    printf("before execl\n");  
  
	ret = execve(CHILD_PATH,arg,env);  
	if(ret != 0)  
    {  
        perror("execl");  
        return -1;  
    }  
    while(1)  
    {  
        printf("after execl\n");  
  
        sleep(1);  
    }  
  
    return 0;  
}  
```  
  
  
## execvp 执行文件函数
  
  
### 函数原型：
  
```c  
int execvp  
(  
	const char *file,   
	char * const *argv  
);  
```  
  
  
### 描述：
  
用来执行参数path字符串所代表的文件路径，接下来的参数代表执行该文件时传递过去的argv[0]、argv[1]// ……，最后一个参数必须用空指针NULL作结束。本函数只能由当前进程主线程调用，否则会返回失败。  
  
### 参数：
  
file ：可执行文件。  
argv ：传递给可执行文件的参数。  
  
### 返回值：
  
成功返回0，失败-1以及对应的错误码。  
  
### 错误码：
  
- EINVAL ：参数无效。  
- ENOMEM ：内存不足。  
- ENOENT ：找不到该命令。  
  
### 样例：
  
子进程：  
  
```c  
#include <stdio.h>  
#include <stdlib.h>  
#include <process.h>  
#include <unistd.h>  
int main (int argc, char *argv[])  
{  
    char *env_parent = (char*)0;  
  
    if (argc != 2)  
    {  
        printf("child process param error!\n");  
        return  (-1);  
    }  
  
    while(1)  
    {  
        printf("this is %s function test\n",argv[1]);  
        env_parent = getenv("PARENT");  
        if (env_parent)   
		{  
            printf("environment variable: PARENT = %s\n", env_parent);  
        }  
  
        sleep(2);  
  
	}  
      
	return  (0);  
}  
```  
  
  
父进程：  
  
```c  
#include <stdio.h>  
#include <stdlib.h>  
#include <process.h>  
#include <unistd.h>  
#define CHILD_PATH  "../HYSylixos_execl_child_demo/HYSylixos_execl_child_demo"  
int main (int argc, char *argv[])  
{  
    int ret = 0;  
    char *arg[3] = {CHILD_PATH,"execvp",NULL};  
  
  
    printf("before execl\n");  
    ret = execvp(CHILD_PATH,arg);  
    if(ret != 0)  
    {  
        perror("execl");  
        return -1;  
    }  
    while(1)  
    {  
        printf("after execl\n");  
  
        sleep(1);  
    }  
  
    return 0;  
}  
```  
  
  
## execvpe 执行文件函数
  
  
### 函数原型：
  
```c  
int execvpe  
(  
	const char *file,  
	char * const *argv,   
	char * const *envp  
);  
```  
  
  
### 描述：
  
用来执行参数path字符串所代表的文件路径，接下来的参数代表执行该文件时传递过去的argv[0]、argv[1]// ……  
  
### 参数：
  
- file ：可执行文件。  
  
- argv ：传递给可执行文件的参数。  
  
- envp ：环境变量。  
  
### 返回值：
  
成功返回0，失败-1以及对应的错误码。  
  
### 错误码： 
  
- EINVAL ：参数无效。  
- ENOMEM ：内存不足。  
  
### 样例：
  
子进程：  
  
```c  
#include <stdio.h>  
#include <stdlib.h>  
#include <process.h>  
#include <unistd.h>  
int main (int argc, char *argv[])  
{  
    char *env_parent = (char*)0;  
  
    if (argc != 2)  
    {  
        printf("child process param error!\n");  
        return  (-1);  
    }  
  
    while(1)  
    {  
        printf("this is %s function test\n",argv[1]);  
        env_parent = getenv("PARENT");  
        if (env_parent)   
		{  
            printf("environment variable: PARENT = %s\n", env_parent);  
        }  
  
        sleep(2);  
  
	}  
      
	return  (0);  
}  
```  
  
  
父进程：  
  
```c  
#include <stdio.h>  
#include <stdlib.h>  
#include <process.h>  
#include <unistd.h>  
#define CHILD_PATH  "../HYSylixos_execl_child_demo/HYSylixos_execl_child_demo"  
int main (int argc, char *argv[])  
{  
    int ret = 0;  
    char *arg[3] = {CHILD_PATH,"execvpe",NULL};  
    char *env[] = { "PARENT=execvpe_demo", (char *)0 };  
  
    printf("before execl\n");  
	ret = execvpe(CHILD_PATH,arg,env);  
	if(ret != 0)  
    {  
        perror("execl");  
        return -1;  
    }  
    while(1)  
    {  
        printf("after execl\n");  
  
        sleep(1);  
    }  
  
    return 0;  
}  
```  
  
  
## posix_spawn 进程创建函数
  
  
### 函数原型：
  
```c  
int posix_spawn  
(  
	pid_t *pid,   
	const char 			*path,  
	const posix_spawn_file_actions_t		*file_actions,  
	const posix_spawnattr_t 			*attrp,   
	char *const 					argv[],  
	char *const 					envp[]  
);  
```  
  
  
### 描述：
  
创建一个进程（子进程）。  
  
### 参数：
  
- pid ：保存新进程id。  
  
- path ：可执行文件路径。  
  
- file_actions ：新进程启动时需要处理的文件操作集。  
  
- attrp ：新进程初始化属性  
  
- argv ：由命令行参数组成的字符串数组。  
  
- envp ：需要预先设置的进程环境变量字符串数组。  
  
### 返回值：
  
此函数成功返回0，失败返回-1并设置错误码  
  
### 错误码： 
  
- EINVAL ：参数无效。  
- ENOENT ：找不到该路径。  
- ENOMEM ：内存不足。  
  
### 样例：
  
子进程：  
  
```c  
#include <stdio.h>  
#include <stdlib.h>  
#include <process.h>  
#include <unistd.h>  
int main (int argc, char *argv[])  
{  
    char *env_parent = (char*)0;  
  
    // ……  
    while(1)  
    {  
        printf("this is %s function test\n",argv[1]);  
        env_parent = getenv("PARENT");  
        if (env_parent)   
		{  
            printf("environment variable: PARENT = %s\n", env_parent);  
        }  
	}  
	// ……  
	return  (0);  
}  
```  
  
  
父进程：  
  
```c  
#include <stdio.h>  
#include <stdlib.h>  
#include <process.h>  
#include <unistd.h>  
#define CHILD_PATH  "../HYSylixos_execl_child_demo/HYSylixos_execl_child_demo"  
#define PARENT     "PARENT=/apps/HYSylixos_posix_spawn_demo/HYSylixos_posix_spawn_demo"  
int main (int argc, char *argv[])  
{  
    posix_spawn_file_actions_t      file_actions;  
    posix_spawnattr_t               spawnattr;  
    pid_t                           pid;  
    char     *cmd[]         = { CHILD_PATH, "execve test", (char *)0 };  
    char     *env[]         = { PARENT, (char *)0 };  
	// ……  
    if (posix_spawn(&pid,CHILD_PATH,&file_actions,&spawnattr, cmd, env) != 0)  
    {  
        posix_spawnattr_destroy(&spawnattr);  
        posix_spawn_file_actions_destroy(&file_actions);  
        return  (-6);  
    }  
	// ……  
    return  (0);  
}  
  
```  
  
  
## posix_spawnp进程创建函数
  
  
### 函数原型：
  
```c  
int posix_spawnp  
(  
	pid_t *pid,   
	const char 			*file,  
	const posix_spawn_file_actions_t	*file_actions,  
	const posix_spawnattr_t 			*attrp,  
	char *const 						 argv[],   
	char *const 						 envp[]  
);  
```  
  
  
### 描述：
  
创建一个进程（子进程）。  
  
### 参数：
  
- pid ：保存新进程id。  
  
- file ：可执行文件名,当文件名不是绝对路径时，会去环境变量中查看对应程序。  
  
- file_actions ：新进程启动时需要处理的文件操作集。  
  
- attrp ：新进程初始化属性。  
  
- argv ：由命令行参数组成的字符串数组。  
  
- envp ：需要预先设置的进程环境变量字符串数组，数组以0结束。  
  
### 返回值：
  
此函数成功返回0，失败返回-1并设置错误码。  
  
### 错误码：
  
- EINVAL ：参数无效。  
- ENOENT ：找不到该路径。  
- ENOMEM ：内存不足。  
  
### 样例：
  
子进程：  
  
```c  
int main (int argc, char *argv[])  
{  
    char *env_parent = (char*)0;  
  
    if (argc != 2)  
    {  
        printf("child process param error!\n");  
        return  (-1);  
    }  
  
    while(1)  
    {  
        printf("this is %s function test\n",argv[1]);  
        env_parent = getenv("PARENT");  
        if (env_parent)   
		{  
            printf("environment variable: PARENT = %s\n", env_parent);  
        }  
        sleep(2);  
	}  
	      
	return  (0);  
}  
```  
  
父进程：  
  
```c  
#include <stdio.h>  
#include <stdlib.h>  
#include <process.h>  
#include <unistd.h>  
#define CHILD_PATH  "../HYSylixos_execl_child_demo/HYSylixos_execl_child_demo"  
#define PARENT     "PARENT=/apps/HYSylixos_posix_spawn_demo/HYSylixos_posix_spawn_demo"  
int main (int argc, char *argv[])  
{  
    posix_spawn_file_actions_t      file_actions;  
    posix_spawnattr_t               spawnattr;  
    pid_t                           pid;  
    char     *cmd[]         = { CHILD_PATH, "execve test", (char *)0 };  
    char     *env[]         = { PARENT, (char *)0 };  
	// ……  
    if (posix_spawnp(&pid,CHILD_PATH,&file_actions,&spawnattr, cmd, env) != 0)  
    {  
        posix_spawnattr_destroy(&spawnattr);  
        posix_spawn_file_actions_destroy(&file_actions);  
        return  (-6);  
    }  
	// ……  
    return  (0);  
}  
  
```  
  
## posix_spawnattr_init 初始化文件属性中的数据结构体
  
  
### 函数原型：
  
```c  
int posix_spawnattr_init  
(  
	posix_spawnattr_t *attrp  
);  
```  
  
  
### 描述：
  
初始化文件属性中的数据结构体。  
  
### 参数：
  
- attrp ：需要初始化的进程属性对象。  
  
### 返回值：
  
此函数成功返回0，失败返回错误码。  
  
### 错误码： 
  
- EINVAL ：参数无效。  
- ENOMEM ：系统内存不足。  
  
### 样例：
  
子进程：  
  
```c  
int main (int argc, char *argv[])  
{  
    char *env_parent = (char*)0;  
  
    if (argc != 2)  
    {  
        printf("child process param error!\n");  
        return  (-1);  
    }  
  
    while(1)  
    {  
        printf("this is %s function test\n",argv[1]);  
        env_parent = getenv("PARENT");  
        if (env_parent)   
		{  
            printf("environment variable: PARENT = %s\n", env_parent);  
        }  
  
        sleep(2);  
  
	}  
      
	return  (0);  
}  
```  
  
  
父进程：  
  
```c  
#include <stdio.h>  
#include <stdlib.h>  
#include <process.h>  
#include <unistd.h>  
#define CHILD_PATH  "../HYSylixos_execl_child_demo/HYSylixos_execl_child_demo"  
#define PARENT     "PARENT=/apps/HYSylixos_posix_spawn_demo/HYSylixos_posix_spawn_demo"  
int main (int argc, char *argv[])  
{  
	// ……  
    posix_spawnattr_t               spawnattr;  
	// ……  
    if (posix_spawnattr_init(&spawnattr) != 0)  
    {  
            fprintf(stderr, "init posix_spawnattr_t failed\n");  
            return  (-1);  
	}  
	// ……  
	posix_spawnattr_destroy(&spawnattr);  
	// ……  
    return  (0);  
}  
```  
  
  
## posix_spawnattr_destroy 销毁文件属性中的数据结构体
  
  
### 函数原型：
  
```c  
int posix_spawnattr_destroy  
(  
	posix_spawnattr_t *attrp  
);  
```  
  
  
### 描述：
  
销毁文件属性中的数据结构体。  
  
### 参数：
  
- attrp ：需要销毁的进程属性对象。  
  
### 返回值：
  
此函数成功返回0，失败返回错误码；  
  
### 错误码：
  
- EINVAL ：参数无效。  
  
### 样例：
  
子进程：  
  
```c  
#include <stdio.h>  
#include <stdlib.h>  
#include <process.h>  
#include <unistd.h>  
int main (int argc, char *argv[])  
{  
    char *env_parent = (char*)0;  
  
    if (argc != 2)  
    {  
        printf("child process param error!\n");  
        return  (-1);  
    }  
  
    while(1)  
    {  
        printf("this is %s function test\n",argv[1]);  
        env_parent = getenv("PARENT");  
        if (env_parent)   
		{  
            printf("environment variable: PARENT = %s\n", env_parent);  
        }  
  
        sleep(2);  
  
	}  
	      
	return  (0);  
}  
```  
  
  
父进程：  
  
```c  
#include <stdio.h>  
#include <stdlib.h>  
#include <process.h>  
#include <unistd.h>  
#define CHILD_PATH  "../HYSylixos_execl_child_demo/HYSylixos_execl_child_demo"  
#define PARENT     "PARENT=/apps/HYSylixos_posix_spawn_demo/HYSylixos_posix_spawn_demo"  
int main (int argc, char *argv[])  
{  
	// ……  
    posix_spawnattr_t               spawnattr;  
	// ……  
    if (posix_spawnattr_init(&spawnattr) != 0)  
    {  
            fprintf(stderr, "init posix_spawnattr_t failed\n");  
            return  (-1);  
	}  
	// ……  
	posix_spawnattr_destroy(&spawnattr);  
	// ……  
    return  (0);  
}  
```  
  
  
## posix_spawnattr_setwd设置进程属性对象中的工作目录
  
  
### 函数原型：
  
```c  
int posix_spawnattr_setwd  
(  
	posix_spawnattr_t *attrp,  
	const char *pwd  
);  
```  
  
  
### 描述：
  
设置进程属性对象中的工作目录。  
  
### 参数：
  
- attrp ：进程属性对象；  
  
- pwd ：以0结尾的新工作目录字符串。  
  
### 返回值：
  
此函数成功返回0，失败返回错误码；  
  
### 错误码：
  
- EINVAL ：参数无效。  
- ENAMETOOLONG ：文件名过长。  
  
### 样例：
  
子进程：  
  
```c  
#include <stdio.h>  
#include <stdlib.h>  
#include <process.h>  
#include <unistd.h>  
int main (int argc, char *argv[])  
{  
    char *env_parent = (char*)0;  
  
    if (argc != 2)  
    {  
        printf("child process param error!\n");  
        return  (-1);  
    }  
  
    while(1)  
    {  
        printf("this is %s function test\n",argv[1]);  
        env_parent = getenv("PARENT");  
        if (env_parent)   
		{  
            printf("environment variable: PARENT = %s\n", env_parent);  
        }  
  
        sleep(2);  
  
	}  
      
	return  (0);  
}  
```  
  
  
父进程：  
  
```c  
#include <stdio.h>  
#include <stdlib.h>  
#include <process.h>  
#include <unistd.h>  
#define CHILD_PATH  "../HYSylixos_execl_child_demo/HYSylixos_execl_child_demo"  
#define PARENT     "PARENT=/apps/HYSylixos_posix_spawn_demo/HYSylixos_posix_spawn_demo"  
int main (int argc, char *argv[])  
{  
    char                            path[256];  
	int                             ret;  
  
    if (posix_spawn_file_actions_init(&file_actions) != 0)  
    {  
        fprintf(stderr, "init posix_spawn_file_actions_t failed\n");  
        return  (-2);  
    }  
  
    // ……  
	ret  = posix_spawnattr_setwd(&spawnattr,  
		 "/apps/HYSylixos_posix_spawn_demo/HYSylixos_posix_spawn_demo");  
    if(ret != 0)  
    {  
       perror("posix_spawnattr_setwd");  
       return  (-1);  
    }  
  
    ret = posix_spawnattr_getwd(&spawnattr,path,256);  
    if(ret != 0)  
    {  
        perror("posix_spawnattr_getwd");  
        return  (-1);  
    }  
    printf("path is %s \n",path);  
    // ……  
    if (posix_spawnp(&pid,CHILD_PATH,&file_actions,&spawnattr, cmd, env) != 0)  
	{  
		posix_spawnattr_destroy(&spawnattr);  
		posix_spawn_file_actions_destroy(&file_actions);  
        return  (-6);  
    }  
  
    // ……  
    return  (0);  
}  
```  
  
  
## posix_spawnattr_getwd 获取进程属性对象中的工作目录
  
  
### 函数原型：
  
```c  
int posix_spawnattr_getwd  
(  
	const posix_spawnattr_t *attrp,   
	char *pwd,  
	size_t 		 size  
);  
```  
  
  
### 描述：
  
获取进程属性对象中的工作目录。  
  
### 参数：
  
- attrp ：进程属性对象。  
  
- pwd ：缓冲区，用于保存工作目录字符串。  
  
- size ：缓冲区长度。  
  
### 返回值：
  
此函数成功返回0，失败返回错误码；  
  
### 错误码：
  
- EINVAL ：参数无效。  
  
### 样例：
  
子进程：  
  
```c  
#include <stdio.h>  
#include <stdlib.h>  
#include <process.h>  
#include <unistd.h>  
int main (int argc, char *argv[])  
{  
    char *env_parent = (char*)0;  
  
    if (argc != 2)  
    {  
        printf("child process param error!\n");  
        return  (-1);  
    }  
  
    while(1)  
    {  
        printf("this is %s function test\n",argv[1]);  
        env_parent = getenv("PARENT");  
        if (env_parent)   
		{  
            printf("environment variable: PARENT = %s\n", env_parent);  
        }  
  
        sleep(2);  
  
	}  
      
	return  (0);  
}  
```  
  
  
父进程：  
  
```c  
#include <stdio.h>  
#include <stdlib.h>  
#include <process.h>  
#include <unistd.h>  
#define CHILD_PATH  "../HYSylixos_execl_child_demo/HYSylixos_execl_child_demo"  
#define PARENT     "PARENT=/apps/HYSylixos_posix_spawn_demo/HYSylixos_posix_spawn_demo"  
int main (int argc, char *argv[])  
{  
    char                            path[256];  
	int                             ret;  
  
    if (posix_spawn_file_actions_init(&file_actions) != 0)  
    {  
        fprintf(stderr, "init posix_spawn_file_actions_t failed\n");  
        return  (-2);  
    }  
  
    // ……  
	ret  = posix_spawnattr_setwd(&spawnattr,  
		 "/apps/HYSylixos_posix_spawn_demo/HYSylixos_posix_spawn_demo");  
    if(ret != 0)  
    {  
       perror("posix_spawnattr_setwd");  
       return  (-1);  
    }  
  
    ret = posix_spawnattr_getwd(&spawnattr,path,256);  
    if(ret != 0)  
    {  
        perror("posix_spawnattr_getwd");  
        return  (-1);  
    }  
    printf("path is %s \n",path);  
    // ……  
    if (posix_spawnp(&pid,CHILD_PATH,&file_actions,&spawnattr, cmd, env) != 0)  
	{  
		posix_spawnattr_destroy(&spawnattr);  
		posix_spawn_file_actions_destroy(&file_actions);  
        return  (-6);  
    }  
  
    // ……  
    return  (0);  
}  
  
```  
  
## posix_spawnattr_setsigmask设置进程属性对象中的信号掩码
  
  
### 函数原型：
  
```c  
int posix_spawnattr_setsigmask  
(  
	posix_spawnattr_t	*attrp,  
	const sigset_t 		*sigmask  
);  
```  
  
  
### 描述：
  
设置进程属性对象中的信号掩码。  
  
### 参数：
  
- attrp ：进程属性对象；  
  
- sigmask ：需要设置的进程信号掩码。  
  
### 返回值：
  
此函数成功返回0，失败返回错误码；  
  
### 错误码：
  
- EINVAL ：参数无效。  
  
### 样例：
  
子进程：  
  
```c  
#include <stdio.h>  
#include <stdlib.h>  
#include <process.h>  
#include <unistd.h>  
int main (int argc, char *argv[])  
{  
    char *env_parent = (char*)0;  
  
    if (argc != 2)  
    {  
        printf("child process param error!\n");  
        return  (-1);  
    }  
  
    while(1)  
    {  
        printf("this is %s function test\n",argv[1]);  
        env_parent = getenv("PARENT");  
        if (env_parent)   
		{  
            printf("environment variable: PARENT = %s\n", env_parent);  
        }  
  
        sleep(2);  
  
	}  
      
	return  (0);  
}  
```  
  
  
父进程：  
  
```c  
#include <stdio.h>  
#include <stdlib.h>  
#include <process.h>  
#include <unistd.h>  
int main (int argc, char *argv[])  
{  
    int                             ret;  
    sigset_t                        set_mask = 1;  
    sigset_t                        mask;  
  
    // ……  
	ret = posix_spawnattr_setsigmask(&spawnattr,&set_mask);  
    if(ret != 0)  
    {  
        perror("posix_spawnattr_getsigmask");  
    }  
    ret = posix_spawnattr_getsigmask(&spawnattr,&mask);  
    if(ret != 0)  
    {  
        perror("posix_spawnattr_getsigmask");  
    }  
    printf("mask is %lld\n",mask);  
    // ……  
    return  (0);  
}  
```  
  
  
## posix_spawnattr_getsigmask ：获取进程属性对象中的信号掩码
  
  
### 函数原型：
  
```c  
int posix_spawnattr_getsigmask  
(  
	const posix_spawnattr_t		*attrp,  
	sigset_t 					*sigmask  
);  
```  
  
  
### 描述：
  
获取进程属性对象中的信号掩码。  
  
### 参数：
  
- attrp ：进程的属性结构体。  
  
- sigmask ：信号掩码。  
  
### 返回值：
  
成功返回0，失败返回错误码。  
  
### 错误码：
  
- EINVAL ：参数无效。  
  
### 样例：
  
子进程：  
  
```c  
#include <stdio.h>  
#include <stdlib.h>  
#include <process.h>  
#include <unistd.h>  
int main (int argc, char *argv[])  
{  
    char *env_parent = (char*)0;  
  
    if (argc != 2)  
    {  
        printf("child process param error!\n");  
        return  (-1);  
    }  
  
    while(1)  
    {  
        printf("this is %s function test\n",argv[1]);  
        env_parent = getenv("PARENT");  
        if (env_parent)   
		{  
            printf("environment variable: PARENT = %s\n", env_parent);  
        }  
  
        sleep(2);  
  
	}  
      
	return  (0);  
}  
```  
  
  
父进程：  
  
```c  
#include <stdio.h>  
#include <stdlib.h>  
#include <process.h>  
#include <unistd.h>  
int main (int argc, char *argv[])  
{  
    int                             ret;  
    sigset_t                        set_mask = 1;  
    sigset_t                        mask;  
  
   
    // ……  
	ret = posix_spawnattr_setsigmask(&spawnattr,&set_mask);  
    if(ret != 0)  
    {  
        perror("posix_spawnattr_getsigmask");  
    }  
    ret = posix_spawnattr_getsigmask(&spawnattr,&mask);  
    if(ret != 0)  
    {  
        perror("posix_spawnattr_getsigmask");  
    }  
    printf("mask is %lld\n",mask);  
    // ……  
    return  (0);  
}  
```  
  
  
## posix_spawnattr_setflags ：获取进程属性对象中的标记位
  
  
### 函数原型：
  
```c  
int posix_spawnattr_setflags  
(  
	posix_spawnattr_t *attrp,  
	short 			flags  
);  
```  
  
  
### 描述：
  
获取属性结构体中的标志位。  
  
### 参数：
  
- attrp ：进程属性结构体。  
  
- Flags ：标志位。  
  
|宏名|对应值|  
|---|---|  
|POSIX_SPAWN_RESETIDS|0x01|  
|POSIX_SPAWN_SETPGROUP|0x02|  
|POSIX_SPAWN_SETSIGDEF|0x04|  
|POSIX_SPAWN_SETSIGMASK|0x08|  
|POSIX_SPAWN_SETSCHEDPARAM|0x10|  
|POSIX_SPAWN_SETSCHEDULER|0x20|  
  
### 返回值：
  
成功返回0，失败返回对应的错误码。  
  
### 错误码：
  
- EINVAL ：参数无效。  
  
### 样例：
  
子进程：  
  
```c  
#include <stdio.h>  
#include <stdlib.h>  
#include <process.h>  
#include <unistd.h>  
int main (int argc, char *argv[])  
{  
    char *env_parent = (char*)0;  
  
    if (argc != 2)  
    {  
        printf("child process param error!\n");  
        return  (-1);  
    }  
  
    while(1)  
    {  
        printf("this is %s function test\n",argv[1]);  
        env_parent = getenv("PARENT");  
        if (env_parent)   
		{  
            printf("environment variable: PARENT = %s\n", env_parent);  
        }  
        sleep(2);  
	}  
	      
	return  (0);  
}  
```  
  
  
父进程：  
  
```c  
#include <stdio.h>  
#include <stdlib.h>  
#include <process.h>  
#include <unistd.h>  
int main (int argc, char *argv[])  
{  
    posix_spawnattr_t               spawnattr;  
    int                             ret;  
    short                           flag;  
    // ……  
    ret = posix_spawnattr_setflags(&spawnattr, POSIX_SPAWN_SETSCHEDPARAM);  
    if(ret != 0)  
    {  
        perror("posix_spawnattr_setflags");  
    }  
  
    ret = posix_spawnattr_getflags(&spawnattr,&flag);  
    if(ret != 0)  
    {  
        perror("posix_spawnattr_getflags");  
    }  
  
    printf("flag is %d\n",flag);  
    // ……  
    return  (0);  
}  
  
```  
  
## posix_spawnattr_getflags ：设置属性结构体中的标志位
  
  
### 函数原型：
  
```c  
int posix_spawnattr_getflags  
(  
	const posix_spawnattr_t 	*attrp,  
  
	short 				*flags  
);  
```  
  
  
### 描述：
  
设置属性结构体中的标志位。  
  
### 参数：
  
- attrp ：进程属性结构体。  
  
- Flags ：标志位。  
  
### 返回值：
  
成功返回0，失败返回对应的错误码。  
  
### 错误码：
  
- EINVAL ：参数无效。  
  
### 样例：
  
子进程：  
  
```c  
#include <stdio.h>  
#include <stdlib.h>  
#include <process.h>  
#include <unistd.h>  
int main (int argc, char *argv[])  
{  
    char *env_parent = (char*)0;  
  
    if (argc != 2)  
    {  
        printf("child process param error!\n");  
        return  (-1);  
    }  
  
    while(1)  
    {  
        printf("this is %s function test\n",argv[1]);  
        env_parent = getenv("PARENT");  
        if (env_parent)   
		{  
            printf("environment variable: PARENT = %s\n", env_parent);  
        }  
  
        sleep(2);  
  
	}  
      
	return  (0);  
}  
```  
  
  
父进程：  
  
```c  
#include <stdio.h>  
#include <stdlib.h>  
#include <process.h>  
#include <unistd.h>  
int main (int argc, char *argv[])  
{  
    posix_spawnattr_t               spawnattr;  
    int                             ret;  
    short                           flag;  
    // ……  
    ret = posix_spawnattr_setflags(&spawnattr, POSIX_SPAWN_SETSCHEDPARAM);  
    if(ret != 0)  
    {  
        perror("posix_spawnattr_setflags");  
    }  
    ret = posix_spawnattr_getflags(&spawnattr,&flag);  
    if(ret != 0)  
    {  
        perror("posix_spawnattr_getflags");  
    }  
  
    printf("flag is %d\n",flag);  
    // ……  
    return  (0);  
}  
```  
  
  
## posix_spawnattr_setpgroup ：设置属性结构体中的进程组ID
  
### 函数原型：
  
```c  
int posix_spawnattr_setpgroup  
(  
	posix_spawnattr_t 	*attrp,   
	pid_t 			pgroup  
);  
```  
  
  
### 描述：
  
设置属性结构体中的进程组ID。  
  
### 参数：
  
- attrp ：进程的属性结构体。  
  
- Pgroup ：组ID号。  
  
### 返回值：
  
成功返回0，失败返回错误码。  
  
### 错误码： 
  
- EINVAL ：参数无效。  
  
### 样例：
  
子进程：  
  
```c  
#include <stdio.h>  
#include <stdlib.h>  
#include <process.h>  
#include <unistd.h>  
int main (int argc, char *argv[])  
{  
    char *env_parent = (char*)0;  
  
    if (argc != 2)  
    {  
        printf("child process param error!\n");  
        return  (-1);  
    }  
  
    while(1)  
    {  
        printf("this is %s function test\n",argv[1]);  
        env_parent = getenv("PARENT");  
        if (env_parent)   
		{  
            printf("environment variable: PARENT = %s\n", env_parent);  
        }  
  
        sleep(2);  
  
	}  
      
	return  (0);  
}  
```  
  
  
父进程：  
  
```c  
#include <stdio.h>  
#include <stdlib.h>  
#include <process.h>  
#include <unistd.h>  
int main (int argc, char *argv[])  
{  
    pid_t                           pid;  
    int                             ret;  
  
    // ……  
    ret = posix_spawnattr_setpgroup(&spawnattr,(pid_t)222);  
    if(ret != 0)  
    {  
        perror("posix_spawnattr_setpgroup");  
    }  
  
    ret = posix_spawnattr_getpgroup(&spawnattr,&pid);  
    if(ret != 0)  
    {  
        perror("posix_spawnattr_getpgroup");  
    }  
    printf("group pid is %d\n",pid);  
    // ……  
    return  (0);  
}  
  
```  
  
## posix_spawnattr_getpgroup ：从属性结构体中获取进程组ID
  
  
### 函数原型：
  
```c  
int posix_spawnattr_getpgroup  
(  
	const posix_spawnattr_t *attrp,   
	pid_t 					*pgroup  
);  
```  
  
  
### 描述：
  
从属性结构体中获取进程组ID  
  
### 参数：
  
- attrp ：进程的属性结构体。  
  
- Pgroup ：组ID号。  
  
### 返回值：
  
成功返回0，失败返回错误码。  
  
### 错误码：
  
- EINVAL ：参数无效。  
  
### 样例：
  
子进程：  
  
```c  
#include <stdio.h>  
#include <stdlib.h>  
#include <process.h>  
#include <unistd.h>  
int main (int argc, char *argv[])  
{  
    char *env_parent = (char*)0;  
  
    if (argc != 2)  
    {  
        printf("child process param error!\n");  
        return  (-1);  
    }  
  
    while(1)  
    {  
        printf("this is %s function test\n",argv[1]);  
        env_parent = getenv("PARENT");  
        if (env_parent)   
		{  
            printf("environment variable: PARENT = %s\n", env_parent);  
        }  
  
        sleep(2);  
  
	}  
      
	return  (0);  
}  
```  
  
  
父进程：  
  
```c  
#include <stdio.h>  
#include <stdlib.h>  
#include <process.h>  
#include <unistd.h>  
int main (int argc, char *argv[])  
{  
    pid_t                           pid;  
    int                             ret;  
  
    // ……  
    ret = posix_spawnattr_setpgroup(&spawnattr,(pid_t)222);  
    if(ret != 0)  
    {  
        perror("posix_spawnattr_setpgroup");  
    }  
  
    ret = posix_spawnattr_getpgroup(&spawnattr,&pid);  
    if(ret != 0)  
    {  
        perror("posix_spawnattr_getpgroup");  
    }  
    printf("group pid is %d\n",pid);  
    // ……  
    return  (0);  
}  
```  
  
  
## posix_spawnattr_getschedpolicy ：获取进程调度优先级
  
  
### 函数原型：
  
```c  
int posix_spawnattr_getschedpolicy  
(  
	const posix_spawnattr_t 	*attrp,  
	int 							*schedpolicy  
);  
```  
  
  
### 描述：
  
该接口用来获取进程属性块创建进程的调度策略。  
  
### 参数：
  
- attrp ：指向进程属性块的指针。  
  
- schedpolicy ：存放调度策略的指针。  
  
### 返回值：
  
成功返回0，失败返回错误码。  
  
### 错误码：
  
- EINVAL ：参数无效。  
  
### 样例：
  
子进程：  
  
```c  
int main (int argc, char *argv[])  
{  
    while(1)  
	{  
    // ……  
        printf("this is child process\n");  
        // ……  
	}  
      
	return  (0);  
}  
```  
  
  
父进程：  
  
```c  
#include <stdio.h>  
#include <stdlib.h>  
#include <process.h>  
#include <unistd.h>  
#define CHILD_PATH  "../HYSylixos_execl_child_demo/HYSylixos_execl_child_demo"  
#define PARENT     "PARENT=/apps/HYSylixos_posix_spawn_demo/HYSylixos_posix_spawn_demo"  
int main (int argc, char *argv[])  
{  
    posix_spawnattr_t               spawnattr;  
    posix_spawnattr_t               spawnattr1;  
    pid_t                           pid;  
    int                             ret;  
    int                             polic;  
    // ……  
	ret = posix_spawnattr_setschedpolicy(&spawnattr,50);  
    if(ret != 0)  
    {  
        perror("posix_spawnattr_getschedpolicy");  
        return -1;  
    }  
  
    ret = posix_spawnattr_setschedpolicy(&spawnattr1,55);  
    if(ret != 0)  
    {  
        perror("posix_spawnattr_getschedpolicy");  
        return -1;  
    }  
  
    ret = posix_spawnattr_getschedpolicy(&spawnattr,&polic);  
    if(ret != 0)  
    {  
        perror("posix_spawnattr_getschedpolicy");  
        return -1;  
    }  
    printf("schedpolicy is %d \n",polic);  
    ret = posix_spawnattr_getschedpolicy(&spawnattr1,&polic);  
    if(ret != 0)  
  
    {  
        perror("posix_spawnattr_getschedpolicy");  
        return -1;  
    }  
  
    printf("schedpolicy is %d \n",polic);  
    // ……  
    return  (0);  
}  
```  
  
## posix_spawnattr_setschedpolicy ：设置调度优先级
  
  
### 函数原型：
  
```c  
int   posix_spawnattr_setschedpolicy   
(  
	posix_spawnattr_t *attrp,  
	int schedpolicy  
)  
```  
  
  
### 描述：
  
该接口用来设置进程属性块创建进程的调度策略。  
  
### 参数：
  
- attrp ：进程属性。  
  
- schedpolicy ：调度策略的指针，取值有：  
  
|宏名|解释|  
|---|---|  
|SCHED_OTHER|抢占式调度算法|  
|SCHED_FIFO|先进先出调度算法|  
|SCHED_RR|不支持轮转调度算法|  
|SCHED_SPORADIC|不支持|  
  
### 返回值：
  
成功返回0，失败返回错误码。  
  
### 错误码：
  
- EINVAL ：参数无效。  
  
### 样例：
  
子进程：  
  
```c  
int main (int argc, char *argv[])  
{  
    while(1)  
	{  
        // ……  
        printf("this is child process\n");  
        // ……  
	}  
      
	return  (0);  
}  
```  
  
  
父进程：  
  
```c  
#include <stdio.h>  
#include <stdlib.h>  
#include <process.h>  
#include <unistd.h>  
#define CHILD_PATH  "../HYSylixos_execl_child_demo/HYSylixos_execl_child_demo"  
#define PARENT     "PARENT=/apps/HYSylixos_posix_spawn_demo/HYSylixos_posix_spawn_demo"  
int main (int argc, char *argv[])  
{  
    posix_spawnattr_t               spawnattr;  
    posix_spawnattr_t               spawnattr1;  
    pid_t                           pid;  
    int                             ret;  
    int                             polic;  
    // ……  
	ret = posix_spawnattr_setschedpolicy(&spawnattr,50);  
    if(ret != 0)  
    {  
        perror("posix_spawnattr_getschedpolicy");  
        return -1;  
    }  
  
    ret = posix_spawnattr_setschedpolicy(&spawnattr1,55);  
    if(ret != 0)  
    {  
        perror("posix_spawnattr_getschedpolicy");  
        return -1;  
    }  
  
    ret = posix_spawnattr_getschedpolicy(&spawnattr,&polic);  
    if(ret != 0)  
    {  
        perror("posix_spawnattr_getschedpolicy");  
        return -1;  
    }  
    printf("schedpolicy is %d \n",polic);  
    ret = posix_spawnattr_getschedpolicy(&spawnattr1,&polic);  
    if(ret != 0)  
  
    {  
        perror("posix_spawnattr_getschedpolicy");  
        return -1;  
    }  
  
    printf("schedpolicy is %d \n",polic);  
    // ……  
    return  (0);  
}  
  
```  
  
## posix_spawnattr_setschedparam ：设置进程属性中的调度参数
  
  
### 函数原型：
  
```c  
int posix_spawnattr_setschedparam  
(  
	posix_spawnattr_t 			*attrp,  
	const struct sched_param 	*schedparam  
);     
```  
  
  
### 描述：
  
设置进程属性对象中的调度参数。  
  
### 参数：
  
- attrp ：进程属性。  
  
- Schedparam ：调度参数。  
  
### 返回值：
  
成功返回0，失败返回错误码。  
  
### 错误码：
  
- EINVAL ：参数无效。  
  
### 样例：
  
子进程：  
  
```c  
int main (int argc, char *argv[])  
{  
    while(1)  
	{  
        // ……  
        printf("this is child process\n");  
        // ……  
	}  
      
	return  (0);  
}  
```  
  
  
父进程：  
  
```c  
#include <stdio.h>  
#include <stdlib.h>  
#include <process.h>  
#include <unistd.h>  
#define CHILD_PATH  "../HYSylixos_execl_child_demo/HYSylixos_execl_child_demo"  
int main (int argc, char *argv[])  
{  
    int ret = 0;  
  
    printf("before execle\n");  
    ret = execlp(CHILD_PATH, CHILD_PATH,"execlp demo",NULL);  
    if(ret != 0)  
    {  
        perror("execle");  
        return -1;  
    }  
  
    printf("after execle\n");  
  
    return 0;  
}  
```  
  
  
## posix_spawnattr_getschedparam：获取进程属性对象中调度参数
  
  
### 函数原型：
  
```c  
int posix_spawnattr_getschedparam  
(  
	const posix_spawnattr_t 	*attrp,  
	struct sched_param 		*schedparam  
);  
```  
  
  
### 描述：
  
获取进程属性对象中的调度参数。  
  
### 参数：
  
- attrp ：进程属性。  
  
- Schedparam ：调度参数。  
  
### 返回值：
  
成功返回0，失败返回错误码。  
  
### 错误码： 
  
- EINVAL ：参数无效。  
  
### 样例：
  
子进程：  
  
```c  
#include <stdio.h>  
#include <stdlib.h>  
#include <process.h>  
#include <unistd.h>  
int main (int argc, char *argv[])  
{  
    while(1)  
	{  
        // ……  
        printf("this is %s function test\n",argv[1]);  
        // ……  
	}  
      
	return  (0);  
}  
```  
  
  
父进程：  
  
```c  
#include <stdio.h>  
#include <stdlib.h>  
#include <process.h>  
#include <unistd.h>  
#define CHILD_PATH  "../HYSylixos_execl_child_demo/HYSylixos_execl_child_demo"  
#define PARENT     "PARENT=/apps/HYSylixos_posix_spawn_demo/HYSylixos_posix_spawn_demo"  
int main (int argc, char *argv[])  
{  
    posix_spawnattr_t               spawnattr;  
    struct sched_param              schedparam;  
    struct sched_param              schedparam1;  
    int                             ret;  
	// ……  
    schedparam.sched_priority = PX_PRIORITY_CONVERT(LW_PRIO_NORMAL);  
    ret = posix_spawnattr_setschedparam(&spawnattr, &schedparam);   
    if(ret != 0)  
    {  
        perror("posix_spawnattr_setschedparam");  
        return -1;  
    }  
  
    ret = posix_spawnattr_getschedparam(&spawnattr, &schedparam1);   
    if(ret != 0)  
    {  
        perror("posix_spawnattr_getschedparam");  
        return -1;  
    }  
    printf("schedpolicy is %d \n",polic);  
    // ……  
    return  (0);  
}  
```  
  
  
## posix_spawn_file_actions_init ：初始化文件操作集对象
  
  
### 函数原型：
  
```c  
int posix_spawn_file_actions_init  
(  
	posix_spawn_file_actions_t *file_actions  
);  
```  
  
  
### 描述：
  
初始化文件操作集对象。  
  
### 参数：
  
- file_actions ：文件属性。  
  
### 返回值：
  
成功返回0，失败返回错误码。  
  
### 错误码： 
  
- EINVAL ：参数无效。  
- ENOMEM ：内存不足。  
  
### 样例：
  
子进程：  
  
```c  
#include <stdio.h>  
#include <stdlib.h>  
#include <process.h>  
#include <unistd.h>  
int main (int argc, char *argv[])  
{  
    char *env_parent = (char*)0;  
  
    if (argc != 2)  
    {  
        printf("child process param error!\n");  
        return  (-1);  
    }  
  
    while(1)  
	{  
    // ……  
        printf("this is %s function test\n",argv[1]);  
        env_parent = getenv("PARENT");  
        if (env_parent)  
        {  
            printf("environment variable: PARENT = %s\n", env_parent);  
        }  
        // ……  
    }  
    return  (0);  
}  
```  
  
  
父进程：  
  
```c  
#include <stdio.h>  
#include <stdlib.h>  
#include <process.h>  
#include <unistd.h>  
int main (int argc, char *argv[])  
{  
	int                          ret;  
	posix_spawn_file_actions_t      file_actions;  
	// ……  
    if (posix_spawn_file_actions_init(&file_actions) != 0)  
    {  
        fprintf(stderr, "init posix_spawn_file_actions_t failed\n");  
        return  (-2);  
	}  
	// ……  
    return 0;  
}  
```  
  
  
## posix_spawn_file_actions_destroy ：销毁文件操作集对象
  
  
### 函数原型：
  
```c  
int posix_spawn_file_actions_destroy  
(  
	posix_spawn_file_actions *file_actions  
);  
```  
  
### 描述：
  
销毁文件操作集对象。  
  
### 参数：
  
- file_actions ：文件属性。  
  
### 返回值：
  
成功返回0，失败返回错误码。  
  
### 错误码： 
  
- EINVAL ：参数无效。  
  
### 样例：
  
子进程：  
  
```c  
#include <stdio.h>  
#include <stdlib.h>  
#include <process.h>  
#include <unistd.h>  
int main (int argc, char *argv[])  
{  
    char *env_parent = (char*)0;  
  
    if (argc != 2)  
    {  
        printf("child process param error!\n");  
        return  (-1);  
    }  
  
    while(1)  
	{  
        // ……  
        printf("this is %s function test\n",argv[1]);  
        env_parent = getenv("PARENT");  
        if (env_parent)  
        {  
            printf("environment variable: PARENT = %s\n", env_parent);  
        }  
        // ……  
    }  
    return  (0);  
}  
```  
  
  
父进程：  
  
```c  
#include <stdio.h>  
#include <stdlib.h>  
#include <process.h>  
#include <unistd.h>  
#define CHILD_PATH  "../child_proc_demo/child_proc_demo"  
int main (int argc, char *argv[])  
{  
    int ret = 0;  
    …..  
    ret = posix_spawn_file_actions_destroy(&file_actions);  
    if(ret != 0)  
    {  
        perror("posix_spawnattr_destroy");  
        return -1;  
	}  
	// ……  
    return 0;  
}  
  
```  
  
## posix_spawn_file_actions_addopen ：添加文件打开操作到文件操作集
  
  
### 函数原型：
  
```c  
int posix_spawn_file_actions_addopen  
(  
	posix_spawn_file_actions_t	*file_actions,  
	int     				fd,   
	const char			*path,   
	int 					oflag,   
	mode_t 				mode  
);  
```  
  
  
### 描述：
  
添加文件关闭操作到文件操作集对象。  
  
### 参数：
  
- file_actions ：文件属性。  
  
- fd ：文件描述符。  
  
- path ：文件路径。  
  
- oflag ：操作文件标志。  
  
- Mode ：操作文件方式。  
  
### 返回值：
  
此函数成功返回0，失败返回错误码；  
  
### 错误码：
  
- EINVAL ：参数无效。  
- EBADF ：错误的文件号。  
- ENOMEM ：系统内存资源不足。  
- ENAMETOOLONG ：路径名太长。  
  
### 样例：
  
子进程：  
  
```c  
#include <stdio.h>  
#include <stdlib.h>  
#include <process.h>  
#include <unistd.h>  
int main (int argc, char *argv[])  
{  
    char *env_parent = (char*)0;  
  
    if (argc != 2)  
    {  
        printf("child process param error!\n");  
        return  (-1);  
    }  
  
    while(1)  
	{  
    	// ……  
        printf("this is %s function test\n",argv[1]);  
        env_parent = getenv("PARENT");  
        if (env_parent)  
        {  
            printf("environment variable: PARENT = %s\n", env_parent);  
        }  
        // ……  
    }  
    return  (0);  
}  
```  
  
父进程：  
  
```c  
#include <stdio.h>  
#include <stdlib.h>  
#include <process.h>  
#include <unistd.h>  
#define CHILD_PATH  "../child_proc_demo/child_proc_demo"  
int main (int argc, char *argv[])  
{  
    int ret = 0;  
    posix_spawn_file_actions_t      file_actions;  
	…...  
    if (posix_spawn_file_actions_addopen(&file_actions,STDOUT_FILENO,log_file,  
                             O_WRONLY | O_CREAT | O_TRUNC,mode) != 0)  
    {  
        fprintf (stderr, "redirection std output failed\n");  
        return  (-4);  
	}  
	// ……  
    return 0;  
}  
  
```  
  
## posix_spawn_file_actions_addclose ：添加文件关闭操作到文件操作集

  
### 函数原型：
  
```c  
int posix_spawn_file_actions_addclose  
(  
	posix_spawn_file_actions_t	*file_actions,  
	int 					fd  
);  
```  
  
  
### 描述：
  
添加文件关闭操作到文件操作集对象。  
  
### 参数：
  
- file_actions ：文件操作集对象。  
  
- fd ：需要关闭的文件号。  
  
### 返回值：
  
此函数成功返回0，失败返回错误码；  
  
### 错误码：
  
- EINVAL ：参数无效。  
- EBADF ：错误的文件号。  
- ENOMEM ：系统内存资源不足。  
  
### 样例：
  
子进程：  
  
```c  
#include <stdio.h>  
#include <stdlib.h>  
#include <process.h>  
#include <unistd.h>  
int main (int argc, char *argv[])  
{  
    char *env_parent = (char*)0;  
  
    if (argc != 2)  
    {  
        printf("child process param error!\n");  
        return  (-1);  
    }  
  
    while(1)  
	{  
    // ……  
        printf("this is %s function test\n",argv[1]);  
        env_parent = getenv("PARENT");  
        if (env_parent)  
        {  
            printf("environment variable: PARENT = %s\n", env_parent);  
        }  
        // ……  
    }  
    return  (0);  
}  
```  
  
  
父进程：  
  
```c  
#include <stdio.h>  
#include <stdlib.h>  
#include <process.h>  
#include <unistd.h>  
#define CHILD_PATH  "../child_proc_demo/child_proc_demo"  
int main (int argc, char *argv[])  
{  
    int ret = 0;  
    posix_spawn_file_actions_t      file_actions;  
	…...  
    if (  posix_spawn_file_actions_addclose(&file_actions, STDIN_FILENO)  != 0 ||  
        posix_spawn_file_actions_addclose(&file_actions, STDOUT_FILENO) != 0 ||  
        posix_spawn_file_actions_addclose(&file_actions, STDERR_FILENO) != 0)  
    {  
        return  (-3);  
	}  
	// ……  
    return 0;  
}  
  
```  
  
## posix_spawn_file_actions_adddup2 ：设置新文件描述符到文件操作集
  
  
### 函数原型：
  
```c  
int  posix_spawn_file_actions_adddup2  
(  
	posix_spawn_file_actions_t	*file_actions,  
	int 					fd,   
	int 					newfd  
);  
```  
  
  
### 描述：
  
设置新的文件描述符到文件操作集对象。  
  
### 参数：
  
- file_actions ：文件操作集对象。  
  
- fd ：需要复制的文件号。  
  
- newfd ：指向参数fd对应文件的新文件号。  
  
### 返回值：
  
此函数成功返回0，失败返回错误码。  
  
### 错误码：
  
- EINVAL ：参数无效。  
- EBADF ：错误的文件号。  
- ENOMEM ：系统内存资源不足。  
  
### 样例：
  
子进程：  
  
```c  
#include <stdio.h>  
#include <stdlib.h>  
#include <process.h>  
#include <unistd.h>  
int main (int argc, char *argv[])  
{  
	// ……  
  
    while(1)  
    {  
        printf("this is %s function test\n",argv[1]);  
        env_parent = getenv("PARENT");  
        if (env_parent)  
        {  
            printf("environment variable: PARENT = %s\n", env_parent);  
        }  
    // ……  
    }  
  
    return  (0);  
}  
```  
  
  
父进程：  
  
```c  
#include <stdio.h>  
#include <stdlib.h>  
#include <process.h>  
#include <unistd.h>  
int main (int argc, char *argv[])  
{  
    int ret = 0;  
	posix_spawn_file_actions_t      file_actions;  
	// ……  
    ret = posix_spawn_file_actions_adddup2(&file_actions,STDOUT_FILENO,200);  
    if(ret != 0)  
    {  
        perror("posix_spawn_file_actions_adddup2");  
        return -1;  
    }  
	// ……  
  
    return 0;  
}  
```  
  
  
## posix_spawnattr_getsigdefault ：获取信号集的默认信号掩码
  
  
### 函数原型：
  
```c  
int   posix_spawnattr_getsigdefault  
(  
	const posix_spawnattr_t *attrp,  
	sigset_t *sigdefault  
);  
```  
  
  
### 描述：
  
获取信号集默认的信号掩码。  
  
### 参数：
  
- attrp ：属性指针。  
  
- sigdefault ：信号掩码。  
  
### 返回值：
  
此函数成功返回0，失败返回错误码。  
  
### 错误码：
  
- EINVAL ：参数无效。  
  
### 样例：
  
子进程：  
  
```c  
#include <stdio.h>  
#include <stdlib.h>  
#include <process.h>  
#include <unistd.h>  
int main (int argc, char *argv[])  
{  
    while(1)  
	{  
        // ……  
        printf("this is child process\n");  
        // ……  
	}  
      
	return  (0);  
}  
```  
  
父进程：  
  
```c  
#include <stdio.h>  
#include <stdlib.h>  
#include <process.h>  
#include <unistd.h>  
#define CHILD_PATH  "../HYSylixos_execl_child_demo/HYSylixos_execl_child_demo"   
int main (int argc, char *argv[])  
{  
    int               ret = 0;  
	posix_spawnattr_t  spawnattr;  
	// ……  
    ret = posix_spawnattr_getsigdefault(&spawnattr,&get_sigdefault);  
    if(ret != 0)  
    {  
        perror("posix_spawnattr_getsigdefault");  
        return -1;  
	}   
	// ……  
}  
```  
  
  
## posix_spawnattr_setsigdefault ：设置信号集默认信号掩码
  
  
### 函数原型：
  
```c  
int   posix_spawnattr_setsigdefault  
(  
	posix_spawnattr_t *attrp,  
	const sigset_t *sigdefault  
);  
```  
  
  
### 描述：
  
设置默认的信号集的信号掩码。  
  
### 参数：
  
- attrp ：属性指针。  
  
- sigmask ：信号掩码。  
  
### 返回值：
  
此函数成功返回0，失败返回错误码。  
  
### 错误码：
  
- EINVAL ：参数无效。  
  
### 样例：
  
子进程：  
  
```c  
#include <stdio.h>  
#include <stdlib.h>  
#include <process.h>  
#include <unistd.h>  
int main (int argc, char *argv[])  
{  
    while(1)  
	{  
        // ……  
        printf("this is child process\n");  
        // ……  
	}  
	      
	return  (0);  
}  
```  
  
  
父进程：  
  
```c  
#include <stdio.h>  
#include <spawn.h>  
#include <unistd.h>  
#include <sched.h>  
#include <sylixos.h>  
#include <sys/resource.h>  
#include <stdlib.h>  
#define CHILD_PATH  "../HYSylixos_execl_child_demo/HYSylixos_execl_child_demo"  
int main (int argc, char *argv[])  
{  
    int                  ret = 0;  
	posix_spawnattr_t     spawnattr;  
    // ……  
    ret = posix_spawnattr_setsigdefault(&spawnattr,&set_sigdefault);  
    if(ret != 0)  
    {  
        perror("posix_spawnattr_getsigdefault");  
        return -1;  
	}  
	// ……  
	return 0;  
}  
  
```  
  
## setpriority ：设置满足条件的所有线程的SylixOS调度优先级
  
  
### 函数原型：
  
```c  
int setpriority  
(  
	int which,  
	id_t who,   
	int value  
);  
```  
  
  
### 描述：
  
设置满足条件的所有线程的SylixOS调度优先级。  
  
### 参数：
  
- Which ：指定参数who的意义；  
  
- Who ：意义由参数which指定，用户线程查找。  
  
- which参数与who参数的对应关系  
  
|宏名|解释|  
|---|---|  
|PRIO_PROCESS|参数who的值为进程ID|  
|PRIO_PGRP|参数who的值为组ID|  
|PRIO_USER|参数who的值为用户ID|  
  
- Value ：要设置的SylixOS优先级  
  
### 返回值：
  
此函数成功返回0，失败返回-1。  
  
### 错误码：
  
- EINVAL ：参数无效。  
- ESRCH ：找不到该进程。  
  
### 样例：
  
子进程：  
  
```c  
#include <stdio.h>  
int main (int argc, char *argv[])  
{  
    while(1)  
	{  
    	// ……  
        printf("this is child process\n");  
        // ……  
	}  
          
	return  (0);  
}  
```  
  
父进程：  
  
```c  
#include <stdio.h>  
#include <spawn.h>  
#include <unistd.h>  
#include <sched.h>  
#include <sylixos.h>  
#include <sys/resource.h>  
#include <stdlib.h>  
int main (int argc, char *argv[])  
{  
    int                       ret;  
    // ……  
    ret= setpriority(PRIO_PROCESS,getpid(),10);   
    if(ret < 0 )  
    {  
        perror("setpriority");  
        return -1;  
    }  
    ret = getpriority(PRIO_PROCESS,getpid());  
    if(ret < 0 )  
    {  
        perror("getpriority");  
        return -1;  
    }  
    printf("father priority is %d,",ret);  
    // ……  
    return  (0);  
}  
```  
  
  
## getpriority ：获取系统调度优先级  
  
  
### 函数原型：
  
```c  
int getpriority  
(  
	int which,   
	id_t who  
);  
```  
  
### 描述：
  
获取系统调度优先级。  
  
### 参数：
  
- Which ：指定参数who的意义。具体参考getpriority函数；  
  
- Who ：参数which指定，用于线程查找。  
  
### 返回值：
  
成功返回满足条件的所有线程中的SylixOS优先级最大值，注意，这里是优先级值最大，因此优先级最低。失败返回-1。  
  
### 错误码：
  
- EINVAL ：参数无效。  
- ESRCH ：找不到该进程。  
  
### 样例：
  
子进程：  
  
```c  
#include <stdio.h>  
int main (int argc, char *argv[])  
{  
    while(1)  
	{  
    	// ……  
        printf("this is child process\n");  
        // ……  
	}  
          
	return  (0);  
}  
```  
  
  
父进程：  
  
```c  
#include <stdio.h>  
#include <spawn.h>  
#include <unistd.h>  
#include <sched.h>  
#include <sylixos.h>  
#include <sys/resource.h>  
#include <stdlib.h>  
int main (int argc, char *argv[])  
{  
    int    ret;  
    // ……  
    ret= setpriority(PRIO_PROCESS,getpid(),10);   
    if(ret < 0 )  
    {  
        perror("setpriority");  
        return -1;  
    }  
    ret = getpriority(PRIO_PROCESS,getpid());  
    if(ret < 0 )  
    {  
        perror("getpriority");  
        return -1;  
    }  
    printf("father priority is %d,",ret);  
    // ……  
    return  (0);  
}  
  
```  
  
## nice ：调整当前进程优先级
  
### 函数原型：
  
```c  
int nice  
(  
	int incr  
);  
```  
  
  
### 描述：
  
调整当前进程优先级。  
  
### 参数：
  
- incr ：要调整的数值。  
  
### 返回值：
  
此函数成功返回0，失败返回-1并置错误码。  
  
### 错误码：
  
- EINVAL ：参数无效。  
  
### 样例：
  
子进程：  
  
```c  
#include <stdio.h>  
int main (int argc, char *argv[])  
{  
    while(1)  
	{  
    	// ……  
        ret = nice(1);                                                                  
        if(ret < 0)  
        {  
            perror("nice");  
            return -1;  
        }  
        // ……  
	}  
      
	return  (0);  
}  
```  
  
父进程：  
  
```c  
#include <stdio.h>  
#include <spawn.h>  
#include <unistd.h>  
#include <sched.h>  
#include <sylixos.h>  
#include <sys/resource.h>  
#include <stdlib.h>  
int main (int argc, char *argv[])  
{  
    int                       ret;  
    // ……  
    ret= setpriority(PRIO_PROCESS,getpid(),10);   
    if(ret < 0 )  
    {  
        perror("setpriority");  
        return -1;  
    }  
    ret = getpriority(PRIO_PROCESS,getpid());  
    if(ret < 0 )  
    {  
        perror("getpriority");  
        return -1;  
    }  
    printf("father priority is %d,",ret);  
    // ……  
    return  (0);  
}  
  
```  
  
## sched_setaffinity ：函数锁定进程所有线程在指定cpu集上运行
  
  
### 函数原型：
  
```c  
int sched_setaffinity  
(  
	pid_t pid, size_t setsize,   
	const cpu_set_t *set  
);  
```  
  
### 描述：
  
函数锁定进程所有线程在指定cpu集上运行，只用于多核情况。  
  
### 参数：
  
- Pid ：指定进程ID。  
  
- Set ：指定允许进程执行的处理器核，它是一个2048位的位集合，每个位代表一个处理器核，如果为1表示允许进程在该核上执行，否则表示不允许。  
  
### 返回值：
  
此函数成功返回0，失败返回-1并设置错误码。。  
  
### 错误码：
  
- EINVAL ：参数无效。  
- ESRCH ：该进程不存在。  
  
### 样例：
  
子进程：  
  
```c  
#include <stdio.h>  
int main (int argc, char *argv[])  
{  
    while(1)  
	{  
        // ……  
        printf("this is child process\n");  
        // ……  
	}  
          
	return  (0);  
}  
```  
  
  
父进程：  
  
```c  
#include <stdio.h>  
#include <spawn.h>  
#include <unistd.h>  
#include <sched.h>  
#include <sylixos.h>  
#include <sys/resource.h>  
#include <stdlib.h>  
int main (int argc, char *argv[])  
{  
    int                       ret;  
    cpu_set_t                 set;  
	// ……  
    set.cpus_bits[0] = 1;  
    ret = sched_setaffinity(pid,sizeof(cpu_set_t),&set);  
    if(ret != 0)  
    {  
        perror("sched_setaffinity");  
        return -1;  
    }  
    ret = sched_getaffinity(pid, sizeof(cpu_set_t),&set);  
    if(ret < 0 )  
    {  
        perror("sched_getaffinity");  
        return -1;  
    }  
	printf("affinity is %d\n",(int)set.cpus_bits[0]);  
	// ……  
    return  (0);  
}  
```  
  
  
## sched_getaffinity ：函数获取进程主线程处理器亲和度设置
  
  
### 函数原型：
  
```c  
int sched_getaffinity  
(  
	pid_t pid, size_t setsize,  
	cpu_set_t *set  
);  
```  
  
  
### 描述：
  
函数获取进程主线程处理器亲和度设置。  
  
### 参数：
  
- pid ：指定进程ID；  
  
- set ：表示允许进程执行的处理器核，它是一个2048位的位集合，每个位代表一个处理器核，如果为1表示允许进程在该核上执行，否则表示不允许。  
  
### 返回值：
  
此函数成功返回0，失败返回-1并设置错误号。  
  
### 错误码：
  
- EINVAL ：参数无效。  
- ESRCH ：该进程不存在。  
  
### 样例：
  
子进程：  
  
```c  
#include <stdio.h>  
int main (int argc, char *argv[])  
{  
    while(1)  
	{  
    	// ……  
        printf("this is child process\n");  
        // ……  
	}  
	      
	return  (0);  
}  
```  
  
  
父进程：  
  
```c  
#include <stdio.h>  
#include <spawn.h>  
#include <unistd.h>  
#include <sched.h>  
#include <sylixos.h>  
#include <sys/resource.h>  
#include <stdlib.h>  
int main (int argc, char *argv[])  
{  
    int                       ret;  
    cpu_set_t                 set;  
  
	// ……  
  
    set.cpus_bits[0] = 1;  
    ret = sched_setaffinity(pid,sizeof(cpu_set_t),&set);  
    if(ret != 0)  
    {  
        perror("sched_setaffinity");  
        return -1;  
    }  
  
    ret = sched_getaffinity(pid, sizeof(cpu_set_t),&set);  
    if(ret < 0 )  
    {  
        perror("sched_getaffinity");  
        return -1;  
    }  
	printf("affinity is %d\n",(int)set.cpus_bits[0]);  
	// ……  
    return  (0);  
}  
  
```  
  
## getpid ：获取进程ID
  
  
### 函数原型：
  
```c  
pid_t getpid  
(  
	void  
);  
```  
  
  
### 描述：
  
获取进程ID。  
  
### 参数：
  
无。  
  
### 返回值：
  
此函数返回调用进程ID。  
  
### 错误码：
  
无。  
  
### 样例：
  
子进程：  
  
```c  
#include <stdio.h>  
int main (int argc, char *argv[])  
{  
    while(1)  
	{  
        // ……  
        printf("this is child process\n");  
        // ……  
	}  
      
	return  (0);  
}  
```  
  
  
父进程：  
  
```c  
#include <stdio.h>  
#include <spawn.h>  
#include <unistd.h>  
#include <sched.h>  
#include <sylixos.h>  
#include <sys/resource.h>  
#include <stdlib.h>  
int main (int argc, char *argv[])  
{  
    int                       ret;  
    cpu_set_t                 set;  
	// ……  
    ret = getpriority(PRIO_PROCESS,getpid());   
    if(ret < 0 )  
    {  
        perror("getpriority");  
        return -1;  
    }  
	printf("father priority is %d,",ret);  
	// …….  
    return  (0);  
}  
```  
  
  
## setpgid ：设置进程组ID
  
  
### 函数原型：
  
```c  
int setpgid  
(  
	pid_t pid,   
	pid_t pgid  
);  
```  
  
  
### 描述：
  
设置进程组ID。  
  
### 参数： 
  
- pid ：目标进程ID；  
  
- pgid ：需要设置的进程组ID。  
  
### 返回值：
  
此函数成功返回0，失败返回-1并设置错误码。  
  
### 错误码：
  
- EINVAL ：参数无效。  
- ERROR_KERNEL_IN_ISR ：系统处于中断中。  
- ERROR_KERNEL_HANDLE_NULL ：句柄出错。  
- ERROR_EVENT_TYPE ：事件类型出错。  
- EDEADLK ：死锁。  
- EINTR ：中断回调。  
- ERROR_THREAD_WAIT_TIMEOUT ：等待超时。  
- ERROR_EVENT_WAS_DELETED ：事件已经被删除。  
  
### 备注：  
  
功能实现，但函数报错。  
  
### 样例：
  
子进程：  
  
```c  
#include <stdio.h>  
int main (int argc, char *argv[])  
{  
    while(1)  
	{  
        // ……  
        printf("this is child process\n");  
        // ……  
	}  
	          
	return  (0);  
}  
```  
  
父进程：  
  
```c  
#include <stdio.h>  
#include <spawn.h>  
#include <unistd.h>  
#include <sched.h>  
#include <sylixos.h>  
#include <sys/resource.h>  
#include <stdlib.h>  
int main (int argc, char *argv[])  
{  
    int                       ret;  
    // ……  
    fprintf(stdout, "pgid: %d,pgrp: %d,uid: %d, gid:%d, euid:%d, egid:%d\n",  
            getpgid(0),getpgrp(),getuid(), getgid(), geteuid(), getegid());   
    if(setpgid(0, 1) < 0);  
    {  
        perror("setpgid failed\n");  
    }  
    // ……  
    fprintf(stdout, "pgid: %d,pgrp: %d,uid: %d, gid:%d, euid:%d, egid:%d\n",  
            getpgid(0),getpgrp(),getuid(), getgid(), geteuid(), getegid());  
    // ……  
}  
```  
  
  
## getpgid ：获取进程组ID
   
  
### 函数原型：
  
```c  
pid_t getpgid  
(  
	pid_t pid  
);  
```  
  
  
### 描述：
  
获取进程组ID。  
  
### 参数：
  
- pid ：进程ID。  
  
### 返回值：
  
此函数成功返回目标进程组ID，失败返回-1并设置错误码；。  
  
### 错误码：
  
- EINVAL ：参数无效。  
- ERROR_KERNEL_IN_ISR ：系统处于中断中。  
- ERROR_KERNEL_HANDLE_NULL ：句柄出错。  
- ERROR_EVENT_TYPE ：事件类型出错。  
- EDEADLK ：死锁。  
- EINTR ：中断回调。  
- ERROR_THREAD_WAIT_TIMEOUT ：等待超时。  
- ERROR_EVENT_WAS_DELETED ：事件已经被删除。  
  
### 样例：
  
子进程：  
  
```c  
#include <stdio.h>  
int main (int argc, char *argv[])  
{  
    while(1)  
	{  
        // ……  
        printf("this is child process\n");  
        // ……  
	}  
      
	return  (0);  
}  
```  
  
  
父进程：  
  
```c  
#include <stdio.h>  
#include <spawn.h>  
#include <unistd.h>  
#include <sched.h>  
#include <sylixos.h>  
#include <sys/resource.h>  
#include <stdlib.h>  
int main (int argc, char *argv[])  
{  
    int                       ret;  
    // ……  
    fprintf(stdout, "pgid: %d,pgrp: %d,uid: %d, gid:%d, euid:%d, egid:%d\n",  
            getpgid(0),getpgrp(),getuid(), getgid(), geteuid(), getegid());   
    if(setpgid(0, 1) != 0);  
    {  
        perror("setpgid failed\n");  
    }  
    // ……  
    fprintf(stdout, "pgid: %d,pgrp: %d,uid: %d, gid:%d, euid:%d, egid:%d\n",  
            getpgid(0),getpgrp(),getuid(), getgid(), geteuid(), getegid());  
    // ……  
}  
  
  
```  
  
## setpgrp ：设置进程为会话头
   
  
### 函数原型：
  
```c  
pid_t setpgrp  
(  
	void  
);  
```  
  
  
### 描述：
  
设置进程为会话头。  
  
### 参数：
  
无。  
  
### 返回值：
  
此函数将调用进程组ID，将其设置为本进程ID，使本进程成为会话头，成功返回0，失败返回-1。  
  
### 错误码：
  
- EINVAL ：参数无效。  
- ERROR_KERNEL_IN_ISR ：系统处于中断中。  
- ERROR_KERNEL_HANDLE_NULL ：句柄出错。  
- ERROR_EVENT_TYPE ：事件类型出错。  
- EDEADLK ：死锁。  
- EINTR ：中断回调。  
- ERROR_THREAD_WAIT_TIMEOUT ：等待超时。  
- ERROR_EVENT_WAS_DELETED ：事件已经被删除。  
  
### 备注：  
  
功能实现，但函数报错。  
  
### 样例：
  
子进程：  
  
```c  
#include <stdio.h>  
int main (int argc, char *argv[])  
{	  
    // …….  
    while(1)  
	{  
        // ……  
        printf("this is child process\n");  
        // ……  
	}  
      
	return  (0);  
}  
```  
  
  
父进程：  
  
```c  
#include <stdio.h>  
#include <spawn.h>  
#include <unistd.h>  
#include <sched.h>  
#include <sylixos.h>  
#include <sys/resource.h>  
#include <stdlib.h>  
int main (int argc, char *argv[])  
{  
    int             ret;  
    // ……  
    ret = setpgrp();  
    if(ret < 0)  
    {  
        perror("setpgrp");  
    }  
printf("pgrp is %d\n",ret);  
    // ……  
}  
```  
  
  
## getpgrp ：获取进程组ID
   
  
### 函数原型：
  
```c  
pid_t getpgrp  
(  
	void  
);  
```  
  
  
### 描述：
  
获取进程组ID。  
  
### 参数：
  
无。  
  
### 返回值：
  
此函数成功返回0，失败返回错误码。  
  
### 错误码：
  
- EINVAL ：参数无效。  
- ERROR_KERNEL_IN_ISR ：系统处于中断中。  
- ERROR_KERNEL_HANDLE_NULL：句柄出错。  
- ERROR_EVENT_TYPE ：事件类型出错。  
- EDEADLK ：死锁。  
- EINTR ：中断回调。  
- ERROR_THREAD_WAIT_TIMEOUT：等待超时。  
- ERROR_EVENT_WAS_DELETED ：事件已经被删除。  
  
### 样例：
  
子进程：  
  
```c  
#include <stdio.h>  
int main (int argc, char *argv[])  
{  
    while(1)  
	{  
    	// ……  
        printf("this is child process\n");  
        // ……  
	}  
      
	return  (0);  
}  
```  
  
  
父进程：  
  
```c  
#include <stdio.h>  
#include <spawn.h>  
#include <unistd.h>  
#include <sched.h>  
#include <sylixos.h>  
#include <sys/resource.h>  
#include <stdlib.h>  
int main (int argc, char *argv[])  
{  
    int         ret;  
    // ……  
    fprintf(stdout, "pgid: %d,pgrp: %d,uid: %d, gid:%d, euid:%d, egid:%d\n",  
            getpgid(0),getpgrp(),getuid(), getgid(), geteuid(), getegid());  
    // ……  
}  
  
```  
  
  
## getppid ：获取父进程ID
   
  
### 函数原型：
  
```c  
pid_t getppid  
(  
	void  
);  
```  
  
  
### 描述：
  
获取父进程ID。  
  
### 参数：
  
无。  
  
### 返回值：
  
此函数返回调用进程父进程ID。  
  
### 错误码：
  
- EINVAL ：参数无效。  
  
### 样例：
  
子进程：  
  
```c  
#include <stdio.h>  
#include <spawn.h>  
#include <unistd.h>  
#include <sched.h>  
#include <sylixos.h>  
#include <sys/resource.h>  
#include <stdlib.h>  
int main (int argc, char *argv[])  
{  
    while(1)  
	{  
    	// ……  
        ret = getppid();  
        if(ret < 0)  
        {  
            perror("getppid");  
        }  
        printf("parent pid is %d \n",ret);  
        // ……  
	}  
      
	return  (0);  
}  
```  
  
  
父进程：  
  
```c  
#include <stdio.h>  
#include <spawn.h>  
#include <unistd.h>  
#include <sched.h>  
#include <sylixos.h>  
#include <sys/resource.h>  
#include <stdlib.h>  
int main (int argc, char *argv[])  
{  
  
	......  
    return  (0);  
}  
  
```  
  
## issetugid ：获取进程执行文件是否设置S_ISUID和S_ISGID位
  
  
### 函数原型：
  
```c  
int issetugid  
(  
	void  
);  
```  
  
  
### 描述：
  
如果文件属性中S_ISUID位为1，则进程启动时会设置S_ISUID位，如果文件属性中S_ISGID位为1，则进程启动时会设置S_ISGID位。  
  
### 参数：
  
无。  
  
### 返回值：
  
如果启动进程中S_ISUID或S_ISGID有一项设置为1，则返回真，否则返回假。  
  
### 错误码：
  
无。  
  
### 样例：
  
子进程：  
  
```c  
#include <stdio.h>  
int main (int argc, char *argv[])  
{  
    while(1)  
	{  
    	// ……  
        printf("this is child process\n");  
        // ……  
	}  
          
	return  (0);  
}  
```  
  
  
父进程：  
  
```c  
#include <stdio.h>  
#include <spawn.h>  
#include <unistd.h>  
#include <sched.h>  
#include <sylixos.h>  
#include <sys/resource.h>  
#include <stdlib.h>  
int main (int argc, char *argv[])  
{  
    // ……  
	if(issetugid())  
	{  
        printf("trul\n");  
    }  
    else  
    {  
        printf("false\n");  
	}  
	// ……  
}  
  
```  
  
## setuid ：设置进程用户ID
  
### 函数原型：
  
```c  
int setuid  
(  
	uid_t  uid  
);  
```  
  
  
### 描述：
  
设置进程用户ID。  
  
### 参数：
  
- uid ：需要设置的进程用户ID。  
  
### 返回值：
  
设置调用进程用户号，成功返回0，失败返回-1并设置错误码。  
  
### 错误码：
  
- EPERM ：没有权限。  
  
### 样例：
  
子进程：  
  
```c  
#include <stdio.h>  
int main (int argc, char *argv[])  
{  
    while(1)  
	{  
    	// ……  
        printf("this is child process\n");  
        // ……  
	}  
      
	return  (0);  
}  
```  
  
  
父进程：  
  
```c  
#include <stdio.h>  
#include <spawn.h>  
#include <unistd.h>  
#include <sched.h>  
#include <sylixos.h>  
#include <sys/resource.h>  
#include <stdlib.h>  
int main (int argc, char *argv[])  
{  
    int                       ret;  
    // ……  
  
    if (setuid(1) != 0)  
    {  
        perror("setuid failed\n");  
	}  
	// ……  
}  
  
```  
  
## getuid ：获取进程实际用户ID
  
  
### 函数原型：
  
```c  
uid_t getuid  
(  
	void  
);  
```  
  
  
### 描述：
  
获取进程实际用户ID。  
  
### 参数：
  
无。  
  
### 返回值：
  
返回调用进程实际用户ID。  
  
### 错误码：
  
无。  
  
### 样例：
  
子进程：  
  
```c  
#include <stdio.h>  
int main (int argc, char *argv[])  
{  
    while(1)  
	{  
    // ……  
        printf("this is child process\n");  
        // ……  
	}  
	      
	return  (0);  
}  
```  
  
  
父进程：  
  
```c  
#include <stdio.h>  
#include <spawn.h>  
#include <unistd.h>  
#include <sched.h>  
#include <sylixos.h>  
#include <sys/resource.h>  
#include <stdlib.h>  
int main (int argc, char *argv[])  
{  
	// …….  
  
    fprintf(stdout, "pgid: %d,pgrp: %d,uid: %d, gid:%d, euid:%d, egid:%d\n",  
            getpgid(0),getpgrp(),getuid(), getgid(), geteuid(), getegid());   
  
    // …….  
    return  (0);  
}  
```  
  
  
## seteuid：设置进程有效用户ID
  
  
### 函数原型：
  
```c  
int seteuid  
(  
	gid_t  euid  
);  
```  
  
  
### 描述：
  
设置进程有效用户ID。  
  
### 参数：
  
- euid ：需要设置的进程有效用户ID。  
  
### 返回值：
  
设置调用进程有效的用户ID，成功返回0，失败返回-1并设置错误码。  
  
### 错误码：
  
无。  
  
### 样例：
  
子进程：  
  
```c  
#include <stdio.h>  
#include <spawn.h>  
#include <unistd.h>  
#include <sched.h>  
#include <sylixos.h>  
#include <sys/resource.h>  
#include <stdlib.h>  
int main (int argc, char *argv[])  
{  
    while(1)  
	{  
    // ……  
        printf("this is child process\n");  
        // ……  
	}  
      
	return  (0);  
}  
```  
  
  
父进程：  
  
```c  
#include <stdio.h>  
#include <spawn.h>  
#include <unistd.h>  
#include <sched.h>  
#include <sylixos.h>  
#include <sys/resource.h>  
#include <stdlib.h>  
int main (int argc, char *argv[])  
{  
    int                       ret;  
    pid_t                     pid;  
    posix_spawnattr_t         spawnattr;  
  
    fprintf(stdout, "pgid: %d,pgrp: %d,uid: %d, gid:%d, euid:%d, egid:%d\n",  
            getpgid(0),getpgrp(),getuid(), getgid(), geteuid(), getegid());  
    if (seteuid(2)  <  0)  
    {  
        fprintf(stderr, "seteuid failed\n");  
    }  
    fprintf(stdout, "pgid: %d,pgrp: %d,uid: %d, gid:%d, euid:%d, egid:%d\n",  
            getpgid(1),getpgrp(),getuid(), getgid(), geteuid(), getegid());  
  
    // …….  
  
    return  (0);  
}  
```  
  
## geteuid ：获取进程有效用户ID
  
  
### 函数原型：
  
```c  
uid_t geteuid  
(  
	void  
);  
```  
  
  
### 描述：
  
获取进程有效用户ID。  
  
### 参数：
  
无。  
  
### 返回值：
  
此函数返回调用进程的有效用户ID。  
  
### 错误码：
  
- EINVAL ：参数无效。  
  
### 样例：
  
子进程：  
  
```c  
#include <stdio.h>  
#include <spawn.h>  
#include <unistd.h>  
#include <sched.h>  
#include <sylixos.h>  
#include <sys/resource.h>  
#include <stdlib.h>  
int main (int argc, char *argv[])  
{  
    while(1)  
	{  
    	// ……  
        printf("this is child process\n");  
        // ……  
	}  
      
	return  (0);  
}  
```  
  
父进程：  
  
```c  
#include <stdio.h>  
#include <spawn.h>  
#include <unistd.h>  
#include <sched.h>  
#include <sylixos.h>  
#include <sys/resource.h>  
#include <stdlib.h>  
int main (int argc, char *argv[])  
{  
    // ……  
    fprintf(stdout, "pgid: %d,pgrp: %d,uid: %d, gid:%d, euid:%d, egid:%d\n",  
            getpgid(1),getpgrp(),getuid(), getgid(), geteuid(), getegid());  
    // ……  
    return  (0);  
}  
  
```  
  
  
## setgid ：设置进程用户组ID
  
  
### 函数原型：
  
```c  
int setgid  
(  
	gid_t  gid  
);  
```  
  
  
### 描述：
  
设置进程用户组ID。如果当前用户为超级用户，即用户ID为0，setgid可以将用户组ID改成任何组ID，但是一旦设置成功，进程的实际用户组ID、有效用户组ID和保存的设置用户组ID全部被设置为新组ID。如果当前用户为普通用户，即用户ID不为0，则只能修改有效用户组ID，且只能被修改为实际用户组ID或保存的设置用户组ID。  
  
### 参数：
  
gid ：需要设置的进程的用户组ID。  
  
### 返回值：
  
设置调用进程的用户组号，成功返回0，失败返回-1。  
  
### 错误码：
  
无。  
  
### 样例：
  
子进程：  
  
```c  
#include <stdio.h>  
#include <spawn.h>  
#include <unistd.h>  
#include <sched.h>  
#include <sylixos.h>  
#include <sys/resource.h>  
#include <stdlib.h>  
int main (int argc, char *argv[])  
{  
    while(1)  
{  
    // ……  
        printf("this is child process\n");  
        // ……  
}  
      
return  (0);  
}  
```  
  
  
父进程：  
  
```c  
#include <stdio.h>  
#include <spawn.h>  
#include <unistd.h>  
#include <sched.h>  
#include <sylixos.h>  
#include <sys/resource.h>  
#include <stdlib.h>  
int main (int argc, char *argv[])  
{  
  
    fprintf(stdout, "pgid: %d,pgrp: %d,uid: %d, gid:%d, euid:%d, egid:%d\n",  
            getpgid(0),getpgrp(),getuid(), getgid(), geteuid(), getegid());  
  
    if (setgid(1) != 0)  
    {  
        fprintf(stderr, "setgid failed\n");  
    }  
  
    fprintf(stdout, "pgid: %d,pgrp: %d,uid: %d, gid:%d, euid:%d, egid:%d\n",  
            getpgid(1),getpgrp(),getuid(), getgid(), geteuid(), getegid());  
  
    // …….  
  
    return  (0);  
}  
  
```  
  
  
## getgid ：获取进程实际用户组ID
  
  
### 函数原型：
  
```c  
gid_t getgid  
(  
	void  
);  
```  
  
  
### 描述：
  
获取进程实际用户组ID。  
  
### 参数：
  
无。  
  
### 返回值：
  
此函数返回调用进程的实际用户组ID。  
  
### 错误码：
  
无。  
  
### 样例：
  
子进程：  
  
```c  
#include <stdio.h>  
#include <spawn.h>  
#include <unistd.h>  
#include <sched.h>  
#include <sylixos.h>  
#include <sys/resource.h>  
#include <stdlib.h>  
int main (int argc, char *argv[])  
{  
    while(1)  
	{  
    // ……  
        printf("this is child process\n");  
        // ……  
	}  
      
	return  (0);  
}  
```  
  
  
父进程：  
  
```c  
#include <stdio.h>  
#include <spawn.h>  
#include <unistd.h>  
#include <sched.h>  
#include <sylixos.h>  
#include <sys/resource.h>  
#include <stdlib.h>  
int main (int argc, char *argv[])  
{  
  
// ……  
  
    fprintf(stdout, "pgid: %d,pgrp: %d,uid: %d, gid:%d, euid:%d, egid:%d\n",getpgid(0),getpgrp(),getuid(), getgid(), geteuid(), getegid());  
  
    // …….  
  
    return  (0);  
}  
```  
  
  
## setegid ：设置进程有效用户组ID
  
### 函数原型：
  
```c  
int setegid  
(  
	gid_t  egid  
);  
```  
  
  
### 描述：
  
设置进程有效用户组ID。如果当前用户为超级用户，setegid可以将有效用户组ID成任何组ID。如果当前用户为普通用户，效用户组ID只能被修改为实际用户组ID或保存的设置用户组ID。  
  
### 参数：
  
- egid ：需要设置的进程的有效用户组ID。  
  
### 返回值：
  
此函数成功返回0，失败返回-1。  
  
### 错误码：
  
无。  
  
### 样例：
  
子进程：  
  
```c  
#include <stdio.h>  
#include <spawn.h>  
#include <unistd.h>  
#include <sched.h>  
#include <sylixos.h>  
#include <sys/resource.h>  
#include <stdlib.h>  
int main (int argc, char *argv[])  
{  
    while(1)  
	{  
        // ……  
        printf("this is child process\n");  
        // ……  
	}  
      
	return  (0);  
}  
```  
  
父进程：  
  
```c  
#include <stdio.h>  
#include <spawn.h>  
#include <unistd.h>  
#include <sched.h>  
#include <sylixos.h>  
#include <sys/resource.h>  
#include <stdlib.h>  
int main (int argc, char *argv[])  
{  
    int    ret;  
    // ……  
    fprintf(stdout, "pgid: %d,pgrp: %d,uid: %d, gid:%d, euid:%d, egid:%d\n",  
            getpgid(0),getpgrp(),getuid(), getgid(), geteuid(), getegid());  
    if (setegid(1) != 0)  
    {  
        fprintf(stderr, "setgid failed\n");  
    }  
  
    fprintf(stdout, "pgid: %d,pgrp: %d,uid: %d, gid:%d, euid:%d, egid:%d\n",  
            getpgid(1),getpgrp(),getuid(), getgid(), geteuid(), getegid());  
  
	// ……  
  
    return  (0);  
}  
```  
  
  
## getegid ：获取进程有效用户组ID
  
  
### 函数原型：
  
```c  
gid_t getegid  
(  
	void  
);  
```  
  
### 描述：
  
获取进程有效用户组ID。  
  
### 参数：
  
无。  
  
### 返回值：
  
此函数返回调用进程有效用户组ID。  
  
### 错误码：
  
无。  
  
### 样例：
  
子进程：  
  
```c  
#include <stdio.h>  
#include <spawn.h>  
#include <unistd.h>  
#include <sched.h>  
#include <sylixos.h>  
#include <sys/resource.h>  
#include <stdlib.h>  
int main (int argc, char *argv[])  
{  
    while(1)  
	{  
    	// ……  
        printf("this is child process\n");  
        // ……  
	}  
      
	return  (0);  
}  
```  
  
  
父进程：  
  
```c  
#include <stdio.h>  
#include <spawn.h>  
#include <unistd.h>  
#include <sched.h>  
#include <sylixos.h>  
#include <sys/resource.h>  
#include <stdlib.h>  
int main (int argc, char *argv[])  
{  
    int                       ret;  
    // ……  
    fprintf(stdout, "pgid: %d,pgrp: %d,uid: %d, gid:%d, euid:%d, egid:%d\n",  
            getpgid(0),getpgrp(),getuid(), getgid(), geteuid(), getegid());  
    if (setegid(1) != 0)  
    {  
        fprintf(stderr, "setgid failed\n");  
    }  
  
    fprintf(stdout, "pgid: %d,pgrp: %d,uid: %d, gid:%d, euid:%d, egid:%d\n",  
            getpgid(1),getpgrp(),getuid(), getgid(), geteuid(), getegid());  
  
	// ……  
  
    return  (0);  
}  
  
```  
  
## setgroups ：设置当前进程的扩展用户组ID
  
  
### 函数原型：
  
```c  
int setgroups  
(  
	int groupsun,   
	const gid_t grlist[]  
);  
```  
  
  
  
### 描述：
  
设置当前进程的扩展用户组ID，调用此函数进程必须拥有root权限，否则返回失败。  
  
### 参数：
  
- Groupsun ：参数grlist数组的大小。  
  
- Grlist ：扩展用户组ID数组。  
  
### 返回值：
  
此函数成功返回0，失败返回-1并设置错误号。  
  
### 错误码：
  
- EINVAL ：参数无效。  
- EPERM ：没有权限。  
  
### 样例：
  
子进程：  
  
```c  
#include <stdio.h>  
#include <spawn.h>  
#include <unistd.h>  
#include <sched.h>  
#include <sylixos.h>  
#include <sys/resource.h>  
#include <stdlib.h>  
int main (int argc, char *argv[])  
{  
    while(1)  
	{  
        // ……  
        printf("this is child process\n");  
        // ……  
	}  
          
	return  (0);  
}  
```  
  
  
父进程：  
  
```c  
#include <stdio.h>  
#include <spawn.h>  
#include <unistd.h>  
#include <sched.h>  
#include <sylixos.h>  
#include <sys/resource.h>  
#include <stdlib.h>  
int main (int argc, char *argv[])  
{  
    int                       ret;  
    gid_t                     groups[]={0};  
  
    // ……  
    fprintf(stdout, "getgroups: %d,pgrp: %d,uid: %d, gid:%d, euid:%d, egid:%d\n",  
            getgroups(1,groups),getpgrp(),getuid(), getgid(), geteuid(), getegid());  
  
    if (setgroups(1,groups) < 0)  
        fprintf(stderr, "setgid failed\n");  
    }  
  
    fprintf(stdout, "getgroups: %d,pgrp: %d,uid: %d, gid:%d, euid:%d, egid:%d\n",  
            getgroups(1,groups),getpgrp(),getuid(), getgid(), geteuid(), getegid());  
    // ……  
  
    return  (0);  
}  
```  
  
  
## getgroups ：获取的扩展用户组ID
  
  
### 函数原型：
  
```c  
int getgroups  
(  
	int groupsize,   
	gid_t grlist[]  
);  
```  
  
  
### 描述：
  
### 参数：
  
- Groupsize ：参数grlist的大小，如果groupsize小于用户扩展用户组ID数量，则只填充groupsize个用户组ID。为0表示只统计扩展用户组ID数量。  
  
- grlist ：用于保存扩展用户组ID的缓冲区数组。为0表示只统计扩展用户组ID数量。  
  
### 返回值：
  
此函数返回本进程扩展用户组ID数量。  
  
### 错误码：
  
无。  
  
### 样例：
  
子进程：  
  
```c  
#include <stdio.h>  
#include <spawn.h>  
#include <unistd.h>  
#include <sched.h>  
#include <sylixos.h>  
#include <sys/resource.h>  
#include <stdlib.h>  
int main (int argc, char *argv[])  
{  
    while(1)  
	{  
    // ……  
        printf("this is child process\n");  
        // ……  
	}  
      
	return  (0);  
}  
```  
  
  
父进程：  
  
```c  
#include <stdio.h>  
#include <spawn.h>  
#include <unistd.h>  
#include <sched.h>  
#include <sylixos.h>  
#include <sys/resource.h>  
#include <stdlib.h>  
int main (int argc, char *argv[])  
{  
    int                       ret;  
    gid_t                     groups[]={0};  
  
    // ……  
    fprintf(stdout, "getgroups: %d,pgrp: %d,uid: %d, gid:%d, euid:%d, egid:%d\n",  
            getgroups(1,groups),getpgrp(),getuid(), getgid(), geteuid(), getegid());  
  
    if (setgroups(1,groups) != 0)  
        fprintf(stderr, "setgid failed\n");  
    }  
  
    fprintf(stdout, "getgroups: %d,pgrp: %d,uid: %d, gid:%d, euid:%d, egid:%d\n",  
            getgroups(1,groups),getpgrp(),getuid(), getgid(), geteuid(), getegid());  
    // ……  
  
    return  (0);  
}  
```  
  
  
## exit ：进程退出
  
  
### 函数原型：
  
```c  
void exit  
(  
	int status  
);  
```  
  
  
### 描述：
  
进程退出，exit函数调用进程中使用atexit函数注册的hook函数。  
  
### 参数：
  
- Status ：进程返回码。  
  
### 返回值：
  
无。  
  
### 错误码：
  
无。  
  
### 样例：
  
子进程：  
  
```c  
int main (int argc, char *argv[])  
{  
    while(1)  
	{  
        // ……  
        printf("this is child process\n");  
		exit(0);  
        // ……  
	}  
          
	return  (0);  
}  
```  
  
父进程：  
  
```c  
#include <stdio.h>  
#include <spawn.h>  
#include <unistd.h>  
#include <sched.h>  
#include <sylixos.h>  
#include <sys/resource.h>  
#include <stdlib.h>  
#define CHILD_PATH  "../HYSylixos_exit_clild_demo/HYSylixos_exit_clild_demo"  
int main (int argc, char *argv[])  
{  
    int                       ret;  
    pid_t                     pid;  
    posix_spawnattr_t         spawnattr;  
  
  
    if (posix_spawnattr_init(&spawnattr) != 0)  
    {  
            fprintf(stderr, "init posix_spawnattr_t failed\n");  
            return  (-1);  
    }  
  
    ret = posix_spawnp(&pid,CHILD_PATH,NULL,NULL,NULL,NULL);  
    if (ret != 0)  
    {  
        perror("posix_spawnp");  
        return  (-6);  
    }  
  
    sleep(1);  
  
    printf("father go on\n");  
  
   …...  
  
    return  (0);  
}  
```  
  
  
## _Exit ：进程退出
  
  
### 函数原型：
  
```c  
void _Exit  
(  
	int status  
);  
```  
  
  
### 描述：
  
进程退出。  
  
### 参数：
  
- Status ：进程返回码。  
  
### 返回值：
  
无。  
  
### 错误码：
  
无。  
  
### 样例：
  
子进程：  
  
```c  
#include <stdio.h>  
#include <spawn.h>  
#include <unistd.h>  
#include <sched.h>  
#include <sylixos.h>  
#include <sys/resource.h>  
#include <stdlib.h>  
int main (int argc, char *argv[])  
{  
    while(1)  
	{  
        // ……  
        printf("this is child process\n");  
        _Exit(0);  
        // ……  
	}  
      
	return  (0);  
}  
```  
  
  
父进程：  
  
```c  
#include <stdio.h>  
#include <spawn.h>  
#include <unistd.h>  
#include <sched.h>  
#include <sylixos.h>  
#include <sys/resource.h>  
#include <stdlib.h>  
#define CHILD_PATH  "../HYSylixos_exit_clild_demo/HYSylixos_exit_clild_demo"  
int main (int argc, char *argv[])  
{  
    int                       ret;  
    pid_t                     pid;  
    posix_spawnattr_t         spawnattr;  
  
  
    if (posix_spawnattr_init(&spawnattr) != 0)  
    {  
            fprintf(stderr, "init posix_spawnattr_t failed\n");  
            return  (-1);  
    }  
  
    ret = posix_spawnp(&pid,CHILD_PATH,NULL,NULL,NULL,NULL);  
    if (ret != 0)  
    {  
        perror("posix_spawnp");  
        return  (-6);  
    }  
  
    sleep(1);  
  
    printf("father go on\n");  
  
   // ……  
  
    return  (0);  
}  
  
```  
  
## _exit ：进程退出
  
  
### 函数原型：
  
```c  
void _exit  
(  
	int status  
);  
```  
  
  
### 描述：
  
进程退出。  
  
### 参数：
  
- Status ：进程返回码。  
  
### 返回值：
  
无。  
  
### 错误码：
  
无。  
  
### 样例：
  
子进程：  
  
```c  
#include <stdio.h>  
#include <spawn.h>  
#include <unistd.h>  
#include <sched.h>  
#include <sylixos.h>  
#include <sys/resource.h>  
#include <stdlib.h>  
int main (int argc, char *argv[])  
{  
    while(1)  
	{  
        // ……  
        printf("this is child process\n");  
		_exit(0)  
        // ……  
	}  
      
	return  (0);  
}  
```  
  
父进程：  
  
```c  
#include <stdio.h>  
#include <spawn.h>  
#include <unistd.h>  
#include <sched.h>  
#include <sylixos.h>  
#include <sys/resource.h>  
#include <stdlib.h>  
#define CHILD_PATH  "../HYSylixos_exit_clild_demo/HYSylixos_exit_clild_demo"  
int main (int argc, char *argv[])  
{  
    int                       ret;  
    pid_t                     pid;  
    posix_spawnattr_t         spawnattr;  
    if (posix_spawnattr_init(&spawnattr) != 0)  
    {  
            fprintf(stderr, "init posix_spawnattr_t failed\n");  
            return  (-1);  
    }  
  
    ret = posix_spawnp(&pid,CHILD_PATH,NULL,NULL,NULL,NULL);  
    if (ret != 0)  
    {  
        perror("posix_spawnp");  
        return  (-6);  
    }  
  
    sleep(1);  
  
    printf("father go on\n");  
  
   // ……  
  
    return  (0);  
}  
```  
  
  
## atexit ：注册进程退出hook
  
  
### 函数原型：
  
```c  
void atexit  
(  
	void (*func)(void)  
);  
```  
  
  
### 描述：
  
注册进程退出hook。  
  
### 参数：
  
- Func ：进程退出hook函数，进程正常退出时（main函数return或调用exit函数）按atexit注册顺序的的逆序调用hook函数。  
  
### 返回值：
  
无。  
  
### 错误码：
  
无。  
  
### 样例：
  
子进程：  
  
```c  
#include <stdio.h>  
#include <spawn.h>  
#include <unistd.h>  
#include <sched.h>  
#include <sylixos.h>  
#include <sys/resource.h>  
#include <stdlib.h>  
void *fuction(void)  
{  
    printf("this is hook fuction\n");  
  
    return NULL;  
}  
  
 int main (int argc, char *argv[])  
{  
    // ……  
    while(1)  
    {  
  
        priority    = getpriority(PRIO_PROCESS, pid);  
        fprintf(stdout, "child process priority after nice: %d\n", priority);  
  
        volatile long int time = 40000000;  
        while(time -- );  
        atexit(fuction());  
  
    }  
  
    return (0);  
  
}  
```  
  
父进程：  
  
```c  
#include <stdio.h>  
#include <spawn.h>  
#include <unistd.h>  
#include <sched.h>  
#include <sylixos.h>  
#include <sys/resource.h>  
#include <stdlib.h>  
#define CHILD_PATH  "../HYSylixos_exit_clild_demo/HYSylixos_exit_clild_demo"  
int main (int argc, char *argv[])  
{  
    int                       ret;  
    pid_t                     pid;  
    posix_spawnattr_t         spawnattr;  
  
  
    if (posix_spawnattr_init(&spawnattr) != 0)  
    {  
            fprintf(stderr, "init posix_spawnattr_t failed\n");  
            return  (-1);  
    }  
  
    ret = posix_spawnp(&pid,CHILD_PATH,NULL,NULL,NULL,NULL);  
    if (ret != 0)  
    {  
        perror("posix_spawnp");  
        return  (-6);  
    }  
  
    sleep(1);  
  
    printf("father go on\n");  
  
   // ……  
  
    return  (0);  
}  
  
```  
  
## wait ：等待子进程结束
  
  
### 函数原型：
  
```c  
pid_t wait  
(  
	int *stat_loc  
);  
```  
  
### 描述：
  
等待某个子进程结束。  
  
### 参数：
  
- stat_loc ：子进程退出码。  
  
### 返回值：
  
此函数成功返回子进程ID，失败返回-1并设置错误码。  
  
### 错误码：
  
- ECHILD ：无子进程。  
- EINVAL ：参数无效。  
- EINTR ：被中断打断。  
  
### 样例：
  
子进程：  
  
```c  
#include <stdio.h>  
#include <spawn.h>  
#include <unistd.h>  
#include <sched.h>  
#include <sylixos.h>  
#include <sys/resource.h>  
#include <stdlib.h>  
int main (int argc, char *argv[])  
{  
    while(1)  
	{  
        // ……  
        printf("this is child process\n");  
        // ……  
	}  
      
	return  (0);  
}  
```  
  
父进程：  
  
```c  
#include <stdio.h>  
#include <spawn.h>  
#include <unistd.h>  
#include <sched.h>  
#include <sylixos.h>  
#include <sys/resource.h>  
#include <stdlib.h>  
#define CHILD_PATH  "../HYSylixos_exit_clild_demo/HYSylixos_exit_clild_demo"  
int main (int argc, char *argv[])  
{  
    int                      ret;  
	int                     stat_loc;  
  
    ret = wait(&stat_loc);   
    if(ret < 0)  
    {  
        perror("wait");  
        return -1;  
    }  
  
   // ……  
  
    return  (0);  
}  
  
  
```  
## waitid ：等待子进程状态改变
  
  
### 函数原型：
  
```c  
int waitid  
(  
	idtype_t idtype,   
	id_t id, siginfo_t *infop,  
	int options  
);  
```  
  
  
### 描述：
  
等待子进程状态改变。  
  
### 参数：
  
- Idtype ：指示参数id的意义，有以下几种情况；  
  
|宏名|解释|  
|---|---|  
|P_PID|等待进程ID等于参数id的子进程|  
|P_PGID|等待进程组ID等于参数id的子进程|  
|P_ALL|等待任一子进程|  
  
- Id ：意义由idtype指定，用于指定子进程；  
  
- Infop ：返回接受到的子进程信号，里面记录状态改变的子进程信息；  
  
- Option ：功能选项，由位掩码组成，掩码位有：  
  
|宏名|解释|  
|---|---|  
|WNOHANG|如果为1，函数不等待，当没有子进程状态改变时直接返回|  
|WUNTRACED|如果为1，当子进程进入停止态时返回，否则只有当进程退出时返回|  
  
### 返回值：
  
如果由于子进程状态改变导致函数返回，返回子进程ID。如果options设置了WNOHANG位，且没有符合条件子进程状态发生改变，则不等待且返回0。其他情况返回-1并设置错误码；  
  
### 错误码：
  
- ECHILD ：无子进程。  
- EINVAL ：参数无效。  
- EINTR ：被中断打断。  
  
### 样例：
  
子进程：  
  
```c  
#include <stdio.h>  
#include <spawn.h>  
#include <unistd.h>  
#include <sched.h>  
#include <sylixos.h>  
#include <sys/resource.h>  
#include <stdlib.h>  
int main (int argc, char *argv[])  
{  
    while(1)  
	{  
    // ……  
        printf("this is child process\n");  
        // ……  
	}  
      
	return  (0);  
}  
```  
  
  
父进程：  
  
```c  
#include <stdio.h>  
#include <spawn.h>  
#include <unistd.h>  
#include <sched.h>  
#include <sylixos.h>  
#include <sys/resource.h>  
#include <stdlib.h>  
#define CHILD_PATH  "../HYSylixos_exit_clild_demo/HYSylixos_exit_clild_demo"  
int main (int argc, char *argv[])  
{  
    int                       ret;  
    pid_t                     pid;  
	siginfo_t                 infopl;  
  
    // ……  
	ret = waitid(P_ALL,pid,&infopl,WUNTRACED);    if(ret < 0)  
    {  
        perror("waitid");  
        return -1;  
    }  
    return  (0);  
}  
  
  
```  
  
## waitpid ：等待指定子进程状态改变
  
  
### 函数原型：
  
```c  
pid_t waitpid  
(  
	pid_t pid,   
	int *stat_loc,  
	int options  
);  
```  
  
  
### 描述：
  
等待指定子进程状态改变。  
  
### 参数：
  
- pid ：可以有以下几种情况：  
  
- pid大于0表示等待进程号为pid的子进程。  
  
- pid等于0表示等待与调用进程同组的子进程。  
  
- pid小于-1表示等待进程组ID为pid绝对值的子进程。  
  
- stat_loc ：子进程退出码。  
  
- Option ：功能选项，由位掩码组成，掩码位。  
  
### 返回值：
  
如果由于子进程状态改变导致函数返回，返回子进程ID。如果options设置了WNOHANG位，且没有子进程状态发生改变，则不等待且返回0。其他情况返回-1并设置错误码；  
  
### 错误码：
  
- ECHILD ：无子进程。  
- EINVAL ：参数无效。  
- EINTR ：被中断打断。  
  
### 样例：
  
子进程：  
  
```c  
#include <stdio.h>  
#include <spawn.h>  
#include <unistd.h>  
#include <sched.h>  
#include <sylixos.h>  
#include <sys/resource.h>  
#include <stdlib.h>  
int main (int argc, char *argv[])  
{  
    while(1)  
	{  
    // ……  
        printf("this is child process\n");  
        // ……  
	}  
	      
	return  (0);  
}  
```  
  
  
父进程：  
  
```c  
#include <stdio.h>  
#include <spawn.h>  
#include <unistd.h>  
#include <sched.h>  
#include <sylixos.h>  
#include <sys/resource.h>  
#include <stdlib.h>  
#define CHILD_PATH  "../HYSylixos_exit_clild_demo/HYSylixos_exit_clild_demo"  
int main (int argc, char *argv[])  
{  
    int                       ret;  
    pid_t                     pid;  
    int                       stat_loc;  
  
  
    printf("father go on\n");  
    ret = waitpid(pid,&stat_loc,WUNTRACED);  
    if(ret < 0)  
    {  
        perror("waitid");  
        return -1;  
    }  
   // ……  
  
    return  (0);  
}  
  
```  
  
## getrusage ：获取进程资源使用情况
  
  
### 函数原型：
  
```c  
int getrusage  
(  
	int who,  
	struct rusage *r_usage  
);  
```  
  
  
### 描述：
  
获取进程资源使用情况。（目前，HYSylixOS只填充ru_utime和ru_stime两个字段，其他字段保留。）  
  
### 参数：
  
- Who ：被获取资源的对象，其值有以下几种情况：  
  
|宏名|解释|  
|---|---|  
|RUSAGE_SELF|获取本进程资源使用情况|  
|RUSAGE_CHILDREN|获取子进程资源使用情况|  
  
- r_usage ：返回进程资源使用情况。  
  
### 返回值：
  
此函数成功返回0，失败返回-1并设置错误码。  
  
### 错误码：
  
- EINVAL ：参数无效。  
  
### 样例：
  
子进程：  
  
```c  
#include <stdio.h>  
#include <spawn.h>  
#include <unistd.h>  
#include <sched.h>  
#include <sylixos.h>  
#include <sys/resource.h>  
#include <stdlib.h>  
int main (int argc, char *argv[])  
{  
    while(1)  
	{  
    	// ……  
        printf("this is child process\n");  
        // ……  
	}  
      
	return  (0);  
}  
```  
  
  
父进程：  
  
```c  
#include <stdio.h>  
#include <spawn.h>  
#include <unistd.h>  
#include <sched.h>  
#include <sylixos.h>  
#include <sys/resource.h>  
#include <stdlib.h>  
int main (int argc, char *argv[])  
{  
    int                       ret;  
    pid_t                     pid;  
    posix_spawnattr_t         spawnattr;  
    int                       stat_loc;  
	struct  rusage            r_usage;  
  
	// ……  
  
    ret = getrusage(RUSAGE_SELF,&r_usage);  
    if(ret != 0)  
    {  
        perror("getrusage");  
        return -1;  
    }  
    printf("-----------------------------------------------\n");  
    printf("father used time %d\n",(int)r_usage.ru_utime.tv_usec);  
    printf("-----------------------------------------------\n");  
	printf("father go on\n");  
	// ……  
	return 0;  
}  
```  
  
  
## times ：获取进程时间
  
  
### 函数原型：
  
```c  
clock_t times  
(  
	struct tms *ptms  
);  
```  
  
### 描述：
  
获取进程时间。  
  
### 参数：
  
- ptms ：进程及其子进程时间使用情况。  
  
### 返回值：
  
此函数返回系统时间。  
  
### 错误码：
  
- EINVAL ：参数无效。  
  
### 样例：
  
子进程：  
  
```c  
#include <stdio.h>  
#include <spawn.h>  
#include <unistd.h>  
#include <sched.h>  
#include <sylixos.h>  
#include <sys/resource.h>  
#include <stdlib.h>  
int main (int argc, char *argv[])  
{  
    while(1)  
	{  
    	// ……  
        printf("this is child process\n");  
        // ……  
	}  
	      
	return  (0);  
}  
```  
  
父进程：  
  
```c  
#include <stdio.h>  
#include <spawn.h>  
#include <unistd.h>  
#include <sched.h>  
#include <sylixos.h>  
#include <sys/resource.h>  
#include <stdlib.h>  
int main (int argc, char *argv[])  
{  
    int                       ret;  
	struct  tms               buffer;  
  
	// ……  
  
    ret = times(&buffer);  
    if(ret < 0)  
    {  
        perror("times");  
        return -1;  
    }  
    printf("-----------------------------------------------\n");  
    printf("used time %d\n",(int)buffer.tms_utime);  
    printf("systerm time is %d \n",ret);  
    printf("-----------------------------------------------\n");  
	printf("father go on\n");  
	// ……  
	return 0;  
}  
  
```  
  
## getenv ：获取环境变量
  
  
### 函数原型：
  
```c  
char *getenv  
(  
	const char *name  
);  
```  
  
  
### 描述：
  
获取环境变量。  
  
### 参数：
  
- name ：环境变量名称。  
  
### 返回值：
  
此函数成功返回查找到的环境变量字符串指针，该指针不能被释放，失败返回0并设置错误码。  
  
### 错误码：
  
- EINVAL ：参数无效。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <spawn.h>  
#include <unistd.h>  
#include <sched.h>  
#include <sylixos.h>  
#include <sys/resource.h>  
#include <stdlib.h>  
int main (int argc, char *argv[])  
{  
    int                       ret;  
    const char                *env_value = 0;  
    const char                *env_name  = "ENV_DEMO";  
  
    // ……  
  
    env_value = getenv(env_name)    if (env_value)  
    {  
         printf("value of %s before setenv is %s\n",  
                    env_name, env_value);  
    }  
    else  
    {  
         printf("value of %s is not setted before setenv\n",  
                 env_name);  
    }  
    // ……  
  
    return  (0);  
}  
```  
  
  
## putenv ：改变或者增加环境变量
  
  
### 函数原型：
  
```c  
int putenv  
(  
	char *string  
);  
```  
  
  
### 描述：
  
用来改变或者增加环境变量的内容。参数string的格式为name = value，如果该环境变量原先存在，则变量内容会依参数string改变，否则此参数内容会成为新的环境变量。  
  
### 参数：
  
- string ：环境变量设置字符串，其格式为：name=value，如果name已存在则先删除原来的定义。  
  
### 返回值：
  
此函数成功返回0，失败返回非0。  
  
### 错误码：
  
无。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <spawn.h>  
#include <unistd.h>  
#include <sched.h>  
#include <sylixos.h>  
#include <sys/resource.h>  
#include <stdlib.h>  
int main (int argc, char *argv[])  
{  
    int                       ret;  
    const char                *env_value =NULL;  
  
    // ……  
  
    env_value = getenv("hello");  
    if (env_value)  
    {  
         printf("value of USER before setenv is %s\n",  
                     env_value);  
    }  
  
    ret = putenv("hello=test");  
    if(ret < 0)  
    {  
        perror("putenv");  
        return -1;  
    }  
  
    printf("hello + %s\n",getenv("hello"));  
  
    // ……  
  
    return  (0);  
}  
```  
  
## setenv ：设置环境变量
  
  
### 函数原型：
  
```c  
int setenv  
(  
	const char *name,   
	const char *value,   
	int overwrite  
);  
```  
  
  
### 描述：
  
设置环境变量。  
  
### 参数：
  
- name ：需要设置的环境变量名称；  
  
- value ：环境变量值；  
  
- overwrite ：环境变量已存在时是否覆盖原有环境变量，为1表示覆盖，为0表示不覆盖。  
  
### 返回值：
  
此函数成功返回0，失败返回-1。  
  
### 错误码：
  
无。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <spawn.h>  
#include <unistd.h>  
#include <sched.h>  
#include <sylixos.h>  
#include <sys/resource.h>  
#include <stdlib.h>  
int main (int argc, char *argv[])  
{  
    int                       ret;  
    const char                *env_value = 0;  
    const char                *env_name  = "ENV_DEMO";  
  
    // ……  
  
        env_value = "test_value";  
     if (setenv(env_name, env_value, 0) != 0)  
    {  
         printf("setenv failed\n");  
    }  
    else  
    {  
         printf("set value of %s to %s\n", env_name, env_value);  
    }  
  
    env_value = getenv(env_name);    if (env_value)  
    {  
         printf("value of %s after setevn is %s\n",  
                 env_name, env_value);  
    }  
    else  
    {  
         printf("value of %s is not setted after setenv\n",  
                 env_name);  
    }  
  
    // ……  
  
    return  (0);  
}  
```  
  
## unsetenv ：清除环境变量
  
  
### 函数原型：
  
```c  
int unsetenv  
(  
	const char *name  
);  
```  
  
  
### 描述：
  
清除环境变量。  
  
### 参数：
  
- name ：需要清除的环境变量名称。  
  
### 返回值：
  
此函数成功返回0，失败返回-1。  
  
### 错误码：
  
无。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <spawn.h>  
#include <unistd.h>  
#include <sched.h>  
#include <sylixos.h>  
#include <sys/resource.h>  
#include <stdlib.h>  
int main (int argc, char *argv[])  
{  
    int                       ret;  
    const char                *env_value = 0;  
    const char                *env_name  = "ENV_DEMO";  
  
    // ……  
  
    if (unsetenv(env_name) != 0)  
    {  
          printf("unsetenv failed\n");  
    }  
    else  
    {  
          printf("unset value of %s\n", env_name);  
    }  
  
    env_value = getenv(env_name);  
    if (env_value)  
    {  
         printf("value of %s after unsetenv is %s\n",  
                    env_name, env_value);  
    }  
    else  
    {  
         printf("value of %s is not setted after unsetenv\n",  
                    env_name);  
    }  
  
    // ……  
  
    return  (0);  
}  
```  
  
## daemon ：设置当前进程为守护进程
  
  
### 函数原型：
  
```c  
int daemon  
(  
	int nochdir,  
	int noclose  
);  
```  
  
  
### 描述：
  
设置当前进程为守护进程。用ps命令可以查看后台运行的守护进程。  
  
### 参数：
  
- Nochdir ：表示是否切换进程当前工作目录到根目录“/”。0表示切换，其他表示不切换；  
  
- Noclose ：表示是否重定向标准输入、标准输出、标准错误输出到“/dev/null”文件。0表示重定向，其他表示不重定向。  
  
### 返回值：
  
此函数成功返回0，失败返回-1并设置错误码。  
  
### 错误码：
  
- ENOSYS ：函数未被定制。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <spawn.h>  
#include <unistd.h>  
#include <sylixos.h>  
#include <sys/resource.h>  
#include <process.h>  
int main (int argc, char *argv[])  
{  
	int ret;  
  
	// ……  
    ret = daemon(0, 0);  
    if(ret < 0)  
    {  
        perror("daemon");  
        retur -1;  
	}  
  
	// ……  
  
    return (0);  
}  
```  
  
## wait3 等待指定子进程状态改变
  
  
### 函数原型：
  
```c  
pid_t wait3  
(  
	int *stat_loc,   
	int options,   
	struct rusage *prusage  
);  
```  
  
  
### 描述：
  
等待指定子进程状态改变  
  
### 参数：
  
- stat_loc ：子进程退出码；  
  
- option ：功能选项，由位掩码组成，掩码位，如表8.5所示：  
  
- prusage ：子进程的资源使用情况。  
  
### 返回值：
  
如果由于子进程状态改变导致函数返回，返回0。如果options设置了WNOHANG位，且没有子进程状态发生改变，则不等待且返回0。其他情况返回-1并设置错误码；  
  
### 错误码：
  
- ECHILD ：无子进程。  
- EINTR ：中断回调错误。  
  
### 样例：
  
子进程：  
  
```c  
#include <stdio.h>  
#include <spawn.h>  
#include <unistd.h>  
#include <sylixos.h>  
#include <sys/resource.h>  
#include <process.h>  
int main (int argc, char *argv[])  
{  
    while(1)  
	{  
    // ……  
        printf("this is child process\n");  
        // ……  
	}  
      
	return  (0);  
}  
```  
  
  
父进程：  
  
```c  
#include <stdio.h>  
#include <spawn.h>  
#include <unistd.h>  
#include <sylixos.h>  
#include <sys/resource.h>  
#include <process.h>  
int main (int argc, char *argv[])  
{  
    int                       ret;  
    posix_spawnattr_t         spawnattr;  
    int                       status;  
    struct rusage             rusage;  
  
    // ……  
  
	ret = wait3(&status, 0, &rusage);  
    if(ret < 0)  
    {  
        perror("wait3");  
        return -1;  
	}  
	// ……  
    return  (0);  
}  
```  
  
  
## wait4 ：等待指定子进程状态改变
  
  
### 函数原型：
  
```c  
pid_t wait4  
(  
	pid_t pid,  
	int *stat_loc,   
	int options,  
	struct rusage *prusage  
);  
```  
  
  
### 描述：
  
如果由于子进程状态改变导致函数返回，返回子进程ID。如果options设置了WNOHANG  
位，且没有子进程状态发生改变，则不等待且返回0。其他情况返回-1并设置错误码；  
  
### 参数：
  
- pid ：进程id号。  
  
- stat_loc ：子进程退出码。  
  
- option ：功能选项，由位掩码组成，掩码位。  
  
- prusage ：子进程的资源使用情况。  
  
### 返回值：
  
如果由于子进程状态改变导致函数返回，返回子进程ID。如果options设置了WNOHANG  
位，且没有子进程状态发生改变，则不等待且返回0。其他情况返回-1并设置错误码；  
  
### 错误码：
  
- ECHILD ：无子进程。  
- EINTR ：中断回调错误。  
  
### 样例：
  
子进程：  
  
```c  
#include <stdio.h>  
#include <spawn.h>  
#include <unistd.h>  
#include <sylixos.h>  
#include <sys/resource.h>  
#include <process.h>  
int main (int argc, char *argv[])  
{  
    while(1)  
	{  
    // ……  
        printf("this is child process\n");  
        // ……  
	}  
      
	return  (0);  
}  
```  
  
  
父进程：  
  
```c  
#include <stdio.h>  
#include <spawn.h>  
#include <unistd.h>  
#include <sylixos.h>  
#include <sys/resource.h>  
#include <process.h>  
int main (int argc, char *argv[])  
{  
    int                       ret;  
    posix_spawnattr_t         spawnattr;  
    int                       status;  
    struct rusage             rusage;  
    // ……  
	ret = wait4(&status, 0, &rusage);  
    return  (0);  
}  
```  
  
## spawnl 选择或者新建子进程
  
  
### 函数原型：
  
```c  
int spawnl  
(  
	int mode,  
	const char *path,   
	const char *argv0,   
	...  
);  
```  
  
  
### 描述：
  
spawnl函数与exec函数的区别是：exec系列函数不会新建进程，只能在现有进程环境执行新的可执行文件。spawn系列函数可以选择在现有进程环境执行可执行文件，也可以选择新建子进程。  
  
### 参数：
  
- mode ：进程创建模式，其值可以有以下几种情况；  
  
|宏名|解释|  
|---|---|  
|P_WAIT|暂停父进程,直到子进程完成了执行|  
|P_NOWAIT|新建子进程，调用线程不等待子进程退出|  
|P_OVERLAY|不新建子进程，在当前进程空间执行新程序|  
|P_NOWAITO|新建子进程，调用线程不等待子进程退出|  
|P_DETACH|子进程与父进程分离|  
  
- path ：可执行文件路径；  
  
- argv0 ：第一个命令行参数，一般情况下为命令名称。  
  
- ... ：为可变参数  
  
### 返回值：
  
此返回函数成功返回0，失败返回-1并设置错误码；  
  
### 错误码：
  
- EINVAL ：参数无效。  
- ENOMEM ：内存不足。  
  
### 样例：
  
子进程：  
  
```c  
#include <stdio.h>  
#include <spawn.h>  
#include <unistd.h>  
#include <sylixos.h>  
#include <sys/resource.h>  
#include <process.h>  
int main (int argc, char *argv[])  
{  
    while(1)  
	{  
        // ……  
        printf("this is child process\n");  
        // ……  
	}  
      
	return  (0);  
}  
```  
  
  
父进程：  
  
```c  
#include <stdio.h>  
#include <spawn.h>  
#include <unistd.h>  
#include <sylixos.h>  
#include <sys/resource.h>  
#include <process.h>  
int main (int argc, char *argv[])  
{  
    int                       ret;  
  
    posix_spawnattr_t         spawnattr;  
    int                       status;  
    struct rusage             rusage;  
  
    // ……  
  
    if (posix_spawnattr_init(&spawnattr) != 0)  
    {  
            fprintf(stderr, "init posix_spawnattr_t failed\n");  
            return  (-1);  
    }  
  
    ret = spawnl(P_WAIT,CHILD_PATH,NULL,NULL);  
    if (ret != 0)  
	{  
   		perror("spawnl ");  
        return  (-6);  
    }  
    // ……  
    return  (0);  
}  
```  
  
  
## spawnle ：选择或者新建子进程
  
  
### 函数原型：
  
```c  
int spawnle  
(  
	int mode,  
	const char *path,  
	const char *argv0,   
	...  
);  
```  
  
  
### 描述：
  
spawnle函数与exec函数的区别是：exec系列函数不会新建进程，只能在现有进程环境执行新的可执行文件。spawn系列函数可以选择在现有进程环境执行可执行文件，也可以选择新建子进程。  
  
### 参数：
  
- mode ：进程创建模式，其值可以有以下几种情况；  
  
|宏名|解释|  
|---|---|  
|P_WAIT|暂停父进程,直到子进程完成了执行|  
|P_NOWAIT|新建子进程，调用线程不等待子进程退出|  
|P_OVERLAY|不新建子进程，在当前进程空间执行新程序|  
|P_NOWAITO|新建子进程，调用线程不等待子进程退出|  
|P_DETACH|子进程与父进程分离|  
  
- path ：可执行文件路径；  
  
- argv0 ：第一个命令行参数，一般情况下为命令名称。  
  
- ... ：为可变参数  
  
### 返回值：
  
此返回函数成功返回0，失败返回-1并设置错误码；  
  
### 错误码：
  
- EINVAL ：参数无效。  
- ENOMEM ：内存不足。  
  
### 样例：
  
子进程：  
  
```c  
int main (int argc, char *argv[])  
{  
    while(1)  
{  
    // ……  
        printf("this is child process\n");  
        // ……  
}  
      
return  (0);  
}  
```  
  
父进程：  
  
```c  
#include <stdio.h>  
#include <spawn.h>  
#include <unistd.h>  
#include <sylixos.h>  
#include <sys/resource.h>  
#include <process.h>  
int main (int argc, char *argv[])  
{  
    int                       ret;  
  
    posix_spawnattr_t         spawnattr;  
    int                       status;  
    struct rusage             rusage;  
  
    // ……  
  
    if (posix_spawnattr_init(&spawnattr) != 0)  
    {  
            fprintf(stderr, "init posix_spawnattr_t failed\n");  
            return  (-1);  
    }  
  
    ret = spawnle(P_WAIT,CHILD_PATH,NULL,NULL);  
    if (ret != 0)  
	{  
   perror("spawnle ");  
        return  (-6);  
    }  
  
    // ……  
  
    return  (0);  
}  
```  
  
  
## spawnlp ：选择或者新建子进程
  
  
### 函数原型：
  
```c  
int spawnlp  
(  
	int mode,   
	const char *file,   
	const char *argv0,  
	...  
);  
```  
  
  
### 描述：
  
spawnlp函数与exec函数的区别是：exec系列函数不会新建进程，只能在现有进程环境执行新的可执行文件。spawn系列函数可以选择在现有进程环境执行可执行文件，也可以选择新建子进程。  
  
### 参数：
  
- mode ：进程创建模式，其值可以有以下几种情况；  
  
|宏名|解释|  
|---|---|  
|P_WAIT|暂停父进程,直到子进程完成了执行|  
|P_NOWAIT|新建子进程，调用线程不等待子进程退出|  
|P_OVERLAY|不新建子进程，在当前进程空间执行新程序|  
|P_NOWAITO|新建子进程，调用线程不等待子进程退出|  
|P_DETACH|子进程与父进程分离|  
  
- file ：可执行文件名，与参数path的区别是不带目录。  
  
- argv0 ：第一个命令行参数，一般情况下为命令名称。  
  
- ... ：为可变参数  
  
### 返回值：
  
此返回函数成功返回0，失败返回-1并设置错误码；  
  
### 错误码：
  
- EINVAL ：参数无效。  
- ENOMEM ：内存不足。  
  
### 样例：
  
子进程：  
  
```c  
int main (int argc, char *argv[])  
{  
    while(1)  
	{  
    // ……  
        printf("this is child process\n");  
        // ……  
	}  
      
	return  (0);  
}  
```  
  
  
父进程：  
  
```c  
#include <stdio.h>  
#include <spawn.h>  
#include <unistd.h>  
#include <sylixos.h>  
#include <sys/resource.h>  
#include <process.h>  
int main (int argc, char *argv[])  
{  
    int                       ret;  
  
    posix_spawnattr_t         spawnattr;  
    int                       status;  
    struct rusage             rusage;  
  
    // ……  
  
    if (posix_spawnattr_init(&spawnattr) != 0)  
    {  
            fprintf(stderr, "init posix_spawnattr_t failed\n");  
            return  (-1);  
    }  
  
    ret = spawnlp(P_WAIT,CHILD_PATH,NULL,NULL);  
    if (ret != 0)  
	{  
        perror("spawnlp ");  
        return  (-6);  
    }  
  
    // ……  
  
    return  (0);  
}  
```  
  
  
## spawnv ：选择或者新建子进程
  
### 函数原型：
  
```c  
int spawnv  
(  
	int mode,   
	const char *path,   
	char * const *argv  
);  
```  
  
  
### 描述：
  
spawnv函数与exec函数的区别是：exec系列函数不会新建进程，只能在现有进程环境执行新的可执行文件。spawn系列函数可以选择在现有进程环境执行可执行文件，也可以选择新建子进程。  
  
### 参数：
  
- mode ：进程创建模式，其值可以有以下几种情况；  
  
|宏名|解释|  
|---|---|  
|P_WAIT|暂停父进程,直到子进程完成了执行|  
|P_NOWAIT|新建子进程，调用线程不等待子进程退出|  
|P_OVERLAY|不新建子进程，在当前进程空间执行新程序|  
|P_NOWAITO|新建子进程，调用线程不等待子进程退出|  
|P_DETACH|子进程与父进程分离|  
  
- path ：可执行文件路径；  
  
- argv ：由命令行参数组成的字符串数组，数组以可执行文件名开始，以0结束。  
  
### 返回值：
  
此返回函数成功返回0，失败返回-1并设置错误码；  
  
### 错误码：
  
- EINVAL ：参数无效。  
- ENOMEM ：内存不足。  
  
### 样例：
  
子进程：  
  
```c  
int main (int argc, char *argv[])  
{  
    while(1)  
{  
    // ……  
        printf("this is child process\n");  
        // ……  
	}  
	      
	return  (0);  
}  
```  
  
父进程：  
  
```c  
#include <stdio.h>  
#include <spawn.h>  
#include <unistd.h>  
#include <sylixos.h>  
#include <sys/resource.h>  
#include <process.h>  
int main (int argc, char *argv[])  
{  
    int                       ret;  
  
    posix_spawnattr_t         spawnattr;  
    int                       status;  
    struct rusage             rusage;  
  
    // ……  
  
    if (posix_spawnattr_init(&spawnattr) != 0)  
    {  
            fprintf(stderr, "init posix_spawnattr_t failed\n");  
            return  (-1);  
    }  
  
    ret =spawnv(P_WAIT,CHILD_PATH,NULL);  
    if (ret != 0)  
	{          
		perror("spawnv");  
        return  (-6);  
    }   
    // ……  
    return  (0);  
}  
```  
  
## spawnve ：选择或者新建子进程
  
  
### 函数原型：
  
```c  
int spawnve  
(  
	int mode,  
	const char *path,   
	char * const *argv,  
	char * const *envp  
);  
```  
  
  
### 描述：
  
spawnve族函数与exec函数的区别是：exec系列函数不会新建进程，只能在现有进程环境执行新的可执行文件。spawn系列函数可以选择在现有进程环境执行可执行文件，也可以选择新建子进程。  
  
### 参数：
  
- mode ：进程创建模式，其值可以有以下几种情况；  
  
|宏名|解释|  
|---|---|  
|P_WAIT|暂停父进程,直到子进程完成了执行|  
|P_NOWAIT|新建子进程，调用线程不等待子进程退出|  
|P_OVERLAY|不新建子进程，在当前进程空间执行新程序|  
|P_NOWAITO|新建子进程，调用线程不等待子进程退出|  
|P_DETACH|子进程与父进程分离|  
  
- path ：可执行文件路径；  
  
- argv ：由命令行参数组成的字符串数组，数组以可执行文件名开始，以0结束；  
  
- envp ：需要预先设置的进程环境变量字符串集合，数组以0结束。  
  
### 返回值：
  
此返回函数成功返回0，失败返回-1并设置错误码；  
  
### 错误码：
  
- EINVAL ：参数无效。  
- ENOMEM ：内存不足。  
  
### 样例：
  
子进程：  
  
```c  
int main (int argc, char *argv[])  
{  
    while(1)  
	{  
        // ……  
        printf("this is child process\n");  
        // ……  
	}  
          
return  (0);  
}  
```  
  
  
父进程：  
  
```c  
#include <stdio.h>  
#include <spawn.h>  
#include <unistd.h>  
#include <sylixos.h>  
#include <sys/resource.h>  
#include <process.h>  
int main (int argc, char *argv[])  
{  
    int                       ret;  
  
    posix_spawnattr_t         spawnattr;  
    int                       status;  
    struct rusage             rusage;  
  
    // ……  
  
    if (posix_spawnattr_init(&spawnattr) != 0)  
    {  
            fprintf(stderr, "init posix_spawnattr_t failed\n");  
            return  (-1);  
    }  
  
    ret = spawnlve(P_WAIT,CHILD_PATH,NULL,NULL);  
    if (ret != 0)  
	{  
        perror("spawnlve ");  
        return  (-6);  
    }  
  
    // ……  
  
    return  (0);  
}  
```  
  
## spawnvp ：选择或者新建子进程
  
  
### 函数原型：
  
```c  
int spawnvp  
(  
	int mode,  
	const char *file,  
	char * const *argv  
);  
```  
  
### 描述：
  
spawnvp函数与exec函数的区别是：exec系列函数不会新建进程，只能在现有进程环境执行新的可执行文件。spawn系列函数可以选择在现有进程环境执行可执行文件，也可以选择新建子进程。  
  
### 参数：
  
- mode ：进程创建模式。  
  
|宏名|解释|  
|---|---|  
|P_WAIT|暂停父进程,直到子进程完成了执行|  
|P_NOWAIT|新建子进程，调用线程不等待子进程退出|  
|P_OVERLAY|不新建子进程，在当前进程空间执行新程序|  
|P_NOWAITO|新建子进程，调用线程不等待子进程退出|  
|P_DETACH|子进程与父进程分离|  
  
- path ：可执行文件路径；  
  
- argv0 ：第一个命令行参数，一般情况下为命令名称。  
  
- ... ：为可变参数  
  
### 返回值：
  
此返回函数成功返回0，失败返回-1并设置错误码；  
  
### 错误码：
  
- EINVAL ：参数无效。  
- ENOENT ：找不到文件。  
- ENOMEM ：内存不足。  
  
### 样例：
  
子进程：  
  
```c  
int main (int argc, char *argv[])  
{  
    while(1)  
	{  
    	// ……  
        printf("this is child process\n");  
        // ……  
	}  
      
	return  (0);  
}  
```  
  
  
父进程：  
  
```c  
#include <stdio.h>  
#include <spawn.h>  
#include <unistd.h>  
#include <sylixos.h>  
#include <sys/resource.h>  
#include <process.h>  
int main (int argc, char *argv[])  
{  
    int                       ret;  
    posix_spawnattr_t         spawnattr;  
    int                       status;  
    struct rusage             rusage;  
  
    // ……  
  
    if (posix_spawnattr_init(&spawnattr) != 0)  
    {  
            fprintf(stderr, "init posix_spawnattr_t failed\n");  
            return  (-1);  
    }  
  
    ret =spawnvp(P_WAIT,CHILD_PATH,NULL);  
    if (ret != 0)  
    {  
        perror("spawnvp ");  
        return  (-6);  
    }  
    // ……  
    return  (0);  
}  
```  
  
## spawnvpe ：选择或者新建子进程
  
  
### 函数原型：
  
```c  
int spawnvpe  
(  
	int mode,   
	const char *file,  
	char * const *argv,  
	char * const *envp  
);  
```  
  
  
### 描述：
  
spawn函数与exec函数的区别是：exec系列函数不会新建进程，只能在现有进程环境执行新的可执行文件。spawn系列函数可以选择在现有进程环境执行可执行文件，也可以选择新建子进程。  
  
### 参数：
  
- mode ：进程创建模式，其值可以有以下几种情况；  
  
|宏名|解释|  
|---|---|  
|P_WAIT|暂停父进程,直到子进程完成了执行|  
|P_NOWAIT|新建子进程，调用线程不等待子进程退出|  
|P_OVERLAY|不新建子进程，在当前进程空间执行新程序|  
|P_NOWAITO|新建子进程，调用线程不等待子进程退出|  
|P_DETACH|子进程与父进程分离|  
  
- Path ：可执行文件路径；  
  
- argv0 ：第一个命令行参数，一般情况下为命令名称。  
  
- ... ：为可变参数  
  
### 返回值：
  
此返回函数成功返回0，失败返回-1并设置错误码；  
  
### 错误码：
  
- EINVAL ：参数无效。  
- ENOENT ：找不到文件。  
- ENOMEM ：内存不足。  
  
### 样例：
  
子进程：  
  
```c  
int main (int argc, char *argv[])  
{  
    while(1)  
	{  
    	// ……  
        printf("this is child process\n");  
        // ……  
	}  
      
	return  (0);  
}  
```  
  
  
父进程：  
  
```c  
#include <stdio.h>  
#include <spawn.h>  
#include <unistd.h>  
#include <sylixos.h>  
#include <sys/resource.h>  
#include <process.h>  
#define  CHILD_PATH  "../HYSylixos_setpriority_child_demo/HYSylixos_setpriority_child_demo"  
int main (int argc, char *argv[])  
{  
    int                      ret;  
    posix_spawnattr_t         spawnattr;  
  
    ret =spawnvpe(P_WAIT,CHILD_PATH,NULL,NULL);  
    if (ret != 0)  
    {  
        perror("spawnle");  
        return  (-6);  
    }  
  
    sleep(1);  
    printf("father go on\n");  
    return  (0);  
}  
```  
  
  
# 信号量  
  
  
## sem_init：创建一个匿名的信号量
  
### 函数原型：
  
```c  
int  sem_init  
(  
	sem_t  *psem,  
	int  pshared,  
	unsigned  int  value  
);  
```  
  
### 描述：
  
该接口用于创建一个匿名的信号量，信号量的初始值为value。  
  
### 参数：
  
- psem ：信号量标识。  
  
- pshared ：共享标志位。（没有用到）  
  
- value ：信号量的初始值。  
  
### 返回值：
  
0 函数执行成功，完成信号量psem的初始化。  
-1 函数执行失败，设置errno指出错误。  
  
### 错误码：
  
- EINVAL ：参数无效。  
- ENOMEM ：内存不足。  
- ENOSPC ：非法操作。  
  
### 备注：
  
GJB 7714-2012错误码为：  
- EINVAL ：参数无效。    
- ENOSPC ：非法操作。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <semaphore.h>  
static    pthread_t  tid1, tid 2;                               /*  线程 id          */                                                 
static    sem_t     psem;  
static void *Thread1 (void *arg)  
{  
	// ……  
  
	return   NULL;  
}  
static void *Thread2 (void *arg)  
{  
	// ……  
  
	return NULL;  
}  
int main (int argc, char **argv)  
{  
	int       ret;  
  
    ret = sem_init (&psem, 1, 1);                               /* 创建一个信号量 */                     
    if(ret != 0)  
	{  
		perror("func sem_init error");  
        return -1;  
    }  
  
    ret = pthread_create (&tid 1, NULL, Thread1, NULL);      /*  创建第 1 个线程 */  
    if(ret != 0)  
    {  
        perror("func pthread_create error");  
       	return -1;  
    }  
  
    ret = pthread_create (&tid 2, NULL, Thread2, NULL);         /*创建第 2 个线程   */           
    if(ret != 0)  
    {  
    	perror("func pthread_create error");  
        return -1;  
    }  
  
    pthread_join (tid1, NULL);                                /*  回收第 1 个线程 */                                            
    pthread_join (tid2, NULL);                                /*  回收第 2 个线程 */  
     return  (0);  
}  
```  
  
  
## sem_destroy：删除一个匿名的信号量
  
  
### 函数原型：
  
```c  
int  sem_destroy  
(  
	sem_t   *psem  
);  
```  
  
  
### 描述：
  
该接口用于删除一个匿名的信号量。  
  
### 参数：
  
- psem ：信号量标识。  
  
### 返回值：
  
0 函数执行成功。  
-1 函数执行失败，设置errno指出错误。  
  
### 错误码：
  
- EINVAL ：参数无效。  
  
### 备注：
  
GJB 7714-2012错误码为：  
- EINVAL ：参数无效。  
- EBUSY ：设备资源忙。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <semaphore.h>   
static    pthread_t  tid1, tid 2;                               /*  线程 id         */                                                 
static    sem_t     psem;:                                       
int main (int argc, char **argv)  
{  
	int       ret;  
  
    ret = sem_init (&psem, 1, 1);                              /* 初始化一个信号量 */                     
    if(ret != 0)  
    {  
        perror("func sem_init error");  
        return -1;  
	}  
	// ……     
                                 
	ret = sem_destroy(&psem)                                /* 销毁信号量       */  
	if(ret != 0)  
	{  
   		perror("func sem_destory error\n");  
        return -1;  
	}  
  
	return  (0);  
}  
```  
  
  
## sem_open：创建或打开命名的信号量
  
### 函数原型：
  
```c  
sem_t  *sem_open  
(    
	const char  *name,   
	int  flag,   
	...  
);  
```  
  
  
### 描述： 
  
该接口用来创建或打开命名的二值或者计数信号量。  
  
### 参数：
  
- name ：信号量对象的名字。  
  
- flag ：信号量对象的标志位。参数可以为O_CREAT、O_EXCL。  
  
|标志位|说明|  
|---|---|  
|O_CREAT|信息量对象不存在时会创建信号量，存在不起作用|  
|O_EXCL|信号量不存在时不会创建|  
  
创建信号量，需要附加4个参数。  
- mode_t mode ：信号量的权限位。  
  
- uint_t value ：信号量的初值。  
  
- int sem_type ：信号量的类型。取值是：  
  
|类型|说明|  
|---|---|  
|SEM_BINARY（0）|二进制信号量|  
|SEM_COUNTING（1）|计数信号量|  
  
- int waitq_type ：信号量资源的排队策略，取值是：  
  
|排队策略|说明|  
|---|---|  
|PTHREAD_WAITQ_FIFO （0）|先进先出策略|  
|PTHREAD_WAITQ_PRIO（1）|优先级策略|  
  
### 返回值：
  
信号量ID函数执行成功，返回信号量的地址。  
SEM_FAILED函数执行失败，并设置errno来指出错误。  
  
### 错误码： 
  
- EINVAL ：参数无效。  
- ENAMETOOLONG ：路径名太长。  
- EEXIST ：文件已存在。  
- ENOMEM ：内存不足。  
- ENOSPC ：非法操作。  
- ENOENT ：文件或设备不存在。  
  
### 备注：
  
GJB 7714-2012错误码为：  
- EINVAL ：参数无效。  
- ENAMETOOLONG ：路径名太长。  
- EEXIST ：文件已存在。  
- ENOSPC ：非法操作。  
- ENOENT ：文件或设备不存在。  
  
### 样例：
  
```c  
#include <unistd.h>  
#include <stdio.h>  
#include <sys/types.h>  
#include <sys/wait.h>  
#include <semaphore.h>  
#include <spawn.h>  
#include <fcntl.h>  
#define NAME "twl"  
static sem_t *psem;  
int main (int argc, char **argv)  
{  
	int                        ret;  
	int                        stat;  
	pid_t                      pid;  
	pid_t                      child_pid;  
	posix_spawnattr_t          attrp;  
	posix_spawn_file_actions_t file_actions;  
  
	psem = sem_open(NAME, O_CREAT|O_RDWR,777,1);     /* 打开一个命名信号量 */                   
	if(psem == NULL)  
	{  
		perror("func sem_open error\n");  
		return -1;  
	}  
 	// ……  
}  
```  
  
  
## sem_close：关闭命名信号量
  
  
### 函数原型：
  
```c  
int  sem_close  
(  
	sem_t  *psem  
);  
```  
  
  
### 描述：
  
该接口表示任务已经完成了对psem所指定信号量的使用，并关闭该信号量。  
  
如果信号量没有调用sem_unlink()移除,那么sem_close()对信号量的状态没有影响。如果信号量在以O_CREAT调用sem_open()成功后调用sem_unlink()。那么当所打开该信号量的任务关闭它后，该信号量就不能访问了。  
  
### 参数：
  
- psem : 信号量标识。  
  
### 返回值：
  
0 函数执行成功。  
-1 函数执行失败，设置errno指出错误。  
  
### 错误码： 
  
- EINVAL ：参数无效。  
  
### 备注：
  
错误码和GJB 7714-2012错误码相同  
  
### 样例：
  
```c  
#include <unistd.h>  
#include <stdio.h>  
#include <sys/types.h>  
#include <sys/wait.h>  
#include <semaphore.h>  
#include <spawn.h>  
#include <fcntl.h>  
#define NAME "twl"  
static sem_t *psem;  
int main (int argc, char **argv)  
{  
	int                        ret;  
	int                        stat;  
	pid_t                      pid;  
	pid_t                      child_pid;  
	posix_spawnattr_t           attrp;  
	posix_spawn_file_actions_t file_actions;  
	  
	psem =sem_open(NAME,O_CREAT|O_RDWR,777,1);    /* 打开一个命名信号量   */  
	if(psem == NULL)  
	{  
		perror("func sem_open error\n");  
		return -1;  
	}  
    // ……  
    ret = sem_close(psem);                               /* 关闭命名信号量       */                                                
	if(ret != 0)  
	{  
		perror("func sem_close error\n");  
		return -1;  
	}  
// ……  
}  
```  
  
  
## sem_unlink：删除命名信号量
  
  
### 函数原型：
  
```c  
int  sem_unlink  
(  
	const char *name  
);  
```  
  
  
### 描述： 
  
该接口用于删除参数name指定的信号量。如果信号量当前被其他任务引用，函数返回0，不影响信号量状态，直到所有任务对该信号量的引用都通过调用sem_close()关闭，sem_unlink()才会删除该信号量。在所有引用被关闭前，sem_unlink()调用不会阻塞，它会立即返回。  
  
### 参数：
  
- name ：信号量名称。  
  
### 返回值：
  
0 函数执行成功。  
-1 函数执行失败，不会改变信号量，设置errno指出错误。  
  
### 错误码：
  
- ENOENT ：文件或设备不存在。  
- EBUSY ：设备资源忙。  
- EINVAL ：参数无效。  
  
### 备注：
  
GJB 7714-2012错误码为：  
- ENAMETOOLONG ：路径名太长。  
- ENOENT ：文件或设备不存在。  
  
### 样例：
  
```c  
#include <unistd.h>  
#include <stdio.h>  
#include <sys/types.h>  
#include <sys/wait.h>  
#include <semaphore.h>  
#include <spawn.h>  
#include <fcntl.h>  
#define NAME "twl"  
static sem_t *psem;  
int main (int argc, char **argv)  
{  
	int                        ret;  
	int                        stat;  
	pid_t                      pid;  
	pid_t                      child_pid;  
	posix_spawnattr_t          attrp;  
	posix_spawn_file_actions_t file_actions;  
  
	psem = sem_open(NAME, O_CREAT|O_RDWR,777,1);     /* 打开一个命名信号量 */                                           
	if(psem == NULL)  
	{  
		perror("func sem_open error\n");  
	 	return -1;  
	}  
    // ……  
    ret = sem_close(psem);                                /* 关闭命名信号量     */                                                
	if(ret != 0)  
	{  
		perror("func sem_close error\n");  
		return -1;  
	}  
  
	ret = sem_unlink(NAME);                              /* 删除命名信号量    */                                                
	if(ret != 0)  
	{  
		perror("func sem_unlink error\n");  
	  	return -1;  
	}  
}    
```  
  
  
## sem_wait：等待信号量
  
  
### 函数原型：
  
```c  
int  sem_wait  
(  
	sem_t  *psem  
);  
```  
  
  
### 描述： 
  
该接口用来锁定psem指定的信号量。  
  
如果信号量当前值为0，那么调用任务不会从sem_wait()中返回，除非它成功锁定信号量或是被信号中断。  
  
如果该接口调用成功，信号量状态变为锁定状态，且保持到sem_post()接口被成功调用。  
  
### 参数：
  
- psem ：信号量标识。  
  
### 返回值：
  
0 调用任务在psem指定的信号量上成功执行了信号量锁定操作。  
-1 函数执行失败，设置errno指出错误，信号量的状态不会改变。  
  
### 错误码：
  
- EINVAL ：参数无效。  
  
### 备注：
  
GJB 7714-2012错误码为：  
- EINVAL ：参数无效。  
- EINTR ：任务收到一个非阻塞或忽略的信号。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <semaphore.h>  
static    sem_t     psem;  
static void *Thread2 (void *arg)  
{  
	int   ret;  
  
	printf("this is Thread 2!\n");  
  
	sem_wait(&psem);                                          /* 等待信号量  */  
	if(ret != 0)  
	{  
		perror("func sem_wait error\n");  
		return (void *)-1;  
	}                                                                     
	printf("Thread 2 completed!\n");  
  
	return (NULL);  
}  
```  
  
  
## sem_trywait：等待信号量（不阻塞）
  
### 函数原型：
  
```c  
int  sem_trywait  
(  
	sem_t  *psem  
);  
```  
  
  
### 描述： 
  
该接口会尝试在信号量psem上执行锁定操作，只有在信号量未被锁定，即信号量的取值为正数的情况下，该接口才能成功锁定信号量，否则该接口将无法锁定信号量。  
该函数不会阻塞，若获取不到信号量，则直接返回。  
  
### 参数：
  
- psem ：信号量标识。  
  
### 返回值：  
  
0 调用任务在psem指定的信号量上成功执行了信号量锁定操作。  
-1 函数执行失败，设置errno指出错误，信号量的状态不会发生改变。  
  
### 错误码：
  
- EINVAL ：参数无效。  
- EAGAIN ：系统资源不足。  
  
### 备注：
  
GJB 7714-2012错误码为：  
- EINVAL ：参数无效。  
- EINTR ：任务收到一个非阻塞或忽略的信号。  
- EAGAIN ：统资源不足。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <semaphore.h>  
static    sem_t     psem;  
static void *Thread2 (void *arg)  
{   
	int   ret;  
   	while(1)  
   	{  
		printf("Thread2 is start\n");  
  
     	ret = sem_trywait (&psem);                       /* 尝试等待信号量        */                                                  
     	if (ret != 0)  
     	{  
			perror("func sem_trywait error\n");  
			return(void *)-1;  
     	}  
     	printf("Thread2 is complete\n");  
  
     	sleep(1);  
   	}  
   	sleep(1);  
  
   	return NULL;  
}  
```  
  
  
## sem_timedwait：等待信号量（带有超时的阻塞）
  
  
### 函数原型：
  
```c  
int  sem_timedwait  
(  
	sem_t  *psem,   
	const struct timespec *abs_timeout  
);  
```  
  
  
### 描述：
  
该接口的功能与sem_wait()接口类似，都是用来锁定psem引用的信号量，但如果在指定的限时时间内没有其他任务执行sem_post()对信号量解锁的话，那么该接口将终止等待，返回错误。  
如果任务等待的时间超过abs_timeout指定的绝对时间，或者调用该接口时已经超过了abs_timoout指定的绝对时间，就会发生超时。如果信号量可以被立即锁定的话，接口调用就不会超时而失败。  
如果信号量可以被立即锁定的话，不会检测sem_timedwait的有效性。  
  
### 参数：
  
- psem ：信号量标识符。  
  
- abs_timeout ：指定的绝对超时时间  
  
### 返回值：
  
0 调用任务在psem指定的信号量上成功执行锁定操作。  
-1 函数执行失败，设置errno指出错误，信号量的状态不会变化。  
  
### 错误码： 
  
- EINVAL ：参数无效。  
- EINTR ：任务收到一个非阻塞或忽略的信号。  
- ETIMEDOUT ：等待超时。  
  
### 备注：
  
错误码和GJB 7714-2012错误码相同。  
  
### 样例：
  
```c  
#include <unistd.h>  
#include <stdio.h>  
#include <stdlib.h>  
#include <semaphore.h>  
#include <sys/time.h>  
#include <pthread.h>  
#include <time.h>  
static    sem_t     psem;  
static void *Thread3 (void *arg)  
{  
	struct timespec ts;  
	int    ret;  
	// ……  
  
	ts.tv_nsec = 0;  
	ts.tv_sec = time(NULL) + 5;                             /* 设置 5s 钟等待超时 */  
	ret = sem_timedwait(&psem, &ts);                       /* 限时等待信号量     */   
	if(ret == -1)  
    {  
		printf("Thread 3 wait timeout\n");  
		pthread_exit(NULL);  
	}  
	// ……  
  
	return (NULL);  
}  
```  
  
  
## sem_post：释放信号量
  
  
### 函数原型：
  
```c  
int  sem_post   
(  
	sem_t  *psem  
);  
```  
  
  
### 描述：
  
该接口用来解锁psem指定的信号量。如果从该接口的操作结果中得到的信号量的值是正数，就表示没有任务在阻塞等待该信号量解锁，信号量的值只需要简单的递增；如果从该接口的操作结果中得到的信号量的值是0，那么其中一个阻塞等待该信号量的任务将成功从sem_wait()接口调用中返回。  
  
### 参数：
  
- psem ：信号量标识。  
  
### 返回值：
  
0 函数执行成功  
-1 函数执行失败，设置errno指出错误。  
  
### 错误码： 
  
- EINVAL ：参数无效。  
  
### 备注：
  
错误码和GJB 7714-2012错误码相同  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <semaphore.h>  
static    sem_t     psem;  
static void *Thread1 (void *arg)  
{  
	int       ret;  
  
	while(1)  
	{   
		printf ("Thread1 is start\n");  
  
		ret = sem_post(&psem);                                   /* 发送信号量 */                                                  
   		if (ret != 0)  
		{  
			perror ("func sem_post error\n");  
   			return (void *)-1;  
		}  
  		printf ("Thread1 is complete\n");  
  
  		sleep(1);  
	}  
	sleep(1);  
  
	return NULL;  
}  
```  
  
  
## sem_flush：将所有阻塞的在这个信号量上的任务解除阻塞
  
  
### 函数原型：
  
```c  
int  sem_flush   
(  
	sem_t  *psem  
);  
```  
  
  
### 描述： 
  
该接口将所有阻塞在psem指定的信号量上的任务解除阻塞，这些解除阻塞的任务全部进入就绪队列，此信号量的状态不会被改变。  
在同步应用中，sem_flush可以用来进行广播。  
  
### 参数：
  
- psem ：信号量标识。  
  
### 返回值：
  
0 函数执行成功。  
-1 函数执行失败。  
  
### 错误码： 
  
- EINVAL ：参数无效。  
  
### 备注：
  
GJB 7714-2012错误码为：  
- EINVAL ：参数无效。  
- EMNOTINITED ：信号量模块未初始化。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <semaphore.h>  
static    sem_t     psem;  
static void *Thread3 (void *arg)  
{  
	int       ret;  
  
	sleep(1);  
	printf("Thread3 is start\n");  
  
     ret = sem_flush (&psem) ;                          /* 释放被阻塞的信号量    */                                               
     if (ret != 0)  
     {  
		 perror("func sem_flush error\n");  
		 return(void *)-1;  
     }  
  
     printf("Thread3 is complete\n");  
  
     return   NULL;  
}  
```  
  
  
## sem_getvalue：获取指定信号量的值

   
### 函数原型：
  
```c  
int  sem_getvalue   
(  
	sem_t  *psem,   
	int  *pivalue  
);  
```  
  
  
### 描述： 
  
该接口用来获取psem指定的信号量的值，并存放在参数pivalue指定的地址中。该接口并不影响信号量的状态。  
如果psem被锁定了，那么pivalue指向的对象会被设为0或负数，如果是负数，其绝对值等于正在等待信号量的任务数目。  
  
### 参数：
  
- psem ：信号量标识。  
  
- Pivalue ：存放信号量的值的指针。  
  
### 返回值：
  
0 函数执行成功。  
-1 函数执行失败，设置errno指出错误。  
  
### 错误码： 
  
- EINVAL ：参数无效。  
  
### 备注：
  
GJB 7714-2012错误码为：  
- EINVAL ：参数无效。  
- EMNOTINITED ：信号量模块未初始化。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <semaphore.h>  
static    sem_t     psem;  
static void *Thread3 (void *arg)  
{  
	int       ret;  
	int       value;  
	while(1)  
    {  
		printf ("Thread3 is start\n");  
  
     	ret = sem_getvalue(&psem, &value);                 /* 获取指定信号量的值 */                                    
     	if(ret == 0)  
     	{  
         	printf("sem value :%d\n", value);  
     	}  
  
     	printf ("Thread3 is complete\n");  
     	sleep(1);  
   	}  
   	sleep(1);  
  
   	return NULL;  
}  
```  
  
  
## sem_getinfo：获取指定信号量的概要信息
  
  
### 函数原型：
  
```c  
int  sem_getinfo  
(  
	sem_t  *sem,   
	sem_info_t  *info  
);  
```  
  
  
### 描述： 
  
该接口用于获取指定的信号量的概要信息。包括等信号量的类型、名字、最大值、当前值、打开次数、link标记和阻塞在指定信号量上的任务数目等信息。  
  
### 参数：
  
- psem ：信号量标识。  
  
- info ：存放信号量信息的指针。  
  
### 返回值：
  
函数调用成功返回0。  
失败则返回-1，并设置错误码。  
  
### 错误码： 
  
- EINVAL ：参数无效。  
  
### 备注：
  
错误码和GJB 7714-2012错误码相同  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <semaphore.h>  
static    sem_t     psem;  
static void *Thread3 (void *arg)  
{  
	int         ret;  
   	sem_info_t  info;  
  
   	while(1)  
   	{  
		printf ("Thread3 is start\n");  
  
     	ret = sem_getinfo(&psem, &info);                  /* 获取指定信号量的信息 */                                      
     	if(ret == 0)  
		{  
        	printf("ulCounter :%d\n", (int)info.SEMINFO_ulCounter );  
         	printf("ulOption  :%d\n", (int)info.SEMINFO_ulOption);  
          	printf("ulBlockNum:%d\n" , (int)info.SEMINFO_ulBlockNum);       
		}  
     	printf ("Thread3 is complete\n");  
  
     	sleep(1);  
   	}  
   	sleep(1);  
  
	return NULL;  
}  
```  
  
  
## sem_show：显示指定信号量的信息
  
  
### 函数原型：
  
```c  
int  sem_show   
(  
	sem_t  *psem,  
	int  level  
);  
```  
  
  
### 描述：
  
该接口用于显示psem指定的信号量的相关信息。  
  
### 参数：
  
- psem ：信号量标识。  
  
- level ：显示信息的详细程度，0为显示概要信息，1为显示详细信息。  
  
### 返回值：
  
0 调用任务在psem指定的信号量上成功执行锁定操作。  
-1 函数执行失败，设置errno指出错误，信号量的状态不会变化。  
  
### 错误码： 
  
- EINVAL ：参数无效。  
  
### 备注：
  
GJB 7714-2012错误码为：  
- EINVAL ：参数无效。  
- EMNOTINITED ：信号量模块未初始化。  
- ETIMEDOUT ：等待超时。  
- EINTR ：任务收到一个非阻塞或忽略的信号。  
- EOBJDELETED ：因信号量对象被删除而终止阻塞等待。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <semaphore.h>  
static    sem_t     psem;  
static void *Thread3 (void *arg)  
{   
	int ret;  
  
   	while(1)  
   	{  
		printf ("Thread3 is start\n");  
  
     	ret = sem_show(&psem, 1);                             /*显示信号量信息 */                                                    
     	if(ret != 0)  
     	{  
         	perror("func sem_show error");  
         	return (void *)-1;  
     	}  
       
     	printf ("Thread3 is complete\n");  
  
     	sleep(1);  
   	}  
   	sleep(1);  
  
	return NULL;  
}  
```  
  
  
## sem_reltimedwait_np：等待信号量（带有超时的阻塞）
  
  
### 函数原型：
  
```c  
int sem_reltimedwait_np  
(  
	sem_t *psem,  
	const struct timespec *rel_timeout  
);  
```  
  
  
### 描述：
  
该接口用来等待一个带有超时阻塞的信号量。  
  
### 参数：
  
- psem ：信号量句柄。  
  
- rel_timeout ：限时时间。这里是相对时间。  
  
### 返回值：
  
0 函数执行成功。  
  
-1 函数执行失败，设置errno指出错误。  
  
### 错误码： 
  
- EINVAL ：参数无效。  
- EINTR ：任务收到一个非阻塞或忽略的信号。  
- ETIMEDOUT ：等待超时。  
  
### 备注：
  
GJB 7714-2012没有此接口  
  
### 样例：
  
```c  
#include <unistd.h>  
#include <stdio.h>  
#include <stdlib.h>  
#include <semaphore.h>  
#include <sys/time.h>  
#include <pthread.h>  
#include <time.h>  
static    sem_t     psem;  
static void *Thread3 (void *arg)  
{  
	struct timespec ts;  
	int        ret;  
  
	printf("this is Thread 3!\n");  
	// ……  
  
	ts.tv_nsec = 0;  
	ts.tv_sec = 5;                                   /* 设置 5s 钟等待超时       */  
	ret = sem_reltimedwait_np(&psem, &ts);           /* 等待带有超时阻塞的信号量 */  
	if(ret == -1)                                
    {  
		printf("Thread 3 wait timeout\n");  
		pthread_exit(NULL);  
	}  
  
	printf("Thread 3 completed!\n");  
  
	return (NULL);  
}  
```  
  
  
## sem_open_method：设置打开信号量的类型
  
  
### 函数原型：
  
```c  
int  sem_open_method  
(  
	int  method,   
	int  *old_method  
);     
```  
  
  
### 描述：
  
该接口设置当前进程使用的信号量的类型。  
  
### 参数： 
  
- method ：新的操作类型。取值有：  
  
|method|说明|  
|---|---|  
|SEM_OPEN_METHOD_POSIX（1）|使用posix类型的信号量|  
|SEM_OPEN_METHOD_GJB（2）|使用国军标类型的信号量|  
  
- old_method ：之前的操作类型。  
  
### 返回值：
  
函数返回0。  
  
### 错误码： 
  
无。  
  
### 备注：
  
GJB 7714-2012没有此接口  
  
### 样例：
  
```c  
#include <unistd.h>  
#include <stdio.h>  
#include <sys/types.h>  
#include <sys/wait.h>  
#include <semaphore.h>  
#include <spawn.h>  
#include <fcntl.h>  
int main (int argc, char **argv)  
{  
	// ……  
	sem_open_method(SEM_OPEN_METHOD_POSIX, NULL);/* 以POSIX信号量打开 */  
	// ……  
  
	return NULL;  
}  
```  
  
  
# 互斥量  
  
  
## pthread_mutexattr_init ：初始化互斥量属性块  
  
  
### 函数原型：
  
```c  
int	pthread_mutexattr_init  
(  
	pthread_mutexattr_t *pmutexattr  
);  
```  
  
  
### 描述：
  
该接口用于以缺省值来初始化一个互斥量属性pmutexattr。在使用互斥量的属性对象对一个或多个互斥量进行初始化后，任何影响属性对象的接口（包括删除接口）都不会影响到之前初始化的互斥量。将属性块的属性设为默认的值。  
  
### 参数：
  
- pmutexattr ：指向互斥量属性对象的指针。  
  
### 返回值：
  
成功返回0；失败返回相应的错误码。  
  
### 错误码：
  
- EINVAL ：参数无效。  
  
### 备注：
  
与GJB 7714-2012差别：无  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <string.h>  
#include <pthread.h>  
int main (int  argc, char  *argv[])  
{  
	int  		ret;  
	pthread_mutexattr_t mutexattr;  
	ret = pthread_mutexattr_init(&mutexattr);               /* 初始化互斥量属性对象  */  
	if(ret != 0)  
	{  
		perror("func pthread_mutexattr_init error");  
		return -1;  
	}  
	// ……  
	return (0);  
}  
```  
  
  
## pthread_mutexattr_destroy ：销毁互斥量属性块
  
  
### 函数原型：
  
```c  
int	pthread_mutexattr_destroy  
(  
	pthread_mutexattr_t *pmutexattr  
);  
```  
  
  
### 描述：
  
该接口用于删除一个互斥量属性对象，也就是将对象变为未初始化状态。  
  
### 参数：
  
- pmutexattr ：指向互斥量属性对象的指针。  
  
### 返回值：
  
成功返回0；失败返回相应的错误码。  
  
### 错误码： 
  
- EINVAL ：参数无效。  
  
### 备注：
  
与GJB 7714-2012错误码的差别：  
  
- EINVAL pmutexattr ：一个无效指针或者pmutexattr指向的的是未初始化的属性对象。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <string.h>  
#include <pthread.h>  
int main (int  argc, char  *argv[])  
{  
	int  		ret;  
	pthread_mutexattr_t mutexattr;  
	ret = pthread_mutexattr_init(&mutexattr);    		/*	初始化互斥量属性对象  */  
	if(ret != 0)  
	{  
		perror("func pthread_mutexattr_init error");  
		return -1;  
	}  
	// ……  
	pthread_mutexattr_destroy(&mutexattr);  
	return (0);  
}  
```  
  
  
## pthread_mutexattr_setprioceiling ：设置属性块的天花板优先级
  
  
### 函数原型：
  
```c  
int	pthread_mutexattr_setprioceiling  
(  
	pthread_mutexattr_t *pmutexattr,   
	int  prioceiling  
);  
```  
  
  
### 描述：
  
该接口用于设置互斥量的优先级置顶属性，该属性定义了互斥量的优先级置顶，该优先级是互斥量所保护的关键执行代码的最低优先级级别。为了避免优先级倒置，互斥量的优先级置顶应该被设置为比可能锁定该互斥量的所有任务的优先级更高或相等的优先级。  
  
### 参数： 
  
- pmutexattr ：指向互斥量属性对象的指针。  
  
- prioceiling ：天花板优先级。  
  
### 返回值：
  
成功返回0；失败返回相应的错误码。  
  
### 错误码： 
  
- EINVAL ：参数无效。  
  
### 备注：
  
与GJB 7714-2012错误码的差别：  
- EINVAL pmutexattr ：是一个无效指针或者pmutexattr指向的的是未初始化的属性对象，或者prioceiling指定的的优先级无效。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <string.h>  
#include <pthread.h>  
static pthread_t tid0, tid1, tid2;  
int main (int  argc, char  *argv[])  
{  
	int  		ret;  
	int     ceiling;  
	pthread_mutexattr_t mutexattr;  
	ret = pthread_mutexattr_setprioceiling(&mutexattr, 100);	        /*	优先级置顶	*/  
	if(ret != 0)  
	{  
		perror("func pthread_mutexattr_setprioceiling error\n");  
		return -1;  
	}  
	ret = pthread_setschedprio(tid0, 100);      		/*  设置线程1的优先级为低 */  
	if(ret != 0)  
	{  
		printf("func  pthread_setschedprio error\n");  
		return (-1);  
	}  
	ret = pthread_setschedprio(tid1, 50);  		  		/*  设置线程2的优先级为中 */  
	if(ret != 0)  
	{  
		printf("func  pthread_setschedprio error\n");  
		return (-1);  
	}  
	ret = pthread_setschedprio(tid2, 20);     			/*  设置线程3的优先级为高 */  
	if(ret != 0)  
	{  
		printf("func  pthread_setschedprio error\n");  
		return (-1);  
	}  
	// ……  
	return (0);  
}  
void    *Thread0(void  *arg)  
{  
	// ……  
}  
void    *Thread1(void  *arg)  
{  
	// ……  
}  
void    *Thread2(void  *arg)  
{  
	// ……  
}  
```  
  
## pthread_mutexattr_getprioceiling ：获取属性块的天花板优先级
  
### 函数原型：
  
```c  
int	pthread_mutexattr_getprioceiling  
(  
	const pthread_mutexattr_t *pmutexattr,   
	int  *prioceiling  
);  
```  
  
  
### 描述：
  
该接口用来获取互斥量属性块的天花板优先级置顶属性。  
  
### 参数：
  
- pmutexattr ：指向互斥量属性对象的指针。  
  
- prioceiling ：存放天花板优先级置顶的指针。  
  
### 返回值：
  
成功返回0；失败返回相应的错误码。  
  
### 错误码：
  
- EINVAL ：参数无效。  
  
### 备注：
  
与GJB 7714-2012错误码的差别：  
- EINVAL pmutexattr ：是无效的指针，或者pmutexattr指向的是未初始化的属性对象。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <string.h>  
#include <pthread.h>  
static pthread_t tid0, tid1, tid2;  
int main (int  argc, char  *argv[])  
{  
	int  		ret;  
	int     ceiling;  
	pthread_mutexattr_t mutexattr;  
	ret = pthread_mutexattr_setprioceiling(&mutexattr, 100);	         /*	优先级置顶	*/  
	if(ret != 0)  
	{  
		perror("func pthread_mutexattr_setprioceiling error\n");  
		return -1;  
	}  
	ret = pthread_mutexattr_getprioceiling(&mutexattr, &ceiling);	  /*	获取优先级置顶	*/  
	if(ret != 0)  
	{  
		perror("func pthread_mutexattr_getprioceiling error\n");  
		return -1;  
	}  
	ret = pthread_setschedprio(tid0, 100);     			/*  设置线程1的优先级为低 */  
	if(ret != 0)  
	{  
		printf("func  pthread_setschedprio error\n");  
		return (-1);  
	}  
   	ret = pthread_setschedprio(tid1, 50);  		  	/*  设置线程2的优先级为中 */  
    if(ret != 0)  
   	{  
   		printf("func  pthread_setschedprio error\n");  
        return (-1);  
   	}  
   	ret = pthread_setschedprio(tid2, 20);     		/*  设置线程3的优先级为高 */  
   	if(ret != 0)  
   	{  
   		printf("func  pthread_setschedprio error\n");  
   		return (-1);  
    }  
	// ……  
	return (0);  
}  
void    *Thread0(void  *arg)  
{  
	// ……  
}  
void    *Thread1(void  *arg)  
{  
	// ……  
}  
void    *Thread2(void  *arg)  
{  
	// ……  
}  
```  
  
  
## pthread_mutexattr_setprotocol ：设置互斥量属性块的算法
  
  
### 函数原型：
  
```c  
int	pthread_mutexattr_setprotocol  
(  
	pthread_mutexattr_t *pmutexattr,  
	int  protocol  
);  
```  
  
  
### 描述：
  
该接口用于设置互斥量属性对象pmutexattr中的协议属性protocol，该属性定义了互斥量为避免优先级反转所使用的协议。  
  
### 参数： 
  
- pmutexattr ：指向互斥量属性对象的指针。  
  
- protocol ：互斥量的协议属性算法类型。取值有：  
  
|值|算法类型|意义|  
|---|---|---|  
|0|PTHREAD_PRIO_NONE|使用默认算法（继承，按FIFO等待）|  
|1|PTHREAD_PRIO_INHERIT|使用优先级继承算法，按优先级等待|  
|2|PTHREAD_PRIO_PROTECT|使用优先级天花板，按优先级等待|  
  
### 返回值：
  
成功返回0；失败返回相应的错误码。  
  
### 错误码：
  
- EINVAL ：参数无效。  
- ENOTSUP ：系统不支持的设置。  
  
### 备注：
  
与GJB 7714-2012错误码的差别：  
- ENOTSUP protocol ：protocol的取值不合法或互斥量的阻塞等待策略冲突。  
- EINVAL pmutexattr ：是一个无效指针，或者pmutexattr指向的的是未初始化的属性对象。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <string.h>  
#include <pthread.h>  
int main (int  argc, char  *argv[])  
{  
	int  		ret;  
	pthread_mutexattr_t	mutexattr;  
	ret=pthread_mutexattr_init(&mutexattr);          		/* 初始化互斥量属性块	*/  
	if(ret != 0)  
	{  
	    perror("func pthread_mutexattr_init error\n");  
	    return -1;  
	}  
	ret=pthread_mutexattr_setprotocol(&mutexattr, PTHREAD_PRIO_PROTECT);  
	if(ret != 0)                            			/*   使用优先级天花板    */  
	{  
		perror("func pthread_mutexattr_setprotocol error\n");  
		return (-1);  
	}       
	// ……  
	return (0);  
}  
```  
  
  
## pthread_mutexattr_getprotocol ：获取互斥量属性块的算法类型
  
  
### 函数原型：
  
```c  
int	pthread_mutexattr_getprotocol  
(  
	const pthread_mutexattr_t *pmutexattr,  
	int  *protocol  
);  
```  
  
  
### 描述：
  
该接口用于获取互斥量属性对象协议属性的算法类型。  
  
### 参数：
  
- pmutexattr ：指向互斥量属性对象的指针。  
  
- protocol ：存放互斥量协议属性算法类型的指针。  
  
### 返回值：
  
成功返回0；失败返回相应的错误码。  
  
### 错误码：
  
- EINVAL ：参数无效。  
  
### 备注：
  
与GJB 7714-2012错误码的差别：  
- EINVAL pmutexattr ：是无效的指针，或者pmutexattr指向的是未初始化的属性对象。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <string.h>  
#include <pthread.h>  
int main (int  argc, char  *argv[])  
{  
	int  		ret;  
	pthread_mutexattr_t	mutexattr         			/*	初始化互斥量属性块	*/  
	ret = pthread_mutexattr_init(&mutexattr);  
	if(ret != 0)  
	{  
		perror("func pthread_mutexattr_init error\n");  
		return -1;  
	}  
	ret=pthread_mutexattr_setprotocol(&mutexattr, PTHREAD_PRIO_PROTECT);  
	if(ret != 0)                            			/*   使用优先级天花板    */  
	{  
		perror("func pthread_mutexattr_setprotocol error\n");  
		return (-1);  
	}       
	ret = pthread_mutexattr_getprotocol(&mutexattr, &protocol); /* 获取互斥量的协议属性*/  
	if(ret != 0)  
	{  
		perror("func pthread_mutexattr_getprotocol error\n");  
	    return -1;  
	}  
	// ……  
	return (0);  
}  
```  
  
  
## pthread_mutexattr_setpshared ：设置互斥量属性的共享属性
  
  
### 函数原型：
  
```c  
int	pthread_mutexattr_setpshared  
(  
	pthread_mutexattr_t *pmutexattr,  
	int  pshared  
);  
```  
  
  
### 描述：
  
该接口用来设置互斥量属性块的共享属性，是否进程共享，只支持私有。  
  
### 参数： 
  
- pmutexattr ：指向互斥量属性对象的指针。  
  
- pshared ：是否共享。  
  
### 返回值：
  
成功返回0；失败返回相应的错误码。  
  
### 错误码：
  
- EINVAL ：参数无效。  
  
### 备注：
  
GJB 7714-2012无此接口。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <string.h>  
#include <pthread.h>  
static pthread_t tid0, tid1, tid2;  
int main (int  argc, char  *argv[])  
{  
	int  		ret;  
	int          shared;  
	pthread_mutexattr_init(&mutexattr);            		/*	初始化互斥量属性块	*/  
	ret = pthread_mutexattr_init(&mutexattr);  
	if(ret != 0)  
	{  
		perror("func pthread_mutexattr_init error\n");  
		return -1;  
	}  
	ret = pthread_mutexattr_setpshared(&mutexattr, 1);	/*设置互斥量属性的共享属性*/  
	if(ret != 0)  
	{  
    	perror("func pthread_mutexattr_setpshared error\n");  
    	return -1;  
	}  
	// ……  
	return (0);  
}  
void	*Thread0(void  *arg)  
{        
	// ……  
}  
void	*Thread1(void  *arg)  
{        
	// ……  
}  
void	*Thread2(void  *arg)  
{        
	// ……  
}  
```  
  
  
## pthread_mutexattr_getpshared ：获取互斥量属性块的共享属性
  
### 函数原型：
  
```c  
int	pthread_mutexattr_getpshared  
(  
	const pthread_mutexattr_t *pmutexattr,  
	int  *pshared  
);  
```  
  
  
### 描述：
  
该接口用来获取互斥量属性块的共享属性，是否进程共享。  
  
### 参数： 
  
- pmutexattr ：指向互斥量属性对象的指针。  
  
- pshared ：存放是否共享的指针。  
  
### 返回值：
  
成功返回0；失败返回相应的错误码。  
  
### 错误码： 
  
- EINVAL ：参数无效。  
  
### 备注：
  
GJB 7714-2012无此接口。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <string.h>  
#include <pthread.h>  
static pthread_t tid0, tid1, tid2;  
int main (int  argc, char  *argv[])  
{  
	int  		ret;  
	int          shared;  
	pthread_mutexattr_init(&mutexattr);            		/*	初始化互斥量属性块	*/  
	ret = pthread_mutexattr_init(&mutexattr);  
	if(ret != 0)  
	{  
		perror("func pthread_mutexattr_init error\n");  
		return -1;  
	}  
	ret = pthread_mutexattr_setpshared(&mutexattr, 1);  
												/*设置互斥量属性的共享属性*/  
	if(ret != 0)  
	{  
    	perror("func pthread_mutexattr_setpshared error\n");  
    	return -1;  
    }  
	ret=pthread_mutexattr_getpshared(&mutexattr,&shared);                               												/*获取互斥量属性的共享属性*/  
	if(ret == 0)  
   	{  
   		printf("mutexattr shared %d\n", shared);			  
   	}  
	// ……  
	return (0);  
}  
void	*Thread0(void  *arg)  
{        
	// ……  
}  
void	*Thread1(void  *arg)  
{        
	// ……  
}  
void	*Thread2(void  *arg)  
{        
	// ……  
}  
```  
  
## pthread_mutexattr_settype ：设置互斥量属性块类型
  
  
### 函数原型：
  
```c  
int	pthread_mutexattr_settype  
(  
	pthread_mutexattr_t *pmutexattr,   
	int  type  
);  
```  
  
  
### 描述：
  
该接口用来设置互斥量属性块类型。  
  
### 参数：
  
- pmutexattr ：指向互斥量属性对象的指针。  
  
- type ：类型，取值有：  
  
|值|互斥量属性块类型|意义|  
|---|---|---|  
|0|PTHREAD_MUTEX_NORMAL|重复 lock 产生死锁|  
|1|PTHREAD_MUTEX_ERRORCHECK|重复 lock 返回错误|  
|2|PTHREAD_MUTEX_RECURSIVE|重复 lock 支持递归|  
  
### 返回值：
  
成功返回0；失败返回相应的错误码。  
  
### 错误码：
  
- EINVAL ：参数无效。  
  
### 备注：
  
与GJB 7714-2012错误码的差别：  
- EINVAL pmutexattr ：是一个无效指针，或者pmutexattr指向的的是未初始化的属性对象，或者type的取值不合法。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <string.h>  
#include <pthread.h>  
void   *Thread0(void  *arg);  
int main (int  argc, char  *argv[])  
{  
	int  		ret;  
	pthread_mutexattr_init(&mutexattr);              		/*	初始化互斥量属性块 */  
	ret = pthread_mutexattr_init(&mutexattr);  
	if(ret != 0)  
	{  
		perror("func pthread_mutexattr_init error\n");  
		return -1;  
	}  
	ret=pthread_mutexattr_settype(&mutexattr,PTHREAD_MUTEX_NORMAL);                                    														/*重复lock 产生死锁	*/  
	if(ret != 0)  
	{  
		perror("func pthread_mutexattr_settype error\n");  
    	return -1;  
	}  
	// ……  
	return (0);  
}  
```  
  
  
## pthread_mutexattr_gettype ：获取互斥量属性块类型
  
  
### 函数原型：
  
```c  
int	pthread_mutexattr_gettype  
(  
	const pthread_mutexattr_t *pmutexattr,   
	int  *type  
);  
```  
  
### 描述：
  
该接口用来获取互斥量属性块类型。  
  
### 参数：
  
- pmutexattr ：指向互斥量属性对象的指针。  
  
- type ：存放互斥量属性块类型的指针。  
  
### 返回值：
  
成功返回0；失败返回相应的错误码。  
  
### 错误码：
  
- EINVAL ：参数无效。  
  
### 备注：
  
与GJB 7714-2012错误码的差别：  
- EINVAL pmutexattr ：是一个无效指针，或者pmutexattr指向的的是未初始化的属性对象。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <string.h>  
#include <pthread.h>  
void   *Thread0(void  *arg);  
int main (int  argc, char  *argv[])  
{  
	int  		ret;  
	pthread_mutexattr_t	mutexattr;          			/*	初始化互斥量属性块 */  
	ret = pthread_mutexattr_init(&mutexattr);  
	if(ret != 0)  
	{  
		perror("func pthread_mutexattr_init error\n");  
		return -1;  
	}  
	ret=pthread_mutexattr_settype(&mutexattr,PTHREAD_MUTEX_NORMAL);             												/*	重复 lock 产生死锁	*/	  
	if(ret != 0)  
	{  
		perror("func pthread_mutexattr_settype error\n");  
		return -1;  
	}  
	ret=pthread_mutexattr_gettype(&mutexattr,&type); 		/*	获取互斥量属性类型	*/  
	if(ret == 0)  
	{  
		printf(" mutexattr type %d\n", type);  
	}  
	// ……  
	return (0);  
}  
```  
  
  
## pthread_mutexattr_setwaitqtype ：设置互斥量属性块等待队列类型
  
  
### 函数原型：
  
```c  
int	pthread_mutexattr_setwaitqtype  
(  
	pthread_mutexattr_t  *pmutexattr,   
	int  waitq_type  
);  
```  
  
  
### 描述：
  
该接口用来设置互斥量属性块等待队列类型。  
  
### 参数：
  
- pmutexattr ：指向互斥量属性对象的指针。  
  
- waitq_type ：等待队列类型。取值有：  
  
|值|互斥量等待队列类型|意义|  
|---|---|---|  
|1|PTHREAD_WAITQ_PRIO|阻塞等待该类互斥量的任务将按照优先级抢占的策略依此解除阻塞|  
|0|PTHREAD_WAITQ_FIFO|阻塞等待该类互斥量的任务将按照先进先出的策略依此解除阻塞|  
  
该属性与PTHREAD_PRIO_INHERIT、PTHREAD_PRIO_PROTECT的属性不可同时设置。  
  
### 返回值：
  
成功返回0；失败返回相应的错误码。  
  
### 错误码：
  
- EINVAL ：参数无效。  
  
### 备注：
  
与GJB 7714-2012错误码的差别：  
- ENOTUSP ：系统不支持的设置；  
- EMNOTINITED ：模块尚未初始化；  
- ENOTSUP waitq_type :设置与互斥量的协议属性相冲突；  
- EINVAL pmutexattr ：是一个无效指针，或者pmutexattr指向的的是未初始化的属性对象。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <string.h>  
#include <pthread.h>  
int main (int  argc, char  *argv[])  
{  
	int  		ret;  
	pthread_mutexattr_init(&mutexattr);              		/*	初始化互斥量属性块	*/  
	ret = pthread_mutexattr_init(&mutexattr);  
	if(ret != 0)  
	{  
		perror("func pthread_mutexattr_init error\n");  
		return -1;  
	}  
	ret =pthread_mutexattr_setwaitqtype(&mutexattr, PTHREAD_WAITQ_PRIO);	                               												/*	优先级抢占依次解除阻塞	*/  
	if(ret != 0)  
	{  
		perror("func pthread_mutexattr_setwaitqtype error\n");  
	    return -1;  
	}  
	// ……  
	return (0);  
}  
```  
  
  
## pthread_mutexattr_getwaitqtype ：获取互斥量属性块等待队列类型
  
  
### 函数原型：
  
```c  
int	pthread_mutexattr_getwaitqtype  
(  
	const pthread_mutexattr_t *pmutexattr,   
	int *waitq_type  
);  
```  
  
  
### 描述：
  
该接口用来获取互斥量属性块等待队列类型。  
  
### 参数： 
  
- pmutexattr ：指向互斥量属性对象的指针。  
  
- waitq_type ：存放等待队列类型的指针。  
  
### 返回值：
  
成功返回0；失败返回相应的错误码。  
  
### 错误码： 
  
- EINVAL ：参数无效。  
  
### 备注：
  
与GJB 7714-2012错误码的差别：  
- EMNOTINITED ：模块尚未初始化；  
- EINVAL pmutexattr ：是一个无效指针，或者pmutexattr指向的的是未初始化的属性对象。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <string.h>  
#include <pthread.h>  
int main (int  argc, char  *argv[])  
{  
	int  		ret;  
	pthread_mutexattr_init(&mutexattr);              		/*	初始化互斥量属性块	*/  
	ret = pthread_mutexattr_init(&mutexattr);  
	if(ret != 0)  
	{  
		perror("func pthread_mutexattr_init error\n");  
		return -1;  
	}  
	ret =pthread_mutexattr_setwaitqtype(&mutexattr, PTHREAD_WAITQ_PRIO);	                               												/*	优先级抢占依次解除阻塞	*/  
	if(ret != 0)  
	{  
		perror("func pthread_mutexattr_setwaitqtype error\n");  
		return -1;  
	}  
	ret = pthread_mutexattr_getwaitqtype(&mutexattr, &waittype);  
	               							/*	获取互斥量的阻塞排队的类型	*/  
	if(ret == 0)  
	{  
		printf("mutexattr waittype %d\n", waittype);  
	}  
	// ……  
	return (0);  
}  
```  
  
## pthread_mutexattr_setcancelsafe ：设置互斥量取消安全类型
  
  
### 函数原型：
  
```c  
int	pthread_mutexattr_setcancelsafe  
(  
	pthread_mutexattr_t  *pmutexattr,  
	int  cancel_safe  
);  
```  
  
  
### 描述：
  
该接口用于设置互斥量的任务删除安全属性，即互斥量在取消时是否安全。其取值包括：  
PTHREAD_CANCEL_SAFE—如果任务锁定具有该属性的互斥量，则对该任务的取消操作将不成功，直到任务解锁互斥量才能取消任务。  
PTHREAD_CANCEL_UNSAFE—如果任务锁定具有该属性的互斥量，则任务的取消状态和取消类型都保持不变。  
  
### 参数：
  
- pmutexattr ：指向互斥量属性对象的指针。  
  
- cancel_type ：互斥量的任务删除安全属性。取值有：  
  
|值|互斥量任务删除类型|意义|  
|---|---|---|  
|1|PTHREAD_CANCEL_SAFE|取消操作将不成功，直到任务解锁互斥量才能取消任务|  
|0|PTHREAD_CANCEL_UNSAFE|任务的取消状态和取消类型都保持不变|  
  
### 返回值：
  
成功返回0；失败返回相应的错误码。  
  
### 错误码：
  
- EINVAL ：参数无效。  
  
### 备注：
  
与GJB 7714-2012错误码的差别：  
- EMNOTINITED ：模块尚未初始化。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <string.h>  
#include <pthread.h>  
int main (int  argc, char  *argv[])  
{  
	int  		ret;  
	int  	 canselsafe;  
	pthread_mutexattr_init(&mutexattr);              		/*	初始化互斥量属性块	*/  
	ret = pthread_mutexattr_init(&mutexattr);  
	if(ret != 0)  
	{  
		perror("func pthread_mutexattr_init error\n");  
		return -1;  
	}  
	ret=pthread_mutexattr_setcancelsafe(&mutexattr,PTHREAD_CANCEL_SAFE);            										/*	设置互斥量的任务删除安全属性	*/  
	if(ret != 0)  
  	{  
      	 perror("func pthread_mutexattr_setcancelsafe error\n");  
      	 return -1;  
  	}  
	// ……  
	return (0);  
}  
void	*Thread0(void  *arg)  
{  
	int ret;  
	ret=pthread_mutex_lock(&lock);			    /*  对资源上锁                   */  
   	if(ret != 0)  
   	{  
       	printf("func pthread_mutex_lock error\n");  
       	return (NULL);  
   	}  
   	count++;  
   	printf("thread_1(): count = %d\n", count);     	/*  输出提示信息                 */  
   	sleep(1);					               	/*  延时1s                       */  
   	return (NULL);  
}  
void	*Thread1(void  *arg)  
{  
   	count++;  
   	printf("thread_2(): count = %d\n", count);  
   	sleep(1);  
   	return (NULL);  
}  
```  
  
  
## pthread_mutexattr_getcancelsafe ：获取互斥量取消安全类型
  
  
### 函数原型：
  
```c  
int	pthread_mutexattr_getcancelsafe  
(  
	const pthread_mutexattr_t *pmutexattr,  
	int *cancel_safe  
);  
```  
  
  
### 描述：
  
该接口用来获取互斥量属性块安全类型。  
  
### 参数： 
  
- pmutexattr ：指向互斥量属性对象的指针。  
  
- cancel_type ：存放安全类型的指针。  
  
### 返回值：
  
成功返回0；失败返回相应的错误码。  
  
### 错误码：
  
- EINVAL ：参数无效。  
  
### 备注：
  
与GJB 7714-2012错误码的差别：  
- EMNOTINITED ：模块尚未初始化。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <string.h>  
#include <pthread.h>  
#include <stdlib.h>  
#include <unistd.h>  
int main (int  argc, char  *argv[])  
{  
	int  		ret;  
	int  	 canselsafe;  
	pthread_mutexattr_init(&mutexattr);            		/*	初始化互斥量属性块	*/  
	ret = pthread_mutexattr_init(&mutexattr);  
	if(ret != 0)  
	{  
		perror("func pthread_mutexattr_init error\n");  
		return -1;  
	}  
	ret=pthread_mutexattr_setcancelsafe(&mutexattr,PTHREAD_CANCEL_SAFE);            										/*	设置互斥量的任务删除安全属性	*/  
	if(ret != 0)  
	{  
		perror("func pthread_mutexattr_setcancelsafe error\n");  
      	return -1;  
	}  
	ret = pthread_mutexattr_getcancelsafe(&mutexattr, &canselsafe);   
	                              		/*	获取互斥量的任务删除安全属性	*/  
	if(ret == 0)  
	{  
		printf("mutexattr canselsafe %d\n", canselsafe);  
	}  
	// ……  
	return (0);  
}  
void	*Thread0(void  *arg)  
{  
	int ret;  
   	ret=pthread_mutex_lock(&lock);			    /*  对资源上锁                   */  
   	if(ret != 0)  
   	{  
       	printf("func pthread_mutex_lock error\n");  
       	return (NULL);  
   	}  
   	count++;  
   	printf("thread_1(): count = %d\n", count);      /*  输出提示信息                 */  
   	sleep(1);					                /*  延时1s                       */  
   	return (NULL);  
}  
void	*Thread1(void  *arg)  
{  
   	count++;  
   	printf("thread_2(): count = %d\n", count);  
   	sleep(1);  
   	return (NULL);  
}  
```  
  
## pthread_mutex_init ：初始化互斥量
  
  
### 函数原型：
  
```c  
int	pthread_mutex_init  
(  
	pthread_mutex_t  *pmutex,   
	const pthread_mutexattr_t *pmutexattr  
);  
```  
  
  
### 描述：
  
该接口用来通过互斥量属性块中的属性创建一个互斥量。当属性块为NULL时，使用默认属性创建互斥量。初始化成功的话，互斥量的状态变为已初始化且未锁定的状态。  
如果试图对一个已初始化的互斥量进行初始化，函数执行失败。  
  
### 参数： 
  
- pmutex ：指向互斥量对象的指针。  
  
- pmutexattr ：指向互斥量属性对象的指针。  
  
### 返回值：
  
成功返回0；失败返回相应的错误码。  
  
### 错误码： 
  
- EAGAIN ：系统资源不足。  
- EINVAL ：参数无效。  
  
### 备注：
  
与GJB 7714-2012的规定差别：无。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <string.h>  
#include <pthread.h>  
#include <stdlib.h>  
#include <unistd.h>  
int main (int  argc, char  *argv[])  
{  
	int  		ret;  
	pthread_mutex_t mutex;  
	pthread_mutexattr_t mutexattr;  
	ret = pthread_mutexattr_init(&mutexattr);   		/*	初始化互斥量属性对象     */  
	if(ret != 0)  
	{  
		perror("func pthread_mutexattr_init error\n");  
		return -1;  
	}  
	ret = pthread_mutex_init(&mutex, &mutexattr);       		/*	初始化互斥量 */  
	if(ret != 0)  
	{  
		perror("func pthread_mutex_init error");  
		return -1;  
	}  
 	// ……  
	return (0);  
}  
```  
  
  
## pthread_mutex_destroy ：销毁互斥量
  
  
### 函数原型：
  
```c  
int	pthread_mutex_destroy  
(  
	pthread_mutex_t  *pmutex  
);  
```  
  
### 描述：
  
该接口用来删除一个互斥量。  
  
### 参数： 
  
- pmutex ：指向互斥量对象的指针。  
  
### 返回值：
  
成功返回0；失败返回相应的错误码。  
  
### 错误码： 
  
- EINVAL ：参数无效。  
  
### 备注：
  
与GJB 7714-2012错误码的差别：  
- EBUSY ：对象忙。  
- EINVAL pmutex ：是一个无效的指针，或者mutex指定的互斥量不存在。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <string.h>  
#include <pthread.h>  
int main (int  argc, char  *argv[])  
{  
	int  		ret;  
	pthread_mutex_t mutex;  
	pthread_mutexattr_t mutexattr;  
	ret = pthread_mutexattr_init(&mutexattr);   		/*	初始化互斥量属性对象     */  
	if(ret != 0)  
	{  
		perror("func pthread_mutexattr_init error");  
		return -1;  
	}  
	ret = pthread_mutex_init(&mutex, &mutexattr);   			/*	初始化互斥量 */  
	if(ret != 0)  
	{  
		perror("func pthread_mutex_init error");  
		return -1;  
	}  
	ret = pthread_mutex_destroy(&mutex);             			/*	删除互斥量	*/  
	if(ret != 0)  
	{  
		perror("func pthread_mutex_init error");  
		return -1;  
	}  
   	// ……  
   	return (0);  
}  
```  
  
## pthread_mutex_lock ：给互斥量上锁
  
  
### 函数原型：
  
```c  
int	pthread_mutex_lock  
(  
	pthread_mutex_t  *pmutex  
);  
```  
  
### 描述：
  
该接口用来锁定互斥量，如果互斥量已经被锁定，调用任务会阻塞直到互斥量变为可用。成功锁定pmutex的任务将拥有互斥量的所有权。  
如果互斥量类型为PTHREAD_MUTEX_NORMAL，则不会检测死锁。  
如果互斥量类型为PTHREAD_MUTEX_ERRORCHECK，会提供错误检验。  
如果互斥量类型为PTHREAD_MUTEX_RECURSIVE，那么互斥量会保存一个锁定计数。  
  
### 参数： 
  
- pmutex ：指向互斥量对象的指针。  
  
### 返回值：
  
成功返回0；失败返回相应的错误码。  
  
### 错误码：
  
- EDEADLK ：检测到死锁。  
- EINVAL ：参数无效。  
  
### 备注：
  
与GJB 7714-2012错误码的差别：  
- EAGAIN ：系统资源不足。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <string.h>  
#include <pthread.h>  
#include <stdlib.h>  
#include <unistd.h>  
static pthread_mutex_t		 lock;                          		/*	互斥锁		*/  
static pthread_t tid0, tid1, tid2;  
static int 	    count = 0;  
void	*Thread0(void  *arg)  
{  
	int ret;  
	while (1)  
	{  
		ret=pthread_mutex_lock(&lock);			      	/*  对资源上锁        */  
		if(ret != 0)  
		{  
			printf("func pthread_mutex_lock error\n");  
			return (NULL);  
		}  
		count++;  
		printf("thread_1(): count = %d\n", count);  		/*  输出提示信息           */  
		sleep(1);					              	/*  延时1s                	*/  
	}  
	return (NULL);  
}  
```  
  
  
## pthread_mutex_trylock ：尝试给互斥量上锁
  
  
### 函数原型：
  
```c  
int	pthread_mutex_trylock  
(  
	pthread_mutex_t  *pmutex  
);  
```  
  
  
### 描述：
  
该接口用来尝试锁定互斥量，除了mutex引用的互斥量已被锁定的情况。  
  
### 参数： 
  
- pmutex ：指向互斥量对象的指针。  
  
### 返回值：
  
成功返回0；失败返回相应的错误码。  
  
### 错误码： 
  
- EDEADLK ：检测到死锁。  
- EINVAL ：参数无效。  
- EAGAIN ：设备资源不足。  
  
### 备注：
  
与GJB 7714-2012错误码的差别：  
- EBUSY : 对象忙。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <string.h>  
#include <pthread.h>  
#include <stdlib.h>  
#include <unistd.h>  
static pthread_mutex_t		 lock;                        		/*	互斥锁		*/  
static pthread_t tid0, tid1, tid2;  
static int 	    count = 0;  
void	*Thread0(void  *arg)  
{  
	int ret;  
	while (1)  
	{  
		ret=pthread_mutex_trylock(&lock);			 		/*  对资源上锁    */  
		if(ret != 0)  
		{  
			printf("func pthread_mutex_trylock error\n");  
			return (NULL);  
		}  
		count++;  
		printf("thread_1(): count = %d\n", count);  	      		/*  输出提示信息  */  
		pthread_mutex_unlock(&lock);			        	/*	 对资源解锁	  */  
		ret=pthread_mutex_unlock(&lock);			    	/*	 对资源解锁	  */  
		if(ret != 0)  
		{  
			printf("func pthread_mutex_unlock error\n");  
			return (NULL);  
		}  
		sleep(1);					              	/*  延时1s                */  
	}  
	return (NULL);  
}     
```  
  
  
## pthread_mutex_timedlock ：限时等待给互斥量上锁
  
  
### 函数原型：
  
```c  
int	pthread_mutex_timedlock  
(  
	pthread_mutex_t  *pmutex,   
	const struct timespec *abs_timeout  
);  
```  
  
  
### 描述：
  
该接口用来限时锁定互斥量（绝对时间）。如果其他任务已经锁定了互斥量，调用任务将阻塞等待一段指定的时间，如果任务在指定的时间内仍无法得到互斥量，它将因超时而停止等待并返回。  
  
### 参数：
  
- pmutex ：指向互斥量对象的指针。  
  
- abs_timeout ：超时时间（绝对时间）。  
  
### 返回值：
  
成功返回0；失败返回相应的错误码。  
  
### 错误码： 
  
- EDEADLK ：检测到死锁。  
- EINVAL ：参数无效。  
- EAGAIN ：设备资源不足。  
- ETIMEDOUT ：等待超时。  
  
### 备注：
  
GJB 7714-2012的规定差别：无  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <string.h>  
#include <pthread.h>  
#include <stdlib.h>  
#include <unistd.h>  
static pthread_mutex_t		 lock;                        		/*	互斥锁		*/  
static pthread_t tid0, tid1, tid2;  
static int 	    count = 0;  
void    *Thread0(void  *arg)  
{  
   	int ret;  
   	ret=pthread_mutex_lock(&mutex);      					/*  对资源上锁      */  
   	if(ret != 0)  
   	{  
       	perror("func pthread_mutex_unlock error\n");  
       	return NULL;  
   	}  
   	count++;  
   	printf("thread_1(): count = %d\n", count);  
   	return (NULL);  
}  
void    *Thread1(void  *arg)  
{  
   	int ret ;  
   	struct timespec outtime;  
   	outtime.tv_nsec = 0;  
   	outtime.tv_sec = time(NULL)+5;  
   	ret = pthread_mutex_timedlock(&mutex, &outtime);  
   	if(ret != 0)  
   	{  
       	perror("func pthread_mutex_timedlock error\n");  
       	return NULL;  
   	}                                              /*  对资源上锁             */  
    	count++;  
    	printf("thread_2(): count = %d\n", count);        /*  输出提示信息            */  
   	ret=pthread_mutex_unlock(&mutex);                /*  对资源解锁            */  
   	if(ret != 0)  
   	{  
       	perror("func pthread_mutex_unlock error\n");  
       	return NULL;  
   	}  
  	return (NULL);  
}   
```  
  
  
## pthread_mutex_reltimedlock_np ：限时等待给互斥量上锁
  
### 函数原型：
  
```c  
int	pthread_mutex_reltimedlock_np  
(  
	pthread_mutex_t  *pmutex,   
	const struct timespec *rel_timeout  
);  
```  
  
  
### 描述：
  
该接口用来限时锁定互斥量（相对时间）。  
  
### 参数： 
  
- pmutex ：指向互斥量对象的指针。  
  
- rel_timeout ：超时时间（相对时间）。  
  
### 返回值：
  
成功返回0；失败返回相应的错误码。  
  
### 错误码： 
  
- EDEADLK ：检测到死锁。  
- EINVAL ：参数无效。  
- EAGAIN ：设备资源不足。  
- ETIMEDOUT ：等待超时。  
  
### 备注：
  
GJB 7714-2012无此接口。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <string.h>  
#include <pthread.h>  
#include <stdlib.h>  
#include <unistd.h>  
static pthread_mutex_t		 lock;                       		   /*	互斥锁		*/  
static pthread_t tid0, tid1, tid2;  
static int 	    count = 0;  
void    *Thread0(void  *arg)  
{  
   	int ret;  
  
   	ret=pthread_mutex_lock(&mutex);                  /*  对资源解锁             */  
   	if(ret != 0)  
   	{  
      	perror("func pthread_mutex_unlock error\n");  
       	return NULL;  
   	}  
   	count++;  
   	printf("thread_1(): count = %d\n", count);  
  
   	return (NULL);  
}  
void    *Thread1(void  *arg)  
{  
   	int ret ;  
   	struct timespec outtime;  
   	outtime.tv_nsec = 0;  
   	outtime.tv_sec = 5;  
   	ret = pthread_mutex_reltimedlock_np(&mutex, &outtime);  
   	if(ret != 0)  
   	{  
       	perror("func pthread_mutex_reltimedlock_np error\n");  
       	return NULL;  
   	}                                      /*  对资源上锁                   */  
    count++;  
    printf("thread_2(): count = %d\n", count);  /*  输出提示信息                 */  
    ret=pthread_mutex_unlock(&mutex);     /*  对资源解锁                    */  
    if(ret != 0)  
    {  
      	perror("func pthread_mutex_unlock error\n");  
     	return NULL;  
   	}  
   	return (NULL);  
}  
```  
  
  
## pthread_mutex_unlock ：解锁互斥量  
  
### 函数原型：
  
```c  
int	pthread_mutex_unlock  
(  
	pthread_mutex_t  *pmutex  
);  
```  
  
  
### 描述：
  
该接口用来解锁互斥量。  
  
### 参数： 
  
- pmutex ：指向互斥量对象的指针。  
  
### 返回值：
  
成功返回0；失败返回相应的错误码。  
  
### 错误码： 
  
- EINVAL ：参数无效。  
- EPERM ：权限不足（不是锁定者）。  
  
### 备注：
  
与GJB 7714-2012的差别规定：无。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <string.h>  
#include <pthread.h>  
#include <stdlib.h>  
#include <unistd.h>  
static pthread_mutex_t		 lock;                     	/*	互斥锁		*/  
static pthread_t tid0, tid1, tid2;  
static int 	    count = 0;  
void	*Thread0(void  *arg)  
{  
	while (1)  
	{  
		pthread_mutex_lock(&lock);			           /*  对资源上锁  */  
		count++;  
		printf("thread_1():count=%d\n",count);      		/*输出提示信息*/  
		pthread_mutex_unlock(&lock);			   		/*	 对资源解锁*/  
		sleep(1);					                 	/*延时1s     */  
	}  
	return (NULL);  
}  
```  
  
  
## pthread_mutex_setprioceiling ：动态设置互斥量的天花板优先级
  
  
### 函数原型：
  
```c  
int	pthread_mutex_setprioceiling  
(  
	pthread_mutex_t  *pmutex,   
	int  prioceiling  
);  
```  
  
### 描述：
  
该接口用来设置互斥量优先级天花板。  
  
### 参数： 
  
- pmutex ：指向互斥量对象的指针。  
  
- prioceiling ：优先级。  
  
### 返回值：
  
成功返回0；失败返回相应的错误码。  
  
### 错误码： 
  
- EINVAL ：参数无效。  
  
### 备注：
  
GJB 7714-2012无此接口。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <string.h>  
#include <pthread.h>  
#include <stdlib.h>  
#include <unistd.h>  
static pthread_mutex_t       mutex;  
static pthread_t tid0, tid1, tid2;  
int main (int  argc, char  *argv[])  
{  
	int  		ret;  
	pthread_mutexattr_t mutexattr;  
	ret=pthread_mutexattr_init(&mutexattr);  
	if(ret != 0)  
	{  
        perror("func pthread_mutexattr_init error\n");  
       	return (-1);  
     }  
	ret=pthread_mutexattr_setprotocol(&mutexattr, PTHREAD_PRIO_PROTECT);  
	if(ret != 0)  
	{  
		perror("func pthread_mutexattr_setprotocol error\n");  
		return (-1);  
	}  
	ret = pthread_mutex_init(&mutex, &mutexattr);        /*  初始化互斥量     */  
	if(ret != 0)  
	{  
		perror("func pthread_mutex_init error");  
		return -1;  
	}  
	ret = pthread_mutex_setprioceiling(&mutex, 100);        	/*  优先级置顶   */  
	if(ret != 0)  
	{  
		perror("func pthread_mutex_setprioceiling error\n");  
		return (-1);  
	}  
	ret = pthread_setschedprio(tid0, 70);          		/*  设置线程1的优先级为高 */  
	if(ret != 0)  
	{  
       	printf("func  pthread_setschedprio error\n");  
		return (-1);  
	}  
	ret = pthread_setschedprio(tid1, 50);           	/*  设置线程2的优先级为中 */  
	if(ret != 0)  
  	{  
		printf("func  pthread_setschedprio error\n");  
		return (-1);  
   	}  
  	ret = pthread_setschedprio(tid2, 20);          		 /*  设置线程3的优先级为低 */  
	if(ret != 0)  
	{  
		printf("func  pthread_setschedprio error\n");  
     	return (-1);  
	}  
	// ……  
	return (0);  
}  
```  
  
  
## pthread_mutex_getprioceiling ：获取互斥量天花板优先级
  
  
### 函数原型：
  
```c  
int	pthread_mutex_getprioceiling  
(  
	pthread_mutex_t  *pmutex,   
	int  *prioceiling  
);  
```  
  
  
### 描述：
  
该接口用来获取互斥量优先级天花板。  
  
### 参数： 
  
- pmutex ：指向互斥量对象的指针。  
  
- prioceiling ：存放优先级的指针。  
  
### 返回值：
  
成功返回0；失败返回相应的错误码。  
  
### 错误码： 
  
- EINVAL ：参数无效。  
  
### 备注：
  
GJB 7714-2012无此接口。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <string.h>  
#include <pthread.h>  
#include <stdlib.h>  
#include <unistd.h>  
static pthread_mutex_t       mutex;  
static pthread_t tid0, tid1, tid2;  
int main (int  argc, char  *argv[])  
{  
	int  		ret;  
	pthread_mutexattr_t mutexattr;  
	ret=pthread_mutexattr_init(&mutexattr);  
	if(ret != 0)  
  	{  
      	perror("func pthread_mutexattr_init error\n");  
     	return (-1);  
  	}  
	ret=pthread_mutexattr_setprotocol(&mutexattr, PTHREAD_PRIO_PROTECT);  
  	if(ret != 0)  
  	{  
      	perror("func pthread_mutexattr_setprotocol error\n");  
      	return (-1);  
 	}  
  	ret = pthread_mutex_init(&mutex, &mutexattr);        /*  初始化互斥量     */  
  	if(ret != 0)  
 	{  
       	perror("func pthread_mutex_init error");  
       	return -1;  
	}  
	ret = pthread_mutex_setprioceiling(&mutex, 100);        /*  优先级置顶   */  
  	if(ret != 0)  
  	{  
      	perror("func pthread_mutex_setprioceiling error\n");  
     	return (-1);  
	}  
	ret = pthread_mutex_getprioceiling(&mutex, &ceiling);  /*  获取优先级置顶 */  
	if(ret != 0)  
	{  
		perror("func pthread_mutex_getprioceiling error\n");  
		return -1;  
	}  
	ret = pthread_setschedprio(tid0, 70);          /*  设置线程1的优先级为高 */  
	if(ret != 0)  
   	{  
       	printf("func  pthread_setschedprio error\n");  
       	return (-1);  
   	}  
   	ret = pthread_setschedprio(tid1, 50);           /*  设置线程2的优先级为中 */  
  	if(ret != 0)  
  	{  
      	printf("func  pthread_setschedprio error\n");  
      	return (-1);  
   	}  
  	ret = pthread_setschedprio(tid2, 20);           /*  设置线程3的优先级为低 */  
 	 if(ret != 0)  
  	{  
      	printf("func  pthread_setschedprio error\n");  
      	return (-1);  
	}  
	// ……  
     
	return (0);  
}  
```  
  
  
## pthread_mutex_getinfo ：获取互斥量信息
  
  
### 函数原型：
  
```c  
int	pthread_mutex_getinfo  
(  
	pthread_mutex_t  *pmutex,   
	pthread_mutex_info_t  *info  
);  
```  
  
  
### 描述：
  
该接口用来获取互斥量信息。包括互斥量的类型、优先级倒置避免协议、优先级置顶、阻塞排队策略、任务取消安全属性、拥有者等信息。  
  
### 参数： 
  
- pmutex ：指向互斥量对象的指针。  
  
- info ：存放互斥量信息的指针。  
  
### 返回值：
  
成功返回0；失败返回相应的错误码。  
  
### 错误码：
  
- EINVAL ：参数无效。  
  
### 备注：
  
与GJB 7714-2012错误码的差别：  
- EMNOTINITED ：模块尚未初始化。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <string.h>  
#include <pthread.h>  
#include <stdlib.h>  
#include <unistd.h>  
static pthread_mutex_t		 lock;                      /*	互斥锁		*/  
static pthread_t tid0, tid1, tid2;  
static int 	    count = 0;  
void	*Thread0(void  *arg)  
{        
	int ret ;  
	pthread_mutex_info_t info;  
	pthread_mutex_t	mutex;  
	ret = pthread_mutex_getinfo(&mutex, &info);		/*	获取互斥量信息	*/  
	if(ret == 0)  
   	{  
      	printf("************************info********************\n");  
       	printf("mutexattr_setcancelsafe  :%d\n", info);  
       	printf("************************end*********************\n");  
	}	  
	// ……  
	return (NULL);  
}  
```  
  
  
## pthread_mutex_show ：显示互斥量信息
  
  
### 函数原型：
  
```c  
int	pthread_mutex_show  
(  
	pthread_mutex_t  *pmutex,  
	int  level  
);  
```  
  
  
### 描述：
  
该接口用来显示互斥量信息。  
  
### 参数：
  
- pmutex ：指向互斥量对象的指针。  
  
- level ：显示级别0为显示概要信息，1为显示互斥量详细信息。  
  
### 返回值：
  
成功返回0；失败返回相应的错误码。  
  
### 错误码：
  
- EINVAL ：参数无效。  
  
### 备注：
  
与GJB 7714-2012错误码的差别：  
- EMNOTINITED ：模块尚未初始化。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <string.h>  
#include <pthread.h>  
#include <stdlib.h>  
#include <unistd.h>  
static pthread_mutex_t		 lock;                  /*	互斥锁		*/  
static pthread_t tid0, tid1, tid2;  
static int 	    count = 0;  
void	*Thread0(void  *arg)  
{        
	int ret;  
	pthread_mutexattr_t	mutex;  
	ret = pthread_mutex_show(&mutex, 1);	            /*	显示互斥量信息	*/  
	if(ret != 0)  
	{  
		perror("func pthread_mutex_show error\n");  
		return 0;  
	}   
	// ……  
	return (NULL);  
}  
```  
  
# 条件变量
  
## pthread_ condattr_init ：初始化条件变量属性块
  
### 函数原型：
  
```c  
#include <pthread.h>  
int  pthread_condattr_init  
(  
	pthread_attr_t  *attr  
);  
```  
  
### 描述：
  
该接口以缺省值初始化条件变量的属性对象attr。在使用条件变量属性对象完成初始化条件变量之后，那些影响属性对象的接口改变属性对象时并不会影响之前已初始化的条件变量。  
  
### 参数：  
  
- attr ： 指向线程属性对象的指针（输出）。  
  
### 返回值：
  
成功返回0，失败返回相应的错误码。  
  
### 错误码：
  
- EINVAL：参数无效。  
  
### 备注：  
  
GJB 7714-2012错误码：无。  
  
### 样例：  
  
```c  
int main (int argc, char **argv)  
{  
	int ret;  
	pthread_condattr_t condattr;  
  
	ret = pthread_condattr_init(&condattr);     /*初始化条件变量属性*/               
	if(ret != 0)  
	{  
		perror("func pthread_condattr_init error\n");  
		return -1;  
	}  
	// ……  
	return0;  
}  
```  
  
  
## pthread_ condattr_destroy ：删除条件变量属性块
  
### 函数原型：
  
```c  
#include <pthread.h>  
int  pthread_condattr_ destroy  
(  
	pthread_attr_t  *attr  
);  
```  
  
  
### 描述：
  
该接口删除条件变量属性对象，属性对象会变为未初始状态。  
  
### 参数：  
  
- attr ： 指向线程属性对象的指针（输出）。  
  
### 返回值：
  
成功返回0，失败返回相应的错误码。  
  
### 错误码：
  
- EINVAL：参数无效。  
- EINVAL attr是无效指针，或者attr指向的是未初始化的属性对象。  
  
### 备注：  
  
GJB 7714-2012错误码：EINVAL。  
  
### 样例：  
  
```c  
int main (int argc, char **argv)  
{  
	int ret;  
	pthread_condattr_t condattr;  
  
	ret = pthread_condattr_init(&condattr);  
	if(ret != 0)  
	{  
		perror("func pthread_condattr_init error\n");  
		return -1;  
	}  
  
	// ……  
  
	ret = pthread_condattr_destroy(&condattr);  /* 销毁条件变量属性*/  
	if(ret != 0)  
	{  
		perror("func pthread_condattr_destroy error\n");  
		return -1;  
	}  
	return 0;  
}  
```  
  
  
## pthread_ condattr_ setclock ：设置条件变量属性块的时钟属性
  
  
### 函数原型：
  
```c  
#include <pthread.h>  
int  pthread_ condattr_ setclock  
(  
	pthread_attr_t  *attr;  
	clockid_t  clock_id  
);  
```  
  
### 描述：
  
该接口用于设置条件变量属性块的计时时钟属性。  
  
### 参数：  
  
- attr ： 指向条件变量属性对象的指针。  
  
- clock_id : 计时时钟。  
  
|宏定义|宏值|解释|  
|---|---|---|  
|CLOCK_REALTIME|0|实时时钟源（唯一支持）|  
  
### 返回值：
  
成功返回0，失败返回相应的错误码。  
  
### 错误码：
  
- EINVAL：参数无效。  
- EINVAL attr是无效指针，或者attr指向的是未初始化的属性对象，或者clock_id的取值不合法。  
  
### 备注：  
  
GJB 7714-2012错误码：EINVAL。  
  
### 样例：  
  
```c  
int main (int argc, char **argv)  
{  
	int ret;  
	pthread_condattr_t condattr;  
  
	ret = pthread_condattr_init(&condattr);  
	if(ret != 0)  
	{  
		perror("func pthread_condattr_init error\n");  
		return -1;  
	}  
  
	// ……  
  
	ret = pthread_condattr_setclock(&condattr, CLOCK_REALTIME);  /* 设置条件变量时钟属性*/  
	if(ret != 0)  
	{  
		perror("func pthread_condattr_setclock error\n");  
		return -1;  
	}  
  
	// ……  
  
	return 0;  
}  
```  
  
  
## pthread_condattr_ getclock ：获取条件变量属性块的时钟属性
  
  
### 函数原型：
  
```c  
#include <pthread.h>  
int  pthread_condattr_getclock  
(  
	pthread_attr_t  *attr;  
	clockid_t  *clock_id  
);  
```  
  
  
### 描述：
  
该接口用来获取条件变量属性块的计时时钟属性。  
  
### 参数：  
  
- attr ：指向条件变量属性对象的指针。  
  
- clock_id : 计时时钟。  
  
### 返回值：
  
成功返回0，失败返回相应的错误码。  
  
### 错误码：
  
- EINVAL：参数无效。  
- EINVAL attr或clock_id是无效指针，或者attr指向的是未初始化的属性对象。  
  
### 备注：  
  
GJB 7714-2012错误码：EINVAL。  
  
### 样例：  
  
```c  
int main (int argc, char **argv)  
{  
	int ret;  
	pthread_condattr_t condattr;   
	clockid_t clockid;  
  
	ret = pthread_condattr_init(&condattr);  
	if(ret != 0)  
	{  
		perror("func pthread_condattr_init error\n");  
		return -1;  
	}  
  
	// ……  
  
	ret = pthread_condattr_getclock(&condattr, &clockid); /* 获取条件变量时钟属性*/  
	if(ret != 0)  
	{  
		perror("func pthread_condattr_getclock error\n");  
		return -1;  
	}  
  
	// ……  
  
	return 0;  
}  
```  
  
  
## pthread_ cond_init ：初始化条件变量
  
  
### 函数原型：
  
```c  
#include <pthread.h>  
int  pthread_cond_init  
(  
	pthread_cond_t  *pcond,   
	const pthread_condattr_t  *pcondattr  
);  
```  
  
### 描述：
  
该接口以pcondattr引用的属性对象初始化pcond指定的条件变量。如果pcondattr为空，则使用缺省的条件变量属性。  
  
### 参数：  
  
- pcond ：存放条件变量对象的指针。  
  
- pcondattr ：指向条件变量属性对象的指针。  
  
### 返回值：
  
成功返回0，失败返回相应的错误码。  
  
### 错误码：
  
- EAGAIN ：系统资源不足。  
- EINVAL ：参数无效。  
  
### 备注：  
  
GJB 7714-2012错误码：
- EINVAL ：参数无效；  
- EAGAIN ：系统资源不足。  
  
### 样例：  
  
```c  
static pthread_mutex_t mutex;  
static 	pthread_cond_t cond;  
static pthread_t tid1, tid2;  
static void *Thread_ rece(void *arg)  
{  
	......  
	return NULL;  
}  
static void *Thread_ send(void *arg)  
{  
	......  
  
	return NULL;  
}  
int main (int argc, char **argv)  
{  
	int ret;  
	pthread_condattr_t condattr;  
  
	ret = pthread_condattr_init(&condattr); /* 初始化条件变量属性*/  
	if(ret != 0)  
	{  
		perror("func pthread_condattr_init error\n");  
		return -1;  
	}  
	ret = pthread_cond_init(&cond, &condattr); /* 初始化条件变量*/  
	if(ret != 0)  
	{  
		perror("func pthread_cond_init error\n");  
		return -1;  
	}  
	ret = pthread_create(&tid1, NULL, Thread_rece, NULL); /* 创建线程1*/  
	if(ret != 0)  
	{  
		perror("func pthread_create error");  
		return -1;  
	}  
  
	ret = pthread_create(&tid2, NULL, Thread_send, NULL); /* 创建线程2*/  
	if(ret != 0)  
	{  
		perror("func pthread_create error");  
		return -1;  
	}  
	ret = pthread_mutex_init(&mutex, NULL);  /* 创建互斥量*/  
	if(ret != 0)  
	{  
		perror("func pthread_mutex_init error\n");  
		return -1;  
	}  
	......  
  
	pthread_join(tid1, NULL);  
	pthread_join(tid2, NULL);  
	return 0;  
}  
```  
  
## pthread_cond_destroy ：删除条件变量
  
### 函数原型：
  
```c  
#include <pthread.h>  
int  pthread_ cond_destroy  
(  
	pthread_cond_t  *pcond  
);  
```  
  
### 描述：
  
该接口用于删除有 pcond 指定的条件变量，并释放相关的资源。  
  
### 参数：  
  
- pcond ： 指向条件变量对象的指针。  
  
### 返回值：
  
成功返回0，失败返回相应的错误码。  
  
### 错误码：
  
- EINVAL ：参数无效。  
  
### 备注：  
  
GJB 7714-2012错误码：
- EINVAL ：参数无效；  
- EBUSY ：对象忙。  
  
### 样例：  
  
```c  
int main (int argc, char **argv)  
{  
	int ret;  
	pthread_condattr_t condattr;  
  
	ret = pthread_condattr_init(&condattr);  /* 初始化条件变量属性*/  
	if(ret != 0)  
	{  
		perror("func pthread_condattr_init error\n");  
		return -1;  
	}  
  
	ret = pthread_cond_init(&cond, &condattr); /* 初始化条件变量*/  
	if(ret != 0)  
	{  
		perror("func pthread_cond_init error\n");  
		return -1;  
	}  
	// ……  
	ret = pthread_cond_destroy(&cond);   /* 销毁条件变量*/  
	if(ret != 0)  
	{  
		perror("func pthread_cond_destroy error\n");  
		return -1;  
	}  
	return 0;  
}  
```  
  
## pthread_ cond_signal ：激活条件变量
  
  
### 函数原型：
  
```c  
#include <pthread.h>  
int  pthread_cond_signal  
(  
	pthread_cond_t  *pcond  
);  
```  
  
  
### 描述：
  
该接口用来向等待条件变量的线程发送信号。  
  
### 参数：  
  
- pcond ： 指向条件变量对象的指针。  
  
### 返回值：
  
成功返回0，失败返回相应的错误码。  
  
### 错误码：
  
- EINVAL ：参数无效。  
- ERROR_KERNEL_HANDLE_NULL ：句柄出错。  
- ERROR_EVENT_TYPE ：事件类型出错。  
  
### 备注：  
  
GJB 7714-2012错误码：
- EINVAL ：参数无效。  
  
### 样例：  
  
```c  
#include <stdio.h>  
#include <string.h>  
#include <pthread.h>  
pthread_mutex_t sumlock = PTHREAD_MUTEX_INITIALIZER;  /* 互斥锁 */  
static	pthread_cond_t G_cond;  
int Gi_sum = 0;  
void *Thread_condsend (void *arg)  
{  
	int i,ret;  
	int num = (int)arg;  
	for (i = 0; i < 10; i++)  
	{  
	// …….  
        Gi_sum ++;  
		printf("t%d: read sum value = %d\n", num + 1, Gi_sum);  
		if (Gi_sum >= 10)  
		{  
	        ret = pthread_cond_signal(&G_cond);     /* 满足条件发出条件变量   */  
	        if(ret != 0)  
	        {  
	            printf("func pthread_cond_signal failed!\n");  
	            return (void*)-1;  
	        }  
		}  
	// ……  
		sleep(1);     /* 延时1s，以更好的观察线程调度情况*/   
	}  
	return (NULL);     
}  
void *Thread_condrecv (void *arg)  
{  
    int i,ret;  
    ret = pthread_mutex_lock(&sumlock);          /* 上锁       */  
    if(ret != 0)  
    {  
        printf("func pthread_mutex_lock failed!\n");  
        return (void*)-1;  
    }  
    for (i = 0;i < 2; i++)  
    {  
        while (Gi_sum < 10)  
        {  
            ret = pthread_cond_wait(&G_cond, &sumlock);      /* 等待条件变量                 */  
            if(ret != 0)  
            {  
                printf("func pthread_cond_wait failed!\n");  
                return (void*)-1;  
            }  
        }  
        Gi_sum = 0;  
        printf("t3: clear sum value\n");  
    }  
    ret = pthread_mutex_unlock(&sumlock);           /* 解锁                         */  
    if(ret != 0)  
    {  
        printf("func pthread_mutex_unlock failed!\n");  
        return (void*)-1;  
    }  
    return (NULL);  
}  
int main (int argc, char *argv[])  
{  
	// ……  
  
  
	for (i = 0; i < 2; i++)  
	{  
	    ret = pthread_create(&(tid[i]), NULL, Thread_condsend, (void *)i); /* 创建发出条件变量线程         */  
	    if (ret != 0)   
	    {  
	      printf("can't create thread !");  
	      return (-1);  
	    }  
	}  
  
	ret = pthread_create(&(tid[2]), NULL, Thread_condrecv, NULL);          /* 创建等待条件变量线程         */  
	if (ret != 0)  
	{  
		printf("can't create thread !");  
		return (-1);  
	}  
	// ……  
  ret = pthread_join(tid[i], NULL);     /* 等待线程结束，并回收资源     */  
        if(ret != 0)  
        {  
            printf("func pthread_join failed!\n");  
            return -1;  
        }  
	}  
	return (0);  
}  
```  
  
  
## pthread_cond_broadcast ：释放所有等待条件变量线程
  
### 函数原型：
  
```c  
#include <pthread.h>  
int  pthread_cond_broadcast  
(  
	pthread_cond_t  *pcond  
);  
```  
  
### 描述：
  
该接口用来释放所有等待该条件变量的线程。  
  
### 参数：  
  
- pcond ： 指向条件变量对象的指针。  
  
### 返回值：
  
成功返回0，失败返回相应的错误码。  
  
### 错误码：
  
- EINVAL ：参数无效。  
- EAGAIN ：系统资源不足  
- ERROR_KERNEL_HANDLE_NULL ：句柄出错。  
- ERROR_EVENT_TYPE ：事件类型出错。  
  
### 备注：  
  
GJB 7714-2012错误码：
- EINVAL ：参数无效。  
  
### 样例：  
  
```c  
static pthread_mutex_t mutex;  
static 	pthread_cond_t cond;  
static pthread_t tid1, tid2;  
static void * Thread_condsend (void *arg)  
{  
	int ret;  
	......  
	ret = pthread_mutex_lock(&mutex);    /* 上锁*/  
	if(ret == 0)  
	{  
		ret = pthread_cond_broadcast(&cond); /*向所有条件变量发送信号*/  
		if(ret != 0)  
		{  
			perror("fun pthread_cond_signal error\n");  
		}  
  
		// ……  
  
		pthread_mutex_unlock(&mutex);   /* 解锁*/  
	}  
	......  
	return NULL;  
}  
```  
  
  
## pthread_cond_wait ：等待条件变量
  
### 函数原型：
  
```c  
#include <pthread.h>  
int  pthread_cond_wait  
(  
	pthread_cond_t *pcond,  
	pthread_mutex_t *pmutex  
);  
```  
  
  
### 描述：
  
该接口用来使当前线程等待指定条件变量。  
  
### 参数：  
  
- pcond ：条件变量控制块。  
  
- pmutex ：互斥信号量。  
  
### 返回值：
  
成功返回0，失败返回相应的错误码。  
  
### 错误码：
  
- EINVAL ：参数无效。  
  
### 备注：  
  
GJB 7714-2012错误码：
- EINVAL ：参数无效。  
- EPRERM ：权限不足。  
  
### 样例：  
  
```c  
static pthread_mutex_t mutex;  
static 	pthread_cond_t cond;  
static pthread_t tid1, tid2;  
static void * Thread_condsend (void *arg)  
{  
	int ret;  
	......  
	ret = pthread_mutex_lock(&mutex);      /* 上锁*/  
	if(ret == 0)  
	{  
		ret = pthread_cond_wait(&cond, &mutex); /* 等待条件变量*/  
		if(ret != 0)  
		{  
			perror("fun pthread_cond_wait error\n");  
		}  
  
		// ……  
  
		pthread_mutex_unlock(&mutex);    /* 解锁*/  
	}  
  
	......  
  
	return NULL;  
}  
```  
  
  
## pthread_cond_timedwait ：限时等待条件变量
  
  
### 函数原型：
  
```c  
#include <pthread.h>  
int  pthread_cond_timedwait  
(  
	pthread_cond_t  *pcond,  
	pthread_mutex_t  *pmutex,  
	const struct timespec  *abs_timeout  
);  
```  
  
  
### 描述：
  
该接口用来使当前线程限时（绝对时间）等待指定条件变量。  
  
### 参数：  
  
- pcond ：条件变量控制块。  
  
- pmutex ：互斥信号量。  
  
- abs_timeout ：超时时间。（绝对时间）。  
  
### 返回值：
  
成功返回0，失败返回相应的错误码。  
  
### 错误码：
  
- EINVAL ：参数无效。  
- ETIMEDOUT ：等待超时。  
  
### 备注：  
  
GJB 7714-2012错误码：
- EINVAL ：参数无效；  
- EPRERM ：权限不足；  
- ETIMEDOUT ：超时。  
  
- 注意：超时时间是绝对时间, 即一个确定的历史时间。  
  
### 样例：  
  
```c  
static pthread_mutex_t mutex;  
static 	pthread_cond_t cond;  
static pthread_t tid1, tid2;  
static void * Thread_condsend (void *arg)  
{  
	int ret;  
	......  
struct timespec ts;  
ts.tv_nsec = 0;  
   ts.tv_sec = time(NULL) + num;  
	ret = pthread_mutex_lock(&mutex);    /* 上锁*/  
	if(ret == 0)  
	{  
		ret = pthread_cond_wait(&cond, &mutex，&ts);   /* 限时等待条件变量*/  
		if(ret != 0)  
		{  
			perror("fun pthread_cond_wait error\n");  
		}  
		// ……  
		pthread_mutex_unlock(&mutex);     /* 解锁*/  
	}  
	......  
	return NULL;  
}  
```  
  
  
## pthread_cond_show ：显示条件变量控制块信息
  
  
### 函数原型：
  
```c  
#include <pthread.h>  
int  pthread_cond_show  
(  
	pthread_cond_t  *pcond,  
	int  level  
);  
```  
  
  
### 描述：
  
该接口用来显示指定条件变量控制块的信息。  
  
### 参数：  
  
- pcond ：条件变量控制块。  
  
- level ：显示等级。（1 或 0）。  
  
### 返回值：
  
成功返回0，失败返回相应的错误码。  
  
### 错误码：
  
- EINVAL ：参数无效。  
- ECALLEDINISR ：系统处于中断中。  
  
### 备注：  
  
GJB 7714-2012错误码：
- EINVAL ：参数无效；  
- EMNOTINITED ：模块尚未初始化；  
- ECALLEDINISR ：接口不能在中断上下文中调用。  
  
### 样例：  
  
```c  
static pthread_mutex_t mutex;  
static 	pthread_cond_t cond;  
static pthread_t tid1, tid2;  
static void * Thread_condsend (void *arg)  
{  
	int ret;  
	......  
	ret = pthread_cond_show(&cond, 1);     /* 显示条件变量控制块信息*/  
	if(ret != 0)  
	{  
		perror("fun pthread_cond_show error\n");  
	}  
  
	......  
  
	return NULL;  
}  
```  
  
  
## pthread_cond_getinfo ：获取条件变量控制块信息
  
  
### 函数原型：
  
```c  
#include <pthread.h>  
int  pthread_cond_getinfo  
(  
	pthread_cond_t  *pcond,  
	pthread_cond_info_t  * info  
);  
```  
  
  
### 描述：
  
该接口用来获取指定条件变量控制块的信息。  
  
### 参数：  
  
- pcond ：指向条件变量对象的指针。  
  
- info ：存放条件变量信息的指针。  
  
### 返回值：
  
成功返回0，失败返回相应的错误码。  
  
### 错误码：
  
- EINVAL ：参数无效。  
  
### 备注：  
  
GJB 7714-2012错误码：
- EINVAL ：参数无效；  
- EMNOTINITED ：模块尚未初始化；  
- ECALLEDINISR ：接口不能在中断上下文中调用。  
  
### 样例：  
  
```c  
static pthread_mutex_t mutex;  
static 	pthread_cond_t cond;  
static pthread_t tid1, tid2;  
static void * Thread_condsend (void *arg)  
{  
	int ret;  
	......  
	pthread_cond_info_t info;  
	ret = pthread_cond_getinfo(&cond, &info); /* 获取条件变量控制块信息*/  
	if(ret == 0)  
	{  
		printf("************************info********************\n");  
  
		printf("cond mutex	:%ld \n", info.mutex);  
		printf("cond cnt 	:%ld\n", info.cnt);  
		printf("cond signal :%ld\n", info.signal);  
  
		printf("************************end*********************\n");  
	}  
	......  
  
	return NULL;  
}  
```  
  
  
## pthread_condattr_setpshared ：设置条件变量属性块的共享属性
  
  
### 函数原型：
  
```c  
#include <pthread.h>  
int  pthread_condattr_setpshared  
(  
	pthread_condattr_t  *pcondattr,   
	int  ishare   
);  
```  
  
  
### 描述：
  
该接口用来设置一个条件变量属性控制块的共享属性。  
  
### 参数：  
  
- pcondattr ：条件变量属性。  
  
- ishare ：共享属性。  
  
|宏定义|宏值|解释|  
|---|---|---|  
|PTHREAD_PROCESS_SHARED|1|使条件变量在多个进程共享的内存空间中。|  
|PTHREAD_PROCESS_PRIVATE|0|该条件变量只能由和初始化该条件变量的任务在同一进程中的任务访问。|  
  
### 返回值：
  
成功返回0，失败返回相应的错误码。  
  
### 错误码：
  
- EINVAL：参数无效。  
  
### 备注：  
  
GJB 7714-2012无此函数接口。  
  
### 样例：  
  
```c  
int main (int argc, char **argv)  
{  
	int ret;  
	pthread_condattr_t condattr;  
  
	ret = pthread_condattr_init(&condattr);  /* 初始化条件变量属性*/  
	if(ret != 0)  
	{  
		perror("func pthread_condattr_init error\n");  
		return -1;  
	}  
  
	ret = pthread_condattr_setpshared(&condattr, 1); /* 设置条件变量的共享属性*/  
	if(ret != 0)  
	{  
		perror("func pthread_condattr_setpshared error\n");  
		return -1;  
	}  
  
	// ……  
  
	return 0;  
}  
```  
  
  
## pthread_condattr_getpshared ：获取条件变量属性块的共享属性
  
  
### 函数原型：
  
```c  
#include <pthread.h>  
int  pthread_condattr_getpshared  
(  
	const pthread_condattr_t  *pcondattr,  
	int  *pishare  
);  
```  
  
  
### 描述：
  
该接口用来获取一个条件变量属性控制块的共享属性。  
  
### 参数：  
  
- pcondattr ：条件变量属性。  
  
- piShared ：存放共享属性的指针。  
  
|宏定义|宏值|解释|  
|---|---|---|  
|PTHREAD_PROCESS_SHARED|1|使条件变量在多个进程共享的内存空间中。|  
|PTHREAD_PROCESS_PRIVATE|0|该条件变量只能由和初始化该条件变量的任务在同一进程中的任务访问。|  
  
### 返回值：
  
成功返回0，失败返回相应的错误码。  
  
### 错误码：
  
- EINVAL ：参数无效。  
  
### 备注：  
  
GJB 7714-2012无此函数接口。  
  
### 样例：  
  
```c  
int main (int argc, char **argv)  
{  
	int ret;  
	pthread_condattr_t condattr;   
	int share;  
  
	ret = pthread_condattr_init(&condattr);    /* 初始化条件变量属性*/  
	if(ret != 0)  
	{  
		perror("func pthread_condattr_init error\n");  
		return -1;  
	}  
	// ……  
	ret = pthread_condattr_getpshared(&condattr, &share); /* 获取条件变量共享属性*/  
	if(ret != 0)  
	{  
		perror("func pthread_condattr_getpshared error\n");  
		return -1;  
	}  
	// ……  
	return 0;  
}  
```  
  
## pthread_cond_reltimedwait_np ：限时等待条件变量
  
  
### 函数原型：
  
```c  
#include <pthread.h>  
int  pthread_cond_reltimedwait_np  
(  
	pthread_cond_t         *pcond,   
	pthread_mutex_t        *pmutex,  
	const struct timespec  *rel_timeout  
);  
```  
  
  
### 描述：
  
该接口用来使当前线程限时（相对时间）等待指定条件变量。  
  
### 参数：  
  
- pcond ：条件变量控制块。  
  
- pmutex ：互斥信号量。  
  
- rel_timeout ：超时时间。（相对时间）。  
  
### 返回值：
  
成功返回0，失败返回相应的错误码。  
  
### 错误码：
  
- EINVAL ：参数无效。  
- ETIMEDOUT ：等待超时。  
  
### 备注：  
  
GJB 7714-2012无此函数接口。  
  
- 注意：超时时间为相对时间。  
  
### 样例：  
  
```c  
static pthread_mutex_t mutex;  
static 	pthread_cond_t cond;  
static pthread_t tid1, tid2;  
static void * Thread_condsend (void *arg)  
{  
	int ret;  
	......  
	ret = pthread_mutex_lock(&mutex);                      /* 上锁*/  
	if(ret == 0)  
	{  
		struct timespec timeout;  
		timeout.tv_nsec = 1L;  
		timeout.tv_sec = 1;  
		ret = pthread_cond_timedwait(&cond, &mutex ,&timeout); /* 限时等待条件变量*/  
		if(ret != 0)  
		{  
			perror("fun pthread_cond_reltimedwait_np error\n");  
		}  
		// ……  
		pthread_mutex_unlock(&mutex);     /* 解锁*/  
	}  
	......  
	return NULL;  
}  
```  
  
  
# 读写锁  
  
  
## pthread_rwlockattr_init ：初始化读写锁属性块
  
  
### 函数原型：
  
```c  
int  pthread_rwlockattr_init  
(  
	pthread_rwlockattr_t  *prwlockattr  
);  
```  
  
  
### 描述：
  
该接口用于初始化一个读写锁属性prwlockattr。默认写优先，进程之间不共享。  
  
### 参数：   
  
- prwlockattr ：读写锁属性块句柄。  
  
### 返回值：
  
成功返回0；失败返回相应的错误码。  
  
### 错误码：
  
- EINVAL ：参数无效。  
- ERROE_NONE ：没有错误。  
  
### 备注：  
  
GJB 7714-2012无此接口。  
  
### 样例：  
  
```c  
#include <stdio.h>  
#include <pthread.h>  
static pthread_t G_tid1, G_tid2;  
static pthread_rwlock_t G_rwlock;  
......  
int main (int argc, char **argv)  
{  
	int ret ;  
	pthread_rwlockattr_t rwlockattr;  
  
	// ……  
  
	ret = pthread_rwlockattr_init(&rwlockattr);			/*  初始化一个读写锁属性块 */  
	if(ret != 0)  
	{  
		perror("func pthread_rwlockattr_init error\n");  
		return -1;  
	}  
  
	ret = pthread_rwlockattr_destroy(&rwlockattr);  
	if(ret != 0)  
	{  
		perror("func pthread_rwlockattr_destroy error\n");  
		return -1;  
	}  
	// ……  
	return 0;  
}  
```  
  
  
## pthread_rwlockattr_destroy ：销毁读写锁属性块
  
  
### 函数原型：
  
```c  
int  pthread_rwlockattr_destroy  
(  
	pthread_rwlockattr_t  *prwlockattr  
);  
```  
  
  
### 描述：
  
该接口用来销毁一个读写锁属性块。  
  
### 参数：   
  
- prwlockattr ：读写锁属性块句柄。  
  
### 返回值：
  
成功返回0；失败返回相应的错误码。  
  
### 错误码： 
  
- EINVAL ：参数无效。  
- ERROE_NONE ：没有错误。  
  
### 备注：  
  
GJB 7714-2012无此接口。  
  
### 样例：  
  
```c  
#include <stdio.h>  
#include <pthread.h>  
static pthread_spinlock_t G_spinlock;  
static pthread_t G_tid1,G_tid2;  
// ……  
int main (int argc, char **argv)  
{  
    int ret;  
   pthread_rwlockattr_t rwlockattr;  
   // ……  
   ret = pthread_rwlockattr_destroy(&rwlockattr);  
if(ret != 0)  
{  
		perror("func pthread_rwlockattr_destroy error\n");  
		return -1;  
	}  
	// ……  
    return  (0);  
}  
```  
  
  
## pthread_rwlockattr_setpshared ：设置读写锁的共享属性
  
  
### 函数原型：
  
```c  
int  pthread_rwlockattr_setpshared  
(  
	pthread_rwlockattr_t *prwlockattr,   
	int  pshared  
);  
```  
  
### 描述：
  
该接口用来设置指定读写锁属性块的共享属性，是否进程共享。  
  
### 参数：   
  
- prwlockattr ：读写锁属性块句柄。  
  
- pshared ：共享属性。  
  
|宏定义|宏值|解释|  
|---|---|---|  
|PTHREAD_PROCESS_SHARED|1|使读写锁在多个进程共享的内存空间中。|  
|PTHREAD_PROCESS_PRIVATE|0|该读写锁只能由和初始化该条件变量的务在同一进程中的任务访问。|  
  
### 返回值：
  
成功返回0；失败返回相应的错误码。  
  
### 错误码： 
  
- EINVAL ：参数无效。  
- ERROE_NONE ：没有错误。  
  
### 样例：  
  
```c  
#include <stdio.h>  
#include <pthread.h>  
static pthread_t G_tid1, G_tid2;  
static pthread_rwlock_t G_rwlock;  
// ……  
int main (int argc, char **argv)  
{  
	int ret ;  
	int shared;  
	// ……  
	ret = pthread_rwlockattr_setpshared(&rwlockattr, 0); /*设置一个读写锁属性块是否进程共享*/  
	if(ret != 0)  
	{  
		perror("func pthread_rwlockattr_setpshared error\n");  
		return -1;  
	}  
	// ……  
  
	return 0;  
}  
```  
  
  
## pthread_rwlockattr_getpshared ：互斥读写锁的共享属性
  
  
### 函数原型：
  
```c  
int  pthread_rwlockattr_getpshared  
(  
	const pthread_rwlockattr_t *prwlockattr,   
	int  *pshared  
);  
```  
  
  
### 描述：
  
该接口用来获取指定读写锁属性块的共享属性。  
  
### 参数：   
  
- prwlockattr ：读写锁属性块句柄。  
  
- pshared ：共享属性。  
  
|宏定义|宏值|解释|  
|---|---|---|  
|PTHREAD_PROCESS_SHARED|1|使读写锁在多个进程共享的内存空间中。|  
|PTHREAD_PROCESS_PRIVATE|0|该读写锁只能由和初始化该条件变量的任务在同一进程中的任务访问。|  
  
### 返回值：
  
成功返回0；失败返回相应的错误码。  
  
### 错误码： 
  
- EINVAL ：参数无效。  
- ERROE_NONE ：没有错误。  
  
### 备注：  
  
GJB 7714-2012无此接口。  
  
### 样例：  
  
```c  
#include <stdio.h>  
#include <pthread.h>  
static pthread_t G_tid1, G_tid2;  
static pthread_rwlock_t G_rwlock;  
// ……  
int main (int argc, char **argv)  
{  
	int ret ;  
	int shared;  
	// ……  
	ret = pthread_rwlockattr_getpshared(&rwlockattr, &shared);		  /*获取一个读写锁属性块是否进程共享*/  
	if(ret == 0)  
	{  
		printf("rwlockattr shared :%d\n", shared);  
	}  
  
	// ……  
	return 0;  
}  
```  
  
  
## pthread_rwlockattr_setkind_np ：设置读写锁的读写优先权
  
  
### 函数原型：
  
```c  
int  pthread_rwlockattr_setkind_np  
(  
	pthread_rwlockattr_t *prwlockattr,  
	int pref  
);  
```  
  
  
### 描述：
  
该接口用来设置指定读写锁属性块的读写优先权。（该锁只能在读锁定时体现其设置的优先级，在写锁定时默认使用等待线程自身的优先级以抢占的方式进行调度）  
  
### 参数：   
  
- prwlockattr ：读写锁属性块句柄。  
  
- pref ：优先权选项。  
  
|宏定义|解释|  
|---|---|  
|PTHREAD_RWLOCK_PREFER_READER_NP|读优先|  
|PTHREAD_RWLOCK_PREFER_WRITER_NP|写优先|  
  
### 返回值：
  
成功返回0；失败返回相应的错误码。  
  
### 错误码：
  
- EINVAL ：参数无效。  
- ERROE_NONE ：没有错误。  
  
### 备注：  
  
GJB 7714-2012无此接口。  
  
### 样例：  
  
```c  
#include <stdio.h>  
#include <pthread.h>  
static pthread_t G_tid1, G_tid2, G_tid3;  
static pthread_rwlock_t G_rwlock;  
// ……  
int main (int argc, char **argv)  
{  
	int ret ;  
	int kind;  
	pthread_rwlockattr_t rwlockattr;  
	// ……  
	/*  
	 * 设置一个读写锁属性块读写优先权  
	*/  
	ret = pthread_rwlockattr_setkind_np(&rwlockattr, PTHREAD_RWLOCK_PREFER_WRITER_NP);  
	if(ret != 0)  
	{  
		perror("func pthread_rwlockattr_setkind_np error\n");  
	}  
  
	// ……  
	return 0;  
}  
```  
  
  
## pthread_rwlockattr_getkind_np ：获取读写锁的读写优先权
  
  
### 函数原型：
  
```c  
int	 pthread_rwlockattr_getkind_np  
(  
	const pthread_rwlockattr_t *prwlockattr,  
	int *pref  
)  
```  
  
  
### 描述：
  
该接口用来获取指定读写锁属性块的读写优先权。  
  
### 参数：   
  
- prwlockattr ：读写锁属性块句柄。  
  
- pref ：优先权选项。  
  
|宏定义|解释|  
|---|---|  
|PTHREAD_RWLOCK_PREFER_READER_NP|读优先|  
|PTHREAD_RWLOCK_PREFER_WRITER_NP|写优先|  
  
### 返回值：
  
成功返回0；失败返回相应的错误码。  
  
### 错误码： 
  
- EINVAL ：参数无效。  
- ERROE_NONE ：没有错误。  
  
### 备注：  
  
GJB 7714-2012无此接口。  
  
### 样例：  
  
```c  
#include <stdio.h>  
#include <pthread.h>  
static pthread_t G_tid1, G_tid2, G_tid3;  
static pthread_rwlock_t G_rwlock;  
// ……  
int main (int argc, char **argv)  
{  
	int ret ;  
	int kind;  
	pthread_rwlockattr_t rwlockattr;  
	// ……  
	/*  
 	* 设置一个读写锁属性块读写优先权  
	*/  
	ret = pthread_rwlockattr_setkind_np(&rwlockattr, PTHREAD_RWLOCK_PREFER_WRITER_NP);  
	if(ret != 0)  
	{  
		perror("func pthread_rwlockattr_setkind_np error\n");  
	}  
  
	ret = pthread_rwlockattr_getkind_np(&rwlockattr, &kind);  /*获取一个读写锁属性块读写优先权*/  
	if(ret == 0)  
	{  
		printf("rwlockattr kind :%d\n", kind);  
	}  
	// ……  
	return 0;  
}  
```  
  
  
## pthread_rwlock_init() ：初始化读写锁对象
  
  
### 函数原型：
  
```c  
int  pthread_rwlock_init  
(  
	pthread_rwlock_t *prwlock,  
	const pthread_rwlockattr_t * prwlockattr  
)  
```  
  
  
### 描述：
  
该接口为 prwlock指导的读写锁分配资源，并把它初始化为未锁定的状态，且设为prwlockattr引用的读写锁属性对象的属性。若 prwlockattr 为空，则使用默认属性。  
  
### 参数：  
  
- prwlock : 存放读写锁对象的指针。  
  
- prwlockattr : 指向读写锁属性对象的指针。  
  
### 返回值：
  
成功返回 0；失败返回相应的错误码。  
  
### 错误码：
  
- EINVAL ：参数有效。  
- ENOSPC ：非法操作。  
- ERROE_NONE ：没有错误。  
  
### 备注：  
  
GJB 7714-2012错误码为：  
- EINVAL ：参数无效。  
- EBUSY ：设备资源忙。  
  
### 样例：  
  
```c  
#include <stdio.h>  
#include <pthread.h>  
static pthread_t G_tid1, G_tid2;  
static pthread_rwlock_t G_rwlock;  
// ……  
int main (int argc, char **argv)  
{  
	int ret ;  
	pthread_rwlockattr_t rwlockattr;  
// ……  
  
	ret = pthread_rwlock_init(&G_rwlock, &rwlockattr);		/*  创建读写锁          */  
	if(ret != 0)  
	{  
		perror("func pthread_rwlock_init error\n");  
		return -1;  
	}  
  
	// ……  
	return 0;  
}  
```  
  
  
## pthread_rwlock_destroy() ：删除读写锁对象
  
  
### 函数原型：
  
```c  
int  pthread_rwlock_destroy   
(  
	pthread_rwlock_t  *prwlock  
);  
```  
  
### 描述： 
  
该接口用于删除prwlock引用的读写锁对象并释放锁使用的资源。对象删除后不可以引用。需要通过pthread_rwlock_init()重新初始化后才可以继续引用。  
  
### 参数：  
  
- prwlock ：指向读写锁对象的指针。  
  
### 返回值：
  
成功返回0；失败返回相应的错误码。  
  
### 错误码： 
  
- EINVAL ：参数无效。  
- ERROE_NONE ：没有错误。  
  
### 备注：  
  
GJB 7714-2012错误码为：  
- EINVAL ：参数无效。  
- EBUSY ：设备资源忙。  
  
### 样例：  
  
```c  
#include <stdio.h>  
#include <pthread.h>  
static pthread_t G_tid1, G_tid2;  
static pthread_rwlock_t G_rwlock;  
// ……  
int main (int argc, char **argv)  
{  
	int ret ;  
	// ……  
  
	ret = pthread_rwlock_destroy(&G_rwlock);			/*  销毁读写锁               */  
	if(ret != 0)  
	{  
		perror("func pthread_rwlock_destroy error\n");  
		return -1;  
	}  
	return 0;  
}  
```  
  
  
## pthread_rwlock_rdlock() ：获取读写锁用于读操作
  
  
### 函数原型：
  
```c  
int  pthread_rwlock_rdlock   
(  
	pthread_rwlock_t  *prwlock  
);  
```  
  
  
### 描述： 
  
该接口用于向prwlock引用的读写锁申请读锁定。如果没有别的任务（写者）持有读写锁，调用任务就会获取读锁。否则，任务将一直阻塞到它能获取锁为止。  
  
### 参数：  
  
- prwlock ：指向读写锁对象的指针。  
  
### 返回值：
  
成功返回0；失败返回相应的错误码。  
  
### 错误码：
  
- EINVAL ：参数无效。  
- ERROE_NONE ：没有错误。  
  
### 备注：  
  
GJB 7714-2012错误码为：  
- EINVAL ：参数无效。  
- EBUSY ：设备资源忙。  
  
### 样例：  
  
```c  
#include <stdio.h>  
#include <pthread.h>  
static pthread_t G_tid1, G_tid2;  
static pthread_rwlock_t G_rwlock;  
static void *Thread1(void *arg)  
{  
	int ret = 0;  
	ret = pthread_rwlock_rdlock(&G_rwlock);  
  
	if(ret != 0)  
	{  
		perror("func pthread_rwlock_rdlock error\n");  
		return (void *)-1;  
	}  
	// ……  
	return NULL;  
}  
```  
  
  
## pthread_rwlock_tryrdlock() ：尝试获取读写锁用于读操作
  
  
### 函数原型：
  
```c  
int  pthread_rwlock_tryrdlock   
(  
	pthread_rwlock_t  *prwlock  
);  
```  
  
  
### 描述： 
  
尝试锁定读写锁用于读操作。当其他任务持有读写锁用于写操作情况下时会失败。  
  
### 参数：  
  
- prwlock ：指向读写锁对象的指针。  
  
### 返回值：
  
成功返回0；失败返回相应的错误码。  
  
### 错误码：
  
- EINVAL ：参数无效。  
- EBUSY ：设备资源忙。  
- EDEADLK ：检测到死锁。  
- ERROE_NONE ：没有错误。  
  
### 备注：  
  
GJB 7714-2012错误码为：  
- EINVAL ：参数无效。  
- EBUSY ：设备资源忙。  
  
### 样例：  
  
```c  
#include <stdio.h>  
#include <pthread.h>  
static pthread_t G_tid1, G_tid2;  
static pthread_rwlock_t G_rwlock;  
// ……  
static void *Thread2(void *arg)  
{  
	int ret = 0;  
  
	ret = pthread_rwlock_tryrdlock(&G_rwlock);  
	if(ret != 0)  
	{  
		perror(" func pthread_rwlock_tryrdlock error\n");  
	}  
	else  
	{  
		printf(" func pthread_rwlock_tryrdlock success\n");  
		pthread_rwlock_unlock(&G_rwlock);  
	}  
	return NULL;  
}  
```  
  
## pthread_rwlock_wrlock() ：获取读写锁用于写操作
  
  
### 函数原型：
  
```c  
int  pthread_rwlock_wrlock   
(  
	pthread_rwlock_t  *prwlock  
);  
```  
  
  
### 描述：
  
该接口用于向prwlock引用的读写锁申请写锁定。如果没有别的任务（读者或写者）持有读写锁prwlock，调用任务就会获取写锁，否则，任务将一直阻塞到它能获取锁为止。  
当同时有同优先级读者任务和写者任务阻塞在锁上时，任务将优先级让写者任务获取该锁。  
如果信号发送给等待读写锁用于写的任务，从信号处理函数返回之后，任务就像没有被打断一样继续等待读写锁。  
  
### 参数：  
  
- prwlock ：指向读写锁对象的指针。  
  
### 返回值：
  
成功返回0，失败返回相应的错误码。  
  
### 错误码： 
  
- EINVAL ：参数无效。  
- ERROE_NONE ：没有错误。  
  
### 备注：  
  
无  
  
### 样例：  
  
```c  
#include <stdio.h>  
#include <pthread.h>  
static pthread_t G_tid1, G_tid2;  
static pthread_rwlock_t G_rwlock;  
static void *Thread1(void *arg)  
{  
	int ret = 0;  
	ret = pthread_rwlock_wrlock(&G_rwlock);  
	if(ret != 0)  
	{  
		perror("func pthread_wrlock_wrlock error\n");  
		return (void*)-1;  
	}  
	// ……  
	return NULL;  
}  
```  
  
  
## pthread_rwlock_reltimedrdlock_np ：限时等待锁定读写锁的读锁
  
  
### 函数原型：
  
```c  
int  pthread_rwlock_reltimedrdlock_np  
(  
	pthread_rwlock_t *prwlock,  
	const struct timespec *rel_timeout  
);  
```  
  
  
### 描述：
  
该接口用来限时等待指定的读写锁的读锁定（相对超时时间）。  
  
### 参数：   
  
- prwlock ：读写锁句柄。  
  
- abs_timeout ：相对超时时间。  
  
### 返回值：
  
成功返回0；失败返回相应的错误码。  
  
### 错误码： 
  
- EINVAL ：参数无效。  
- ETIMEDOUT ：等待超时。  
- EDEADLK ：检测到死锁。  
  
### 备注：  
  
GJB 7714-2012无此接口。  
  
### 样例：  
  
```c  
#include <stdio.h>  
#include <pthread.h>  
static pthread_t G_tid1, G_tid2;  
static pthread_rwlock_t G_rwlock;  
// ……  
static void *Thread2(void *arg)  
{  
	int ret = 0;  
	struct timespec outtime;  
  
	outtime.tv_nsec = 0;  
	outtime.tv_sec = 1;  
  
	ret = pthread_rwlock_reltimedrdlock_np(&G_rwlock, &outtime);	/*等待一个读写锁可读(带有超时的阻塞)*/  
	if(ret != 0)  
	{  
		perror("func pthread_rwlock_reltimedrdlock_np error\n");  
	}  
	printf("this is Thread2\n");  
	pthread_rwlock_unlock(&G_rwlock);  
  
	return NULL;  
}  
```  
  
  
## pthread_rwlock_trywrlock() ：尝试获取读写锁用于写操作
  
  
### 函数原型：
  
```c  
int  pthread_rwlock_trywrlock   
(  
	pthread_rwlock_t  *prwlock  
);  
```  
  
  
### 描述：
  
尝试锁定读写锁的写操作，该接口和pthread_rwlock_wrlock()接口一样申请写锁存，除了有其他任务持有读写锁用于写操作或读操作情况下时会失败。  
  
### 参数：  
  
- prwlock ：指向读写锁对象的指针。  
  
### 返回值：
  
成功返回0；失败返回相应的错误码。  
  
### 错误码： 
  
- EINVAL ：参数无效。  
- EBUSY ：设备资源忙。  
- EDEADLK ：检测到死锁。  
- ERROE_NONE ：没有错误。  
  
### 备注：  
  
GJB 7714-2012错误码为：  
- EINVAL rwlock ：是一个无效的指针；  
- EINVAL rwlock ：指定的读写锁不存在；  
- EBUSY ：因为读写锁已经被锁定用于读操作或写操作，所以无法获取锁用于写。  
  
### 样例：  
  
```c  
#include <stdio.h>  
#include <pthread.h>  
static pthread_t G_tid1, G_tid2;  
static pthread_rwlock_t G_rwlock;  
..….  
static void *Thread2(void *arg)  
{  
	int ret = 0;  
  
	ret = pthread_rwlock_trywrlock(&G_rwlock);  
	if(ret != 0)  
	{  
		perror("func pthread_rwlock_trywrlock error\n");  
	}  
	else  
	{  
		printf("func pthread_rwlock_trywrlock ture\n");  
		pthread_rwlock_unlock(&G_rwlock);  
	}  
  
	return NULL;  
}  
```  
  
  
## pthread_rwlock_unlock() ：释放读写锁
  
  
### 函数原型：
  
```c  
int  pthread_rwlock_unlock   
(  
	pthread_rwlock_t  *prwlock  
);  
```  
  
  
### 描述： 
  
该接口会释放prlock引用的读写锁上的一个锁。如果调用该接口释放了读写锁对象的一个读锁且读写锁仍有其他读写，那么读写锁对象保持读锁定状态。如果该接口释放了最后一个读锁，读写锁对象将进入解锁状态。如果调用给接口释放了一个写锁，读写锁对象也将进入解锁状态。  
  
### 参数：  
  
- prwlock ：指向对写锁对象的指针。  
  
### 返回值：
  
成功返回0；失败返回相应的错误码。  
  
### 错误码： 
  
- EINVAL ：参数无效。  
- EPERM ：权限不足（不是锁定者）。  
- ERROE_NONE ：没有错误。  
  
### 备注：  
  
GJB 7714-2012错误码为：  
- EINVAL ：参数无效。  
  
### 样例：  
  
```c  
#include <stdio.h>  
#include <pthread.h>  
static pthread_t G_tid1, G_tid2;  
static pthread_rwlock_t G_rwlock;  
static void *Thread1(void *arg)  
{  
	int ret = 0;  
	// ……  
	ret = pthread_rwlock_unlock(&G_rwlock);  
 if(ret != 0)  
	{  
		perror("func pthread_rwlock_unlock error\n");  
		return (void *)-1;  
	}  
  
	return NULL;  
}  
```  
  
  
## pthread_rwlock_timedrdlock() ：限时等待获取读写锁用于读操作
  
  
### 函数原型：
  
```c  
int  pthread_rwlock_timedrdlock   
(  
	pthread_rwlock_t *prwlock,  
	const struct timespec *abs_timeout  
);  
```  
  
  
### 描述： 
  
该接口用于在指定的时间内获取prwlock引用的读写锁申请读操作。如果在abs_timeout引用指定的时间中获取不到读锁时失败。  
该接口像pthread_rwlock_rdlock()一样向rwlock引用的读写锁申请读锁定。然而，如果不等待其他任务解锁就无法获得锁的话，当指定的超时过期时，等待就会终止。超时由CLOCK_REALTIME时钟度量。当abs_tyimeout指定的绝对时间过去时，或在调用接口时abs_timeout指定的绝对时间已经过去，称为超时过期。  
如果锁可以立即获取，接口不会以超时失败。如果锁可以立即获取，abs_timeout参数的有效性也不需要检查。如果导致执行信号处理函数的信号发送给因调用pthread_rwlock_timedrdlock()而阻塞在读写锁上的任务，那么从信号处理函数返回之后，任务就像没有被打断过一样继续等待读写锁。  
  
### 参数：  
  
- prwlock ：指向读写锁对象的指针。  
  
- abs_timeout ：绝对时间点。  
  
### 返回值：
  
成功返回0；失败返回相应的错误码。  
  
### 错误码： 
  
- EINVAL ：参数无效。  
- ETIMEDOUT ：等待超时。  
- EDEADLK ：检测到死锁。  
- ERROE_NONE ：没有错误。  
  
### 备注：  
  
GJB 7714-2012错误码为：  
- EINVAL ：参数无效。  
- ETIMEDOUT ：操作超时。  
- EAGAIN ：系统资源不足。  
  
### 样例：  
  
```c  
#include <stdio.h>  
#include <pthread.h>  
static pthread_t G_tid1, G_tid2;  
static pthread_rwlock_t G_rwlock;  
// ……  
static void *Thread2(void *arg)  
{  
	int ret = 0;  
	struct timespec outtime;  
  
	outtime.tv_nsec = 0;  
	outtime.tv_sec = time(NULL) + 2;  
  
	ret = pthread_rwlock_timedrdlock(&G_rwlock, &outtime);			/*等待一个读写锁可读(带有超时的阻塞)*/  
	if(ret == 0)  
	{  
		perror("the timedrdlock\n");  
		return (void *)-1;  
	}  
	printf("this is Thread2\n");  
  
	pthread_rwlock_unlock(&G_rwlock);  
  
	return NULL;  
}  
```  
  
  
## pthread_rwlock_timedwrlock() ：限时等待获取读写锁用于写操作
  
  
### 函数原型：
  
```c  
int  pthread_rwlock_timedwrlock   
(  
	pthread_rwlock_t *prwlock,  
	const struct timespec *abs_timeout  
);  
```  
  
  
### 描述： 
  
该接口像pthraed_rwlock_wrlock()一样向rwlock引用的读写锁申请读锁定。然而，如果不等待其他任务解锁就无法获取锁的话，当指定的超时过期时，等待就会终止。超时由CLOCK_REALTIME时钟度量。当abs_timeout指定的绝对时间过去时，或在调用接口时abs_timeout指定的绝对时间已经过去，称为超时过期。  
  
### 参数：  
  
- prwlock ：指向读写锁对象的指针。  
  
- abs_timeout ：绝对时间点。  
  
### 返回值：
  
成功返回0；失败返回相应的错误码。  
  
### 错误码： 
  
- EINVAL ：参数无效。  
- ETIMEDOUT ：等待超时。  
- EDEADLK ：检测到死锁。  
- ERROE_NONE ：没有错误。  
  
### 备注：  
  
GJB 7714-2012错误码为：  
- EINVAL ：参数无效。  
- ETIMEDOUT ：等待超时。  
  
### 样例：  
  
```c  
#include <stdio.h>  
#include <pthread.h>  
static pthread_t G_tid1, G_tid2;  
static pthread_rwlock_t G_rwlock;  
// ……  
static void *Thread2(void *arg)  
{  
  
	int ret = 0;  
	struct timespec outtime;  
  
	outtime.tv_nsec = 0;  
	outtime.tv_sec = time(NULL) + 2;  
  
	ret = pthread_rwlock_timedwrlock(&G_rwlock, &outtime);			/*等待一个读写锁可读(带有超时的阻塞)*/  
	if(ret != 0)  
	{  
		perror("the pthread_rwlock_timedwrlock error\n");  
		return (void *)-1;  
	}  
	printf("this is Thread2\n");  
  
	pthread_rwlock_unlock(&G_rwlock);  
	return NULL;  
}  
```  
  
## pthread_rwlock_reltimedwrlock_np ：限时等待锁定读写锁的写锁
  
  
### 函数原型：
  
```c  
int   pthread_rwlock_reltimedwrlock_np  
(  
	pthread_rwlock_t *prwlock,  
	const struct timespec *rel_timeout  
);  
```  
  
  
### 描述：
  
该接口用来限时等待指定的读写锁的读锁定（相对超时时间）。  
  
### 参数：   
  
- prwlock ：读写锁句柄。  
  
- abs_timeout ：相对超时时间。  
  
### 返回值：
  
成功返回0；失败返回相应的错误码。  
  
### 错误码： 
  
- EINVAL ：参数无效。  
- ETIMEDOUT ：等待超时。  
- EDEADLK ：检测到死锁。  
- ERROE_NONE ：没有错误。  
  
### 备注：  
  
GJB 7714-2012无此接口。  
  
### 样例：  
  
```c  
#include <stdio.h>  
#include <pthread.h>  
static pthread_t G_tid1, G_tid2;  
static pthread_rwlock_t G_rwlock;  
// ……  
static void *Thread2(void *arg)  
{  
	int ret = 0;  
	struct timespec outtime;  
  
	outtime.tv_nsec = 0;  
	outtime.tv_sec = 1;  
  
	ret = pthread_rwlock_reltimedwrlock_np(&G_rwlock, &outtime);   /*等待一个读写锁可读 (带有超时的阻塞)*/  
	if(ret != 0)  
	{  
		perror("the pthread_rwlock_reltimedwrlock_np error\n");  
	}  
	printf("this is Thread2\n");  
	pthread_rwlock_unlock(&G_rwlock);  
  
	return NULL;  
}  
```  
  
## pthread_rwlock_getinfo() ：获取读写锁的信息
  
  
### 函数原型：
  
```c  
int   pthread_rwlock_getinfo  
(  
	pthread_rwlock_t  *prwlock,   
	pthread_rwlock_info_t  *info  
);  
```  
  
### 描述： 
  
该接口用于获取指定的读写锁的概要信息。  
  
### 参数：  
  
- prwlock ：执行读写锁对象的指针。  
  
- info ：存放读写锁信息的指针。  
  
### 返回值：
  
成功返回0；失败返回相应的错误码。  
  
### 错误码： 
  
- EINVAL ：参数无效。  
- ERROE_NONE ：没有错误。  
  
### 备注：  
  
GJB 7714-2012错误码为：  
- EINVAL ：参数无效。  
- ECALLEDINISR ：系统处于中断中。  
- EMNOTINITED ：模块未初始化。  
  
### 样例：  
  
```c  
#include <stdio.h>  
#include <pthread.h>  
static pthread_t G_tid1, G_tid2;  
static pthread_rwlock_t G_rwlock;  
// ……  
static void *Thread2(void *arg)  
{  
	int ret = 0;  
	pthread_rwlock_info_t info;  
  
	ret=pthread_rwlock_getinfo(&G_rwlock,&info);		/*  获得读写锁信息          */  
	if(ret == 0)  
	{  
		printf("*************info*************\n");  
		printf("rpend :%u\n",info.rpend);  
		printf("wpend :%u\n",info.wpend);  
		printf("opcnt :%u\n",info.opcnt);  
		printf("**************end************\n");  
	}  
  
	return NULL;  
}  
```  
  
  
# 消息队列
  
  
## mq_create ：创新匿名消息队列
  
  
### 函数原型：
  
```c  
#include <mqueue.h>  
mqd_t	mq_create  
(  
	int  flags,   
	int  maxmsg,  
	int  msgsize,   
	int  waitqtype  
)  
```  
  
### 描述：
  
该接口用来创建一个匿名消息队列。  
  
### 参数：
  
- flag：消息队列对象的标志位。参数可以为：  
  
|flag|说明|  
|---|---|  
|O_NONBLOCK|该标志使得一个消息队列在队列为空时的读和队列塞满时的写不被阻塞。|  
|0||  
  
- Maxmsg ：消息队列的最大消息数。  
  
- msgsize ：每条消息的最大长度。  
  
- Waitqtype ：排队类型。取值有：  
  
|waitqtype|说明|  
|---|---|  
|PTHREAD_WAITQ_PRIO|优先级抢占|  
|PTHREAD_WAITQ_FIFO|先进先出|  
  
### 返回值：
  
成功返回相应的信号量句柄；失败返回-1，并置错误码。  
  
### 错误码： 
  
- EINVAL ：参数无效。  
- ECALLEDINISR ：系统处于中断。  
- ENOMEM ：内存不足。  
  
### 备注：  
  
GJB 7714-2012错误码为：  
- EINVAL ：参数无效。  
- ECALLEDINISR ：该接口不能在中段上下文中调用。  
- EMFILE ：系统中打开了过多的消息队列描述符。  
- ECALLEDINISR ：消息队列模块尚未初始化。  
- ENOMEM ：内存不足。  
  
### 样例：  
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <mqueue.h>  
#include <string.h>  
static mqd_t Gmqd;  
static pthread_t  Gtid1, Gtid2;  
int  main (int  argc, char  *argv[])  
{  
	int ret;  
  
 	// ……  
  
	/*  
	*创建一个未命名的消息队列     
	*/  
	Gmqd = mq_create(O_NONBLOCK, 20, 1024, PTHREAD_WAITQ_PRIO );          
	if(Gmqd == MQ_FAILED)  
	{  
		perror("func mq_create error\n");  
		return -1;  
	}  
  
	// ……  
  
	return (0);  
}  
```  
  
  
## mq_delete ：删除指定的消息队列
  
  
### 函数原型：
  
```c  
int	mq_delete  
(  
	mqd_t  mqd  
);  
```  
  
  
### 描述：
  
该接口用来删除mq_create创建的消息队列。  
  
### 参数： 
  
- mqd ：消息队列句柄。  
  
### 返回值：
  
成功返回0；失败返回-1，并置错误码。  
  
### 错误码： 
  
- EINVAL ：参数无效。  
- ECALLEDINISR ：系统处于中断。  
- EBADF ：句柄无效。  
  
### 备注：  
  
GJB 7714-2012错误码为：  
- ENOENT ：指定的消息队列不存在。  
- EINVAL ：所删除的消息队列不是mq_create()创建的未命名消息队列。  
- ECALLEDINISR ：该接口不能在中段上下文中调用。  
- EMNOTINITED ：消息队列模块尚未初始化。  
  
### 样例：  
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <mqueue.h>  
#include <string.h>  
static mqd_t Gmqd;  
static pthread_t  Gtid1, Gtid2;  
int  main (int  argc, char  *argv[])  
{  
	// ……  
  
	mq_delete(Gmqd);                  /*  删除消息队列Gmqd           */  
  
 	return (0);  
}  
```  
  
  
## mq_open ：打开一个命名消息队列
  
  
### 函数原型：
  
```c  
mqd_t            mq_open  
(  
	const char  *name,   
	int  flag,  
	...  
);  
```  
  
  
### 描述：
  
该接口用来打开一个命名的消息队列。一般用于进程间通信。  
  
### 参数： 
  
- name ：创建或打开消息队列的路径与名字。默认当前路径。  
  
- flag ：消息队列对象的打开标志位。参数可以为：  
  
|flag|说明|  
|---|---|  
|O_CREAT|信息量对象不存在时会创建信号量，存在不起作用。|  
|O_NONBLOCK|该标志使得一个消息队列在队列为空时的读和队列塞满时的写不被阻塞。|  
|O_EXCL|信号量不存在时不会创建。|  
|O_RDONLY|以只读的方式打开。|  
|O_WRONLY|以只写的方式打开。|  
|O_RDWR|以读写的当时打开。|  
  
创建消息队列时，需要附加2个参数。  
  
- mode_tmode ：消息队列的权限位。  
  
- pmqattr ：消息队列的属性句柄。为NULL时使用默认属性。  
  
### 返回值：
  
成功返回相应的信号量句柄；失败返回(mqd_t)-1，并置错误码。  
  
### 错误码：
  
- EINVAL ：参数无效。  
- ENAMETOOLONG ：路径名太长。  
- EEXIST ：文件已存在。  
- ENOMEM ：内存不足。  
- ENOENT ：文件或设备不存在。  
  
### 备注：  
  
GJB 7714-2012错误码为：  
- EEXIST：同时设置了O_EXCL和O_CREAT。  
- EINVAL：参数无效。  
- ENFILE：系统中打开了过多的消息队列  
- EMFILE：形同中打开了过多的消息队列描述符。  
- ENAMETOOLONG：参数name的长度超过了NAME_MAX。  
- ENOENT：没有设置O_CREAD，且指定的消息队列不存在。  
  
### 样例：  
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <mqueue.h>  
#include <string.h>  
#include <SylixOS.h>  
#include <spawn.h>  
#include <unistd.h>  
#include <sys/types.h>  
#include <sys/wait.h>  
#define QUEUE_NAME  "mqdname"  
#define MAX_SIZE    1024  
int  main (int  argc   char  *argv[ ] )  
{  
	mq_attr_t  attr;  
    mqattr.mq_flags   = 0;  
    mqattr.mq_maxmsg  = 10;          /*  消息队列最多容纳 10 个消息 */  
    mqattr.mq_msgsize = 1024;         /*  每条消息的最大字节数        */  
    mqattr.mq_curmsgs = 0;  
	/*   
    *创建一个消息队列              
	*/  
    mq = mq_open(QUEUE_NAME, O_CREAT | O_RDWR, 0644, &mqattr);            
	if (mq  == MQ_FAILED)   
	{  
    	printf("open msg failed\n");  
		return  (-1);  
    }  
	// ……  
	return NULL;  
}  
```  
  
  
## mq_close ：关闭指定的消息队列
  
  
### 函数原型：
  
```c  
int              mq_close  
(  
	mqd_t  mqd  
);  
```  
  
  
### 描述：
  
该接口用来关闭指定的命名消息队列。  
  
### 参数：
  
- mqd：消息队列句柄。  
  
### 返回值：
  
成功返回0；失败返回-1，并置错误码。  
  
### 错误码： 
  
- EBADF：句柄无效。  
- ENOENT：文件或设备不存在。  
  
### 备注：   
  
GJB 7714-2012错误码为：  
- EBADF：mqdes指定的消息队列描述符不合法。  
  
### 样例：  
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <mqueue.h>  
#include <string.h>  
#include <SylixOS.h>  
#include <spawn.h>  
#include <unistd.h>  
#include <sys/types.h>  
#include <sys/wait.h>  
int  main (int  argc   char  *argv[ ] )  
{  
	mqd_t  mq;  
	int   ret;  
	// ……  
  
	ret = mq_close(mq);              /*  关闭消息队列              */  
	if (ret != 0)  
	{  
   	 	perror("mq_close failed\n");  
    	return -1;  
	}  
	return NULL;  
}  
```  
  
  
## mq_unlink ：删除指定命名消息队列
  
  
### 函数原型：
  
```c  
int              mq_unlink  
(  
	const char  *name  
);  
```  
  
  
### 描述：
  
该接口用来删除指定的命名消息队列。  
  
### 参数： 
  
- name：删除消息队列的路径与名字，默认当前路径。  
  
### 返回值：
  
成功返回0；失败返回-1，并置错误码。  
  
### 错误码： 
  
- EBUSY ：设备资源忙。  
- EINVAL ：参数无效。  
- ENOENT ：文件或设备不存在。  
  
### 备注：  
  
GJB 7714-2012错误码为：  
- EINVAL ：name是无效指针。  
- ENAMETOOLONG ：变量name的长度大于NAME-MAX。  
- ENOENT ：指定的命名消息队列不存在。  
  
### 样例：  
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <mqueue.h>  
#include <string.h>  
#include <SylixOS.h>  
#include <spawn.h>  
#include <unistd.h>  
#include <sys/types.h>  
#include <sys/wait.h>  
#define QUEUE_NAME  "mqdname"  
int  main (int  argc   char  *argv[ ] )  
{  
	mqd_t  mq;  
	int   ret;  
  
	// ……  
  
	ret = mq_unlink(QUEUE_NAME);     /*  删除消息队列MQD_NAME   */  
	if (ret != 0)  
	{  
    	perror("mq_close failed\n");  
    	return -1;  
	}  
  
	return NULL;  
}  
```  
  
  
## mq_getattr ：获取消息队列属性
  
  
### 函数原型：
  
```c  
int              mq_getattr  
(  
	mqd_t  mqd,   
	struct mq_attr *pmqattr  
);  
```  
  
### 描述：
  
该接口用来获取指定消息队列的属性。  
  
### 参数： 
  
- mqd ：消息队列句柄。  
  
- pshared ：存放消息队列属性的指针。  
  
### 返回值：
  
成功返回0；失败返回-1，并置相应的错误码。  
  
### 错误码： 
  
- EINVAL ：参数无效。  
- EBADF ：句柄无效。  
  
### 备注：  
  
GJB 7714-2012错误码为：  
- EBADF：mqdes指定的消息队列描述符不合法。  
  
### 样例：  
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <mqueue.h>  
#include <string.h>  
static mqd_t Gmqd;  
static pthread_t  Gtid1, Gtid2;  
int  main (int  argc, char  *argv[])  
{  
	int ret = 0;  
	struct mq_attr mqattr;  
  
	// ……  
  
	ret = mq_getattr(Gmqd, &mqattr);             /*  获取消息队列的属性 */  
	if(ret != 0)  
	{  
		perror("func mq_getattr error\n");  
		return -1;  
	}  
  
	mqattr.mq_maxmsg = 40;  
	ret = mq_setattr(Gmqd, &mqattr, NULL);     /*  设置消息队列的属性   */  
	if(ret != 0)  
	{  
		perror("func mq_setattr error\n");  
		return -1;  
	}  
  
	// ……  
  
	return (0);  
}  
```  
  
## mq_setattr ：设置消息队列的属性
  
  
### 函数原型：
  
```c  
int              mq_setattr  
(  
	mqd_t  mqd,   
	const struct mq_attr *pmqattrNew,   
 	struct mq_attr *pmqattrOld  
);  
```  
  
### 描述：
  
该接口用来设置指定消息队列的属性，并返回设置前的属性。  
  
### 参数： 
  
- mqd：消息队列句柄。  
  
- pmqattrNew：消息队列属性。  
  
- pmqattrOld：存放消息队列先前属性的指针。  
  
### 返回值：
  
成功返回0；失败返回-1，并置相应的错误码。  
  
### 错误码： 
  
- EBADF：句柄无效。  
  
### 备注：  
  
GJB 7714-2012错误码为：  
- EBADF：mqdes指定的消息队列描述符不合法，或者mqdes指定的消息队列是只读的。  
  
### 样例：  
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <mqueue.h>  
#include <string.h>  
static mqd_t Gmqd;  
static pthread_t  Gtid1, Gtid2;  
int  main (int  argc, char  *argv[])  
{  
	int ret = 0;  
	struct mq_attr mqattr;  
	// ……  
	ret = mq_getattr(Gmqd, &mqattr);             /*  获取消息队列的属性 */  
	if(ret != 0)  
	{  
		perror("func mq_getattr error\n");  
		return -1;  
	}  
	mqattr.mq_maxmsg = 40;  
	ret = mq_setattr(Gmqd, &mqattr, NULL);     /*  设置消息队列的属性   */  
	if(ret != 0)  
	{  
		perror("func mq_setattr error\n");  
		return -1;  
	}  
  
	// ……  
  
	return (0);  
}  
```  
  
  
## mq_send ：向消息队列发送消息
  
  
### 函数原型：
  
```c  
int              mq_send  
(  
	mqd_t  mqd,   
	const char  *msg,   
	size_t  msglen,   
	unsigned msgprio  
);  
```  
  
  
### 描述：
  
该接口用来向指定消息队列发送一条消息。  
  
### 参数： 
  
- mqd ：消息队列句柄。  
  
- msg ：消息指针。  
  
- msglen ：消息长度。  
  
- msgprio ：消息的优先级。  
  
### 返回值：
  
成功返回0；失败返回-1，并置相应的错误码。  
  
### 错误码： 
  
- EINVAL ：参数无效。  
- EBADF ：句柄无效。  
- EMSGSIZE ：消息长度超出限制。  
- ETIMEDOUT ：等待超时。  
- EINTR ：任务收到一个非阻塞或忽略的信号。  
- ENOENT ：文件或设备不存在。  
  
### 备注：  
  
GJB 7714-2012错误码为：  
- EINVAL ：msg_ptr是无效的指针，或者msg_prio的取值不合法。  
- EBADF ：mqdes指定的消息队列描述符不合法，或者mqdes指定的消息队列是只读的。  
- EMSGSIZE ：msg_len指定的消息长度超过了消息队列的最大消息长度属性。  
- EAGAIN ：消息队列设置了O_NONBLOCK标志，并且调用该接口时消息队列已满。  
- EINTR ：调用任务被信号中断。  
  
### 样例：  
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <mqueue.h>  
#include <string.h>  
static mqd_t Gmqd;  
static pthread_t  Gtid1, Gtid2;  
void* Thread 1(void  *arg)  
{  
	int ret=0;  
  
    // ……  
  
	ret = mq_send(Gmqd, "HYSylixOS system", 1024, 20);  
	if(ret != 0)  
	{  
		perror("func mq_send error\n");  
		return (void *)-1;  
	}  
	..….  
  
	return NULL;  
}  
```  
  
  
## mq_receive ：从消息队列接受消息
  
  
### 函数原型：
  
```c  
ssize_t          mq_receive  
(  
	mqd_t  mqd,   
	char  *msg,   
	size_t  msglen,   
	unsigned *pmsgprio  
);  
```  
  
  
### 描述：
  
该接口用来从指定消息队列接受一条消息。  
  
### 参数： 
  
- mqd ：消息队列句柄。  
  
- msg ：消息缓冲区指针。  
  
- msglen ：缓冲区长度。  
  
- pmsgprio ：接受消息的优先级。  
  
### 返回值：
  
成功返回接受消息的长度；失败返回-1，并置相应的错误码。  
  
### 错误码： 
  
- EINVAL：参数无效。  
- EBADF：句柄无效。  
- EMSGSIZE：缓存区长度太小。  
- ETIMEDOUT：等待超时。  
- EINTR：任务收到一个非阻塞或忽略的信号。  
- ENOENT:文件或设备不存在。  
  
### 备注：  
  
GJB 7714-2012错误码为：  
- EINVAL ：参数msg_ptr是无效的指针。  
- EBADF ：mqdes指定的消息队列描述符不合法，或者mqdes指定的消息队列是只写的。  
- EMSGSIZE ：msg_len的值小于消息队列的消息大小属性。  
- EAGAIN ：消息队列描述符mqdes中设置了O_NONBLOCK标志位，且调用该接口时消息队列是空的。  
- EINTR ：接口调用被信号中断。  
  
### 样例：  
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <mqueue.h>  
#include <string.h>  
static mqd_t Gmqd;  
static pthread_t  Gtid1, Gtid2;  
void* Thread 2(void  *arg)  
{  
	int ret = 0;  
	unsigned int prio;  
	char msg[1024];  
	memset(msg, 0, 1024);  
  
	// ……  
  
	ret = mq_receive(Gmqd, msg, 1024, &prio);                           if(ret == -1)  
	{  
		perror("func mq_receive error");  
		return (void *)-1;  
	}  
	printf("receive msg :%s\n", msg);  
  
	// …….  
  
	return NULL;  
}  
```  
  
  
## mq_timedsend ：限时向消息队列发送消息
  
  
### 函数原型：
  
```c  
int              mq_timedsend  
(  
	mqd_t  mqd,   
	const char  *msg,   
	size_t  msglen,   
	unsigned msgprio,  
	const struct timespec *abs_timeout  
);  
```  
  
### 描述：
  
该接口用来限时向指定消息队列发送一条消息（绝对时间）。  
  
### 参数： 
  
- mqd ：消息队列句柄。  
  
- msg ：消息指针。  
  
- msglen ：消息长度。  
  
- msgprio ：消息的优先级。  
  
- abs_timeout ：绝对超时时间。  
  
### 返回值：
  
成功返回0；失败返回-1，并置相应的错误码。  
  
### 错误码： 
  
- EINVAL ：参数无效。  
- EBADF ：句柄无效。  
- EMSGSIZE ：消息长度超出限制。  
- ETIMEDOUT ：等待超时。  
- EINTR ：任务收到一个非阻塞或忽略的信号。  
- ENOENT ：文件或设备不存在。  
  
### 备注：  
  
GJB 7714-2012错误码为：  
- EINVAL ：msg_ptr是无效的指针，或者msg_prio的取值不合法，或者在任务被阻塞的情况下abs_timeout指定的纳秒值小于0或者大于等于1000000000  
- EBADF ：mqdes指定的消息队列描述符不合法，或者mqdes指定的消息队列是只读的。  
- ETIMEDOUT ：消息队列打开时没有设置O_NONNBLOCK标志，并且在消息添加进消息队列之前到达了超时时间。  
- EMSGSIZE ：msg_len指定的消息长度超过了消息队列的最大消息长度属性。  
- EAGAIN ：消息队列设置了O_NONBLOCK标志，并且调用该接口时消息队列已满。  
- EINTR：调用任务被信号中断。  
  
### 样例：  
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <mqueue.h>  
#include <string.h>  
static mqd_t Gmqd;  
static pthread_t  Gtid1, Gtid2;  
void* Thread 1(void  *arg)  
{  
	int ret=0;  
	int i = 0;  
	struct timespec outtime;  
  
	outtime.tv_nsec = 0;  
	outtime.tv_sec = 2;  
	while(++i)  
	{  
		/*    
		*限时向消息队列发送消息   
		*/  
		ret = mq_timedsend(Gmqd, "HYSylixOS good system",1024, 20, &outtime);     
		if(ret != 0)  
		{  
			perror("func mq_timedsend error\n");  
			printf("发送次数 = %d\n",i);  
			break;  
		}  
	}  
	mq_close(Gmqd);                                                       
	return NULL;  
}  
```  
  
## mq_timedreceive ：限时从消息队列接受消息
  
  
### 函数原型：
  
```c  
ssize_t	mq_timedreceive  
(  
	mqd_t  mqd,   
	char  *msg,   
	size_t  msglen,  
	unsigned *pmsgprio,   
	const struct timespec *abs_timeout  
);  
```  
  
  
### 描述：
  
该接口用来限时从指定消息队列接受一条消息（绝对时间）。  
  
### 参数：
  
- mqd ：消息队列句柄。  
  
- msg ：消息缓冲区指针。  
  
- msglen ：缓冲区长度。  
  
- pmsgprio ：接受消息的优先级。  
  
- abs_timeout ：绝对超时时间。  
  
### 返回值：
  
成功返回接受消息的长度；失败返回-1，并置相应的错误码。  
  
### 错误码：
  
- EINVAL ：参数无效。  
- EBADF ：句柄无效。  
- EMSGSIZE ：缓冲区长度太小。  
- ETIMEDOUT ：等待超时。  
- EINTR ：任务收到一个非阻塞或忽略的信号。  
- ENOENT ：文件或设备不存在。  
  
### 备注：  
  
GJB 7714-2012错误码为：  
- EINVAL ： msg_ptr是无效的指针，或者任务被阻塞的情况下，参数abs_timeout指定的纳秒值小于0或者大于等于1000000000  
- EBADF ：mqdes指定的消息队列描述符不合法，或者mqdes指定的消息队列是只写的。  
- EMSGSIZE ：msg_len的值小于消息队列的消息大小属性。  
- EAGAIN ：消息队列描述符mqdes中设置了O_NONBLOCK标志位，且接口调用时消息队列是空的。  
- EINTR ：接口调用被信号中断。  
- ETIMEDOUT：消息队列打开时没有设置标志O_NONBLOCK，但在指定时间到达前没有消息可接受。  
  
### 样例：  
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <mqueue.h>  
#include <string.h>  
static mqd_t Gmqd;  
static pthread_t  Gtid1, Gtid2;  
void* Thread 2(void  *arg)  
{  
	int ret = 0;  
	int j = 0;  
	unsigned int prio;  
	char msg[1024];  
	memset(msg, 0, 1024);  
  
	struct timespec outtime;  
	outtime.tv_nsec = 0;  
	outtime.tv_sec = 2;  
  
	while(++j)  
	{  
		/*    
		*限时从消息队列接收消息       
		*/  
		ret = mq_timedreceive(Gmqd, msg, 1024, &prio, &outtime);              
		if(ret == -1)  
		{  
			perror("func mq_timedreceive error\n");  
			printf("接收次数 = %d\n",j);  
			break;  
		}  
		printf("receive msg :%s\n", msg);  
	}  
  
	mq_close(Gmqd);                                                       
	return NULL;  
}  
```  
  
  
## mq_reltimedsend_np ：限时向消息队列发送消息
  
  
### 函数原型：
  
```c  
int              mq_reltimedsend_np  
(  
	mqd_t  mqd,   
	const char  *msg,   
	size_t  msglen,   
	unsigned msgprio,   
	const struct timespec *rel_timeout  
);  
```  
  
  
### 描述：
  
该接口用来限时向指定消息队列发送一条消息（相对时间）。  
  
### 参数： 
  
- mqd：消息队列句柄。  
  
- msg：消息指针。  
  
- msglen：消息长度。  
  
- msgprio：消息的优先级。  
  
- abs_timeout：相对超时时间。  
  
### 返回值：
  
成功返回0；失败返回-1，并置相应的错误码。  
  
### 错误码： 
  
- EINVAL ：参数无效。  
- EBADF ：句柄无效。  
- EMSGSIZE ：消息长度超出限制。  
- ETIMEDOUT ：等待超时。  
- EINTR ：任务收到一个非阻塞或忽略的信号。  
- ENOENT ：文件或设备不存在。  
  
### 样例：  
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <mqueue.h>  
#include <string.h>  
static mqd_t Gmqd;  
static pthread_t  Gtid1, Gtid2;  
void* Thread 1(void  *arg)  
{  
	int ret=0;  
	int i = 0;  
	struct timespec outtime;  
  
	outtime.tv_nsec = 0;  
	outtime.tv_sec = 1;  
	while(++i)  
	{  
		/*   
		*限时向消息队列发送消息  
		*/  
		ret = mq_reltimedsend_np(Gmqd, "HYSylixOS good system", 1024, 20, &outtime);   
		if(ret != 0)  
		{  
			perror("func mq_reltimedsend_np error\n");  
			printf("发送次数 = %d\n",i);  
			break;  
		}  
	}  
  
	mq_close(Gmqd);  
	return NULL;  
}  
```  
  
  
## mq_reltimedreceive_np ：限时从消息队列接受消息
  
  
### 函数原型：
  
```c  
ssize_t          mq_reltimedreceive_np  
(  
	mqd_t  mqd,   
	char  *msg, size_t  msglen,   
	unsigned *pmsgprio,   
	const struct timespec *rel_timeout  
);  
```  
  
  
### 描述：
  
该接口用来限时从指定消息队列接受一条消息（相对时间）。  
  
### 参数： 
  
- mqd：消息队列句柄。  
  
- msg：消息缓冲区指针。  
  
- msglen：缓冲区长度。  
  
- pmsgprio：接受消息的优先级。  
  
- abs_timeout：绝对超时时间。  
  
### 返回值：
  
成功返回接受消息的长度；失败返回-1，并置相应的错误码。  
  
### 错误码： 
  
- EINVAL：参数无效。  
- EBADF：句柄无效。  
- EMSGSIZE：缓冲区长度太小。  
- ETIMEDOUT：等待超时。  
- EINTR：任务收到一个非阻塞或忽略的信号。  
- ENOENT：文件或设备不存在。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <mqueue.h>  
#include <string.h>  
static mqd_t Gmqd;  
static pthread_t  Gtid1, Gtid2;  
void* Thread 2(void  *arg)  
{  
	int  ret = 0;  
	int  j = 0;  
	unsigned int  prio;  
	char msg[1024];  
	memset(msg, 0, 1024);  
  
	struct timespec outtime;  
	outtime.tv_nsec = 0;  
	outtime.tv_sec = 1;  
  
	while(++j)  
	{  
		/*    
		*限时从消息队列接收消息        
		*/  
  
		ret = mq_reltimedreceive_np(Gmqd, msg, 1024, &prio, &outtime);       
		if(ret == -1)  
		{  
			perror("func mq_reltimedreceive_np error\n");  
			printf("接收次数 = %d\n",j);  
			break;  
		}  
		printf("receive msg :%s\n", msg);  
	}  
  
	mq_close(Gmqd);   
	return NULL;  
}  
```  
  
  
## mq_getinfo ：获取消息队列的信息
  
  
### 函数原型：
  
```c  
int              mq_getinfo  
(  
	mqd_t  mqd,   
	mq_info_t  *info  
);  
```  
  
  
### 描述：
  
该接口用来获取指定消息队列的信息。  
  
### 参数： 
  
- mqd：消息队列句柄。  
  
- info：存放消息队列信息的指针。  
  
### 返回值：
  
成功返回0；失败返回-1，并置相应的错误码。  
  
### 错误码： 
  
- EINVAL：参数无效。  
- EBADF：句柄无效。  
  
### 备注：  
  
GJB 7714-2012错误码为：  
- EINVAL：参数mq_info是无效的指针。  
- EBADF：mqdes指定的消息队列描述符不合法。  
- EMNOTINITED：消息队列模块尚未初始化。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <mqueue.h>  
#include <string.h>  
static mqd_t Gmqd;  
static pthread_t  Gtid1, Gtid2;  
svoid* Thread 1(void  *arg)  
{  
	int ret=0;  
	mq_info_t info;  
  
	// ……  
  
	ret = mq_getinfo(Gmqd, &info);           /*  获取消息队列的信息  */  
	if(ret != 0)  
	{  
		perror("func mq_getinfo error\n");  
		return (void *)-1;  
	}  
    printf("mq flags   %lx\n", info.attr.mq_flags);  
    printf("mq maxmsg  %lu\n", info.attr.mq_maxmsg);  
    printf("mq msgsize %lu\n", info.attr.mq_msgsize);  
    printf("mq curmsg  %lu\n", info.attr.mq_curmsgs);  
	printf("mq mode    0%o\n", info.mode);  
  
	// ……  
                                                
	return NULL;  
  
}  
```  
  
  
## mq_show ：显示消息队列的信息
  
  
### 函数原型：
  
```c  
int              mq_show  
(  
	mqd_t  mqd,   
	Int  level  
);  
```  
  
### 描述：
  
该接口用来显示指定消息队列的信息。  
  
### 参数： 
  
- mqd：消息队列句柄。  
  
- level：显示信息级别。可为0或1。(未使用)  
  
### 返回值：
  
成功返回0；失败返回-1，并置相应的错误码。  
  
### 错误码： 
  
- EINVAL：参数无效。  
- EBADF：句柄无效。  
  
### 备注：  
  
GJB 7714-2012错误码为：  
- EINVAL：level取值不合法。  
- EBADF：mqdes指定的消息队列描述符不合法。  
- EMNOTINITED：消息队列模块尚未初始化。  
  
### 样例：  
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <mqueue.h>  
#include <string.h>  
static mqd_t Gmqd;  
static pthread_t  Gtid1, Gtid2;  
void* Thread 1(void  *arg)  
{  
	int ret=0;  
	mq_info_t info;  
  
	// ……  
  
	ret = mq_getinfo(Gmqd, &info);           /*  获取消息队列的信息  */  
	if(ret != 0)  
	{  
		perror("func mq_getinfo error\n");  
		return (void *)-1;  
	}  
  
	mq_show(Gmqd, 1);                   /*  显示消息队列的信息  */  
  
	// ……  
                                                
	return NULL;  
}  
```  
  
  
## mq_notify ：给消息队列可读时注册信号
  
  
### 函数原型：
  
```c  
int              mq_notify  
(  
	mqd_t  mqd,   
	const struct sigevent  *pnotify  
);  
```  
  
  
### 描述：
  
该接口用来在消息队列可读时，注册一个通知信号。  
  
### 参数：   
  
- mqd：消息队列句柄。  
  
- pnotify：信号事件。  
  
### 返回值：
  
成功返回0；失败返回-1，并置相应的错误码。  
  
### 错误码： 
  
- EINVAL：参数无效。  
- EBADF：句柄无效。  
  
### 样例：  
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <mqueue.h>  
#include <string.h>  
static mqd_t Gmqd;  
static pthread_t  Gtid1, Gtid2;  
int  main (int  argc, char  *argv[])  
{  
	int ret;  
	struct sigevent notify;  
  
	// ……  
  
	notify.sigev_signo = SIGEV_THREAD_ID | SIGEV_SIGNAL;  
	notify.sigev_signo = SIGUSR1;  
	notify.sigev_notify_thread_id = Gtid1;  
	ret = mq_notify(Gmqd, &notify);                     /*  注册信号   */  
	if(ret != 0)  
	{  
		perror("func mq_notify error\n");  
		return -1;  
	}  
  
	// ……   
  
    return (0);  
}  
```  
  
## mq_open_method ：设置打开消息队列的类型
  
  
### 函数原型：
  
```c  
int              mq_open_method  
(  
	int  method,   
	int *old_method  
);     
```  
  
  
### 描述：
  
该接口设置当前进程使用的消息队列的类型。  
  
### 参数：  
  
- method：新的操作类型。取值有：  
  
|method|说明|  
|---|---|  
|MQ_OPEN_METHOD_POSIX（0）|以posix方式打开|  
|MQ_OPEN_METHOD_GJB（1）|以国军标方式打开|  
  
- info：存放原操作类型的指针。  
  
### 返回值：
  
函数返回0。  
  
### 错误码：
  
无。  
  
### 样例：  
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <mqueue.h>  
#include <string.h>  
static mqd_t Gmqd;  
static pthread_t  Gtid1, Gtid2;  
int  main (int  argc, char  *argv[])  
{  
	// ……  
  
	mq_open_method(MQ_OPEN_METHOD_POSIX, NULL);  
  
	// ……  
  
	return NULL;  
}  
```  
  
  
# 线程屏障
  
  
## pthread_barrierattr_init ：初始化线程屏障属性块
  
  
### 函数原型：
  
```c  
int	pthread_barrierattr_init  
(  
	pthread_barrierattr_t  *pbarrierattr  
);  
```  
  
  
### 描述：
  
该接口用来初始化一个线程屏障属性块。  
  
### 参数：
  
- pbarrierattr：线程屏障属性块。  
  
### 返回值：
  
函数返回0。  
  
### 错误码：
  
无。  
  
### 备注：
  
无。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <string.h>  
#include <stdlib.h>  
#define BARRIER_SIZE 3  
static pthread_barrier_t G_barrier;  
static int a[20] = {1, 5, 6, 7, 4, 9, 2, 8, 3, 10, 11, 15, 16, 17, 14, 19, 12, 18, 13, 20};  
int main (int argc, char **argv)  
{  
    int ret;  
    pthread_barrierattr_t barrierattr;  
    // ……  
    ret = pthread_barrierattr_init (&barrierattr);/*初始化线程屏障属性快  */  
    if (ret != 0)  
    {  
        printf ("func pthread_barrierattr_init error\n");  
        return -1;  
    }  
	// ……  
	return (0);  
}  
```  
  
  
## pthread_barrierattr_destroy ：销毁线程屏障属性块
  
  
### 函数原型：
  
```c  
int	pthread_barrierattr_destroy  
(  
	pthread_barrierattr_t  *pbarrierattr  
);  
```  
  
### 描述：
  
该接口用来销毁一个线程屏障属性块。  
  
### 参数：
  
- pbarrierattr：线程屏障属性块。  
  
### 返回值：
  
函数返回0。  
  
### 错误码：
  
无。  
  
### 备注：
  
无。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <string.h>  
#include <stdlib.h>  
#define BARRIER_SIZE 3  
static pthread_barrier_t G_barrier;  
static int a[20] = {1, 5, 6, 7, 4, 9, 2, 8, 3, 10, 11, 15, 16, 17, 14, 19, 12, 18, 13, 20};  
int main (int argc, char **argv)  
{  
    int ret;  
    pthread_barrierattr_t barrierattr;  
    pthread_t tid1, tid2;  
    // ……  
	ret = pthread_barrierattr_destroy (&barrierattr);/*销毁线程屏障属性快  */  
    if (ret != 0)  
    {  
        printf ("func barrierattr_destroy error\n");  
        return -1;  
    }  
    // ……  
    return (0);  
}  
```  
  
  
## pthread_barrierattr_setpshared ：设置线程屏障属性块的共享状态
  
  
### 函数原型：
  
```c  
int	pthread_barrierattr_setpshared  
(  
	pthread_barrierattr_t  *pbarrierattr,  
	int  shared  
);  
```  
  
  
### 描述：
  
该接口用来设置一个线程屏障属性块的共享状态。  
  
### 参数：
  
- pbarrierattr ：线程屏障属性块。  
  
- shared ：共享状态。取值有：  
  
|共享状态|说明|  
|---|---|  
|PTHREAD_PROCESS_SHARED|共享|  
|PTHREAD_PROCESS_PRIVATE|私有|  
  
### 返回值：
  
函数返回0。  
  
### 错误码：
  
无。  
  
### 备注：
  
无。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <string.h>  
#include <stdlib.h>  
#define BARRIER_SIZE 3  
static pthread_barrier_t G_barrier;  
static int a[20] = {1, 5, 6, 7, 4, 9, 2, 8, 3, 10, 11, 15, 16, 17, 14, 19, 12, 18, 13, 20};  
int main (int argc, char **argv)  
{  
    int ret;  
    pthread_barrierattr_t barrierattr;  
    pthread_t tid1, tid2;  
    // ……  
    /*  
    *设置线程屏障属性快的共享状态  
    */  
    ret=pthread_barrierattr_setpshared(&barrierattr, PTHREAD_PROCESS_SHARED);  
    if (ret != 0)  
    {  
        printf ("func setpshared error\n");  
        return -1;  
    }  
  
    // ……  
  
    return (0);  
}  
```  
  
  
## pthread_barrierattr_getpshared ：获取线程屏障属性块的共享状态
  
  
### 函数原型：
  
```c  
int	pthread_barrierattr_getpshared  
(  
	const pthread_barrierattr_t  *pbarrierattr,  
	int  *pshared  
);  
```  
  
### 描述：
  
该接口用来获取指定线程屏障属性块的共享状态。  
  
### 参数：
  
- pbarrierattr ：线程屏障属性块。  
  
- pshared ：存放共享状态的指针。  
  
### 返回值：
  
函数返回0。  
  
### 错误码：
  
无。  
  
### 备注：
  
无。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <string.h>  
#include <stdlib.h>  
#define BARRIER_SIZE 3  
static pthread_barrier_t G_barrier;  
static int a[20] = {1, 5, 6, 7, 4, 9, 2, 8, 3, 10, 11, 15, 16, 17, 14, 19, 12, 18, 13, 20};  
int main (int argc, char **argv)  
{  
    int ret;  
    pthread_barrierattr_t barrierattr;  
    pthread_t tid1, tid2;  
	// ……  
  
	/*  
	*获取线程屏障属性快的共享状态  
	*/  
	ret=pthread_barrierattr_getpshared(&barrierattr,&ptr);  
    if (ret != 0)  
    {  
        printf ("func getpbarrier error\n");  
        return -1;  
    }  
    printf ("getpshared :%d\n", ptr);  
  // ……  
  
    return (0);  
}  
```  
  
  
## pthread_barrier_init ：初始化线程屏障
  
### 函数原型：
  
```c  
int	pthread_barrier_init  
(  
	pthread_barrier_t            *pbarrier,   
	const pthread_barrierattr_t  *pbarrierattr,  
	unsigned int                  count  
);  
```  
  
  
### 描述：
  
该接口用来创建并初始化线程屏障。  
  
### 参数：
  
- pbarrier：保存线程屏障句柄的指针。  
  
- pbarrierattr：线程屏障属性块。  
  
- count：线程屏障的计数数量。  
  
### 返回值：
  
成功返回0，失败返回相应的错误码。  
  
### 错误码：
  
- EINVAL：参数无效。  
- ENOSPC：磁盘空间不足。  
  
### 备注：
  
无。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <string.h>  
#include <stdlib.h>  
#define BARRIER_SIZE 3  
static pthread_barrier_t G_barrier;  
static int a[20] = {1, 5, 6, 7, 4, 9, 2, 8, 3, 10, 11, 15, 16, 17, 14, 19, 12, 18, 13, 20};  
int main (int argc, char **argv)  
{  
    int ret;  
    pthread_barrierattr_t barrierattr;  
    pthread_t tid1, tid2;  
    // ……  
    /*  
    *初始化线程屏障  
    */  
    ret = pthread_barrier_init (&G_barrier, &barrierattr, BARRIER_SIZE);  
    if (ret != 0)  
    {  
        printf ("func barrier_init error\n");  
        return -1;  
    }  
  
    // ……  
  
    return (0);  
}  
```  
  
  
## pthread_barrier_destroy ：销毁线程屏障
  
  
### 函数原型：
  
```c  
int	pthread_barrier_destroy  
(  
	pthread_barrier_t  *pbarrier  
);  
```  
  
  
### 描述：
  
该接口用来删除或销毁线程屏障。  
  
### 参数：
  
- pbarrier：线程屏障句柄的指针。  
  
### 返回值：
  
成功返回0，失败返回相应的错误码。  
  
### 错误码：
  
- EINVAL：参数无效。  
  
### 备注：
  
无。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <string.h>  
#include <stdlib.h>  
#define BARRIER_SIZE 3  
static pthread_barrier_t G_barrier;  
static int a[20] = {1, 5, 6, 7, 4, 9, 2, 8, 3, 10, 11, 15, 16, 17, 14, 19, 12, 18, 13, 20};  
int main (int argc, char **argv)  
{  
    int ret;  
    pthread_barrierattr_t barrierattr;  
    pthread_t tid1, tid2;  
    // ……  
  
    ret = pthread_barrier_destroy (&G_barrier); /*销毁线程屏障*/  
    if (ret != 0)  
    {  
        printf ("func barrier_destroy error\n");  
        return -1;  
    }  
  
    return (0);  
}  
```  
  
  
## pthread_barrier_wait ：等待线程屏障
   
  
### 函数原型：
  
```c  
int	pthread_barrier_wait  
(  
	pthread_barrier_t  *pbarrier  
);  
```  
  
  
### 描述：
  
该接口用来等待线程屏障。调用此接口第一个返回的线程返回PTHREAD_BARRIER_SERIAL_THREAD，其他的线程返回0。  
  
### 参数：
  
- pbarrier：线程屏障句柄的指针。  
  
### 返回值：
  
成功：  
  
|返回值|宏定义|  
|---|---|  
|第一个返回|PTHREAD_BARRIER_SERIAL_THREAD|  
|其它|0|  
  
失败返回相应的错误码。  
  
### 错误码：
  
- EINVAL：参数无效。  
  
### 备注：
  
无。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <string.h>  
#include <stdlib.h>  
#define BARRIER_SIZE 3  
static pthread_barrier_t G_barrier;  
static int a[20] = {1, 5, 6, 7, 4, 9, 2, 8, 3, 10, 11, 15, 16, 17, 14, 19, 12, 18, 13, 20};  
int main (int argc, char **argv)  
{  
    int ret;  
    pthread_barrierattr_t barrierattr;  
    pthread_t tid1, tid2;  
    // ……  
  
    ret = pthread_barrier_wait (&G_barrier);   /*等待线程屏障*/  
    if ( (ret != 0) && (ret != 1) )  
    {  
        printf ("func barrier_wait error\n");  
        return -1;  
    }  
  
    // ……  
  
    return (0);  
}  
```  
  
  
# 管道
  
  
## mkfifo() 创建命名管道
  
  
### 函数原型：
  
```c  
INT		mkfifo  
(  
	CPCHAR  pcFifoName,  
	mode_t  mode  
);  
```  
  
  
### 描述：
  
该接口用来创建一个新的命名管道。  
  
### 参数：
  
- pcFifoName：指定了命名管道的设备文件路径；  
  
- mode：指定了命名管道的设备文件模式，与open函数的模式位相同。  
  
|权限宏定义|宏值|解释|  
|---|---|---|  
|O_RDONLY|0x0000|以只读的方式打开|  
|O_WRONLY|0x0001|以只写的方式打开|  
|O_RDWR|0x0002|以读写的方式打开|  
|O_CREAT|0x0200|如果文件不存在，则创建文件，且 open 函数的第三个参数指定文件权限模式|  
|O_TRUNC|0x0400|如果文件存在，而且如果以只写或读写方式成功打开，则将其长度截断为 0|  
|O_APPEND|0x0008|将读写指针追加到文件的尾端|  
|O_EXCL|0x0800|如果指定了 O_CREAT，而文件存在，则出错。如果文件不存在，则创建|  
|O_NONBLOCK|0x4000|以非阻塞的方式打开文件|  
|O_SYNC|0x2000|使每次 write 等待物理 I/O 操作完成，包括由该 write 操作引起的文件属性更 新。|  
|O_DSYNC|0x0020|使每次 write 等待物理 I/O 操作完成，但是如果该写操作并不影响读取刚写入的数据，则不许等待文件属性被更新|  
|O_NOCTTY|0x8000|如果 cpcName 引用的是终端设备，则不将该设备分配作为此进程的控制终端|  
|O_NOFOLLOW|0x20000|如果 cpcName 引用的是符号链接，则出错|  
|O_CLOEXEC|0x80000|把 FD_CLOEXEC 标志设置为文件描述符标志|  
|O_LARGEFILE|0x100000|打开大文件标志|  
|O_ASYNC|0x0040|signal pgrp when data read|  
|O_SHLOCK|0x0010|以原子操作的方式创建文件|  
|O_EXLOCK|0x0020|以原子操作的方式创建文件|  
|O_NDELAY|0x4000|以非阻塞的方式打开文件|  
|O_BINARY|0x1000|以二进制模式打开文件|  
|O_FSYNC|0x2000|同步写入|  
  
### 返回值：
  
成功返回0，失败返回-1并设置错误号；  
  
### 错误码：
  
- EFAULT ：错误地址；  
- ENOENT ：文件不存在；  
- ELOOP ：符号链接过多；  
- EXDEV ：交叉连接；  
  
### 备注：
  
创建命名管道类似于创建文件。  
利用命名管道进行通信时，先创建管道，打开管道，写入数据；  
再打开管道，读出数据，关闭管道，删除管道。  
  
### 样例：
  
```c  
无
```  
  
  
## pipe() 创建匿名管道
  
  
### 函数原型：
  
```c  
INT pipe  
(  
	INT  iFd[2]  
);  
```  
  
  
### 描述：
  
该接口用于创建一个匿名管道，用在子父进程之间进行通信。  
  
### 参数：
  
- iFd[2]：用于记录匿名管道的两个文件描述符：  
  
|文件描述符|说明|  
|---|---|  
|iFd[0]|读端文件描述符|  
|iFd[1]|写端文件描述符|  
  
### 返回值：
  
成功返回0，失败返回-1并设置错误号；  
  
### 错误码：
  
- ENVAl：参数无效；  
- ERROR_ID_FILE_EXIST：文件已存在；  
  
### 备注：
  
1.读管道：  
  
管道中有数据：read返回实际读到的字节数；  
  
管道中无数据：  
  
（1）管道写端全部被关闭，read返回0；  
  
（2）写端没有全部被关闭，read阻塞等待。  
  
2.写管道：  
  
管道读端全部被关闭：  
  
进程异常终止（会产生SIGPIPE信号，可以设置信号处理函数或者忽略信号，使进程不中止）  
  
管道读端没有全部关闭：  
  
（1）管道已满，write阻塞；  
  
（2）管道未满，write将数据写入，并返回实际写入的字节数。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <spawn.h>  
#include <unistd.h>  
#include <string.h>  
#include <wait.h>  
#define SEND_STR    "From parent."  
int main (int argc, char *argv[])  
{  
      
    int                 ret, i;  
	// ……  
    char *cmd[] = {"HYSylixOS-pipe", (char *)0};  
  
    ret = pipe (fd);              /*  建立匿名管道               */  
    if (ret < 0)  
    {  
        printf ("func pipe error.\n");  
        return (-1);  
    }  
  
    // ……  
  
    return  (0);  
}  
```  
  
  
## pipe2() 创建匿名管道
  
  
### 函数原型：
  
```c  
INT		pipe2  
(  
	INT  iFd[2],  
	INT  iFlag  
);  
```  
  
  
### 描述：
  
该接口用于创建一个匿名管道，用在子父进程之间进行通信。  
  
### 参数：
  
- iFd：用于记录匿名管道的两个文件描述符：iFd[0]为读端文件描述符，iFd[1]为写端文件描述符；  
  
- iFlag：匿名管道的文件标志，可以使用0或以下的宏组合。  
  
|宏名|含义|  
|---|---|  
|O_NONBLOCK|读写管道是一个非阻塞操作|  
|O_CLOEXEC|当exec发生时，管道将被关闭|  
  
### 返回值：
  
成功返回0，失败返回-1并设置错误号。  
  
### 错误码：
  
- ENVAl：参数无效；  
- ERROR_ID_FILE_EXIST：文件已存在；  
  
### 备注：
  
1.读管道：  
  
管道中有数据：read返回实际读到的字节数；  
  
管道中无数据：  
  
（1）管道写端全部被关闭，read返回0；  
  
（2）写端没有全部被关闭，read阻塞等待。  
  
2.写管道：  
  
管道读端全部被关闭：  
  
进程异常终止（会产生SIGPIPE信号，可以设置信号处理函数或者忽略信号，使进程不中止）；  
  
管道读端没有全部关闭：  
  
（1）管道已满，write阻塞；  
  
（2）管道未满，write将数据写入，并返回实际写入的字节数。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <spawn.h>  
#include <unistd.h>  
#include <string.h>  
#include <wait.h>  
#define SEND_STR    "From parent."  
int main (int argc, char *argv[])  
{  
	int  ret, i;  
	// ……  
    ret = pipe2 (fd, O_NONBLOCK);                                                       
	if (ret < 0)   
	{  
        printf ("func pipe error.\n");  
        return (-1);  
    }  
	// ……  
    return  (0);  
}  
```  
  
# 信号  
  
  
## sigqueue() ：发送实时信号，允许信号带参数
  
  
### 函数原型：
  
```c  
INT sigqueue  
(  
	LW_OBJECT_HANDLE ulId,  
	INT iSigNo,  
	const union sigval sigvalue  
);  
```  
  
  
### 描述：
  
sigqueue()主要应用于发送实时信号，支持信号带参数(信号类型，指向信号的指针)。  
sigqueue()将输入参数iSigNo指定的信号以排队方式发送到参数ulId所指定的任务，并且参数sigvalue作为信号的参数值。该函数只会对实时信号（信号编号范围在SIGRTMIN-SIGRTMAX）进行排队，其它信号有可能会丢失。  
如果iSigNo为0(空信号)，会执行错误检验，但不发送信号，空信号可以用来检验ulId的有效性。  
  
### 参数：  
  
- ulId ：任务ID/进程ID；  
  
- iSigNo ：信号；  
  
- sigvalue ：信号附加值。  
  
|信号类型|宏值|描述|  
|---|---|---|  
|SIGHUP|1|挂断控制终端或进程|  
|SIGINT|2|来自键盘的中断|  
|SIGQUIT|3|来自键盘的退出|  
|SIGILL|4|非法指令|  
|SIGTRAP|5|跟踪断点|  
|SIGABRT|6|异常结束|  
|SIGUNUSED|7|没有使用|  
|SIGFPE|8|协处理器出错|  
|SIGKILL|9|强迫进程结束|  
|SIGBUS|10|总线错误，通常是指示一个实现定义的硬件故障|  
|SIGSEGV|11|无效内存引用|  
|SIGUNUSED2|12|暂时没有使用2|  
|SIGPIPE|13|管道写出错，无读者|  
|SIGALRM|14|实时定时器报警|  
|SIGTERM|15|进程中止|  
|SIGCNCL|16|线程取消信号|  
|SIGSTOP|17|停止进程执行|  
|SIGTSTP|18|tty发出停止进程|  
|SIGCONT|19|恢复进程继续执行|  
|SIGCHLD|20|子进程停止或被中止|  
|SIGTTIN|21|后台进程请求输入|  
|SIGTTOU|22|后台进程请求输出|  
|SIGCANCEL|SIGTERM|同 SIGTERM 相同|  
|SIGIO|23|异步 I/O 事件|  
|SIGXCPU|24|进程超出了软 CPU 事件限制|  
|SIGXFSZ|25|进程超出了软文件长度限制|  
|SIGUTALRM|26|函数 setitimer 设置的虚拟间隔定时器已经超时|  
|SIGPROF|27|函数 setitimer 设置的梗概间隔定时器已经超时|  
|SIGWINCH|28|更改了窗口的大小|  
|SIGINFO|29|信息请求|  
|SIGPOLL|SIGIO|同 SIGIO 相同|  
|SIGUSR1|30|用户定义信号 1|  
|SIGUSR2|31|用户定义信号 2|  
|SIGPWR|33|电源失败重新开始|  
|SIGSYS|34|错误的系统调用|  
|SIGURG|35|网络连接上接到带外的数据时，可选择地产生此信号|  
|SIGRTMIN-SIGRTMAX|48-63|实时信号范围|  
  
### 返回值：
  
成功返回0，失败返回1并置相应的错误码。  
  
### 错误码：
  
- EINVAL ：参数无效；  
- ESRCH ：任务id对应的任务不存在；  
- ERROR_THREAD_OTHER_DELETE ：已经有其他线程在等待删除。  
  
### 备注：  
  
GJB 7714-2012的错误码为：  
- EAGAIN ：系统资源不足。  
- EINVAL ：参数无效。  
- ESRCH ：任务id对应的任务不存在。  
  
### 样例：  
  
```c  
#include <stdio.h>  
#include <stdlib.h>  
#include <string.h>  
#include <pthread.h>  
#include <signal.h>  
void *Thread1 (void *arg)  
{  
    int ret;  
  
    printf ("this is G_tid1 !\n");  
  
    ret = sigqueue (G_tid2, SIGINT, (sigval_t)0);      /*  向指定线程发送信号         */  
    if (ret != 0)  
    {  
        printf ("func sigqueue error \n");  
        return (PVOID)-1;  
    }  
	// ……  
}  
```  
  
  
## sigsuspend() ：挂起任务信号
  
  
### 函数原型：
  
```c  
INT sigsuspend  
(  
	const sigset_t *sigsetMask  
);  
```  
  
  
### 描述：
  
sigsuspend()使用sigsetMask指定的一组信号取代调用任务当前的信号掩码，然后将任务挂起，直到任务接收到一个信号来执行信号处理函数或终止任务。  
在取代当前信号掩码时，如存在由之前信号掩码导致的阻塞信号，则该任务不会挂起，而是对该阻塞信号调用相应的处理程序，并恢复调用函数之前的信号掩码，返回-1。若无阻塞信号，则恢复信号掩码，将该任务挂起，直到接收到一个信号来执行信号处理函数或终止任务。  
如果要执行的是终止任务的操作，那么sigsuspend()函数永远不会做返回操作。如果是要执行一个信号处理函数，那么sigsuspend()函数会在处理函数返回后再返回，并将信号掩码保存在调用sigsuspend()前就存在的设置中。  
  
### 参数：  
  
- sigsetMask：信号集。  
  
### 返回值：
  
成功返回0；失败返回-1，并置相应的错误码。  
  
### 错误码：
  
- EINTR ：被中断系统调用。  
- EINVAL ：参数无效。  
  
### 备注：  
  
GJB 7714-2012错误码为：  
- EINTR ：接口调用期间有信号运行过。  
  
### 样例：  
  
```c  
无
```  
  
  
## sigwait() ：等待未决信号
  
  
### 函数原型：
  
```c  
INT sigwait  
(  
	const sigset_t *sigset,  
	INT *piSig  
);  
```  
  
  
### 描述：
  
sigwait()会从sigset中选取一个未决的信号，自动的把它从系统的未决信号集中清除掉，然后将其信号编号返回到参数piSig中,信号将不会被执行。  
如果在调用该接口前，同一个信号有多个实例挂在信号队列中，第一个未决的信号将从sigwait()返回，而其余则继续保持在队列中。  
如果调用接口时，sigset中所指向的信号集中没有处于未决状态的信号，任务会被挂起，直到有信号处于未决状态。  
  
### 参数：  
  
- sigset ：信号集；  
  
- piSig ：存放获取到的信号的指针。  
  
### 返回值：
  
成功返回0；失败返回-1，并置相应的错误码。  
  
### 错误码：
  
- EINTR ：被中断系统调用。  
- EINVAL ：参数无效。  
  
### 备注：  
  
GJB 7714-2012的错误码为：  
- EINVAL ：参数无效。  
  
### 样例：  
  
```c  
#include <stdio.h>  
#include <stdlib.h>  
#include <string.h>  
#include <pthread.h>  
#include <signal.h>  
void *Thread2 (void *arg)  
{  
    int      ret;  
    sigset_t set;  
    int      ptr;  
    // ……  
    ret = sigwait (&set, &ptr);       /*  等待信号                   */  
    if (ret != 0)  
    {  
        printf ("func sigwait error\n");  
        return (PVOID)-1;  
	}  
	else  
    {  
        printf ("ptr :%d\n",ptr);  
    }  
}  
```  
  
## sigtimedwait() ：限时等待未决信号
  
  
### 函数原型：
  
```c  
INT sigtimedwait  
(  
	const sigset_t * sigset,  
	struct siginfo * psiginfo,  
	const struct timespec *pt  
);  
```  
  
  
### 描述：
  
sigtimedwait()用来在指定时间限制内等待sigset中的处于未决状态的信号，并返回未决信号的信息，等待的时间限制由pt指定。  
如果信号集sigset中的有信号处于未决状态，则获取未决信号的信息并将其存放在psiginfo中；如果没有未决信号，则任务挂起，直到sigset中至少有一个信号处于未决状态，或者pt指定的时间超时。  
  
### 参数：  
  
- sigset ：信号集；  
  
- psiginfo ：存放信号信息的指针；  
  
- pt ：等待的时间。  
  
### 返回值：
  
成功返回所选信号集，失败返回-1，并置相应的错误码。  
  
### 错误码：
  
- EINTR ：被中断系统调用。  
- EAGAIN ：系统资源不足。  
- EINVAL ：参数无效。  
  
### 备注：  
  
GJB 7714-2012的错误码为：  
- EINVAL ：参数无效。  
- EAGAIN ：在指定的时间段内，信号集没有产生。  
- EINTR ：被中断系统调用。  
  
- 在调用此函数时，需要先行设置超时时间。  
  
### 样例：  
  
```c  
#include <stdio.h>  
#include <stdlib.h>  
#include <string.h>  
#include <pthread.h>  
#include <signal.h>  
void *Thread2 (void *arg)  
{  
    int            ret;  
    sigset_t        set;  
    struct timespec timeout;  
    struct siginfo  info;  
    timeout.tv_nsec = 3;  
timeout.tv_sec  = 3;  
// ……  
    ret = sigtimedwait (&set, &info, &timeout);     /*  限时等待信号的到来         */  
    if (ret != -1)  
    {  
        printf ("info error :%d\n", info.si_errno);  
        printf ("info code :%d\n", info.si_code);  
        printf ("info no :%d\n", info.si_signo);  
        return (PVOID)-1;  
}  
// ……  
}  
```  
  
## sigwaitinfo() ：等待未决信号
  
  
### 函数原型：
  
```c  
INT sigwaitinfo  
(  
	const sigset_t *psigset,  
	struct siginfo *psiginfo  
);  
```  
  
  
### 描述：
  
sigwaitinfo()该接口会从psigset指定的信号集中选取未决的信号。如果调用接口时sigset_ptr中没有未决的信号，会把任务挂起，直到sigset_ptr中有信号转为未决信号或任务被一个捕捉到的非阻塞的信号中断，信号将不再被执行。  
  
### 参数：  
  
- psigset ：信号集；  
  
- psiginfo ：存放信号信息的指针。  
  
### 返回值：
  
成功返回所选信号的编号，失败返回-1，并置相应的错误码。  
  
### 错误码：
  
- EINTR ：被中断系统调用。  
- EINVAL ：参数无效。  
  
### 备注：  
  
GJB 7714-2012的错误码为：  
- EINTR ：任务收到一个非阻塞或忽略的信号。  
  
### 样例：  
  
```c  
#include <stdio.h>  
#include <stdlib.h>  
#include <string.h>  
#include <pthread.h>  
#include <signal.h>  
static pthread_t G_tid1, G_tid2;  
void *Thread2 (void *arg)  
{  
    int            ret;  
    sigset_t       set;  
    struct siginfo info;  
ret = sigwaitinfo (&set, &info);     /*  等待信号到来               */  
// ……  
    if (ret == -1)  
    {  
        perror ("func sigwaitinfo error");  
        return (PVOID)-1;  
    }  
  
    printf ("info error :%d\n", info.si_errno);  
    printf ("info code :%d\n", info.si_code);  
    printf ("info no :%d\n", info.si_signo);  
  
    // ……  
}  
```  
  
  
## sighold() ：将需要阻塞的信号加入当前任务中
   
  
### 函数原型：
  
```c  
INT sighold  
(  
	INT iSigNo  
);  
```  
  
  
### 描述：
  
sigqueue()将信号iSigNo加入到当前执行的任务的信号掩码中。  
  
### 参数：  
  
- iSigNo ：指定的信号不能为：  
  
|信号|宏值|数明|  
|---|---|---|  
|SIGILL|4|非法指令|  
|SIGABRT|6|异常结束|  
|SIGFPE|8|协处理器出错|  
|SIGKILL|9|强迫进程结束|  
|SIGBUS|10|总线错误，通常是指示一个实现定义的硬件故障|  
|SIGSEGV|11|无效内存引用|  
|SIGSTOP|17|停止进程执行|  
  
### 返回值：
  
成功返回0，失败返回-1并置相应的错误码。  
  
### 错误码：
  
- EINVAL ：参数无效。  
  
### 备注：  
  
无。  
  
### 样例：  
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <signal.h>  
void *Thread (void *arg)  
{  
	int ret;  
	void *old;  
  
    ret = sighold (SIGINT);                /*  将信号加到信号掩码中       */  
	if (ret != 0)  
	{  
		printf ("dunc sighold error\n");  
		return  (PVOID)-1;  
	}  
	// ……  
}  
```  
  
  
## sigignore() ：忽略信号
   
  
### 函数原型：
  
```c  
INT sigignore  
(  
	INT iSigNo  
);  
```  
  
  
### 描述：
  
sigignore()将当前运行的任务中指定信号的处理函数设置为SIG_IGN。  
  
### 参数：  
  
- iSigNo：指定的信号，不能指定的信号有：  
  
|信号|宏值|说明|  
|---|---|---|  
|SIGSTOP|17|停止进程执行|  
|SIGKILL|9|强迫进程结束|  
  
### 返回值：
  
成功返回0，失败返回-1；并置相应的错误码。  
  
### 错误码：
  
- EINVAL：参数无效。  
  
### 备注：  
  
无。  
  
### 样例：  
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <signal.h>  
static pthread_t G_tid1, G_tid2, G_tid3;  
void *Thread1 (void *arg)  
{  
    int     ret;  
    void *old;  
  
    // ……  
  
    ret = sigignore (SIGHUP);             /*  忽略信号                   */  
    if (ret != 0)  
    {  
        printf("func sigignore error\n");  
        return  (PVOID)-1;  
}  
  
    // ……  
}  
```  
  
  
## sigpasue() ：将信号从调用任务的信号掩码中删除，将任务挂起
   
  
### 函数原型：
  
```c  
INT sigpause  
(  
	INT iSigMask  
);  
```  
  
  
### 描述：
  
sigpause()将信号iSigMask从调用任务的信号掩码中删除，并将调用任务挂起，直至调用任务接收到一个信号。sigpause()返回前，恢复调用任务信号掩码的初始状态。  
  
### 参数：  
  
- iSigNo：信号编号，范围[1，31]。  
  
### 返回值：
  
成功返回0；失败返回-1，并置相应的错误码。  
  
### 错误码：
  
- EINTER ：被中断系统调用。  
- EINVAL ：参数无效。  
- ERROR_THREAD_OTHER_DELETE ：已经有其他线程在等待删除。  
  
### 备注：  
  
SylixOS中iSigMask为信号掩码。  
GJB 7714-2012中iSigMask参数为某个信号编号。  
- GJB 7714-2012错误码为：  
- EINVAL ：参数无效。  
  
### 样例：  
  
```c  
无
```  
  
## sigrelse() ：将信号从调用任务的信号掩码中删除
   
  
### 函数原型：
  
```c  
INT sigrelse  
(  
	INT iSigNo  
);  
```  
  
  
### 描述：
  
sigrelse()将信号iSigNo从调用任务的信号掩码中删除。  
  
### 参数：  
  
- iSigNo：指定的信号。  
  
### 返回值：
  
成功返回0，失败返回-1并置相应的错误码。  
  
### 错误码：
  
- EINVAL：参数无效。  
  
### 备注：  
  
无。  
  
### 样例：  
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <signal.h>  
void *Thread (void *arg)  
{  
	int ret;  
	sigset_t set;  
	int mask;  
	void *old;  
  
	// ……  
    ret = sigrelse (SIGINT);         /*  将信号从掩码中删除         */  
	if (ret != 0)  
	{  
		printf ("func sigrelse error\n");  
		return  (PVOID)-1;  
	}  
  
	// ……  
}  
```  
  
  
## sigaddset() ：将指定信号添加到指定信号集中
   
  
### 函数原型：
  
```c  
INT sigaddset  
(  
	sigset_t *psigset,  
	INT iSigNo  
);  
```  
  
  
### 描述：
  
将 iSigNo 指定的信号添加到 psigset 指定的信号集中。  
  
### 参数：  
  
- psigset ：信号集。  
  
- iSigNo ：要添加的信号。  
  
### 返回值：
  
成功返回 0；失败返回-1，并置相应的错误码。  
  
### 错误码：
  
- EINVAL：参数无效。  
  
### 备注：  
  
注意参数类型。  
  
### 样例：  
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <signal.h>  
#include <string.h>  
void *Thread (void *arg)  
{  
	int ret;  
	sigset_t set, oset, pend;  
  
	// ……  
  
    ret = sigaddset (&set, SIGINT);        /*  向信号集中添加信号         */  
	if (ret != 0)  
	{  
		printf ("func sigaddset error\n");  
		return (PVOID)-1;  
	}  
	// ……  
}  
```  
  
  
## sigdelset() ：将指定信号从指定信号集中删除
   
  
### 函数原型：
  
```c  
INT sigdelset  
(  
	sigset_t *psigset,  
	INT iSigNo  
);  
```  
  
  
### 描述：
  
该接口将 iSigNo 指定的信号从 psigset 指定的信号集中删除。  
  
### 参数：  
  
- psigset ：信号集。  
  
- iSigNo ：要添加的信号。  
  
### 返回值：
  
成功返回 0；失败返回-1，并置相应的错误码。  
  
### 错误码：
  
- EINVAL：参数无效。  
  
### 备注：  
  
注意参数类型。  
  
### 样例：  
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <signal.h>  
  
void *Thread (void *arg)  
{  
  
    int      ret;  
    sigset_t set;  
  
    // ……  
  
    ret = sigdelset (&set, SIGHUP);              /*  将信号从信号集中删除       */  
    if (ret != 0)  
    {  
        printf ("func sigdelset error\n");  
        return  (PVOID)-1;  
    }  
  
    // ……  
}  
```  
  
  
## sigemptyset() ：置空信号集
   
  
### 函数原型：
  
```c  
INT sigemptyset  
(  
	sigset_t *psigset  
);  
```  
  
  
### 描述：
  
该接口将对指定的信号集进行初始化，并将信号集置空。  
  
### 参数：  
  
- psigset ：信号集。  
  
### 返回值：
  
函数返回0。  
  
### 错误码：
  
无。  
  
### 备注：  
  
GJB 7714-2012中函数成功返回0，失败返回-1。  
  
### 样例：  
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <signal.h>  
#include <string.h>  
void *Thread (void *arg)  
{  
  
	int ret;  
	sigset_t set, oset, pend;  
  
    ret = sigemptyset (&set);            /*  清空信号集                 */  
	if (ret != 0)  
	{  
		printf ("func sigemptyset error\n");  
		return (PVOID)-1;  
	}  
  
    // ……  
}  
	// ……  
```  
  
  
## sigfillset() ：置满信号集
   
  
### 函数原型：
  
```c  
INT sigfillset  
(  
	sigset_t *psigset  
);  
```  
  
  
### 描述：
  
该接口将psigset 指向的信号集初始化，并将信号集置满。  
  
### 参数：  
  
- psigset ：信号集。  
  
### 返回值：
  
函数返回0。  
  
### 错误码：
  
无。  
  
### 备注：  
  
GJB 7714-2012中函数成功返回0，失败返回-1。  
  
### 样例：  
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <signal.h>  
void *Thread (void *arg)  
{  
  
    int      ret;  
    sigset_t set;  
  
    ret = sigfillset (&set);           /*  置满信号集                 */  
    if (ret != 0)  
    {  
        printf ("func sigfillset error\n");  
        return  (PVOID)-1;  
    }  
  
    // ……  
}  
	// ……  
```  
  
  
## sigismember() ：检测信号是否为指定信号集中的一员
   
  
### 函数原型：
  
```c  
INT sigismember  
(  
	const sigset_t *psigset,  
	INT iSigNo  
);  
```  
  
  
### 描述：
  
sigismember()用于检测信号iSigNo是否为psigset指向的信号集中的成员。  
  
### 参数：  
  
- psigset ：信号集；  
  
- iSigNo ：指定的信号。  
  
### 返回值：
  
成功返回0或1，1代表信号集包含此信号，0表示不包含；失败返回-1，并置相应的错误码。  
  
### 错误码：
  
- EINVAL：参数无效。  
  
### 备注：  
  
GJB 7714-2012中函数成功返回1，失败返回-1。  
  
### 样例：  
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <signal.h>  
void *Thread (void *arg)  
{  
  
    int      ret;  
    sigset_t set;  
  
    // ……  
  
    ret = sigismember (&set, SIGHUP);       /*  检测信号是否在信号集中     */  
    if (ret == 1)  
    {  
        printf ("argument in !\n");  
    }  
    else if (ret == 0)  
    {  
        printf ("argument desn't in !\n");  
    }  
    else  
    {  
        printf ("func sigismember error %d\n", ret);  
    }  
  
    // ……  
}  
```  
  
  
## sigaction() ：检测或设置信号的处理函数
   
  
### 函数原型：
  
```c  
INT sigaction  
(  
	INT iSigNo,  
	const struct sigaction *psigactionNew,  
	struct sigaction *psigactionOld  
);  
```  
  
### 描述：
  
该接口允许调用任务检测和或指定信号的相关行为。如果该接口调用失败，则信号的相关行为不会改变。  
  
### 参数：  
  
- iSigNo ：信息编号；  
  
- psigactionNew ：新的信号处理函数指针，如果取值为0，该接口不会改变制定信号的行为。  
  
- psigactionOld ：保存原信号处理函数的指针，如果不是空指针，指定信号之前的相关行为将保存在psigactionOld指向的位置。  
  
### 返回值：
  
成功返回0，失败返回相应的错误码。  
  
### 错误码：
  
- EINVAL：参数无效。  
  
### 备注：  
  
可以通过改变sa_flag的值来改变相关信号的行为：  
  
|Sa_flag参数|宏值|描述|  
|---|---|---|  
|SA_NOCLDSTOP|0x00000001|子进程被删除时不要产生信号|  
|SA_NOCLDWAIT|0x00000008|不产生僵尸进程|  
|SA_SIGINFO|0x00000002|信号句柄需要 siginfo 参数|  
|SA_ONSTACK|0x00000004|自定义堆栈|  
|SA_RESTART|0x10000000|执行信号句柄后, 重启调用|  
|SA_INTERRUPT|0x20000000||  
|SA_NOMASK|0x40000000|不阻止在指定信号处理句柄中再收到信号|  
|SA_NODEFER|SA_NOMASK||  
|SA_ONESHOT|0x80000000|信号句柄一旦被调用就恢复到默认状态|  
|SA_RESETHAND|SA_ONESHOT|执行句柄后, 将信号句柄设置为|  
  
### 样例：  
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <signal.h>  
void *Thread (void *arg)  
{  
    int                 ret;  
    struct sigaction     act, oact;  
    act.sa_flags = 0;  
    act._u._sa_handler = handle;  
  
    // ……  
  
    ret = sigaction (SIGHUP, &act, NULL);  
    if (ret != 0)  
    {  
        printf ("func aigaction error\n");  
        return  (PVOID)-1;  
    }  
  
    // ……  
}  
	// ……  
```  
  
  
## signal() ：设置信号处理函数
   
  
### 函数原型：
  
```c  
PSIGNAL_HANDLE signal  
(  
	INT iSigNo,  
	PSIGNAL_HANDLE pfuncHandler  
);  
```  
  
  
### 描述：
  
signal()会选择以下三种方法中的一种来处理接收到的信号iSigNo：  
pfuncHandler 值为 SIG_IGN 信号会被忽略；  
pfuncHandler 值为SIG_DFL 对信号做默认的方式处理；  
或者为一个函数指针，pfuncHandler指向的函数被调用。  
  
### 参数：  
  
- iSigNo ：信号，不能屏蔽SIGKILL  
  
- pfuncHandler ：取值如下：  
  
|种类|说明|  
|---|---|  
|SIG_IGN|将信号忽略|  
|SIG_DFL|做默认处理方式|  
|处理函数|处理函数被调用|  
  
### 返回值：
  
成功返回信号处理函数的句柄；失败返回-1，并置相应的错误码。  
  
### 错误码：
  
- EINVAL：参数无效。  
  
### 备注：  
  
该接口不能屏蔽的信号：SIGKILL(9),  
  
### 样例：  
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <signal.h>  
void *Thread1 (void *arg)  
{  
  
    int     ret;  
    void*     old;  
  
    old = signal (SIGINT, handle);          /*  设置信号处理函数           */  
    if (old == SIG_ERR)  
    {  
        printf("func signal error\n");  
        return  (PVOID)-1;  
    }  
  
    // ……  
}  
	// ……  
  
```  
  
  
## kill() ：向任务发送信号，不允许信号带参数
  
  
### 函数原型：
  
```c  
INT kill  
(  
	LW_OBJECT_HANDLE ulId,  
	INT iSigNo  
);  
```  
  
  
### 描述：
  
该接口用于向任务发送信号。要发送的信号有 iSigNo 指定，如果 iSigNo 为0（空信号），会执行错误检验，但不发送信号，可以用来检验 ulId 的有效性。  
如果认为 ulId 没有阻塞 iSigNo，那么在接口返回之前，信号 iSigNo 会被传递给任务ulID。如果失败，则信号不发送。  
  
### 参数：  
  
- ulId ：任务ID。  
  
- iSigNo ：要发送的信号。  
  
|信号类型|宏值|描述|  
|---|---|---|  
|SIGHUP|1|挂断控制终端或进程|  
|SIGINT|2|来自键盘的中断|  
|SIGQUIT|3|来自键盘的退出|  
|SIGILL|4|非法指令|  
|SIGTRAP|5|跟踪断点|  
|SIGABRT|6|异常结束|  
|SIGUNUSED|7|没有使用|  
|SIGFPE|8|协处理器出错|  
|SIGKILL|9|强迫进程结束|  
|SIGBUS|10|总线错误，通常是指示一个实现定义的硬件故障|  
|SIGSEGV|11|无效内存引用|  
|SIGUNUSED2|12|暂时没有使用2|  
|SIGPIPE|13|管道写出错，无读者|  
|SIGALRM|14|实时定时器报警|  
|SIGTERM|15|进程中止|  
|SIGCNCL|16|线程取消信号|  
|SIGSTOP|17|停止进程执行|  
|SIGTSTP|18|tty发出停止进程|  
|SIGCONT|19|恢复进程继续执行|  
|SIGCHLD|20|子进程停止或被中止|  
|SIGTTIN|21|后台进程请求输入|  
|SIGTTOU|22|后台进程请求输出|  
|SIGCANCEL|SIGTERM|同 SIGTERM 相同|  
|SIGIO|23|异步 I/O 事件|  
|SIGXCPU|24|进程超出了软 CPU 事件限制|  
|SIGXFSZ|25|进程超出了软文件长度限制|  
|SIGUTALRM|26|函数 setitimer 设置的虚拟间隔定时器已经超时|  
|SIGPROF|27|函数 setitimer 设置的梗概间隔定时器已经超时|  
|SIGWINCH|28|更改了窗口的大小|  
|SIGINFO|29|信息请求|  
|SIGPOLL|SIGIO|同 SIGIO 相同|  
|SIGUSR1|30|用户定义信号 1|  
|SIGUSR2|31|用户定义信号 2|  
|SIGPWR|33|电源失败重新开始|  
|SIGSYS|34|错误的系统调用|  
|SIGURG|35|网络连接上接到带外的数据时，可选择地产生此信号|  
|SIGRTMIN-SIGRTMAX|48-63|实时信号范围|  
  
### 返回值：
  
成功返回0；失败返回-1，并置相应的错误码。  
  
### 错误码：
  
- ESRCH ：任务不存在。  
- EINVAL ：参数无效。  
- ERROR_THREAD_OTHER_DELETE ：已经有其他线程在等待删除。  
  
### 备注：  
  
GJB 7714-2012 错误码为：  
- EINVAL ：参数无效。  
- ESRCH ：线程不存在。  
- EAGAIN ：系统资源不足。  
  
### 样例：  
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <signal.h>  
static pthread_t G_tid1, G_tid2, G_tid3;  
void *Thread2 (void *arg)  
{  
    void *old;  
    sigset_t set;  
    int ret;  
  
    // ……  
  
    ret = kill (G_tid1, SIGHUP);              /*  向指定线程发送信号         */  
    if (ret != 0)  
    {  
        printf("func kill error\n");  
        return  (PVOID)-1;  
    }  
  
    // ……  
}  
```  
  
## raise() ：把信号发送到当前任务
   
  
### 函数原型：
  
```c  
INT raise  
(  
	INT iSigNo  
);  
```  
  
  
### 描述：
  
raise()该接口把信号发送到当前任务。如果一个信号处理函数被调用，那么raise()将在信号处理函数返回后再返回。  
  
### 参数：  
  
- iSigNo ：要发送的信号。  
  
### 返回值：
  
成功返回0；失败返回-1，并置相应的错误码。  
  
### 错误码：
  
- ESRCH ：任务不存在。  
- EINVAL ：参数无效。  
  
### 备注：  
  
GJB 7714-2012 错误码为：  
- EINVAL ：参数无效。  
  
### 样例：  
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <signal.h>  
void *Thread (void *arg)  
{  
  
	int 	ret;  
	// ……  
  
    ret = raise (SIGINT);            /*  向当前任务发送信号         */  
	if (ret != 0)  
	{  
		printf("func raise error\n");  
		return  (PVOID)-1;  
	}  
  
	// ……  
}  
```  
  
  
## sigprocmask() ：检查或改变当前任务的信号掩码
   
  
### 函数原型：
  
```c  
INT sigprocmask  
(  
	INT iHow,  
	const sigset_t *sigset,  
	sigset_t *sigsetOld  
);  
```  
  
  
### 描述：
  
该接口用来检查或改变当前任务的信号掩码，在该接口调用时，如果有未阻塞的信号处于未决状态，那么该接口返回前会处理被未决的信号。  
  
该接口不能屏蔽的信号有：  
  
|信号种类|宏值|说明|  
|---|---|---|  
|SIGILL|4|非法指令|  
|SIGABRT|6|异常结束|  
|SIGFPE|8|协处理器出错|  
|SIGKILL|9|强迫进程结束|  
|SIGBUS|10|总线错误，通常是指示一个实现定义的硬件故障|  
|SIGSEGV|11|无效内存引用|  
|SIGSTOP|17|停止进程执行|  
  
### 参数：
  
- iHow ：其取值决定了改接口对信号集的处理方式：  
  
|处理方式|说明|  
|---|---|  
|SIG_BLOCK|将 sigset 中的信号添加到任务的阻塞信号集中；|  
|SIG_UNBLOCK|从任务的阻塞信号集中去除 sigset 中的信号；|  
|SIG_SETMASK|重新设置任务的阻塞信号集，设置为 sigset 中的信号。|  
  
- sigset ：指向新信号掩码的指针，为空，则 iHow 不起作用，用来得到原信号掩码。  
  
- sigsetOld ：保存原信号掩码的变量指针。  
  
### 返回值：
  
成功返回0；失败返回-1，并置相应的错误码。  
  
### 错误码：
  
- EINVAL：参数无效。  
  
### 备注：  
  
注意设置iHow 的三种不同工作方式。  
  
### 样例：  
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <signal.h>  
void *Thread1 (void *arg)  
{  
  
	int      ret;  
	sigset_t set;  
	int      mask;  
	// ……  
  
    ret = sigprocmask (SIG_BLOCK, &set,NULL);   /*  设置新的信号掩码集         */  
	if (ret != 0)  
	{  
		printf ("func sigprocmask error\n");  
		return  (PVOID)-1;  
	}  
    // ……  
}  
```  
  
  
## bsd_signal() ：设置指定信号的处理函数
   
  
### 函数原型：
  
```c  
sighandler_t    bsd_signal  
(  
	INT   iSigNo,   
	PSIGNAL_HANDLE  pfuncHandler  
);  
```  
  
### 描述：
  
该接口与signal()接口功能相同。  
pfuncHandler 值为 SIG_IGN 信号会被忽略；pfuncHandler 值为SIG_DFL对信号做默认的方式处理；或者为一个函数指针，pfuncHandler指向的函数被调用。  
  
### 参数：  
  
- iSigNo ：信号  
  
- pfuncHandler ：取值如下：  
  
|信号种类|说明|  
|---|---|  
|SIG_IGN|将信号忽略|  
|SIG_DFL|做默认处理方式|  
|处理函数|处理函数被调用|  
  
### 返回值：
  
成功返回信号处理函数的句柄；失败返回-1，并置相应的错误码。  
  
### 错误码：
  
- EINVAL：参数无效。  
  
### 备注：  
  
功能与signal()相同，只是signal()为void类型，bsd_signal()为信号处理句柄函数类型。  
  
### 样例：  
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <signal.h>  
void *Thread1 (void *arg)  
{  
  
    int   ret;  
    void* old;  
  
    old = bsd_signal (SIGINT, handle);  /*  设置信号处理函数           */  
    if (old == SIG_ERR)  
    {  
        printf ("func signal error\n");  
        return  (PVOID)-1;  
    }  
  
	// ……  
}  
```  
  
  
## sigmask() ：通过信号的值来获取信号掩码
   
  
### 函数原型：
  
```c  
INT   sigmask  
(  
	INT  iSigNo  
);  
```  
  
  
### 描述：
  
该接口通过一个指定信号来获取一个信号掩码。  
  
### 参数：  
  
- iSigNo：信号  
  
### 返回值：
  
成功返回相应的信号掩码；失败返回0，并置相应的错误码。  
  
### 错误码：
  
- EINVAL：参数无效。  
  
### 备注：  
  
建议使用sigprocmask()接口。  
  
### 样例：  
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <signal.h>  
void *Thread (void *arg)  
{  
	int      ret;  
  
    ret = sigmask (SIGSTOP);         /*  获取信号掩码               */  
    if (ret == 0)  
    {  
		printf ("func sigmask error\n");  
		return (PVOID)-1;  
	}  
  
	// ……  
}  
```  
  
  
## siggetmask() ：获取当前线程的信号掩码
   
### 函数原型：
  
```c  
INT    siggetmask  
(  
	VOID  
);  
```  
  
  
### 描述：
  
该接口用来获取当前线程的信号掩码。  
  
### 参数：  
  
无  
  
### 返回值：
  
成功返回信号掩码；失败返回-1，并置相应的错误码。  
  
### 错误码：
  
- EINVAL：参数无效。  
  
### 备注：  
  
无  
  
### 样例：  
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <signal.h>  
void *Thread (void *arg)  
{  
  
	int      ret;  
	sigset_t set;  
	int      mask;  
	// ……  
  
	mask = siggetmask();  
	printf ("get mask is :%d\n", mask);  
	if ( (mask & SIGINT) )  
	{  
		printf ("sigprocmask set SIG_BLOCK success !\n");  
	}  
  
	// ……  
}  
  
```  
  
  
## sigsetmask() ：设置当前任务的信号掩码
   
  
### 函数原型：
  
```c  
INT      sigsetmask  
(  
	INT  iMask  
);  
```  
  
  
### 描述：
  
该接口用来设置当前任务的信号掩码。  
  
### 参数：  
  
- iMask：信号集。  
  
### 返回值：
  
成功返回0；失败返回-1，并置相应的错误码。  
  
### 错误码：
  
- EINVAL：参数无效。  
  
### 备注：  
  
无  
  
### 样例：  
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <signal.h>  
void *Thread1 (void *arg)  
{  
    int      ret;  
    sigset_t set;  
    int      mask;  
    // ……  
  
    ret = sigsetmask (set);          /*  给任务设置新的掩码         */  
    if (ret != 0)  
    {  
        printf ("func sigsetmask error\n");  
        return  (PVOID)-1;  
    }  
  
    // ……  
}  
```  
  
  
## sigblock() ： 将新的需要阻塞的信号添加到当前任务
   
  
### 函数原型：
  
```c  
INT             sigblock  
(  
	INT    iBlock  
);  
```  
  
  
### 描述：
  
该接口用来将新的需要阻塞的信号添加到当前任务中。  
  
### 参数：  
  
- iBlock：新的阻塞信号掩码。  
  
### 返回值：
  
成功返回0；失败返回-1，并置相应的错误码。  
  
### 错误码：
  
- EINVAL：参数无效。  
  
### 备注：  
  
无  
  
### 样例：  
  
```c  
无
```  
  
  
## sigvec() ：设置信号的处理函数
   
  
### 函数原型：
  
```c  
INT             sigvec  
(  
	INT  iSigNo,  
	const struct sigvec *pvec,   
	struct sigvec *pvecOld  
);  
```  
  
  
### 描述：
  
该接口允许调用任务指定信号的相关行为。  
  
如果该接口调用失败，则信号的相关行为不会改变。  
  
### 参数：  
  
- iSigNo ：信号；  
  
- pvec ：新的处理结构；  
  
- pvecOld：旧的处理结构。  
  
### 返回值：
  
成功返回0；失败返回-1，并置相应的错误码。  
  
### 错误码：
  
- EINVAL：参数无效。  
  
### 备注：  
  
无。  
  
### 样例：  
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <signal.h>  
void *Thread (void *arg)  
{  
    int             ret;  
    struct sigvec     act, oact;  
  
    act.sv_handler = handle;  
    act.sv_flags = 0;  
    // ……  
  
    ret = sigvec (SIGHUP, &act, &oact);       /*  设置信号处理函数           */  
    if (ret != 0)  
    {  
        printf ("func sigvec error\n");  
        return  (PVOID)-1;  
    }  
    // ……  
}  
  
```  
  
  
## sigpending() ：获取当前信号集中未处理的信号
   
  
### 函数原型：
  
```c  
INT sigpending  
(  
	sigset_t  *sigset  
);  
```  
  
  
### 描述：
  
该接口用来获取当前任务中被阻塞而没有得到处理的信号，并将这些信号放在由sigset指定的信号集中。  
  
### 参数：  
  
- sigset：信号集。  
  
### 返回值：
  
成功返回0，失败返回-1。  
  
### 错误码：
  
无。  
  
### 备注：  
  
无。  
  
### 样例：  
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <signal.h>  
#include <string.h>  
void *Thread (void *arg)  
{  
  
    int ret;  
    sigset_t set, oset, pend;  
  
    // ……  
    ret = sigpending (&pend);  
    if (ret != 0)  
    {  
        printf ("func sigpending error\n");  
        return (PVOID)-1;  
    }  
    printf ("pending set is :%llu\n", pend);  
  
    // ……  
}  
	// ……  
  
```  
  
## pause() ：等待信号的到来
  
  
### 函数原型：
  
```c  
INT pause  
(  
	VOID  
);  
```  
  
### 描述：
  
该接口用来等待一个信号的到来，如果没有接收到信号则任务会挂起。  
  
### 参数：  
  
无  
  
### 返回值：
  
成功执行信号处理函数；失败返回-1，并置相应的错误码。  
  
### 错误码：
  
- EINTR：被中断系统调用。  
  
### 备注：  
  
无。  
  
### 样例：  
  
```c  
#include <stdio.h>  
#include <stdlib.h>  
#include <string.h>  
#include <pthread.h>  
#include <signal.h>  
static pthread_t G_tid1, G_tid2;  
void *Thread2 (void *arg)  
{  
    int   ret;  
    // ……  
  
    ret = pause ();         /*  等待未决信号               */  
    if (ret != -1)  
    {  
        printf ("func pause error\n");  
        return (PVOID)-1;  
    }  
  
    // ……  
}  
```  
  
  
## pthread_kill() ：向指定posix线程发送信号
  
  
### 函数原型：
  
```c  
int pthread_kill  
(  
	pthread_t  thread,   
	int signo  
);  
```  
  
  
### 描述：
  
该接口用于向任务发送信号。要发送的信号有 signo 指定，如果 signo 为 0（空信号），会执行错误检验，但不发送信号。  
  
### 参数：  
  
- thread：线程句柄；  
  
- signo： 信号。  
  
### 返回值：
  
成功返回0；失败返回-1，并置相应的错误码。  
  
### 错误码：
  
- ESRCH：任务不存在。  
  
### 备注：  
  
无。  
  
### 样例：  
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <signal.h>  
static pthread_t G_tid1, G_tid2, G_tid3;  
void *Thread3 (void *arg)  
{  
    int     ret;  
  
    // ……  
    ret = pthread_kill (G_tid2, SIGHUP);  
    if (ret != 0)  
    {  
        printf("func kill error\n");  
        return  (PVOID)-1;  
    }  
  
    // ……  
}  
```  
  
  
## pthread_sigmask() ：修改posix线程信号掩码
  
  
### 函数原型：
  
```c  
int pthread_sigmask  
(  
	int  how,  
	const sigset_t  *newmask,  
	sigset_t *oldmask  
);  
```  
  
  
### 描述：
  
该接口用来修改posix线程的信号掩码，在该接口调用时，如果有未阻塞的信号处于未决状态，那么该接口返回前会处理未决信号。  
  
该接口不能屏蔽的信号有：  
  
|信号种类|宏值|说明|  
|---|---|---|  
|SIGILL|4|非法指令|  
|SIGABRT|6|异常结束|  
|SIGFPE|8|协处理器出错|  
|SIGKILL|9|强迫进程结束|  
|SIGBUS|10|总线错误，通常是指示一个实现定义的硬件故障|  
|SIGSEGV|11|无效内存引用|  
|SIGSTOP|17|停止进程执行|  
  
### 参数：  
  
- how ：其取值决定了改接口对信号集的处理方式：  
  
|处理方式|说明|  
|---|---|  
|SIG_BLOCK|将 sigset 中的信号添加到任务的阻塞信号集中；|  
|SIG_UNBLOCK|从任务的阻塞信号集中去除 sigset 中的信号；|  
|SIG_SETMASK|重新设置任务的阻塞信号集，设置为 sigset 中的信号。|  
  
- newmask ：指向新信号掩码的指针，为空，则 how 不起作用，用来得到原信号掩码。  
  
- oldmask ：保存原信号掩码的变量指针。  
  
### 返回值：
  
成功返回0；失败返回-1，并置相应的错误码。  
  
### 错误码：
  
- EINVAL：参数无效。  
  
### 备注：  
  
注意设置how 的三种不同工作方式。  
  
### 样例：  
  
```c  
#include <stdio.h>  
#include <pthread.h>  
#include <signal.h>  
void *Thread1 (void *arg)  
{  
    int      ret;  
    sigset_t set;  
    int      mask;  
    // ……  
    ret = pthread_sigmask (SIG_BLOCK, &set,NULL);     /*  修改掩码         */  
    if (ret != 0)  
    {  
        printf ("func sigprocmask error\n");  
        return  (PVOID)-1;  
    }  
  
    // ……  
}  
```  
  
  
## siginterrupt() ：改变信号SA_RESTART选项
  
  
### 函数原型：
  
```c  
INT             siginterrupt  
(  
	INT  iSigNo,  
	INT  iFlag  
);  
```  
  
  
### 描述：
  
该接口用来改变信号的SA_RESTART选项。  
  
### 参数：  
  
- iSigNo：信号；  
  
- iFlag： 标志，1表示能够重启调用，0表示不能够重启调用。  
  
### 返回值：
  
成功返回0，失败返回-1，并置相应的错误码。  
  
### 错误码：
  
- EINVAL：参数无效  
  
### 备注：  
  
无。  
  
### 样例：  
  
```c  
无
```  
  
# 中断异常  
  
  
## int_install_handler： 安装中断处理函数
  
  
### 函数原型：
  
```c  
int   Int_install_handler  
(  
	const char  *name,   
	int         vecnum,   
	int         prio,   
	void       (*handler)(void *),  
	void       *param  
);  
```  
  
  
### 描述：
  
该接口用来安装中断处理函数，使得相关硬件产生中断后由安装的中断函数在中断线程环境中处理。HYSylixoS默认支持256个中断源，也就是支持256个中断向量。具体使用哪个中断向量，可通过阅读芯片手册获得。  
  
### 参数：
  
- name ：中断名称。  
  
- vecnum ：中断向量值。  
  
- prio ：中断优先级。（没有使用）  
  
- handler ：中断处理函数。  
  
- param ：每次中断要传人的参数。  
  
### 返回值：
  
成功返回0，失败返回相应的错误码。  
  
### 错误码： 
  
- EACCES ：权限不足（需要进程ID为0）。  
- EINVAL ：参数无效。  
- ENOMEM ：内存不足。  
  
### 样例：
  
```c  
int module_init (void)  
{  
    int ret = 0;  
    // ……  
    ret = int_install_handler("init_time2", irq, 20, Timer2Handle, NULL);  
    if(ret != 0)  
    {  
         printk("func int_install_handler error\n");  
         return -1;  
    }  
    // ……  
    return 0;  
}  
```  
  
  
## int_unstall_handler ：卸载中断处理函数
  
  
### 函数原型：
  
```c  
int    int_uninstall_handler  
(  
	int   vecnum  
);  
```  
  
  
### 描述：
  
该接口用来卸载中断处理函数，并屏蔽相关中断。  
  
### 参数：
  
- vecnum ：系统规定的中断向量号。  
  
### 返回值：
  
成功返回0，失败返回相应的错误码。  
  
### 错误码：
  
- EACCES ：权限不足（需要进程ID为0）。  
- EINVAL ：参数无效。  
  
### 样例：
  
```c  
void module_exit (void)  
{  
	int ret;  
	// ……  
  
    ret = int_uninstall_handler(irq);  
    if(ret != 0)  
    {  
         printk("func int_uninstall_handler error\n");  
	}  
  
	// ……  
}  
```  
  
  
## int_lock：锁中断
  
  
### 函数原型：
  
```c  
int     int_lock  
(  
	void  
);  
```  
  
### 描述：
  
该接口在当前任务下锁中断，从而实现屏蔽系统中断，此接口可以嵌套使用。  
  
### 参数：
  
无。  
  
### 返回值：
  
成功返回中断掩码。  
  
### 错误码： 
  
无。  
  
### 样例：
  
```c  
static void Timer2Handle(void *arg)  
{  
    int     level;  
  
    // ……  
  
    level = int_lock();  
  
    // ……  
  
	int_unlock(level);  
}  
```  
  
  
## int_unlock：解锁中断
  
  
### 函数原型：
  
```c  
void  int_unlock   
(  
	int level  
);  
```  
  
  
### 描述：
  
该接口在当前任务下解锁中断，重新使能系统中断。  
  
### 参数：
  
- level ：中断掩码。  
  
### 返回值：
  
无。  
  
### 错误码： 
  
无。  
  
### 样例：
  
```c  
static void Timer2Handle(void *arg)  
{  
    int     level;  
  
    // ……  
  
    level = int_lock();  
  
    // ……  
  
	int_unlock(level);  
}  
```  
  
  
## int_enable_pic ：使能指定中断号的中断
  
  
### 函数原型：
  
```c  
void  int_enable_pic   
(  
	uint32_t irq  
);  
```  
  
  
### 描述：
  
该接口使能指定中断号的中断。  
  
### 参数：
  
- irq ：要使能的中断向量值。  
  
### 返回值：
  
无。  
  
### 错误码： 
  
- ERROR_KERNEL_VECTOR_NULL ：中断向量出错。  
  
### 样例：
  
```c  
int module_init (void)  
{  
    // ……  
    int_enable_pic(irq);  
    // ……  
    return 0;  
}  
```  
  
  
## int_disable_pic：屏蔽指定中断号的中断
  
  
### 函数原型：
  
```c  
void  int_disable_pic   
(  
	uint32_t irq  
);  
```  
  
  
### 描述：
  
该接口屏蔽指定中断号的中断，使该中断不能发生。  
  
### 参数：
  
- irq ：屏蔽的中断向量值。  
  
### 返回值：
  
无。  
  
### 错误码： 
  
无。  
  
### 样例：
  
```c  
void module_exit (void)  
{  
    // ……  
    int_disable_pic(irq);  
    // ……  
}  
```  
  
  
## exception_handler_set：设置用户异常处理函数
  
  
### 函数原型：
  
```c  
EXC_HANDLER  exception_handler_set   
(  
	EXC_HANDLER exc_handler  
);  
```  
  
  
### 描述：
  
该接口设置异常处理句柄，发生异常时通过设置的处理函数进行异常处理。  
  
### 参数：
  
- exc_handler ：异常处理函数。  
  
### 返回值：
  
返回线程的处理函数。  
  
### 错误码：
  
- ENOSYS ：函数不支持此操作。  
  
### 样例：
  
```c  
无
```  
  
  
## shared_int_install ：安装共享中断的处理函数
  
  
### 函数原型：
  
```c  
#include <pthread.h>  
int  	shared_int_install  
(  
	int     vecnum,   
	void  (*handler)(void *),   
	void   *param  
);  
```  
  
### 描述：
  
该接口用来设置共享处理函数，使得相关硬件产身该中断后调用安装的所有相关处理函数。  
  
### 参数：
  
- vecnum ：系统规定的中断向量号。  
  
- handler ：要安装的中断处理函数，不能为空。  
  
- param ：调用中断处理函数时要传人的参数。  
  
### 返回值：
  
成功返回0，失败返回-1，并置相应的错误码。  
  
### 错误码： 
  
- EACCES ：权限不足（需要进程ID为0）。  
- EINVAL ：参数无效。  
- ENOMEM ：内存不足。  
  
### 样例：
  
```c  
int module_init (void)  
{  
int ret = 0;  
  
    // ……  
  
    ret = shared_int_install(irq, ShareHandle, NULL);  
    if(ret != 0)  
    {  
        printk("func int_install_handler error\n");  
        return -1;  
}  
// ……  
  
    return 0;  
}  
```  
  
  
## shared_int_unstall ：卸载共享中断的处理函数
  
  
### 函数原型：
  
```c  
int     shared_int_uninstall  
(  
	int     vecnum,  
	void  (*handler)(void *)  
);  
```  
  
### 描述：
  
该接口用来卸载共享中断处理函数，当同一中断所有共享函数都卸载时，屏蔽该中断。  
  
### 参数：
  
- vecnum : 系统规定的中断向量号。  
  
- handler ：已安装的共享中断函数。  
  
### 返回值：
  
成功返回0，失败返回-1，并置相应的错误码。  
  
### 错误码： 
  
- EACCES ：权限不足（需要进程ID为0）。  
- EINVAL ：参数无效。  
  
### 样例：
  
```c  
void module_exit (void)  
{  
    int ret;  
  
    ret = shared_int_uninstall(irq, ShareHandle);  
    if(ret != 0 )  
    {  
        printk("shared_int_uninstall is error\n");  
	}  
	// ……  
}  
```  
  
# 时钟  
  
  
## Tick_Get ：获得系统时钟tick计数值
  
### 函数原型：
  
```c  
#include <pthread.h>  
uint64_t  tick_get  
(  
	void  
);  
```  
  
### 描述：
  
该接口返回系统时钟滴答计时器的当前计数值。  
  
### 参数：  
  
无。  
  
### 返回值：
  
返回系统时钟滴答计数值。  
  
### 错误码：
  
无。  
  
### 备注：  
  
GJB 7714-2012错误码：无。  
  
### 样例：  
  
```c  
#include <stdio.h>  
#include <pthread.h>  
int main (int argc, char **argv)  
{  
	uint64_t tick;  
	tick = tick_get();                          /* 获取系统tick数             */  
	printf("system tick num at present :%llu\n", tick);  
	// ……  
	return (0);  
}  
```  
  
  
## Tick_Set ：设置系统时钟tick计数值
  
  
### 函数原型：
  
```c  
#include <pthread.h>  
void  tick_set  
(  
	uint64_t ticks  
);  
```  
  
  
### 描述：
  
该接口将系统时钟滴答计数器设置为指定的值。  
  
### 参数：  
  
- ticks ：系统时钟滴答计数值。  
  
### 返回值：
  
无。  
  
### 错误码：
  
- ENOTSUP ：系统不支持的操作。  
  
### 备注：  
  
GJB 7714-2012错误码：无。  
  
### 样例：  
  
```c  
无
```  
  
  
## sys_clk_rate_get ：获取系统时钟频率（每秒节拍数）
  
  
### 函数原型：
  
```c  
#include <pthread.h>  
uint32_t  sys_clk_rate_get  
(  
	void  
);  
```  
  
  
### 描述：
  
该接口用于获取系统时钟频率，频率值为系统时钟每秒的时钟滴答数。  
  
### 参数：  
  
无。  
  
### 返回值：
  
返回系统时钟频率。  
  
### 错误码：
  
无。  
  
### 备注：  
  
GJB 7714-2012错误码：无。  
  
### 样例：  
  
```c  
#include <stdio.h>  
#include <pthread.h>  
int main (int argc, char **argv)  
{  
	uint32_t rate;  
	// ……  
	rate = sys_clk_rate_get();                    /* 获取系统tick数(64位)        */  
	printf("system rate num at present :%u\n", rate);  
	// ……  
	return (0);  
}	  
```  
  
  
## Sys_clk_rate_set ：设置系统时钟频率
  
  
### 函数原型：
  
```c  
#include <pthread.h>  
int  sys_clk_rate_set  
(  
	int ticks_per_second  
);  
```  
  
### 描述：
  
该接口用来设置系统 tick 频率。  
  
### 参数：  
  
- ticks_per_second ：要设置的时钟频率，即每秒钟的 tick 数。  
  
### 返回值：
  
成功返回0，失败返回-1。  
  
### 错误码：
  
- ENOTSUP ：系统不支持的操作。  
  
### 备注：  
  
GJB 7714-2012返回值：
- 0 设置成功；  
-1 设置失败。  
  
### 样例：  
  
```c  
无
```  
  
  
## clock_getcpuclockid ：访问任务CPU时间时钟
  
  
### 函数原型：
  
```c  
#include <time.h>  
int  clock_getcpuclockid  
(  
	pid_t pid,  
	clockid_t *clock_id  
);  
```  
  
### 描述：
  
该接口获取有 pid 指定任务 CPU 时间时钟的标识符。  
  
### 参数：  
  
- pid ：任务 ID。  
  
- clock_id ：时钟标识符。  
  
### 返回值：
  
成功返回 0，失败返回-1，并置相应的错误码。  
  
### 错误码：
  
- EINVAL：参数无效。  
  
### 备注：  
  
GJB 7714-2012错误码：ESRCH ：找不到指定的任务。  
  
### 样例：  
  
```c  
int main(int argc, char **argv)  
{  
	int res = 0;  
	clockid_t clock_id;  
	// ……  
	res = clock_getcpuclockid(0, &clock_id);  /* 访问任务CPU时间时钟          */  
	if(res == 0)  
	{  
		printf("clock_id = %d\n", clock_id);  
	}  
	// ……  
	return 0;  
}  
```  
  
  
## clock_gettime ：获取指定时钟的当前值
  
  
### 函数原型：
  
```c  
#include <time.h>  
int  clock_gettime  
(  
	clockid_t clockid,  
	struct timespec *tv  
);  
```  
  
  
### 描述：
  
该接口用于获取指定时钟 clockid 的当前值，并存放在参数 tv 指定的地址中。  
  
### 参数：  
  
- clockid：时钟标识符。取值：  
  
|宏定义|说明|  
|---|---|  
|CLOCK_REALTIME 0|实际的物理时间|  
|CLOCK_MONOTONIC 1|单调增长时间|  
|CLOCK_PROCESS_CPUTIME_ID 2|进程从启动开始消耗的 CPU 时间|  
|CLOCK_THREAD_CPUTIME_ID 3|线程从启动开始消耗的 CPU 时间|  
  
- tp ：存放时钟当前值的指针。  
  
### 返回值：
  
成功返回 0，失败返回-1，并置相应的错误码。  
  
### 错误码：
  
- ESRCH ：任务不存在。  
- EINVAL ：参数无效。  
  
### 备注：  
  
GJB 7714-2012错误码：EINVAL 参数tp为非法指针，或者参数clock_id的取值不合法；  
  
### 样例：  
  
```c  
int main(int argc, char **argv)  
{  
	int res = 0;  
	struct timespec tp;  
	// ……  
	res = clock_gettime(CLOCK_REALTIME, &tp);    /* 获取进程CPU占用时间   */  
	if(res == 0)  
	{  
		printf("CLOCK_REALTIME value :%lld : %ld\n", tp.tv_sec, tp.tv_nsec);  
	}  
	// ……  
	return 0;  
}  
```  
  
  
## Clock_settime ：设置指定时钟的时间值
  
  
### 函数原型：
  
```c  
#include <time.h>  
INT  clock_settime  
(  
	clockid_t clockid,  
	const struct timespec *tv  
);  
```  
  
### 描述：
  
该接口会把 clock_id 指定的时钟设为 tv 指定的值。如果时间值是介于指定时钟精度的两个连续非负整倍数间，那么它会被截取为较小的精度倍数。时钟可以是系统范围或是任务的。  
  
### 参数：  
  
- clockid ：时钟标识符。取值可以是 CLOCK_REALTIME（0）  
  
- tv ：时间值。  
  
### 返回值：
  
成功返回 0，失败返回-1，并置相应的错误码。  
  
### 错误码：
  
- ESRCH ：任务不存在。  
- EINVAL ：参数无效。  
  
### 备注：  
  
GJB 7714-2012错误码：
- EINVAL ：参数无效，或者赋给变量tp的纳秒值小于0或大于等于1’000’000’000。  
  
### 样例：  
  
```c  
int main(int argc, char **argv)  
{  
	int res = 0;  
	// ……  
	struct timespec tv;  
	tv.tv_sec = 20000;  
	tv.tv_nsec = 10000;  
	res = clock_settime(CLOCK_REALTIME, &tv);   /* 设置时间，从1970.1.1 8:00开始*/  
	if(res != 0)  
	{  
		perror("fun clock_settime error\n");  
	}  
	// ……  
	return 0;  
}  
```  
  
  
## Clock_getres ：获取时钟精度
  
  
### 函数原型：
  
```c  
#include <time.h>  
INT  clock_getres  
(  
	clockid_t clockid,  
	struct timespec *res  
);  
```  
  
  
### 描述：
  
该接口用来获取 clockid 指定的时钟的精度。  
  
### 参数：  
  
- clockid ：时钟标识符。  
  
- res ：存放时钟精度的指针。  
  
### 返回值：
  
成功返回 0， 失败返回-1，并置相应的错误码。  
  
### 错误码：
  
- EINVAL ：参数无效。  
  
### 备注：  
  
GJB 7714-2012错误码：
- EINVAL ：参数无效，或者res是无效指针。  
  
### 样例：  
  
```c  
int main(int argc, char **argv)  
{  
	int res = 0;  
	// ……  
	struct timespec tp;  
	res = clock_getres(CLOCK_REALTIME, &tp);       /* 获取时钟精度       */  
	if(res == 0)  
	{  
		printf("CLOCK_REALTIME precision :%lld : %ld\n", tp.tv_sec, tp.tv_nsec);  
	}  
	// ……  
	return 0;  
}  
```  
  
  
## wdg_create ：创建看门狗定时器
  
  
### 函数原型：
  
```c  
#include <pthread.h>  
int  wdg_create  
(  
	wdg_t *wdg_id  
);  
```  
  
  
### 描述：
  
该接口用于创建一个看门狗，并将看门狗ID通过参数wdg_id输出。创建的看门狗定时器处于非活动状态。需要显式地通过wdg_start()函数激活。  
  
### 参数：  
  
- wdg_id ：获取新建看门狗ID的指针。  
  
### 返回值：
  
成功返回0，失败返回相应的错误码。  
  
### 错误码：
  
- ERROR_KERNEL_IN_ISR ：系统处于中断中。  
- ERROR_TIMER_FULL ：定时器已满。  
- EINVAL ：参数无效。  
  
### 备注：  
  
GJB 7714-2012错误码为 ：  
- ECALLEDINISR ：系统处于中断中。  
- EMNOTINITED ：模块未初始化。  
- EAGAIN ：系统资源不足。  
  
### 样例：  
  
```c  
#include <stdio.h>  
#include <pthread.h>  
static	wdg_t wdg;  
int main (int argc, char **argv)  
{  
	int ret = 0；  
	ret = wdg_create(&wdg);              /* 创建看门狗定时器             */  
	if(ret != 0)  
	{  
		perror("func wdg_create error\n");  
		return -1;  
	}  
  
	// ……  
  
    return  (0);  
}  
```  
  
  
## nanosleep ：任务休眠（纳秒作为休眠计时单位）
  
  
### 函数原型：
  
```c  
#include <pthread.h>  
INT  nanosleep  
(  
	const struct timespec *rqtp,  
	struct timespec *rmtp  
);  
```  
  
  
### 描述：
  
该接口将使调用的任务休眠，知道经过参数 rqtp  
指定的时间间隔。但在任务休眠期间，若接收到信号，都会打断任务的休眠。  
  
### 参数：  
  
- rqtp ：请求的休眠时间。（相对时间）  
  
- rmtp ：记录剩余的休眠时间。  
  
### 返回值：
  
自然唤醒返回 0，否则返回-1，并置相应的错误码。  
  
### 错误码：
  
- EINTR ：被信号激活。  
- EINVAL ：参数无效。  
  
### 备注：  
  
GJB 7714-2012错误码：
- EINVAL ：参数无效；  
- EINTR ：被信号激活。  
  
### 样例：  
  
```c  
int main(int argc, char **argv)  
{  
	int res = 0;  
	struct timespec tp;  
	// ……  
	tp.tv_sec = 5000000;  
	res = nanosleep(&tp, NULL);  
	if(res == 0)  
	{  
		printf("nanosleep normal quit\n");  
	}  
	// ……  
	return 0;  
}  
```  
  
  
## wdg_start ：启动看门狗定时器
  
  
### 函数原型：
  
```c  
#include <pthread.h>  
int  wdg_start  
(  
	wdg_t wdg_id,  
	int ticks,  
	void (*func)(void *arg),  
	void *arg  
);  
```  
  
  
### 描述：
  
启动看门狗定时器。  
  
### 参数：  
  
- wdg_id ：看门狗ID。  
  
- ticks ：看门狗延迟时间，应为正值。  
  
- func ：看门狗触发时的执行函数。  
  
- Arg ：执行函数的参数。  
  
### 返回值：
  
成功返回0，失败返回相应的错误码。  
  
### 错误码：
  
- ERROR_TIMER_CALLBACK_NULL ：回调函数为空。  
- ERROR_TIMER_TIME ：时间出错。  
- EINVAL ：参数无效。  
  
### 备注：  
  
GJB 7714-2012错误码为：  
- EINVAL ：参数无效。  
- EMNOTINITED ：模块未初始化。  
  
### 样例：  
  
```c  
#include <stdio.h>  
#include <pthread.h>  
static	wdg_t wdg;  
static int i = 0;  
void fun(void *arg)  
{  
	int ret = 0;  
	ret = wdg_start(wdg, 100, fun, NULL);       /* 启动看门狗定时器             */  
	if(ret != 0)  
	{  
		perror("func wdg_start error\n");  
		return ;  
	}  
	i ++;  
	return ;  
}  
int main (int argc, char **argv)  
{  
	int ret = 0;  
  
  
	ret = wdg_create(&wdg);  
	if(ret != 0)  
	{  
		perror("func wdg_create error\n");  
		return -1;  
	}  
	ret = wdg_start(wdg, 100, fun, NULL);  
	if(ret != 0)  
	{  
		perror("func wdg_start error\n");  
		return -1;  
	}  
  
	while(i<10)  
	{  
		printf("%d\n", i);  
		sleep(1);  
	}  
  
    return  (0);  
}  
```  
  
## wdg_cancel ：停止看门狗计时器计时
  
  
### 函数原型：
  
```c  
#include <pthread.h>  
Int  wdg_cancel  
(  
	wdg_t wdg_id  
);  
```  
  
  
### 描述：
  
该接口通过将看门狗的延迟计数归零来取消wdg_id指定的看门狗。  
  
### 参数：  
  
- wdg_id ：看门狗ID。  
  
### 返回值：
  
成功返回0，失败返回相应的错误码。  
  
### 错误码：
  
- ERROR_KERNEL_HANDLE_NULL ：句柄出错。  
- ERROR_TIMER_NULL ：定时器为空。  
- ERROR_TIMER_TIME ：时间出错。  
  
### 备注：  
  
GJB 7714-2012错误码为：  
- EINVAL ：参数无效。  
- EMNOTINITED ：模块未初始化。  
  
### 样例：  
  
```c  
#include <stdio.h>  
#include <pthread.h>  
static  wdg_t wdg;  
int main (int argc, char **argv)  
{  
	int ret = 0;  
	ret = wdg_create(&wdg);  
	if(ret != 0)  
	{  
		perror("func wdg_create error\n");  
		return -1;  
	}  
	// ……  
	ret = wdg_cancel(wdg);                     /* 停止看门狗定时器       */  
	if(ret != 0)  
	{  
	perror("func wdg_cancel error\n");  
	return -1;  
	}  
	// ……  
	return (0);  
}  
```  
  
  
## wdg_delete ：删除看门狗定时器
  
  
### 函数原型：
  
```c  
#include <pthread.h>  
int  wdg_delete  
(  
	wdg_t wdg_id  
);  
```  
  
  
### 描述：
  
该接口用来删除看门狗定时器。  
  
### 参数：  
  
- wdg_id ：看门狗定时器句柄。  
  
### 返回值：
  
成功返回0，失败返回相应的错误码。  
  
### 错误码：
  
- EINVAL：参数无效。  
  
### 备注：  
  
GJB 7714-2012错误码为：  
- EINVAL ：参数无效。  
- ECALLEDINISR ：系统处于中断中。  
- EMNOTINITED ：模块未初始化。  
  
### 样例：  
  
```c  
#include <stdio.h>  
#include <pthread.h>  
static  wdg_t wdg;  
int main (int argc, char **argv)  
{  
	int ret = 0;  
	ret = wdg_create(&wdg);  
	if(ret != 0)  
	{  
		perror("func wdg_create error\n");  
		return -1;  
	}  
	// ……  
	ret = wdg_cancel(wdg);  
	if(ret != 0)  
	{  
		perror("func wdg_cancel error\n");  
		return -1;  
	}  
	ret = wdg_delete(wdg);                    /* 删除看门狗定时器        */  
	if(ret != 0)  
	{  
		perror("func wdg_delete error\n");  
		return -1;  
	}  
	return (0);  
}  
```  
  
  
## wdg_getinfo ：获取看门狗定时器概要信息
  
  
### 函数原型：
  
```c  
#include <pthread.h>  
Int  wdg_getinfo  
(  
	wdg_t wdg_id,  
	wdg_info_t *info  
);  
```  
  
  
### 描述：
  
该接口用来获取wdg_id指定的定时器的概要信息。包括定时器相关的信号信息、看门狗ID、时钟ID、任务ID、定时器状态、定时器启动信息等。  
  
### 参数：  
  
- wdg_id ：看门狗定时器句柄。  
  
- info ：存放看门狗定时器信息的指针。  
  
### 返回值：
  
成功返回0，失败返回相应的错误码。  
  
### 错误码：
  
- EINVAL：参数无效。  
  
### 备注：  
  
GJB 7714-2012错误码为：  
- EINVAL ：参数无效。  
- EMNOTINITED ：模块未初始化。  
  
### 样例：  
  
```c  
void fun(void *arg)  
{  
	// ……  
  
	wdg_info_t info;  
	ret = wdg_getinfo(wdg, &info);      /* 获取看门狗定时器概要信息     */  
	if(ret != 0){  
		printf(“func wdg_getinfo failed!\n”);  
	}  
  
	// ……  
  
}  
```  
  
  
## wdg_show ：显示看门狗定时器信息
  
  
### 函数原型：
  
```c  
#include <pthread.h>  
int  wdg_show  
(  
	wdg_t wdg_id  
)  
```  
  
  
### 描述：
  
该接口用于显示定时器的信息。  
  
### 参数：  
  
- wdg_id ：定时器ID。  
  
### 返回值：
  
成功返回0，失败返回相应的错误码。  
  
### 错误码：
  
- EINVAL ：参数无效。  
  
### 备注：  
  
GJB 7714-2012错误码为：  
- EINVAL ：参数无效。  
- EMNOTINITED ：模块未初始化。  
  
### 样例：  
  
```c  
void fun(void *arg)  
{  
	// ……  
  
	wdg_show(wdg);           /* 显示看门狗定时器信息         */  
  
	// ……  
  
}  
```  
  
  
## timer_create ：创建定时器
  
  
### 函数原型：
  
```c  
#include <pthread.h>  
INT  timer_create  
(  
	clockid_t clockid,  
	struct sigevent *sigeventT,  
	timer_t *ptimer  
)  
```  
  
### 描述：
  
该解口用于以clockid指定的时钟作为计时基准创建定时器。  
  
### 参数：  
  
- clockid ：时钟标识。取值为或。  
  
|宏定义|说明|  
|---|---|  
|CLOCK_REALTIME|实时时钟源（设置时间会影响）|  
|CLOCK_MONOTONIC|单调时钟源（设置时间不会影响）|  
  
- sigeventT ：设置定时器发出的信号。  
  
- ptimer ：存放定时器标志的指针。  
  
### 返回值：
  
成功返回0，失败返回相应的错误码。  
  
### 错误码：
  
- EINVAL ：参数无效。  
- ENOTSUP ：系统不支持的操作。  
  
### 备注：  
  
GJB 7714-2012错误码为：  
- EINVAL ：参数无效。  
- ENOTSUP ：系统不支持的设置。  
- EAGAIN ：系统资源不足。  
  
### 样例：  
  
```c  
#include <stdio.h>  
#include <signal.h>  
#include <sys/time.h>  
#include <pthread.h>  
  
void *timer_thread (void *arg)  
{  
    // ……  
}  
int main (int argc, char *argv[])  
{  
    struct timespec		interval;  
    struct itimerspec	tspec;  
    pthread_t			tid;  
    int					ret;  
    struct sigevent		    sigev;  
    timer_t				timerid;  
  
	// ……  
  
    sigev.sigev_notify 				= SIGEV_THREAD_ID | SIGEV_SIGNAL;  
    sigev.sigev_signo 			  	= SIGUSR1;  
    sigev.sigev_notify_thread_id 	= tid;  
  
    ret = timer_create(CLOCK_MONOTONIC, &sigev, &timerid);   /* 创建定时器    */  
    if (ret) {  
        fprintf(stderr, "timer create failed.\n");  
        return -1;  
    }  
    // ……  
  
    return  0;  
}  
```  
  
  
## timer_delete ：删除定时器
  
  
### 函数原型：
  
```c  
#include <pthread.h>  
INT  timer_delete  
(  
	timer_t timer  
);  
```  
  
  
### 描述：
  
该接口用于删除timer指定的定时器。  
  
### 参数：  
  
- timer ：定时器句柄。  
  
### 返回值：
  
成功返回0，失败返回相应的错误码。  
  
### 错误码：
  
- EINVAL ：参数无效。  
- ETIMEDOUT ：等待超时。  
  
### 备注：  
  
GJB 7714-2012 错误码：
- EINVAL ：参数无效。  
  
### 样例：  
  
```c  
#include <stdio.h>  
#include <signal.h>  
#include <sys/time.h>  
#include <pthread.h>  
int main (int argc, char *argv[])  
{  
timer_t       timerid;  
// …….  
    ret = timer_delete(timerid);          /* 删除定时器                   */  
    if (ret)  
    {  
        fprintf(stderr, "timer delete failed.\n");  
        return -1;  
    }  
    return  0;  
}  
  
```  
  
  
## timer_settime ：设置定时器时间
  
  
### 函数原型：
  
```c  
#include <pthread.h>  
int  timer_settime  
(  
	timer_t timer,  
	INT iFlag,  
	const struct itimerspec *ptvNew,  
	itimerspec *ptvOld  
);  
```  
  
  
### 描述：
  
该接口用于将参数 ptvNew 的成员 it_value 的值设置定时器 timer 的定时时间，并在it_value 不为零的情况下启动定时器。  
  
### 参数：  
  
- Timer 定时器句柄  
  
- iFlag 标志  
  
- ptvNew 新的时间信息  
  
- ptvOld 先早的时间信息  
  
- iFlag = 0 使用TIMER_ABSTIME绝对时间；  
  
        = 非零值 使用 clockidTimer时间。  
  
### 返回值：
  
成功返回0，失败返回相应的错误码。  
  
### 错误码：
  
- EINVAL ：参数无效。  
  
### 备注：  
  
GJB 7714-2012 错误码：
- EINVAL ：参数无效。  
  
### 样例：  
  
```c  
#include <stdio.h>  
#include <signal.h>  
#include <sys/time.h>  
#in int main (int argc, char *argv[])  
{  
    struct timespec     interval;  
    struct itimerspec   tspec;  
  
    pthread_t           tid;  
    int                 ret;  
    struct sigevent     sigev;  
	timer_t             timerid; clude <pthread.h>  
	// ……  
	ret = timer_settime(timerid, 0, &tspec, LW_NULL); /* 设置定时器时间     */  
if (ret)   
	{  
        fprintf(stderr, "timer settime failed.\n");  
        return -1;  
    }  
    return  0;  
}  
```  
  
## timer_gettime ：获取定时器时间
  
### 函数原型：
  
```c  
#include <pthread.h>  
INT  timer_gettime  
(  
	timer_t timer,  
	struct itimerspec *ptvTime  
);  
```  
  
  
### 描述：
  
该接口会把定时器 timer 到期前的时间和定时器的重载入值保存到参数 ptvTime  
指向的位置。  
  
### 参数：  
  
- Timer ：定时器标识。  
  
- ptvTime ：存放时间期限的地址。如果定时器关闭的情况下会将 0 保存到 ptvTime  
    中，否则把之前的定时器到期前的时间总数和定时器的重载入值保存到 ptvTime 中。  
  
### 返回值：
  
成功返回0，失败返回相应的错误码。  
  
### 错误码：
  
- EINVAL ：参数无效。  
  
### 备注：  
  
GJB 7714-2012 错误码：
- EINVAL ：参数无效。  
  
### 样例：  
  
```c  
#include <stdio.h>  
#include <signal.h>  
#include <sys/time.h>  
#include <pthread.h>  
void *timer_thread (void *arg)  
{  
	// ……  
	return NULL;  
}  
int main (int argc, char *argv[])  
{	  
	// ……  
	ret = timer_gettime(timerid, &tv);       /* 获取定时器时间               */  
	if(ret == 0)  
	{  
		printf("it_interval %lld :%ld\n", tv.it_interval.tv_sec, 									tv.it_interval.tv_nsec);  
		printf("it_value  %lld :%ld\n", tv.it_value.tv_sec, tv.it_value.tv_nsec);  
	}  
	......  
	return 0;  
}  
```  
  
  
## timer_getinfo ：显示定时器信息
  
  
### 函数原型：
  
```c  
#include <pthread.h>  
Int  timer_getinfo  
(  
	wdg_t wdg_id,  
	wdg_info_t *info  
);  
```  
  
  
### 描述：
  
该接口用来获取 wdg_id 指定的定时器的概要信息。包括定时器相关的信号信息、看门狗 ID、时钟 ID、任务 ID、定时器状态、定时器启动信息等。  
  
### 参数：  
  
- wdg_id ：看门狗 ID。  
  
- info ：存放返回定时器信息的结构指针。  
  
### 返回值：
  
成功返回0，失败返回相应的错误码。  
  
### 错误码：
  
- EINVAL ：参数无效。  
  
### 备注：  
  
GJB 7714-2012 错误码：
- EINVAL ：参数无效。  
  
### 样例：  
  
```c  
#include <stdio.h>  
#include <signal.h>  
#include <sys/time.h>  
#include <pthread.h>  
void *timer_thread (void *arg)  
{  
	// ……  
}  
int main (int argc, char *argv[])  
{  
	timer_t timerid;  
	timer_info_t info;  
	// ……	  
	ret = timer_getinfo(timerid, &info);        /* 显示定时器信息               */  
	if(ret == 0)  
	{  
		printf("run   :%d\n", info.run);  
		printf("clockid :%d\n", info.clockid);  
		printf("cnt    :%ld\n", info.cnt);  
		printf("interval :%lu\n", info.interval);  
	}  
	return 0;  
}  
```  
  
  
## ftime ：获取和当前时间相关的参数
  
  
### 函数原型：
  
```c  
#include <sys/timeb.h>  
int   ftime  
(  
	struct timeb *tp  
);  
```  
  
### 描述：
  
该接口用来获取当前时间与相关参数。  
  
### 参数：  
  
- tp ：存放时间和相关参数的指针。  
  
### 返回值：
  
成功返回0，失败返回-1，并置相应的错误码。  
  
### 错误码：
  
- EINVAL ：参数无效。  
  
### 备注：  
  
GJB 7714-2012错误码：无此接口。  
  
### 样例：  
  
```c  
#include <stdio.h>  
#include <sys/timeb.h>  
int main (int argc, char **argv)  
{  
	int ret = 0;   
	struct timeb t;  
  
	ret = ftime(&t);             /* 获取和当前时间相关的参数     */  
	if(ret == 0)  
	{  
		// ……  
	}  
  
	// ……  
  
    return  (0);  
}  
```  
  
## time ：获取UTC时间
  
  
### 函数原型：
  
```c  
#include < time.h>  
time_t  time  
(  
	time_t  *time  
);  
```  
  
  
### 描述：
  
该接口用来获取UTC时间,time_t在某些类Unix系统中定义为一个32位的有符号整型数，能表示的最大正秒数为2147483647秒，它能表示的最晚时间为2038年1月19日03:14:07。  
  
### 参数：  
  
- time ：UTC时间。  
  
### 返回值：
  
输出参数time为获得的UTC时间，与返回值相同，该参数可以为NULL。  
  
### 错误码：
  
无。  
  
### 备注：  
  
GJB 7714-2012错误码：无此接口。  
  
### 样例：  
  
```c  
#include <stdio.h>  
#include <gjbext.h>  
int main (int argc, char **argv)  
{  
	clock_t start, end;  
	start = time(NULL); /            * 获取和当前时间     */  
  
    // ……  
  
	end = time(NULL);  
	printf("func use time %f\n", 1.0 * (end - start)/CLOCKS_PER_SEC);   
  
    // ……  
  
    return  (0);  
}  
```  
  
  
## timelocal ：获取本地时间
  
  
### 函数原型：
  
```c  
#include < time.h>  
time_t  time  
(  
	time_t  *time  
);  
```  
  
  
### 描述：
  
该接口用于显示定时器的信息。  
  
### 参数：  
  
- time ：本地时间。  
  
### 返回值：
  
输出参数time为获得的本地时间，与返回值相同，该参数可以为NULL。  
  
### 错误码：
  
无。  
  
### 备注：  
  
GJB 7714-2012错误码：无此接口。  
  
### 样例：  
  
```c  
#include <stdio.h>  
#include <time.h>  
int main (int argc, char **argv)  
{  
    int i;  
    clock_t start, end;  
  
    start = timelocal(NULL);  
    for(i=0;i<6;i++)  
    {  
        printf("func printf word%d!\n",i);  
        sleep(1);  
    }  
    end = timelocal(NULL);  
  
    printf("func use time %f\n", 1.0 * (end - start)/CLOCKS_PER_SEC);   /* 两次获取时间之差             */  
  
    return 0;  
}  
```  
  
  
## gettimeofday ：获取时间及时区信息
  
  
### 函数原型：
  
```c  
#include < time.h>  
Int  gettimeofday  
(  
	struct timeval  *tv,   
	struct timezone  *tz  
);  
```  
  
  
### 描述：
  
该接口用于获取时间及时区信息。  
  
### 参数：  
  
- tv ：保存获取的时间信息。  
  
- tz ：保存获取的时区信息。  
  
### 返回值：
  
此函数返回0，没有错误值返回。  
  
### 错误码：
  
无。  
  
### 备注：  
  
GJB 7714-2012错误码：无此接口。  
  
### 样例：  
  
```c  
#include <stdio.h>  
#include <time.h>  
  
int main (int argc, char **argv)  
{  
    int ret;  
    struct timeval tv;  
    struct timezone tz;  
  
    ret = gettimeofday(&tv, &tz);             /* 获取时间及时区信息   */  
    if(ret == 0)  
    {  
        printf("second is %lld\n",tv.tv_sec);  
        printf("usecond is %ld\n",tv.tv_usec);  
        printf("dsttime is %d\n",tz.tz_dsttime);  
        printf("minuteswest is %d\n",tz.tz_minuteswest);  
    }  
  
    return  (0);  
}  
```  
  
## settimeofday ：设置时间及时区信息
  
  
### 函数原型：
  
```c  
#include < time.h>  
int  settimeofday  
(  
	const struct timeval  *tv,  
	const struct timezone  *tz  
);  
```  
  
  
### 描述：
  
该接口用于设置时间及时区信息。  
  
### 参数：  
  
- tv ：保存获取的时间信息。  
  
- tz ：保存获取的时区信息。  
  
### 返回值：
  
此函数返回0，没有错误值返回。  
  
### 错误码：
  
无。  
  
### 备注：  
  
GJB 7714-2012错误码：无此接口。  
  
### 样例：  
  
```c  
#include <stdio.h>  
#include <time.h>  
int main (int argc, char **argv)  
{  
    int ret;  
    struct timeval tv;  
    struct timezone tz;  
    struct timeval tv1;  
    struct timezone tz1;  
    tv.tv_sec=124515125;  
    tv.tv_usec=146354;  
    tz.tz_dsttime=0;  
    tz.tz_minuteswest=60;  
  
    ret = settimeofday(&tv, &tz);           /* 设置时间及时区信息           */  
    if(ret!=0)  
    {  
        printf("func settimeofday failed!\n");  
    }  
  
    ret = gettimeofday(&tv1, &tz1);  
    if(ret == 0)  
    {  
        printf("second  is %lld\n",tv1.tv_sec);       /*秒*/  
        printf("usecond is %ld\n",tv1.tv_usec);       /*  微秒*/  
        printf("dsttime is %d\n",tz1.tz_dsttime);      /*  时间的修正方式 必须为0  */  
        printf("minuteswest is %d\n",tz1.tz_minuteswest);/*  是格林威治时间往西方的时差 */  
    }  
    return  (0);  
}  
```  
  
  
## tzset ：设置系统的时区
  
  
### 函数原型：
  
```c  
#include < time.h>  
void   tzset  
(  
	Void  
);  
```  
  
  
### 描述：
  
该接口用于设置系统的时区。  
  
### 参数：  
  
无。  
  
### 返回值：
  
无。  
  
### 错误码：
  
无。  
  
### 备注：  
  
GJB 7714-2012错误码：无此接口。  
  
### 样例：  
  
```c  
#include <stdlib.h>  
#include <time.h>  
  
int main(int argc, char *argv[])  
{  
    system("TZ=CST-6:0:0");  
tzset();                              /* 设置系统的时区               */  
ret = gettimeofday(&tv, &tz);  
    if(ret == 0)  
    {  
       printf("minuteswest is %d\n",tz.tz_minuteswest);  
    }  
    return  (0);  
}  
```  
  
  
## gmtime ：转换为结构体类型时间
  
  
### 函数原型：
  
```c  
#include < time.h>  
struct  tm  *gmtime  
(  
	const  time_t  *time  
);  
```  
  
  
### 描述：
  
该接口用于转换为结构体类型时间格式。  
  
### 参数：  
  
-*time：本地时间。  
  
### 返回值：
  
此函数成功返回tm结构的指针，失败返回NULL。  
  
### 错误码：
  
无。  
  
### 备注：  
  
GJB 7714-2012错误码：无此接口。  
  
### 样例：  
  
```c  
#include <stdlib.h>  
#include <time.h>  
int main (int argc, char *argv[])  
{  
    struct tm   *tm_old;  
    struct tm   *tm_new;  
    time_t      time_old;  
    time_t      time_new;  
  
    time_old = time(NULL);  
    sleep(5);  
    time_new = time(NULL);  
  
    tm_old = gmtime(&time_old);            /* 转换为tm类型时间             */  
    tm_new = gmtime(&time_new);  
  
    fprintf(stdout, "old: %d:%d\n", tm_old->tm_min, tm_old->tm_sec);  
    fprintf(stdout, "new: %d:%d\n", tm_new->tm_min, tm_new->tm_sec);  
  
    return  (0);  
}  
```  
  
  
## gmtime_r ：转换为结构体类型时间
  
  
### 函数原型：
  
```c  
#include < time.h>  
struct  tm  *gmtime_r  
(  
	const  time_t  *time,   
	struct  tm  *ptmBuffer  
);  
```  
  
  
### 描述：
  
该接口用于转换为结构体类型时间格式（可重入）。  
  
### 参数：  
  
- time :本地时间。  
  
- ptmBuffer :tm结构缓冲区  
  
### 返回值：
  
此函数成功返回tm结构的指针，失败返回NULL。  
  
### 错误码：
  
无。  
  
### 备注：  
  
GJB 7714-2012错误码：无此接口。  
  
### 样例：  
  
```c  
#include <stdlib.h>  
#include <time.h>  
int main (int argc, char *argv[])  
{  
    struct tm   tm_old;  
    struct tm   tm_new;  
    time_t      time_old;  
    time_t      time_new;  
  
    time_old = time(NULL);  
    sleep(5);  
    time_new = time(NULL);  
  
    gmtime_r(&time_old, &tm_old);             /* 转换为tm类型时间(缓冲区变量) */  
    gmtime_r(&time_new, &tm_new);  
  
    fprintf(stdout, "old: %d:%d\n", tm_old.tm_min, tm_old.tm_sec);  
    fprintf(stdout, "new: %d:%d\n", tm_new.tm_min, tm_new.tm_sec);  
  
    return  (0);  
}  
```  
  
  
## asctime ：转换为常用时间格式
  
  
### 函数原型：
  
```c  
#include < time.h>  
char  *asctime  
(  
	Const  struct tm  *ptm  
);  
```  
  
  
### 描述：
  
该接口用于转换为常用时间格式, 处理参数ptm时，将不会作任何时区转换,函数返回的时间字符串的格式如“Tue May 21 13:46:221991n”，因此在使用该函数的可重入版本asctime_r时，其参数pcBuffer必须保证长度不小于26个字节。  
  
### 参数：  
  
- Ptm : tm结构的指针。  
  
### 返回值：
  
此函数成功返回格式化后的时间字符串指针，失败返回NULL。  
  
### 错误码：
  
无。  
  
### 备注：  
  
GJB 7714-2012错误码：无此接口。  
  
### 样例：  
  
```c  
#include <stdlib.h>  
#include <time.h>  
int main (int argc, char *argv[])  
{  
    time_t t;  
    time(&t);  
    printf("%s",asctime(gmtime(&t)));               /* 转换为常用时间格式    */  
  
    return  (0);  
}  
```  
  
  
## asctime_r ：转换为常用时间格式
  
  
### 函数原型：
  
```c  
#include < time.h>  
Char  *asctime_r  
(	  
	const struct tm  *ptm,   
	char  *pcBuffer  
);  
```  
  
  
### 描述：
  
该接口用于转换为常用时间格式, 处理参数ptm时，将不会作任何时区转换,函数返回的时间字符串的格式如“Tue May 21 13:46:221991n”，因此在使用该函数的可重入版本asctime_r时，其参数pcBuffer必须保证长度不小于26个字节（可重入）。  
  
### 参数：  
  
- ptm ：tm结构的指针  
  
- ptmBuffer ：tm结构缓冲区。  
  
### 返回值：
  
此函数成功返回格式化后的时间字符串指针，失败返回NULL。  
  
### 错误码：
  
无。  
  
### 备注：  
  
GJB 7714-2012错误码：无此接口。  
  
### 样例：  
  
```c  
#include <stdlib.h>  
#include <time.h>  
int main (int argc, char *argv[])  
{  
    PCHAR a[30],b[30];  
    struct tm *t,*t1;  
    time_t t_old,t_new;  
    time(&t_old);  
    sleep(3);  
    time(&t_new);  
  
    fprintf(stdout,"%s",asctime_r(gmtime(&t_old), a[0])); /* 转换为常用时间格式(缓冲区变量)*/  
    fprintf(stdout,"%s",asctime_r(gmtime(&t_new), b[0]));  
  
    return  (0);  
}  
```  
  
  
## localtime ：转换为当地时间
  
  
### 函数原型：
  
```c  
#include < time.h>  
struct  tm  *localtime  
(  
	const time_t  *time  
);  
```  
  
  
### 描述：
  
该接口用于转换为当地时间，内部会进行UTC时间到本地时间的转换处理。  
  
### 参数：  
  
- time：UTC时间。  
  
### 返回值：
  
此函数成功返回tm结构的指针，失败返回NULL。  
  
### 错误码：
  
无。  
  
### 备注：  
  
GJB 7714-2012错误码：无此接口。  
  
### 样例：  
  
```c  
#include <stdlib.h>  
#include <time.h>  
int main (int argc, char *argv[])  
{  
    struct tm   *tm_old;  
    struct tm   *tm_new;  
    time_t      time_old;  
    time_t      time_new;  
  
    time_old = time(NULL);  
    sleep(5);  
    time_new = time(NULL);  
  
    tm_old = localtime(&time_old);            /* 转换为当地时间               */  
    tm_new = localtime(&time_new);  
  
    fprintf(stdout, "old: %d:%d\n", tm_old->tm_min, tm_old->tm_sec);  
    fprintf(stdout, "new: %d:%d\n", tm_new->tm_min, tm_new->tm_sec);  
  
    return  (0);  
}  
```  
  
  
## localtime_r ：转换为当地时间
  
  
### 函数原型：
  
```c  
#include < time.h>  
Struct  tm  *localtime_r  
(  
	const time_t  *time,  
	struct tm  *ptmBuffer  
);  
```  
  
  
### 描述：
  
该接口用于转换为当地时间（可重入），内部会进行UTC时间到本地时间的转换处理。  
  
### 参数：  
  
- time：UTC时间  
  
- ptmBuffer: tm结构缓冲区。  
  
### 返回值：
  
此函数成功返回tm结构的指针，失败返回NULL。  
  
### 错误码：
  
无。  
  
### 备注：  
  
GJB 7714-2012错误码：无此接口。  
  
### 样例：  
  
```c  
#include <stdlib.h>  
#include <time.h>  
int main (int argc, char *argv[])  
{  
    struct tm   tm_old;  
    struct tm   tm_new;  
    time_t      time_old;  
    time_t      time_new;  
  
    time_old = time(NULL);  
    sleep(5);  
    time_new = time(NULL);  
  
    localtime_r(&time_old, &tm_old);            /* 转换为当地时间(缓冲区变量)   */  
    localtime_r(&time_new, &tm_new);  
  
    fprintf(stdout, "old: %d:%d\n", tm_old.tm_min, tm_old.tm_sec);  
    fprintf(stdout, "new: %d:%d\n", tm_new.tm_min, tm_new.tm_sec);  
  
    return  (0);  
}  
```  
  
  
## ctime ：转换为常用时间格式
  
  
### 函数原型：
  
```c  
#include < time.h>  
Char  *ctime  
(  
	const time_t  *time  
);  
```  
  
  
### 描述：
  
该接口用于转换为常用时间格式。  
  
### 参数：  
  
- Time: time_t类型的指针。  
  
### 返回值：
  
此函数成功返回格式化后的时间字符串指针，失败返回NULL。  
  
### 错误码：
  
无。  
  
### 备注：  
  
GJB 7714-2012错误码：无此接口。  
  
### 样例：  
  
```c  
#include <stdlib.h>  
#include <time.h>  
int main (int argc, char *argv[])  
{  
    time_t t;  
    time(&t);  
    printf("%s",ctime(&t));                       /* 转换为常用时间格式           */  
  
    return  (0);  
}  
```  
  
  
## ctime_r ：转换为常用时间格式
  
  
### 函数原型：
  
```c  
#include < time.h>  
Char  *ctime_r  
(  
	const time_t  *time,  
	char  *pcBuffer  
);  
```  
  
  
### 描述：
  
该接口用于转换为常用时间格式（可重入）。  
  
### 参数：  
  
- Time: time_t类型的指针  
  
- ptmBuffer: tm结构缓冲区。  
  
### 返回值：
  
此函数成功返回格式化后的时间字符串指针，失败返回NULL。  
  
### 错误码：
  
无。  
  
### 备注：  
  
GJB 7714-2012错误码：无此接口。  
  
### 样例：  
  
```c  
#include <stdlib.h>  
#include <time.h>  
int main (int argc, char *argv[])  
{  
    PCHAR a[30],b[30];  
    time_t t_old,t_new;  
    time(&t_old);  
    sleep(3);  
    time(&t_new);  
  
    fprintf(stdout,"%s",ctime_r(&t_old, a[0]));      /* 转换为常用时间格式(缓冲区变量)*/  
    fprintf(stdout,"%s",ctime_r(&t_new, b[0]));  
  
    return  (0);  
}  
```  
  
  
## mktime ：将本地时间转换为UTC时间
  
  
### 函数原型：
  
```c  
#include < time.h>  
time_t  mktime  
(  
	struct tm  *ptm  
);  
```  
  
  
### 描述：
  
该函数的功能是将本地时间转换为UTC时间，它内部会进行本地时间到UTC时间的转换，因此正确的输入参数应该为本地时间。  
  
### 参数：  
  
- ptms ：tm类型的指针。  
  
### 返回值：
  
该函数成功返回转换后的UTC时间。  
  
### 错误码：
  
无。  
  
### 备注：  
  
GJB 7714-2012错误码：无此接口。  
  
### 样例：  
  
```c  
#include <stdlib.h>  
#include <time.h>  
int main (int argc, char *argv[])  
{  
    time_t t;  
    timelocal(&t);  
  
    printf("%lld\n",mktime(gmtime(&t)));             /* 将本地时间转换为UTC时间 */  
  
    return  (0);  
}  
```  
  
  
## timegm ：将UTC时间转换为本地时间
  
  
### 函数原型：
  
```c  
#include < time.h>  
time_t  timegm  
(  
	Struct  tm  *ptm  
);  
```  
  
  
### 描述：
  
该接口进行tm到time_t数据类型的转换，正确的输入参数应该为UTC时间。  
  
### 参数：  
  
- ptm ：tm类型的指针。  
  
### 返回值：
  
该函数成功返回转换后的本地时间。  
  
### 错误码：
  
无。  
  
### 备注：  
  
GJB 7714-2012错误码：无此接口。  
  
### 样例：  
  
```c  
#include <stdlib.h>  
#include <time.h>  
int main (int argc, char *argv[])  
{  
    time_t t;  
    time(&t);  
  
    printf("%lld\n",timegm(gmtime(&t)));            /* 将UTC时间转换为本地时间*/  
  
    return  (0);  
}  
```  
  
  
## clock ：获取进程或函数的时钟计数
  
  
### 函数原型：
  
```c  
#include < time.h>  
clock_t  clock  
(  
	Void  
);  
```  
  
  
### 描述：
  
该接口用于获取处理器调用某个进程或函数的时钟计数。  
  
### 参数：  
  
无。  
  
### 返回值：
  
进程或函数的时钟计数。  
  
### 错误码：
  
无。  
  
### 备注：  
  
GJB 7714-2012错误码：无此接口。  
  
### 样例：  
  
```c  
#include <stdlib.h>  
#include <time.h>  
int main (int argc, char *argv[])  
{  
    int i;  
    clock_t clockid;  
    for(i=0;i<20;i++)  
    {  
        printf("func time!\n");  
    }  
    clockid = clock();                        /* 进程的时钟计数     */  
    printf("clock is %lu\n",clockid);  
  
    return  (0);  
}  
```  
  
## pthread_getcpuclockid ：获得线程时钟源类型
  
  
### 函数原型：
  
```c  
#include <pthread.h>  
int  pthread_getcpuclockid  
(  
	pthread_t  thread,   
	clockid_t  *clock_id  
);  
```  
  
  
### 描述：
  
该接口用于获得线程时钟源。只支持一种CLOCK_THREAD_CPUTIME_ID线程:从启动开始所消耗的CPU时间。  
  
### 参数：  
  
- Thread ：线程ID  
  
- clock_id ：返回时钟类型。  
  
### 返回值：
  
此函数成功返回0，失败返回非0值。  
  
### 错误码：
  
无。  
  
### 备注：  
  
GJB 7714-2012错误码：无此接口。  
  
### 样例：  
  
```c  
#include <stdlib.h>  
#include <time.h>  
#include <pthread.h>  
int ret;  
static clock_t clockid;  
static pthread_t thread;  
void *routine (void *arg)  
{  
    // ……  
}  
int main (int argc, char *argv[])  
{  
    int ret;  
    clock_t clockid;  
    pthread_t thread;  
  
    // ……  
    ret = pthread_getcpuclockid(thread, &clockid);       /* 获取线程时钟源    */  
    if (ret != 0)  
    {  
        fprintf(stderr, "pthread getcpuclockid failed.\n");  
        return  (-1);  
    }  
    printf("pthread func time is %d\n",pthread_getcpuclockid(thread, &clockid));  
  
    return  (0);  
}  
```  
  
  
## timeradd ：时间相加
  
  
### 函数原型：
  
```c  
#include <sys/time.h>  
void	 timeradd  
(  
	struct timeval  *a,   
	struct timeval  *b,   
	struct timeval  *result  
);  
```  
  
  
### 描述：
  
该接口用于两个时间相加，结构保存于result中，内部会自动处理微秒到秒的进位问题。（注意，此操作内部不会有溢出或大小等任何安全性检测，因此结果可能出现不符合预期时间值，这些问题需要应用程序处理），此接口为一个宏定义。  
  
### 参数：  
  
- a ：时间参数  
  
- b ：时间参数  
  
- result ：保留结果。  
  
### 返回值：
  
无。  
  
### 错误码：
  
无。  
  
### 备注：  
  
GJB 7714-2012错误码：无此接口。  
  
### 样例：  
  
```c  
#include <stdio.h>  
#include <sys/time.h>  
  
#define timeval_show(des, tv) \  
        fprintf(stdout, des "sec = %llu, usec = %lu.\n",  \  
tv.tv_sec, tv.tv_usec)  
  
int main(int argc, char *argv[])  
{  
    struct timeval  a;  
    struct timeval  b;  
    struct timeval  result;  
  
    a.tv_sec    = 100;  
    a.tv_usec   = 800000;  
    b.tv_sec    = 80;  
    b.tv_usec   = 330000;  
  
    timeval_show("time a: ", a);  
    timeval_show("time b: ", b);  
  
    timeradd(&a, &b, &result);                                /* 时间相加      */  
  
    // ……  
  
    return  (0);  
}  
```  
  
  
## timersub ：时间相减
  
  
### 函数原型：
  
```c  
#include <sys/time.h>  
Void 	timersub  
(  
	struct timeval *a,  
	struct timeval *b,  
	struct timeval *result  
);  
```  
  
  
### 描述：
  
该接口用于两个时间相减，结构保存于result中，内部会自动处理微秒到秒的进位问题。（注意，此操作内部不会有溢出或大小等任何安全性检测，因此结果可能出现不符合预期时间值，这些问题需要应用程序处理），此接口为一个宏定义。  
  
### 参数：  
  
- a ：时间参数  
  
- b ：时间参数  
  
- result ：保留结果。  
  
### 返回值：
  
无。  
  
### 错误码：
  
无。  
  
### 备注：  
  
GJB 7714-2012错误码：无此接口。  
  
### 样例：  
  
```c  
#include <stdio.h>  
#include <sys/time.h>  
  
#define timeval_show(des, tv) \  
        fprintf(stdout, des "sec = %llu, usec = %lu.\n",  \  
tv.tv_sec, tv.tv_usec)  
  
int main(int argc, char *argv[])  
{  
    struct timeval  a;  
    struct timeval  b;  
    struct timeval  result;  
  
    a.tv_sec    = 100;  
    a.tv_usec   = 800000;  
    b.tv_sec    = 80;  
    b.tv_usec   = 330000;  
  
    timeval_show("time a: ", a);  
    timeval_show("time b: ", b);  
  
    timeradd(&a, &b, &result);  
    timeval_show("time a + b: ", result);  
    timersub(&a, &b, &result);                             /* 时间相减            */  
    // ……  
  
    return  (0);  
}  
```  
  
  
## timerclear ：时间值清零
  
  
### 函数原型：
  
```c  
#include <sys/time.h>  
void	 timerclear  
(  
	struct timeval  *tvp  
);  
```  
  
  
### 描述：
  
该接口用于将一个时间值清零。  
  
### 参数：  
  
- tvp ：时间指针。  
  
### 返回值：
  
无。  
  
### 错误码：
  
无。  
  
### 备注：  
  
GJB 7714-2012错误码：无此接口。  
  
### 样例：  
  
```c  
#include <stdio.h>  
#include <sys/time.h>  
  
#define timeval_show(des, tv) \  
        fprintf(stdout, des "sec = %llu, usec = %lu.\n",  \  
tv.tv_sec, tv.tv_usec)  
  
  
int main(int argc, char *argv[])  
{  
    struct timeval  a;  
    struct timeval  b;  
    struct timeval  result;  
  
    a.tv_sec    = 100;  
    a.tv_usec   = 800000;  
    b.tv_sec    = 80;  
    b.tv_usec   = 330000;  
  
    timeval_show("time a: ", a);  
    timeval_show("time b: ", b);  
  
    // ……  
  
    timerclear(&result);            /* 时间清零     */  
    if (timerisset(&result))  
    {  
        timeval_show("time is set: ", result);  
    } else {  
        timeval_show("time is zero: ", result);  
    }  
  
    // ……  
  
    return  (0);  
}  
```  
  
## timerisset ：检测时间值是否为0
  
  
### 函数原型：
  
```c  
#include < time.h>  
int	 timerisset  
(  
	struct timeval  *tvp  
);  
```  
  
  
### 描述：
  
该接口用于检测该时间值是否为0。  
  
### 参数：  
  
- tvp ：时间指针。  
  
### 返回值：
  
成功返回0，失败返回其它非零值。  
  
### 错误码：
  
无。  
  
### 备注：  
  
GJB 7714-2012错误码：无此接口。  
  
### 样例：  
  
```c  
#include <stdio.h>  
#include <sys/time.h>  
  
#define timeval_show(des, tv) \  
        fprintf(stdout, des "sec = %llu, usec = %lu.\n",  \  
tv.tv_sec, tv.tv_usec)  
  
int main(int argc, char *argv[])  
{  
    struct timeval  a;  
    struct timeval  b;  
    struct timeval  result;  
  
    a.tv_sec    = 100;  
    a.tv_usec   = 800000;  
    b.tv_sec    = 80;  
    b.tv_usec   = 330000;  
  
    // ……  
    if (timerisset(&result))               /* 检测时间是否为0              */  
    {  
        timeval_show("time is set: ", result);  
    } else {  
        timeval_show("time is zero: ", result);  
    }  
  
    // ……  
  
    return  (0);  
}  
```  
  
  
## timercmp ：比较两个时间
  
  
### 函数原型：
  
```c  
#include < time.h>  
int		timercmp  
(  
	struct timeval  *a,  
	struct timeval  *b,  
	CMP  
);  
```  
  
  
### 描述：
  
该接口用于比较两个时间，CMP为一个操作符，如>、==、<、!=、<=等，此接口为一个宏定义，此接口为一个宏定义。  
  
### 参数：  
  
- a ：时间参数  
  
- b ：时间参数  
  
- CMP ：比较符号。  
  
### 返回值：
  
成功返回0，失败返回其它非零值。  
  
### 错误码：
  
无。  
  
### 备注：  
  
GJB 7714-2012错误码：无此接口。  
  
### 样例：  
  
```c  
#include <stdio.h>  
#include <sys/time.h>  
  
#define timeval_show(des, tv) \  
        fprintf(stdout, des "sec = %llu, usec = %lu.\n",  \  
tv.tv_sec, tv.tv_usec)  
  
  
int main(int argc, char *argv[])  
{  
    struct timeval  a;  
    struct timeval  b;  
    struct timeval  result;  
  
    a.tv_sec    = 100;  
    a.tv_usec   = 800000;  
    b.tv_sec    = 80;  
    b.tv_usec   = 330000;  
  
    // ……  
  
    if (timercmp(&a, &b, >))                 /* 两个时间相比较            */  
    {  
        fprintf(stdout, "time a is larger than time b.\n");  
    } else {  
        fprintf(stderr, "time a is not larger than time b.\n");  
    }  
  
    return  (0);  
}  
```  
  
  
# 日志
  
  
## openlog ：打开一个到系统日志记录程序的链接
  
  
### 函数原型：
  
```c  
void openlog  
(  
	const char *ident,   
	int logopt,   
	int facility  
);  
```  
  
  
### 描述：
  
该接口用来打开一个到系统日志记录程序的连接。  
  
### 参数：
  
- ident : 任意的字符串，将固定地加在每行日志的前面以标识这个日志，该标志通常设置为程序的名称。  
  
- logopt ：选项标志。取值有：  
  
|选项标志|说明|  
|---|---|  
|LOG_PID|每条消息包含进程ID|  
|LOG_CONS|如果发送中终端显示|  
|LOG_ODELAY|打开连接时延时|  
|LOG_NDELAY|打开连接时不延时|  
|LOG_NOWAIT|不等待子进程|  
|LOG_PERROR|同时打印到标准错误|  
  
- facility ：能力参数，取值如下：  
  
|facility|说明|  
|---|---|  
|LOG_AUTH|认证相关的日志|  
|LOG_KERN|内核相关的日志|  
|LOG_MAIL|邮件相关的日志|  
|LOG_DAEMON|守护进程相关的日志|  
|LOG_USER|用户相关的日志|  
|LOG_SYSLOG|syslog本身相关的日志|  
|LOG_LPR|打印相关的日志|  
|LOG_NEWS|新闻相关的日志|  
|LOG_UUCP|Unix to Unix cp相关的日志|  
|LOG_CRON|任务计划相关的日志|  
|LOG_AUTHPRIV|权限、授权相关的日志|  
|LOG_FTP|FTP相关的日志|  
|LOG_LOCAL0~LOG_LOCAL7|用户自定义使用|  
  
### 返回值：
  
无。  
  
### 错误码：
  
无。  
  
### 备注：
  
无。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <socket.h>  
#include <sys/un.h>  
#include <syslog.h>  
#include <pthread.h>  
#define LOGFILE         "log"  
void *Thread2 (void *arg)  
{  
    sleep (3);  
    openlog("SylixOS-SYSLOG", LOG_PID, LOG_LOCAL0); /* 打开一个到日志的链接  */  
  
    // ……  
    return (0);  
}  
```  
  
## closelog ：关闭到系统日志记录程序的链接
  
  
### 函数原型：
  
```c  
void     closelog  
(  
	void  
);  
```  
  
  
### 描述：
  
该接口用来关闭到系统日志记录程序的连接。  
  
### 参数：
  
无。  
  
### 返回值：
  
无。  
  
### 错误码：
  
无。  
  
### 备注：
  
无。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <socket.h>  
#include <sys/un.h>  
#include <syslog.h>  
#include <pthread.h>  
#define LOGFILE         "log"  
void *Thread2 (void *arg)  
{  
    sleep (3);  
	// ……  
	syslog (LOG_ALERT, "ggm logalert\n");   /*  向日志发送消息             */  
	// ……  
    closelog();  
    return (0);  
}  
```  
  
  
## setlogmask ：设置Syslog的记录屏蔽字
  
  
### 函数原型：
  
```c  
int      setlogmask  
(  
	int maskpri  
);  
```  
  
  
### 描述：
  
该接口用来设置默认系统日志结构的syslog记录屏蔽字。  
  
### 参数：
  
- maskpri ：屏蔽码。  
  
### 返回值：
  
返回原先的屏蔽码。  
  
### 错误码：
  
无。  
  
### 备注：
  
无。  
  
### 样例：
  
```c  
无
```  
  
  
## setlogmask_r ：设置指定日志结构记录屏蔽码
  
  
### 函数原型：
  
```c  
int      setlogmask_r  
(  
	int maskpri,   
	struct syslog_data *data  
);  
```  
  
  
### 描述：
  
该接口用来设置指定日志结构记录屏蔽字。  
  
### 参数： 
  
- maskpri ：屏蔽码。  
  
- data ：指定的日志结构。  
  
### 返回值：
  
成功返回原先的屏蔽码，失败返回-1；并置相应的错误码。  
  
### 错误码：
  
- EINVAL ：参数无效。  
  
### 备注：
  
无。  
  
### 样例：
  
```c  
无
```  
  
  
## syslog ：提交信息到系统日志
  
  
### 函数原型：
  
```c  
void     syslog  
(  
	int priority,   
	const char *message,  
 	...  
);  
```  
  
  
### 描述：
  
该接口用来提交信息到系统日志。  
  
### 参数：
  
- priority ：消息的紧急级别。取值有：  
  
|等级名|说明|  
|---|---|  
|LOG_EMERG|导致系统不可用|  
|LOG_ALERT|必须马上处理的|  
|LOG_CRIT|比较严重的|  
|LOG_ERR|错误信息|  
|LOG_WARNING|可能影响系统功能，警告事件|  
|LOG_NOTICE|不影响正常功能，需要注意的信息|  
|LOG_INFO|一般信息|  
|LOG_DEBUG|程序或系统的调试信息|  
  
- message ：消息的格式，之后是格式对应的参数。如printf格式一样使用。  
  
### 返回值：
  
无。  
  
### 错误码：
  
无。  
  
### 备注：
  
在openlog时可以通过是否设置LOG_PERROR与LOG_CONS这两个选项标志来控制是否打印到终端上；还可以利用网络来将日志打印到指定文件中。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <socket.h>  
#include <sys/un.h>  
#include <syslog.h>  
#include <pthread.h>  
#define LOGFILE         "log"  
void *Thread2 (void *arg)  
{  
    sleep (3);  
	// ……  
	syslog (LOG_ALERT, "ggm logalert\n");   /*  向日志发送消息             */  
	// ……  
    return (0);  
}  
```  
  
  
## syslog_r ：提交信息到指定日志
  
  
### 函数原型：
  
```c  
void     syslog_r  
(  
	int priority,  
	struct syslog_data *data,  
	const char *message,  
 	...  
);  
```  
  
  
### 描述：
  
该接口用来提交信息到打开的指定的日志中。  
  
### 参数：
  
- priority ：消息的紧急级别，取值同表1.9.3。  
  
- data ：指定的日志结构。  
  
- message ：消息的格式，之后是格式对应的参数。如printf格式一样使用。  
  
### 返回值：
  
无。  
  
### 错误码：
  
- EINVAL：参数无效。  
  
### 备注：
  
在openlog时可以通过是否设置LOG_PERROR与LOG_CONS这两个选项标志来控制是否打印到终端上；还可以利用网络来将日志打印到指定文件中。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <syslog.h>  
int main (int argc, char **argv)  
{  
	struct syslog_data data = SYSLOG_DATA_INIT;  
	data.log_stat = LOG_CONS;  
  
    // ……  
  
    syslog_r (LOG_ALERT, &data, "ggm logalert\n");   /*  提交信息到系统日志    */  
    // ……  
    return  (0);  
}  
```  
  
  
# 自旋锁
  
  
## pthread_spin_init ：创建一个自旋锁
  
### 函数原型：
  
```c  
int		pthread_spin_init  
(  
	pthread_spinlock_t  *pspinlock,   
	int  pshare  
);  
```  
  
  
### 描述：
  
该接口用来创建一个自旋锁。  
  
### 参数：
  
- pspinlock ：用来初始化自旋锁控制块指针。  
  
- pshare ：共享属性。（目前没用）  
  
### 返回值：
  
成功返回0，失败返回相应的错误码。  
  
### 错误码：
  
- EINVAL ：参数无效。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <pthread.h>  
static pthread_spinlock_t G_spinlock;  
// ……  
int main (int argc, char **argv)  
{  
    int ret;  
    ret = pthread_spin_init(&G_spinlock,1);		    /*  创建自旋锁              */  
    if(ret != 0)  
    {  
    	printf("funcpthread_spin_init error\n");  
    	return -1;  
    }  
  
	// ……  
    return  (0);  
}  
```  
  
  
## pthread_spin_destroy ：删除指定自旋锁
  
  
### 函数原型：
  
```c  
int		pthread_spin_destroy  
(  
	pthread_spinlock_t  *pspinlock  
);  
```  
  
  
### 描述：
  
该接口用来销毁一个自旋锁。  
  
### 参数：
  
- pspinlock ：需要销毁的自旋锁控制块指针。  
  
### 返回值：
  
成功返回0，失败返回相应的错误码。  
  
### 错误码：
  
- EINVAL ：参数无效。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <pthread.h>  
static pthread_spinlock_t G_spinlock;  
// ……  
int main (int argc, char **argv)  
{  
	int ret;  
	// ……  
  
    ret = pthread_spin_destroy(&G_spinlock);	        /*  销毁自旋锁              */  
    if(ret != 0)  
    {  
    	perror("func pthread_spin_destroy\n");  
    	return -1;  
    }  
  
    printf("thread end\n");  
    return  (0);  
}  
```  
  
  
## pthread_spin_lock ：给自旋锁上锁
  
  
### 函数原型：
  
```c  
int		pthread_spin_lock  
(  
	pthread_spinlock_t  *pspinlock  
);  
```  
  
  
### 描述：
  
该接口用来给自旋锁上锁，不能在中断中调用。  
  
### 参数：
  
- pspinlock ：自旋锁控制块指针。  
  
### 返回值：
  
成功返回0，失败返回相应的错误码。  
  
### 错误码：
  
- EINVAL ：参数无效。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <pthread.h>  
static pthread_spinlock_t G_spinlock;  
static void *Thread1(void *arg)  
{  
	int ret;  
  
	ret = pthread_spin_lock(&G_spinlock);			    /*  lock 一个自旋锁          */  
	if(ret != 0)  
	{  
		printf("func spin lock error\n");  
		return (void*)-1;  
	}  
	// ……  
  
	printf("this is Thread1\n");  
	return NULL;  
}  
```  
  
  
## pthread_spin_unlock ：解锁自旋锁
  
### 函数原型：
  
```c  
int		pthread_spin_unlock  
(  
	pthread_spinlock_t  *pspinlock  
);  
```  
  
  
### 描述：
  
该接口用来给自旋锁解锁，不能在中断中调用。  
  
### 参数：
  
- pspinlock ：自旋锁控制块指针。  
  
### 返回值：
  
成功返回0，失败返回相应的错误码。  
  
### 错误码：
  
- EINVAL ：参数无效。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <pthread.h>  
static pthread_spinlock_t G_spinlock;  
static void *Thread1(void *arg)  
{  
	int ret;  
	// ……  
	ret = pthread_spin_unlock(&G_spinlock);		    /*  unlock 一个自旋锁        */  
	if(ret != 0)  
	{  
		printf("func create unlock error\n");  
		return (void*)-1;  
	}  
  
	printf("create pthread1\n");  
	return NULL;  
}  
```  
  
  
## pthread_spin_trylock ：尝试给自旋锁上锁
  
  
### 函数原型：
  
```c  
int		pthread_spin_trylock  
(  
	pthread_spinlock_t  *pspinlock  
);  
```  
  
  
### 描述：
  
该接口用来尝试给自旋锁上锁，不能在中断中调用。  
  
### 参数：
  
- pspinlock ：自旋锁控制块指针。  
  
### 返回值：
  
成功返回0，失败返回相应的错误码。  
  
### 错误码：
  
- EINVAL ：参数无效。  
- EBUSY ：设备资源忙。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <pthread.h>  
static pthread_spinlock_t G_spinlock;  
// ……  
static void *Thread2(void *arg)  
{  
	int ret;  
	ret = pthread_spin_trylock(&G_spinlock);		    /*  trylock 一个自旋锁        */  
	if(ret != 0)  
	{  
		printf("func pthread_spin_trylock error\n");  
	}  
	pthread_spin_unlock(&G_spinlock);  
  
	printf("this is Thread2\n");  
	return NULL;  
}  
```  
  
  
## pthread_spin_lock_irq_np ：给自旋锁上锁，同时锁定中断
  
  
### 函数原型：
  
```c  
int		pthread_spin_lock_irq_np  
(  
	pthread_spinlock_t  *pspinlock,   
pthread_int_t *irqctx  
);  
```  
  
  
### 描述：
  
该接口用来给自旋锁上锁，并同时锁定中断，不能在中断中调用。  
  
### 参数：
  
- pspinlock ：自旋锁控制块指针。  
  
- irqctx ：存放体系结构相关中断状态保存结构的指针。  
  
### 返回值：
  
成功返回0，失败返回相应的错误码。  
  
### 错误码：
  
- EINVAL ：参数无效。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <pthread.h>  
static pthread_spinlock_t G_spinlock;  
static void *Thread1(void *arg)  
{  
	int ret;  
	pthread_int_t init;  
  
	ret = pthread_spin_lock_irq_np(&G_spinlock,&init);    
											/*   lock 一个自旋锁, 同时锁定中断*/  
	if(ret != 0)  
	{  
		perror("func pthread_spin_lock error\n");  
		return (void*)-1;  
	}  
	printf("this is Thread1\n");  
	// ……  
  
	return NULL;  
}  
```  
  
## pthread_spin_unlock_irq_np ：该自旋锁上锁，同时解锁中断
  
  
### 函数原型：
  
```c  
int		pthread_spin_unlock_irq_np  
(  
	pthread_spinlock_t  *pspinlock,   
	pthread_int_t irqctx  
);  
```  
  
  
### 描述：
  
该接口用来给自旋锁解锁，并同时解锁中断，允许中断响应。不能在中断中调用。  
  
### 参数：
  
- pspinlock ：自旋锁控制块指针。  
  
- irqctx ：体系结构相关中断状态保存结构。  
  
### 返回值：
  
成功返回0，失败返回相应的错误码。  
  
### 错误码：
  
- EINVAL ：参数无效。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <pthread.h>  
static pthread_spinlock_t G_spinlock;  
static void *Thread1(void *arg)  
{  
	int ret;  
	pthread_int_t init;  
  
	ret = pthread_spin_lock_irq_np(&G_spinlock,&init); /* lock 一个自旋锁, 同时锁定中断*/  
	if(ret != 0)  
	{  
		perror("func pthread_spin_lock error\n");  
		return (void*)-1;  
	}  
	printf("func pthread_spin_lock success\n");  
  
	ret = pthread_spin_unlock_irq_np(&G_spinlock,init);	/*unlock 一个自旋锁, 同时锁定中断*/  
	if(ret != 0)  
	{  
		perror("func pthread_spin_unlock_irq_np error\n");  
		return (void*)-1;  
	}  
  
	printf("this is Thread1\n");  
	return NULL;  
}  
```  
  
## pthread_spin_trylock_irq_np ：尝试给自旋锁上锁，同时锁定中断
  
  
### 函数原型：
  
```c  
int		pthread_spin_trylock_irq_np  
(  
	pthread_spinlock_t  *pspinlock,   
	pthread_int_t *irqctx  
);  
```  
  
  
### 描述：
  
该接口用来尝试给自旋锁上锁，如果成功同时锁定中断，不能在中断中调用。  
  
### 参数：
  
- pspinlock ：自旋锁控制块指针。  
  
- irqctx ：存放体系结构相关中断状态保存结构的指针。  
  
### 返回值：
  
成功返回0，失败返回相应的错误码。  
  
### 错误码：
  
- EINVAL ：参数无效。  
- EBUSY ：设备资源忙。  
  
### 样例：
  
```c  
#include <stdio.h>  
#include <pthread.h>  
static pthread_spinlock_t G_spinlock;  
static void *Thread1(void *arg)  
{  
	printf(" this is Thread1\n");  
	return NULL;  
}  
static void *Thread2(void *arg)  
{  
	int ret;  
	pthread_int_t init;  
	/*  
 	* trylock 一个自旋锁, 如果成功同时锁定中断  
	*/  
	ret = pthread_spin_trylock_irq_np(&G_spinlock,&init);  
	if(ret == 0)  
	{  
		perror("func pthread_spin_trylock_irq_np success\n");  
		pthread_spin_unlock_irq_np(&G_spinlock,init);  
	}  
  
	printf("create pthread2\n");  
	return NULL;  
}  
```  
  
  
# 一次性初始化
  
  
## pthread_once_t ：一次性初始化
  
  
### 函数原型：
  
```c  
int  pthread_once  
(  
	pthread_once_t  *once,  
 	void (*pfunc)(void)  
);  
```  
  
  
### 描述：
  
该接口用来一次性初始化。  
  
### 参数：  
  
once ：pthread_once_t类型变量的指针。  
pfunc :完成一次性初始化的函数指针。  
  
### 返回值：
  
正确返回0，失败返回相应的错误码。  
  
### 错误码： 
  
- ERROR_NONE ：没有错误。  
  
### 样例：  
  
```c  
#include <stdio.h>  
#include <pthread.h>  
  
static pthread_once_t   G_once = PTHREAD_ONCE_INIT;  
static pthread_mutex_t  G_lock;  
static pthread_cond_t   G_cond;  
static int             G_count = 0;  
  
static void var_init(void)  
{  
	pthread_mutex_init(&G_lock,NULL);			/*  创建一个互斥量             */  
	pthread_cond_init(&G_cond,NULL);			/*  初始化条件变量             */  
}  
  
static void *thread1(void *arg)  
{  
	pthread_once(&G_once,var_init);				/*  线程安全的仅执行一次指定函数*/  
	while (1)  
	{  
		pthread_mutex_lock(&G_lock);			/*  lock 一个互斥量              */  
		pthread_cond_wait(&G_cond, &G_lock);	/*  等待一个条件变量广播信号    */  
		printf("thread_a(): count = %d\n",G_count);  
		pthread_mutex_unlock(&G_lock);  
	}  
	return (NULL);  
}  
  
static void *thread2(void *arg)  
{  
	pthread_once(&G_once,var_init);  
  
	while(1)  
	{  
		pthread_mutex_lock(&G_lock);  
  
		G_count++;  
  
		pthread_cond_broadcast(&G_cond);		/*  发送一个条件变量广播信号     */  
		pthread_mutex_unlock(&G_lock);  
  
		sleep(1);  
	}  
	return (NULL);  
  
}  
int main (int argc, char **argv)  
{  
    pthread_t   tid1;  
    pthread_t   tid2;  
    int         ret;  
    ret = pthread_create(&tid1, NULL, thread1, NULL);	/*  建立线程1               */  
    if(ret != 0)  
    {  
    	fprintf(stderr, "pthread create failed.\n");  
    	return  (-1);  
    }  
    ret = pthread_create(&tid2, NULL, thread2, NULL);	/*  建立线程2               */  
    if (ret != 0)  
    {  
        fprintf(stderr, "pthread create failed.\n");  
        return  (-1);  
    }  
  
     pthread_join(tid1, NULL);  
     pthread_join(tid2, NULL);  
  
    return  (0);  
}  
```  
  
  
