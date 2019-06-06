# 概述

Shell 是操作系统“外壳”程序，它向用户提供一个命令行类型的使用界面，也可称作命令解析器，系统开发人员可使用此接口来操作计算机。HYSylixOS 的Shell 程序为tShell。
tShell 程序是系统开发人员操作HYSylixOS 操作系统最为简单与便捷的接口，ttinyShell 运行在内核空间，内部内建了HYSylixOS 内核里的常用命令，tShell 程序运行界面，如图1.1 所示。

@import "./image/image001.png"

<center>图1.1 ttinyShell运行界面</center>


tShell内建的命令，分为系统命令、文件命令、网络命令、时间命令、动态装载命令和其他命令，详细说明可使用help [keyword]命令在HYSylixOS设备上查看。
由于内核版本与裁剪配置不同，所以tShell内建的命令在不同版本不同配置的HYSylixOS系统上会有所不同。
当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。

# 系统命令
## 简介

<center>表2-1系统命令介绍</center>

|命令名|简要说明|
|---|---|
|aborts|显示当前操作系统异常处理统计信息|
|affinity|显示或设置线程或进程调度的CPU集合|
|buss|显示系统中挂载的所有总线信息|
|clear|清除当前屏幕|
|color|根据LS_COLORS初始化配色方案|
|cpuus|查看cpu利用率|
|devs|显示系统中挂载的所有设备|
|drvlics|显示系统中所有安装的驱动程序表许可证信息|
|drvs|显示设备驱动程序表信息|
|echo|回显命令，此命令将回显用户输入的参数|
|env|查看当前的环境变量表|
|eventset|展示事件集信息|
|exec|执行一个程序|
|exit|退出当前shell终端|
|free|显示系统当前的内存信息|
|help|显示所有shell命令|
|ttinyShell|所有内建命令列表，如果带有参数，则可显示以参数为名的命令介绍|
|hostname|显示或设置当前HYSylixOS镜像主机名|
|Ints|查看系统中断向量表信息|
|kill|向指定的线程或进程发送信号，默认发送SIGKILL信号|
|login|切换用户，重新登录|
|loglevel|显示或设置当前内核日志（printk）打印等级|
|mems|查看操作系统内核内存堆与系统内存堆内存使用情况|
|part|显示指定的内存分区信息|
|pcis|显示PCI总线的信息|
|ps|查看系统所有进程的信息|
|reboot|重新启动计算机|
|renice|设置指定进程的优先级|
|restart|重新启动线程|
|sem|显示信号信息|
|shell|使用ttydevice作为标准文件创建一个shell|
|shstack|显示或者设置shell任务堆栈大小|
|shutdown|关闭或重启系统|
|sigqueue|发送信号给线程或进程|
|sleep|当前线程睡眠指定的时间|
|sprio|设置指定线程的优先级|
|ss|查看系统中所有线程与中断系统堆栈使用情况|
|top|查看cpu的使用率|
|tp|查看系统中被阻塞的线程信息|
|ts|查看系统线程信息|
|tty|显示当前shell终端对应的tty文件|
|vardel|删除一个指定的系统环境变量|
|varload|从指定参数的文件中提取装载环境变量表|
|vars|显示当前的环境变量|
|varsave|保存当前的操作系统环境变量表|
|virtuals|显示 vmm 虚拟存储器信息|
|which|检查参数指定的文件位置|
|who|查看当前登录用户身份|
|zones|查看操作系统物理页面分区使用情况|

## 命令解析	
系统Shell命令的详细介绍和使用方法，主要有命令格式，命令说明，命令说明，常用选项，使用说明，参数说明，备注，样例，配置以及输出定义。  
### aborts
#### 命令格式：
```shell
aborts
```
#### 命令说明：
显示当前操作系统异常处理统计信息。
#### 常用选项：
无。
#### 使用说明：
该命令会显示系统当前内存访问异常、内存分配错误、分配物理页错误和页面映射错误次数。
#### 参数说明：
无。
#### 备注：
无。
#### 样例：

@import "./image/image002.png"
<center>图2-1 aborts命令结果</center>
#### 配置：
当LW_CFG_VMM_EN>0时，对虚拟内存支持，该命令可用。
#### 输出定义：
系统当前内存访问异常、内存分配错误、分配物理页错误和页面映射错误次数。

### affinity
#### 命令格式：
```shell
affinity 
affinity		[pid/thread id]  [cpu id]
affinity		[pid/thread id]  clear
```

#### 命令说明：
显示或设置线程或进程调度的CPU集合。
#### 常用选项：
-clear：清除该进程或线程对cpu集合的调度。
#### 使用说明：
该命令有三种用法：
1.	affinity不跟参数时，显示线程和进程调度的cpu集合；
2.	当affinity参数是线程id或进程id、cpu id时，设置该进程或线程调度的cpu集合；
3.	当affinity参数是线程id或进程id、clear时，清除该进程或线程对cpu集合的调度。
#### 参数说明：
[pid/thread id]：线程或进程的id号。
[cpu id]：cpu编号。
#### 备注：
线程或进程调度cpu的信息存放在文件/proc/kernel/affinity中。
#### 样例：
 
@import "./image/image003.png"
@import "./image/image004.png"
<center>图2.2 affinity命令结果</center>

#### 配置：
当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。当(LW_CFG_SMP_EN > 0) 和(LW_CFG_POSIX_EN > 0)时，系统对多处理器支持并且使能posix兼容库后，该命令将会被包含。
#### 输出定义：
线程名、线程句柄、进程id、cpu id各项信息。
### buss
#### 命令格式：
```shell
buss
```
#### 命令说明：
显示系统中挂载的所有总线信息。
#### 常用选项：
无。
#### 使用说明：
该命令用来显示系统中挂载的所有总线信息。
#### 参数说明：
无。
#### 备注：
无。
#### 样例：
@import "./image/image005.png"
图2.3 buss命令结果
#### 配置：
该命令属于系统提供的tshell命令。当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。
#### 输出定义：
总线名称、挂载数目。
### clear
#### 命令格式：
```shell
clear
```
#### 命令说明：
清除当前屏幕
#### 常用选项：
无。
#### 使用说明：
该命令用来清除当前屏幕显示的信息。
#### 备注：
无。
#### 样例：
 
 @import "./image/image006.png"
 @import "./image/image007.png"
图2.4 clear命令结果
#### 配置：
该命令属于系统提供的tshell命令，当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令，则包含该命令。
#### 输出定义：
无。
### color
#### 命令格式：
```shell
color
```
#### 命令说明：
根据LS_COLORS 初始化配色方案。
#### 常用选项：
无。
#### 使用说明：
该命令用于更新颜色方案。
#### 参数说明：
无。
#### 备注：
无。
#### 样例：
 @import "./image/image008.png"
图2.5 color命令结果
#### 配置：
该命令属于系统提供的tshell命令，当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令，则包含该命令。
#### 输出定义：
无。
### cpuus
#### 命令格式：
```shell
cpuus
cpuus  –n  times  -t  wait_seconds
```
#### 命令说明：
查看cpu利用率。
#### 常用选项：
-n：检测cpu利用次数。
-t：指明每次检查用时。
#### 使用说明：
该命令有2种用法，其中-n后面跟的参数是检测cpu利用率的次数，-t指明每次检查用的时间。cpuus 默认检测1次，检测的时间是1s，cpuus –n times –t wait_seconds 可以指明检测times次，每次检测的时间是wait_seconds秒。规定检测时间不得超过10秒。
#### 参数说明：
无。
#### 备注：
无。
#### 样例：
 @import "./image/image009.png"
图2.6 cpuus命令结果
#### 配置：
该命令属于系统提供的tshell命令，当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令，则包含该命令。
#### 输出定义：
线程名、线程句柄、优先级、cpu使用率。
### devs
#### 命令格式：
```shell
devs
devs –a
```
#### 命令说明：
显示系统中挂载的所有设备。
#### 常用选项：
–a：显示挂载设备全部信息。
#### 使用说明：
该命令用于显示系统中挂载设备的信息，其中devs不带参数会显示挂载设备的设备号、打开情况和设备名字，devs带参数-a会显示挂载设备全部的信息，包括设备号、打开情况、设备名字以及设备类型。
#### 参数说明：
无。
#### 备注：
无。
#### 样例：
  @import "./image/image010.png"
  @import "./image/image011.png"
图2.7 devs命令结果

#### 配置：
该命令属于系统提供的tshell命令，当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令，则包含该命令。
#### 输出定义：
drv：设备号；
open：打开情况；
name：设备名字；
type：设备类型。
### drvlics
#### 命令格式：
```shell
drvlics
```
#### 命令说明：
显示系统中所有安装的驱动程序表许可证信息。
#### 常用选项：
无。
#### 使用说明：
该命令用于显示系统中安装设备的设备号、设备的描述、作者和版本号。
#### 参数说明：
无。
#### 备注：
无。
#### 样例：
 @import "./image/image012.png"
图2.8 drvlics命令结果
#### 配置：
该命令属于系统提供的tshell命令，当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令，则包含该命令。
#### 输出定义：
drv：设备号；
description：设备的描述；
author：作者；
license：版本号。
### drvs
#### 命令格式：
```shell
drvs
```
#### 命令说明：
显示设备驱动程序表信息。
#### 常用选项：
无。
#### 使用说明：
该命令会显示设备驱动的驱动号（drv）及创建（create）、删除（delete）、打开（open）、关闭（close）、读（read）、写（write）、控制（ioctl）、读设备扩展（readex）、写设备扩展（writeex）、选择功能（select）、读写指针移动（lseek）、建立链接文件、读取链接文件、文件映射函数的地址。
#### 参数说明：
无。
#### 备注：
无。
#### 样例：
 @import "./image/image013.png"
图2.9 drvs命令结果
#### 配置：
该命令属于系统提供的tshell命令，当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令，包含该命令。
#### 输出定义：
drv：驱动号；
create：创建；
delete：删除；
open：打开；
close：关闭；
read：读；
write：写；
ioctl：控制。
###  env
#### 命令格式：
```shell
env
```
#### 命令说明：
查看当前的环境变量表。
#### 常用选项：
无。
#### 使用说明：
该命令用来显示系统全局的环境变量表。
#### 参数说明：
无。
#### 备注：
无。
#### 样例：
 @import "./image/image014.png"
图2.10 env命令结果
#### 配置：
该命令属于系统提供的tshell命令。当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。
#### 输出定义：
无。
###  echo
#### 命令格式：
```shell
echo  message
```
#### 命令说明：
回显命令，此命令将回显用户输入的参数。
#### 常用选项：
无。
#### 使用说明：
该命令显示echo后的参数。
#### 参数说明：
message：要显示的内容或参数。
#### 备注：
无。
#### 样例：
 @import "./image/image015.png"
图2.11 echo命令结果
#### 配置：
该命令属于系统提供的tshell命令。当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。
#### 输出定义：
无。
###  *eventset
#### 命令格式：
```shell
eventset  eventset  handle
```
#### 命令说明：
展示事件集信息。
#### 常用选项：
无。
#### 使用说明：
该命令用于显示事件集的相关信息。
#### 参数说明：
无。
#### 备注：
仅在x86环境可用。
#### 样例：
	使用SylixOS事件集操作函数编写程序，打印事件集id号。
 
 @import "./image/image016.png"
图2.12 eventset命令结果
#### 配置：
该命令属于系统提供的tshell命令。当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。
#### 输出定义：
无。
###  exec
#### 命令格式：
```shell
exec  program file  arguments
```
#### 命令说明：
执行一个程序。
#### 常用选项：
无。
#### 使用说明：
该命令用于执行一个程序，exec后跟文件名，参数列表。 
#### 参数说明：
无。
#### 备注：
无。
#### 样例：
 @import "./image/image017.png"
图2.13 exec命令结果
#### 配置：
当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。当LW_CFG_MODULELOADER_EN> 0，需要提供模块装载服务，该命令将会被包含。
#### 输出定义：
无。
###  exit
#### 命令格式：
```shell
exit
```
#### 命令说明：
退出当前shell终端。
#### 常用选项：
无。
#### 使用说明：
该命令用于退出当前shell终端。 
#### 参数说明：
无。
#### 备注：
无。
#### 样例：
 @import "./image/image018.png"
图2.14 exit命令结果
#### 配置：
该命令属于系统提供的tshell命令。当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。
#### 输出定义：
无。
###  free
#### 命令格式：
```shell
free
```
#### 命令说明：
显示系统当前的内存信息。
#### 常用选项：
无。
#### 使用说明：
该命令用于显示当前内存的信息，如果对虚拟内存支持，会显示vmm物理存储器的信息。
#### 参数说明：
无。
#### 备注：
无。
#### 样例：
 @import "./image/image019.png"
图2.15 free命令结果
#### 配置：
该命令属于系统提供的tshell命令，当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令，则包含该命令。
#### 输出定义：
进程名称，内存总大小，已使用，使用百分比等。
###  help
#### 命令格式：
```shell
help
help  keyword
help  –s  keyword
```
#### 命令说明：
显示ttinyShell所有内建命令列表，如果带有参数，则可显示以参数为名的命令介绍。
#### 常用选项：
-s：显示keyword为名的命令介绍。
#### 使用说明：
该命令只有3种用法，当help后面不跟参数时，显示ttinyShell所有内建命令列表，help keyword与help –s keyword的功能相同，都是显示以keyword为名的命令介绍
#### 参数说明：
-keyword：关键词，显示关键词的用法。
#### 备注：
help参数keyword支持所用通配符（*、?），当使用通配符时，会列出匹配的内建命令列表
#### 样例：
 
 @import "./image/image020.png"
 @import "./image/image021.png"
图2.16 help命令结果
#### 配置：
该命令属于系统提供的tshell命令，当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令，则包含该命令。
#### 输出定义：
显示系统内的shell命令或显示某条shell命令的具体用法。
###  hostname
#### 命令格式：
```shell
hostname
hostname  name
```
#### 命令说明：
显示或设置当前SylixOS镜像主机名。
#### 常用选项：
无。
#### 使用说明：
该命令有2种用法， 当hostname后面没跟参数时，显示当前的SylixOS镜像主机名，如果跟了参数，则将Sylix镜像名改为该参数。
#### 参数说明：
name：自定义的主机名。
#### 备注：
无。
#### 样例：
 @import "./image/image022.png"
图2.17 hostname命令结果
#### 配置：
该命令属于系统提供的tshell命令，当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令，则包含该命令。
#### 输出定义：
无。
###  ints
#### 命令格式：
```shell
ints
ints  cupidstart
ints  cupidstart  cupidend
```
#### 命令说明：
查看系统中断向量表信息。
#### 常用选项：
无。
#### 使用说明：
该命令用于显示所有cpu的中断向量表信息 。
#### 参数说明：
cupidstart：显示cpu id大于等于cupidstart上的中断向量表信息。
cupidstart  cupidend：则显示cpu id大于等于cupidstart且小于等于cpuidend上的中断向量表信息。
#### 备注：
无。
#### 样例：
 @import "./image/image023.png"
图2.18 ints命令结果
#### 配置：
该命令属于系统提供的tshell命令，当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令，则包含该命令。
#### 输出定义：
irq：中断号；
entry：起始地址；
param：内存地址；
###  kill
#### 命令格式：
```shell
kill  tid/pid
kill  -n  signum  tid/pid 
```
#### 命令说明：
向指定的线程或进程发送信号，默认发送SIGKILL信号。
#### 常用选项：
-n：发送数字signum所代表的信号。
#### 使用说明：
该命令有2种用法， kill tid/pid，向进程或线程发送SIGKILL（9）信号，kill –n signum tid/pid向进程或线程发送数字signum所代表的信号。
#### 参数说明：
tid/pid：进程或线程号。
#### 备注：
进程号是十进制，线程号是十六进制。
#### 样例：
 @import "./image/image024.png"
图2.19 kill命令结果
#### 配置：
当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。当LW_CFG_SIGNAL_EN > 0时，允许系统使用信号，该命令将会被包含。
#### 输出定义：
系统内个线程的详细信息。
###  login
#### 命令格式：
```shell
login
```
#### 命令说明：
切换用户，重新登录。
#### 常用选项：
无。
#### 使用说明：
该命令用于切换用户，输入login回车会提示你输入你登录的用户名、密码，验证通过则切换用户，否则还是以原先的用户身份登录。
#### 参数说明：
无。
#### 备注：
要登录的用户必须使能，否则登录失败。
#### 样例：
 @import "./image/image025.png"
图2.20 login命令结果
#### 配置：
该命令属于系统提供的tshell命令，当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令，则包含该命令。
#### 输出定义：
无。
###  loglevel
#### 命令格式：
```shell
loglevel
loglevel level 
```
#### 命令说明：
显示或设置当前内核日志（printk）打印等级。
#### 常用选项：
无。
#### 使用说明：
该命令有2种用法， 显示当前内核日志printk接口的打印等级。若后面带参数，则将printk的打印等级设置成相应的等级。 
#### 参数说明：
level：等级设置。
#### 备注：
无。
#### 样例：
 @import "./image/image026.png"
图2.21 loglevel命令结果
#### 配置：
当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。当LW_CFG_LOG_LIB_EN > 0时，允许系统提供日志管理库，该命令将会被包含。
#### 输出定义：
无。
###  mems
#### 命令格式：
```shell
mems 
mems rid 
```
#### 命令说明：
查看操作系统内核内存堆与系统内存堆内存使用情况。
#### 常用选项：
无。
#### 使用说明：
该命令用来查看系统内核内存堆与系统内存堆使用情况。
#### 参数说明：
无。
#### 备注：
无。
#### 样例：
 @import "./image/image027.png"
图2.22 mems命令结果
#### 配置：
该命令属于系统提供的tshell命令，当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令，则包含该命令。
#### 输出定义：
系统内核内存堆与系统内存堆使用情况,有内核、内存大小、使用情况、内存片段等参数。
###  part
#### 命令格式：
```shell
part  partition  handle
```
#### 命令说明：
显示指定的内存分区信息。
#### 常用选项：
无。
#### 使用说明：
该命令用来显示指定的内存分区信息 
#### 参数说明：
无。
#### 备注：
无。
#### 样例：
运行以下程序
```C
#include <SylixOS.h>
typedef struct my_element
{
	INT  iValue;
	}MY_ELEMENET;
	#define ELEMENT_MAX(8)
	UINT8	_G_pucMyElementPool[sizeof(MY_ELEMENET) * ELEMENT_MAX];
	LW_HANDLE_G_hMyPartition;
	int main(int argc, char *argv[])
	{
		_G_hMyPartition = Lw_Partition_Create("my_partition",_G_pucMyElementPool,ELEMENT_MAX,sizeof(MY_ELEMENET),LW_OPTION_DEFAULT, LW_NULL);
		if(_G_hMyPartition == LW_HANDLE_INVALID)
		{
			fprintf(stderr, "create partition failed.\n");
			return  (-1);
		}
	printf("%x\n", _G_hMyPartition);
	while(1);
	return(0);
}
```
得到内存分区句柄。
 
 @import "./image/image028.png"
图2.23 part命令结果
#### 配置：
该命令属于系统提供的tshell命令，当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令，则该命令可用。
#### 输出定义：
无。
###  pcis
#### 命令格式：
```shell
pcis
```
#### 命令说明：
显示PCI总线的信息。
#### 常用选项：
无。
#### 使用说明：
该命令显示PCI总线的信息。
#### 参数说明：
无。
#### 备注：
	PCI总线的信息存放在/proc/pci 文件中。
	仅在x86环境可用。
#### 样例：
 
 @import "./image/image029.png"
图2.24 pcis命令结果
#### 配置：
该命令属于系统提供的tshell命令，当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令，则包含该命令。
#### 输出定义：
pic各总线的信息。
###  ps
#### 命令格式：
```shell
ps
```
#### 命令说明：
查看系统所有进程的信息。
#### 常用选项：
无。
#### 使用说明：
该命令用来显示系统中所有进程的信息。
#### 参数说明：
无。
#### 备注：
无。
#### 样例：
 @import "./image/image030.png"
图2.25 ps命令结果
#### 配置：
当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。当LW_CFG_MODULELOADER_EN> 0，需要提供模块装载服务，该命令将会被包含。
#### 输出定义：
系统中所有进程的信息，进程名称，创建者，使用者，ID进程标识符等。
###  reboot
#### 命令格式：
```shell
reboot
reboot –n/-f
```
#### 命令说明：
重新启动计算机。
#### 常用选项：
无。
#### 使用说明：
该命令有2种用法，reboot后面不跟参数时以冷启动的方式重新启动计算机。当reboot后面跟的参数是-n/-f时，立即重新启动计算机；如果跟的是其他参数，则提示参数错误。
#### 参数说明：
无。
#### 备注：
无。
#### 样例：
 @import "./image/image031.png"
图2.26 reboot命令结果
#### 配置：
该命令属于系统提供的tshell命令，当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令，则包含该命令。
#### 输出定义：
无。
###  renice 
#### 命令格式：
```shell
renice priority pid
renice priority  -p pid
renice priority  -g pgrp
renice priority  -u user
```
#### 命令说明：
设置指定进程的优先级
#### 常用选项：
无。
#### 使用说明：
该命令有4种用法，
renice priority  pid    调整pid进程的优先级；
renice priority  -p  pid    调整pid进程的优先级；
renice priority  -g  pgrp    调整进程组的优先级；
renice priority  –u  user    调整进程使用者的优先级。
#### 参数说明：
-priority：设定的优先级。
#### 备注：
无。
#### 样例：
 
 @import "./image/image032.png"
 @import "./image/image033.png"
 @import "./image/image034.png"
图2.27 renice命令结果
#### 配置：
该命令属于系统提供的tshell命令，当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令，则包含该命令。
#### 输出定义：
无。
###  restart 
#### 命令格式：
```shell
restart  tid  argment
```
#### 命令说明：
重新启动线程。
#### 常用选项：
无。
#### 使用说明：
该命令用来重新启动线程。
#### 参数说明：
无。
#### 备注：
线程id是十六进制的。
只能重启内核线程。
本案例重启的是tshell进程。
#### 样例：
 @import "./image/image035.png"
图2.28 restart命令结果
#### 配置：
当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。当LW_CFG_THREAD_RESTART_EN>0时，允许任务重新启动，该命令将会被包含。
#### 输出定义：
无。
###  sem 
#### 命令格式：
```shell
sem  semaphore handle
```
#### 命令说明：
显示信号信息。
#### 常用选项：
无。
说明：
该命令显示指定信号的信息。
#### 参数说明：
semaphore handle：指定信号句柄。
#### 备注：
无。
#### 样例：
 @import "./image/image036.png"
图2.29 sem命令结果
#### 配置：
该命令属于系统提供的tshell命令，当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令，则包含该命令。
#### 输出定义：
无。
###  shell
#### 命令格式：
```shell
shell  ttydevice  nologin
```
#### 命令说明：
使用ttydevice作为标准文件创建一个shell。
#### 常用选项：
无。
#### 使用说明：
用tty设备作为标准文件创建shell。
#### 参数说明：
无。
#### 备注：
无。
#### 样例：
 @import "./image/image037.png"
图2.30 shell命令结果
#### 配置：
该命令属于系统提供的tshell命令，当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令，则包含该命令。
#### 输出定义：
无。
###  shstack
#### 命令格式：
```shell
shstack
shstack  new stack size
```
#### 命令说明：
显示或者设置shell任务堆栈大小。
#### 常用选项：
无。
#### 使用说明：
该命令有2种用法，无参数时显示默认的shell任务堆栈大小。当后面跟参数时设置之后启动的shell任务的堆栈大小。
#### 参数说明：
无。
#### 备注：
设置仅对之后启动的shell有效。
#### 样例：
 @import "./image/image038.png"
图2.22 mems命令结果
#### 配置：
该命令属于系统提供的tshell命令，当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令，则包含该命令。
#### 输出定义：
无。
###  shutdown 
#### 命令格式：
```shell
shutdown
shutdown  -r
shutdown  -h
shutdown  -f
```
#### 命令说明：
关闭或重启系统。
#### 常用选项：
-h：关机重启系统；
–f：是关机，冷启动操作系统；
–r：是立即重新启动系统。
#### 使用说明：
该命令有4种用法，
其中shutdown与shutdown  -h的功能是一样，都是关机重启系统；
shutdown  –f是关机，冷启动操作系统；
shutdown –r是立即重新启动系统。
#### 参数说明：
无。
#### 备注：
无。
#### 样例：
 @import "./image/image039.png"
图2.32 shutdown命令结果
#### 配置：
该命令属于系统提供的tshell命令，当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令，则包含该命令。
#### 输出定义：
无。
###  sigqueue 
#### 命令格式：
```shell
sigqueue  -n  signum  tid/pid
sigqueue  tid/pid
```
#### 命令说明：
发送信号给线程或进程。
#### 常用选项：
无。
#### 使用说明：
该命令有2种用法， sigqueue tid/pid，向进程或线程发送SIGKILL（9）信号；sigqueue  –n signum tid/pid向进程或线程发送数字signum所代表的信号。
#### 参数说明：
tid/pid：进程或线程；
#### 备注：
进程号是十进制，线程号是十六进制。
#### 样例：
查看指定进程中的线程信息
发送消息，并再次查看线程信息
 @import "./image/image040.png"
图2.22 mems命令结果
#### 配置：
当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。当LW_CFG_SIGNAL_EN > 0时，允许系统使用信号，该命令将会被包含。
#### 输出定义：
无。
###  sleep 
#### 命令格式：
```shell
sleep  n
sleep  ns
sleep  nm
sleep  nh
sleep  nd
```
#### 命令说明：
当前线程睡眠指定的时间。
#### 常用选项：
n：睡眠n秒；
ns：睡眠n秒；
nm：n分钟；
nh：n小时；
nd：睡眠n天。
#### 使用说明：
该命令有5种用法，其中sleep n 和sleep ns都是睡眠n秒；sleep nm，睡眠n分钟；sleep nh，睡眠n小时，sleep nd 睡眠n天。
#### 参数说明：
无。
#### 备注：
无。
#### 样例：
 @import "./image/image041.png"
图2.34sleep命令结果
#### 配置：
该命令属于系统提供的tshell命令，当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令，则包含该命令。
#### 输出定义：
无。
###  sprio 
#### 命令格式：
```shell
sprio  priority  thread_id
```
#### 命令说明：
设置指定线程的优先级。
#### 常用选项：
无。
说明：
该命令用来设置指定线程的优先级 。
#### 参数说明：
无。
#### 备注：
thread_id是16进程。
#### 样例：
使用ts命令查看现有进程的优先级
使用sprio 改变线程的优先级
再次使用ts命令查看是否改变了相应进程的优先级
 @import "./image/image042.png"
图2.35 sprio命令结果
#### 配置：
该命令属于系统提供的tshell命令，当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令，则包含该命令。
#### 输出定义：
同ts的输出定义。
###  ss 
#### 命令格式：
```shell
ss
```
#### 命令说明：
查看系统中所有线程与中断系统堆栈使用情况。
#### 常用选项：
无。
#### 使用说明：
该命令用来查看线程和中断堆栈的使用情况。 
#### 参数说明：
无。
#### 备注：
无。
#### 样例：
 @import "./image/image043.png"
图2.36 ss命令结果
#### 配置：
该命令属于系统提供的tshell命令，当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令，则包含该命令。
#### 输出定义：
name：线程名。
tid：线程句柄。
pid：进程id。
###  top 
#### 命令格式：
```shell
top
top  -n  times  -t  wait_seconds
```
#### 命令说明：
查看cpu的使用率。
#### 常用选项：
-n：检测次数；
-t：检测时间。
#### 使用说明：
该命令有2种用法，其中-n后面跟的参数是检测cpu利用率的次数，-t指明每次检查用的时间。cpuus 默认检测1次，检测的时间是1s，top –n times –t wait_seconds 可以指明检测times次，每次检测的时间是wait_seconds秒。规定检测时间不得超过10秒。
#### 参数说明：
无。
备注
无。
#### 样例：
 @import "./image/image044.png"
图2.37 top命令结果
#### 配置：
该命令属于系统提供的tshell命令，当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令，则包含该命令。
#### 输出定义：
系统内进程名称、线程句柄、优先级、占用cpu资源的百分比。
###  tp 
#### 命令格式：
```shell
tp
tp  pid
```
#### 命令说明：
查看系统中被阻塞的线程信息
#### 常用选项：
无。
#### 使用说明：
该命令有2种用法，tp不跟参数，会显示所有进程中被阻塞的线程信息；带参数会显示相应进程下被阻塞的线程信息 。
#### 参数说明：
无。
#### 备注：
无。
#### 样例：
 @import "./image/image045.png"
图2.38 tp命令结果
#### 配置：
该命令属于系统提供的tshell命令，当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令，则包含该命令。
#### 输出定义：
显示系统中所有被阻塞的信息，线程名、线程句柄、线程组，延时、未解决事件。
###  ts 
#### 命令格式：
```shell
ts 
ts  pid
```
#### 命令说明：
查看系统线程信息
#### 常用选项：
无。
#### 使用说明：
该命令有2种用法，ts不跟参数，会显示所有进程中的线程信息；带参数会显示相应进程下的线程信息。
#### 参数说明：
无。
#### 备注：
无。
#### 样例：
 @import "./image/image046.png"
图2.39 ts命令结果
#### 配置：
该命令属于系统提供的tshell命令，当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令，则包含该命令。
#### 输出定义：
系统进程中的所有线程，如：线程名、线程句柄、进程id、cpu id等。
###  tty
#### 命令格式：
```shell
tty	
```
#### 命令说明：
显示当前shell终端对应的tty文件。
#### 常用选项：
无。
#### 使用说明：
该命令用来显示当前shell终端对应的tty文件。
#### 参数说明：
无。
#### 备注：
无。
#### 样例：
 @import "./image/image047.png"
图2.40 tty命令结果
#### 配置：
该命令属于系统提供的tshell命令，当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令，则包含该命令。
#### 输出定义：
无。
###  vardel
#### 命令格式：
```shell
vardel  variable
```
#### 命令说明：
删除一个指定的系统环境变量。
#### 常用选项：
无。
#### 使用说明：
该命令用来删除指定的系统环境变量。
#### 参数说明：
variable：变量名。
#### 备注：
无。
#### 样例：
查看有那些环境变量
删除环境变量并在此查看
 @import "./image/image048.png"
 @import "./image/image049.png"
图2.41 vardel命令结果
#### 配置：
该命令属于系统提供的tshell命令，当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令，则包含该命令。
#### 输出定义：
variable：变量名。
value：路径。
###  varload 
#### 命令格式：
```shell
varload
varload  profile
```
#### 命令说明：
从指定参数的文件中提取装载环境变量表。
#### 常用选项：
varload。
#### 使用说明：
该命令有2种用法，不带参数默认从/etc/profile文件中提取；带参数则从指定的文件中提取环境变量。
#### 参数说明：
profile：指定文件中提取变量。
#### 备注：
无。
#### 样例：
查看当前的环境变量
创建一个path文件，在文件中写MYPATH=”/home/”，保存。
装载命令
再次查看环境变量
 @import "./image/image050.png"
 @import "./image/image051.png"
图2.42 varload命令结果
#### 配置：
该命令属于系统提供的tshell命令，当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令，则包含该命令。
#### 输出定义：
variable：变量名。
value：路径。
###  vars
#### 命令格式：
```shell
vars
```
#### 命令说明：
显示当前的环境变量。
#### 常用选项：
无。
#### 使用说明：
该命令用来查看当前环境变量。
#### 参数说明：
无。
#### 备注：
无。
#### 样例：
 @import "./image/image052.png"
图2.43 vars命令结果
#### 配置：
该命令属于系统提供的tshell命令，当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令，则包含该命令。
#### 输出定义：
variable：变量名。
value：路径。
###  varsave 
#### 命令格式：
```shell
varsave 
arsave  rofile
```
#### 命令说明：
保存当前的操作系统环境变量表。
#### 常用选项：
无。
#### 使用说明：
该命令有2种用法，varsave 不带参数默认将现在的环境变量保存到/etc/profile文件中；如果带参数则将环境变量保存到相应的文件中。
#### 参数说明：
rofile：保存文件名。
#### 备注：
如果profile文件不存在，varsave会先创建该文件，如果存在则将环境变量响应的值修改。
#### 样例：
 @import "./image/image053.png"
 @import "./image/image054.png"
图2.44 varsave命令结果
#### 配置：
该命令属于系统提供的tshell命令，当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令，则包含该命令。
#### 输出定义：
无。
###  virtuals 
#### 命令格式：
```shell
virtuals
```
#### 命令说明：
显示 vmm 虚拟存储器信息。
#### 常用选项：
无。
#### 使用说明：
该命令用来显示虚拟存储器信息。
#### 参数说明：
无。
#### 备注：
无。
#### 样例：
 @import "./image/image055.png"
图2.45 virtuals命令结果
#### 配置：
当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。当LW_CFG_VMM_EN>0时，对虚拟内存支持，该命令将会被包含。
#### 输出定义：
储存空间来源及大小。
###  which
#### 命令格式：
```shell
which  programfile
```
#### 命令说明：
检查参数指定的文件位置。
#### 常用选项：
无。
#### 使用说明：
该命令在环境变量指定的路径下查找指定的文件所在的位置，并显示文件所在的路径。
#### 参数说明：
programfile：指定文件名。
#### 备注：
无。
#### 样例：
 @import "./image/image056.png"
图2.46 which命令结果
#### 配置：
当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。当LW_CFG_MODULELOADER_EN> 0，需要提供模块装载服务，该命令将会被包含。
#### 输出定义：
无。
###  who
#### 命令格式：
```shell
who
```
#### 命令说明：
查看当前登录用户身份。
#### 常用选项：
无。
#### 使用说明：
该命令用来查看当前登录用户信息。 
#### 参数说明：
无。
#### 备注：
无。
#### 样例：
 @import "./image/image057.png"
图2.47 who命令结果
#### 配置：
该命令属于系统提供的tshell命令，当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令，则包含该命令。
#### 输出定义：
无。
###  zones 
#### 命令格式：
```shell
zones
```
#### 命令说明：
查看操作系统物理页面分区使用情况。
#### 常用选项：
无。
#### 使用说明：
该命令用来显示vmm物理存储器信息。
#### 参数说明：
无。
#### 备注：
无。
#### 样例：
 @import "./image/image058.png"
图2.48 zones命令结果
#### 配置：
当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。当LW_CFG_VMM_EN>0时，对虚拟内存支持，该命令将会被包含。
#### 输出定义：
zone：划分区域。
physical：磁盘分区。
pagesize：页大小。

# 文件命令
## 简介
和文件相关的命令有：
表3-1 文件命令介绍

|命令名	|简要说明|
|---|---|
|cat	显示文件内容
|cd	切换当前目录
|ch	改变目录
|chmod	改变文件或目录的访问权限
|close	关闭一个文件
|cmp	比较一个文件
|cp	拷贝一个文件
|df	查看指定目录的文件系统信息
|dosfslabel|查看fat文件系统卷标
|dsize|计算一个指定的目录包含的所有文件信息
|fatugid|设置fat文件系统用户与组id
|fdentrys|列出操作系统所有正在操作的文件信息
|fdisk|显示或制作一个磁盘分区表
|files|列出操作系统所有正在操作的文件信息
|gzip|压缩或解压缩一个文件
|ll|显示指定目录下的文件详细信息，默认当前目录
|ln|创建符号链接文件
|logfileadd|向内核日志打印函数加入指定的内核文件描述符
|logfileclear|从内核日志打印文件表中清除指定的内核文件描述符
|logfiles|显示内核日志打印文件列表
|ls|显示指定目录下的文件名，默认当前目录
|mkdir|创建目录
|mkfifo|创建一个命名管道，只能在根文件系统设备下创建
|mkfs|格式化指定的磁盘
|mmaps|显示系统mmap信息
|mount|挂载一个卷
|msgq|显示消息队列的信息
|mv|移到或重命名一个文件
|open|打开一个文件
|pwd|查看当前的工作目录
|rm|删除一个文件
|rmdir|删除一个文件夹
|showmount|查看系统中所有已经挂载的卷
|shfile|执行指定的shell脚本
|sync|将所有系统缓存的信息写入到物理设备
|tmpname|获得一个可以创建的临时文件名
|touch|创建一个普通文件
|umount|卸载一个卷
|untar|解包或解压缩一个tar或tar.gz文件包
|vi|启动vi编辑器
|yaffscmd|显示、设置和擦除一个块
|zlib|添加一个.gz的压缩文件
##	命令解析
### cat 
#### 命令格式：
```shell
cat  filename
```
#### 命令说明：
显示文件内容。
#### 常用选项：
无。
#### 使用说明：
该命令用于显示文件中的内容，其中filename可以包含文件的路径。
#### 参数说明：
filename：文件名。
#### 备注：
无。
#### 样例：
 @import "./image/image059.png"
图3.1 cat命令结果
#### 配置：
当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。当LW_CFG_MAX_VOLUMES> 0)，系统同时支持的卷的数量>0时，该命令将会被包含。
#### 输出定义：
输出hello中的内容。
### cd 
#### 命令格式：
```shell
cd  path
```
#### 命令说明：
切换当前目录。
#### 常用选项：
无。
#### 使用说明：
该命令用于切换到指定的目录下。
#### 参数说明：
path：路径。
#### 备注：
无。
#### 样例：
 @import "./image/image060.png"
图3.2 cd命令结果
#### 配置：
当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。当LW_CFG_MAX_VOLUMES> 0)，系统同时支持的卷的数量>0时，该命令将会被包含。
#### 输出定义：
无。
### ch 
#### 命令格式：
```shell
ch  dir
```
#### 命令说明：
改变目录。
#### 常用选项：
无。
#### 使用说明：
该命令用于切换到指定的目录下 。
#### 参数说明：
dir：目录。
#### 备注：
无。
#### 样例：
 @import "./image/image061.png"
图3.3ch命令结果
#### 配置：
当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。当LW_CFG_MAX_VOLUMES> 0)，系统同时支持的卷的数量>0时，该命令将会被包含。
#### 输出定义：
无。
### chmod 
#### 命令格式：
```shell
chmod  newmode  filename
```
#### 命令说明：
改变文件或目录的访问权限。
#### 常用选项：
无。
#### 使用说明：
该命令用来改变文件或目录的访问权限。
#### 参数说明：
newmode：新权限；
filename：修改权限的文件名。
#### 备注：
无。
#### 样例：
 @import "./image/image062.png"
图3.4 chmod命令结果
#### 配置：
当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。当LW_CFG_MAX_VOLUMES> 0)，系统同时支持的卷的数量>0时，该命令将会被包含。
#### 输出定义：
输出当前目录下所有文件的详细信息。
### close
#### 命令格式：
```shell
close  fd
```
#### 命令说明：
关闭一个文件。
#### 常用选项：
无。
#### 使用说明：
该命令用来关闭一个打开的文件。
#### 参数说明：
fd：文件名。
#### 备注：
无。
#### 样例：
 @import "./image/image063.png"
图3.5 close命令结果
#### 配置：
该命令属于系统提供的tshell命令。当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。
#### 输出定义：
无。
### cmp
#### 命令格式：
```shell
cmp  file one  file two
```
#### 命令说明：
比较一个文件。
#### 常用选项：
无。
#### 使用说明：
该命令用来比较两个文件是否相同。
#### 参数说明：
file one：文件1；
file two：文件2。
#### 备注：
无。
#### 样例：
 @import "./image/image064.png"
图3.6 cmp命令结果
#### 配置：
当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。当LW_CFG_MAX_VOLUMES> 0)，系统同时支持的卷的数量>0时，该命令将会被包含。
#### 输出定义：
文件是否相同。
### cp 
#### 命令格式：
```shell
cp  src file name  dst file name
```
#### 命令说明：
拷贝一个文件。
#### 常用选项：
无。
#### 使用说明：
该命令用来将src file内容拷贝到dst file中。
#### 参数说明：
src file name：拷贝源文件；
dst file name：拷贝目标文件。
#### 备注：
如果dst file不存在会先创建在拷贝。如果dst file存在会询问是否覆盖dst file，如果选择不覆盖则此次拷贝失败。
#### 样例：
 @import "./image/image065.png"
图3.7 cp命令结果
#### 配置：
当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。当LW_CFG_MAX_VOLUMES> 0)，系统同时支持的卷的数量>0时，该命令将会被包含。
#### 输出定义：
复制文件的大小、使用时间和复制速率。
### df
#### 命令格式：
```shell
df  volume name
```
#### 命令说明：
查看指定目录的文件系统信息。
#### 常用选项：
无。
#### 使用说明：
该命令查看指定目录下的文件系统信息。
#### 参数说明：
volume name：被指定目录。
#### 备注：
无。
#### 样例：
 @import "./image/image066.png"
图3.8 df命令结果
#### 配置：
当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。当LW_CFG_MAX_VOLUMES> 0)，系统同时支持的卷的数量>0时，该命令将会被包含。
#### 输出定义：
volume：目标名称。
total：总大小。
fs type：文件类型。
### *dosfslabel 
#### 命令格式：
```shell
dosfslabel  vol  
dosfslabel  vol  vol newlabel
```
#### 命令说明：
查看或设置文件系统卷标。
#### 常用选项：
无。
#### 使用说明：
该命令只有2种用法，带参数vol用于查看相应卷标的系统
#### 参数说明：
无。
#### 备注：
仅在x86环境可用。
#### 样例：
 @import "./image/image067.png"
图3.9 dosfslabel命令结果
#### 配置：
当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。当LW_CFG_MAX_VOLUMES> 0)且LW_CFG_FATFS_EN > 0时，支持的卷的数量>0并且允许小型 FAT 文件系统时，该命令将会被包含。
#### 输出定义：
卷标。
###  dsize 
#### 命令格式：
```shell
dsize  pathname
```
#### 命令说明：
计算一个指定的目录包含的所有文件信息。
#### 常用选项：
无。
#### 使用说明：
该命令用来计算指定目录包含文件的信息，包含文件数和文件的总大小。 
#### 参数说明：
pathname：要查看文件信息的目录。
#### 备注：
无。
#### 样例：
 @import "./image/image068.png"
图3.10 dsize命令结果
#### 配置：
当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。当LW_CFG_MAX_VOLUMES> 0)，系统同时支持的卷的数量>0时，该命令将会被包含。
#### 输出定义：
文件数量和文件大小。
###  fatugid
#### 命令格式：
```shell
fatugid  uid  gid
```
#### 命令说明：
设置fat文件系统用户与组id。
#### 常用选项：
无。
#### 使用说明：
该命令用于设置fat文件系统用户和组id 。
#### 参数说明：
uid：用户；
gid：用户组。
#### 备注：
无。
#### 样例：
 @import "./image/image069.png"
图3.11 fatugid命令结果
#### 配置：
当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。当LW_CFG_MAX_VOLUMES> 0)且LW_CFG_FATFS_EN > 0时，支持的卷的数量>0并且允许小型 FAT 文件系统时，该命令将会被包含。
#### 输出定义：
无。
###  fdentrys 
#### 命令格式：
```shell
fdentrys
```
#### 命令说明：
列出操作系统所有正在操作的文件信息。
#### 常用选项：	
无。
#### 使用说明：
该命令用于显示操作系统所有正在操作的文件信息。
#### 参数说明：
无。
#### 备注：
所有的文件信息包括进程打开的文件。
#### 样例：
 @import "./image/image070.png"
图3.12 fdentrys命令结果
#### 配置：
该命令属于系统提供的tshell命令。当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。
#### 输出定义：
文件的名称、实现路径和文件类型。
###  *fdisk 
#### 命令格式：
```shell 
fdisk  block I/O device 
fdisk  -f  block I/O device
```
#### 命令说明：
显示或制作一个磁盘分区表。
#### 常用选项：
无。
#### 使用说明：
该命令只有2种用法，当后面不跟选项-f是显示当前的分区表，跟选项-f用于制作磁盘分区表。
#### 参数说明：
无。
#### 备注：
仅在x86环境可用。
制作磁盘分区表需要先确定该磁盘没有被挂载。使用showmount查看是否被挂载，如挂载使用umount卸载。
分区完毕需要重新启动并将分好的每个区进行初始化才可以使用。
#### 样例：
 @import "./image/image071.png"
 @import "./image/image072.png"
图3.13 fdisk命令结果
#### 配置：
当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。当LW_CFG_MAX_VOLUMES> 0)且LW_CFG_OEMDISK_EN> 0时，支持的卷的数量>0并且需要自动挂载与分区工具，该命令将会被包含。
#### 输出定义：
part：布局。
type：类型。
###  files 
#### 命令格式：
```shell
files   
```
#### 命令说明：
列出操作系统所有正在操作的文件信息。
#### 常用选项：
无。
#### 使用说明：
该命令用来显示操作系统正在操作的文件信息。
#### 参数说明：
无。
#### 备注：
该命令会显示内核文件，而不会显示线程打开的文件。
#### 样例：
 @import "./image/image073.png"
图3.14 命令结果
#### 配置：
该命令属于系统提供的tshell命令。当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。
#### 输出定义：
文件名、文件类型号驱动名。
###  gzip
#### 命令格式：
```shell
gzip  [-c]  [-d]  [-f]  [-h]  [-r]  [-1 to -9]  [files...]
```
#### 命令说明：
压缩或解压缩一个文件。
#### 常用选项：
无。
#### 使用说明：
该命令用于压缩或解压一个文件，其中参数有：
-c 		将输出写到标准输出上，并保留原有文件；
-d  	将压缩文件解压；
-f  	Huffman编码和字符串匹配结合；
-h 		仅仅使用Huffman压缩算法压缩文件；
-r		使用RLE压缩算法压缩文件；
-1 to -9  指定压缩的等级。
#### 参数说明：
files：文件名。
#### 备注：
无
#### 样例：
 @import "./image/image074.png"
图3.15 gzip命令结果
#### 配置：
该命令属于系统提供的tshell命令。当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。
#### 输出定义：
无。
###  ll
#### 命令格式：
```shell
ll
ll [path name]
```
#### 命令说明：
显示指定目录下的文件详细信息。
#### 常用选项：
无。
#### 使用说明：
该命令用于显示指定目录下文件的详细信息。当不跟参数时，默认会显示当前目录下的文件信息；当跟上参数路径时，会显示相应目录下的文件信息。
#### 参数说明：
path name：需要指定目录时，在此填写。
#### 备注：
无。
#### 样例：
 @import "./image/image075.png"
图3.16 ll命令结果
#### 配置：
当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。当LW_CFG_MAX_VOLUMES> 0)，系统同时支持的卷的数量>0时，该命令将会被包含。
#### 输出定义：
无。
###  ln
#### 命令格式：
```shell
ln  [-s | -f]  actualpath  sympath
```
#### 命令说明：
创建符号链接文件。
#### 常用选项：
-s：链接。
#### 使用说明：
该命令有用来创建符号连接文件，其中默认带参数-s。actualpath为实际路径，symoath为链接文件名。
#### 参数说明：
actualpath：为实际路径；
symoath：为链接文件名。
#### 备注：
无。
#### 样例：
 @import "./image/image076.png"
图3.17 ln命令结果
#### 配置：
当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。当LW_CFG_MAX_VOLUMES> 0)，系统同时支持的卷的数量>0时，该命令将会被包含。
#### 输出定义：
无。
###  logfileadd 
#### 命令格式：
```shell
logfileadd  file descriptor
```
#### 命令说明：
向内核日志打印函数加入指定的内核文件描述符。
#### 常用选项：
无。
#### 使用说明：
该命令用于向内核日志函数加入指定的内核文件描述符。
#### 参数说明：
file descriptor：添加的文件描述符。
#### 备注：
无。
#### 样例：
 @import "./image/image077.png"
图3.18 logfileadd命令结果
#### 配置：
当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。当LW_CFG_LOG_LIB_EN>0时，允许系统提供日志管理库，该命令将会被包含。
#### 输出定义：
无。
###  logfileclear 
#### 命令格式：
```shell
logfileclear  file descriptor
```
#### 命令说明：
从内核日志打印文件表中清除指定的内核文件描述符。
#### 常用选项：
无。
#### 使用说明：
该命令用来删除内核日志打印文件表中指定的内核文件描述符。
#### 参数说明：
file descriptor：要被清除的描述文件符。
#### 备注：
无。
#### 样例：
 @import "./image/image078.png"
图3.19 logfileclear命令结果
#### 配置：
当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。当LW_CFG_LOG_LIB_EN>0时，允许系统提供日志管理库，该命令将会被包含。
#### 输出定义：
无。
###  logfiles 
#### 命令格式：
```shell
logfiles
```
#### 命令说明：
显示内核日志打印文件列表。
#### 常用选项：
无。
#### 使用说明：
该命令用来显示内核日志打印文件列表。
#### 参数说明：
无。
#### 备注：
无。
#### 样例：
 @import "./image/image079.png"
图3.20 logfiles命令结果
#### 配置：
当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。当LW_CFG_LOG_LIB_EN>0时，允许系统提供日志管理库，该命令将会被包含。
#### 输出定义：
无。
###  ls
#### 命令格式：
```shell
ls
ls  path name 
```
#### 命令说明：
显示指定目录下的文件名，默认当前目录。
#### 常用选项：
无。
#### 使用说明：
该命令用来显示指定目录下的文件名。不带参数默认打印当前当前目录下的文件名；带路径则打印相应目录下的文件名列表。
#### 参数说明：
path name：指定路径名称。
#### 备注：
无。
#### 样例： 
@import "./image/image080.png"
图3.21 ls命令结果
#### 配置：
当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。当LW_CFG_MAX_VOLUMES> 0)，系统同时支持的卷的数量>0时，该命令将会被包含。
#### 输出定义：
输出目录下文件和文件夹。
###  mkdir 
#### 命令格式：
```shell
mkdir  directory
```
#### 命令说明：
创建目录。
#### 常用选项：
无。
#### 使用说明：
该命令用来在相应目录下创建一个文件夹，默认当前路径。 
#### 参数说明：
directory：新建的文件夹名称。
#### 备注：
directory参数可以带路径，将在指定的路径下创建文件夹。
#### 样例：
 @import "./image/image081.png"
图3.22 mkdir命令结果
#### 配置：
当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。当LW_CFG_MAX_VOLUMES> 0)，系统同时支持的卷的数量>0时，该命令将会被包含。
#### 输出定义：
无。
###  mkfifo 
#### 命令格式：
```shell
mkfifo  fifo name
```
#### 命令说明：
创建一个命名管道，只能在根文件系统设备下创建。
#### 常用选项：
无。
#### 使用说明：
该命令在根目录下创建一个命名管道。
#### 参数说明：
fifo name：管道名称。
#### 备注：
无。 
#### 样例：
 @import "./image/image082.png"
图3.23 mkfifo命令结果
#### 配置：
当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。当LW_CFG_MAX_VOLUMES> 0)，系统同时支持的卷的数量>0时，该命令将会被包含。
#### 输出定义：
无。
###  *mkfs 
#### 命令格式：
```shell	
mkfs  media name
```
#### 命令说明：
格式化指定的磁盘。
#### 常用选项：
无。
#### 使用说明：
该命令用来格式化指定的磁盘。
#### 参数说明：
无。
#### 备注：
仅在x86环境可用。
#### 样例：
 @import "./image/image083.png"
图3.24 mkfs命令结果
#### 配置：
当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。当LW_CFG_MAX_VOLUMES> 0)，系统同时支持的卷的数量>0时，该命令将会被包含。
#### 输出定义：
无。
###  mmaps 
#### 命令格式：
```shell
mmaps
```
#### 命令说明：
显示系统mmap信息。
#### 常用选项：
无。
#### 使用说明：
该命令用来显示系统mmap信息，其中信息有：起始地址（ADDR）、映射长度（SIZE）、文件偏移量（OFFSET）、页面属性，可写（WRITE）、映射标识，共享（SHARE）、文件描述符（FD）。
#### 参数说明：
无。
#### 备注：
无。
#### 样例：
 @import "./image/image084.png"
图3.25 mmaps命令结果
#### 配置：
当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。当LW_CFG_POSIX_EN>0和LW_CFG_VMM_EN>0时，使能posix兼容库并且对对虚拟内存支持，该命令将会被包含。
#### 输出定义：
无。
###  *mount 
#### 命令格式：
```shell
mount  -t fstype  -o option  blk dev  mount path
```
#### 命令说明：
挂载一个卷。
#### 常用选项：
无。
#### 使用说明：
该命令用来挂载一个卷。
-t	指定设备的文件系统类型
nfs 		网络文件系统
ramfs  	基于内存的文件系统
romfs	简单的、紧凑的、只读文件系统
tpsfs 	SylixOS针对大容量存储设备集成的文件管理系统
-o	指定读写模式
ro		只读模式挂载
rw		用读写模式挂载。
blk dev 设备名称
mount path	挂载路径
#### 参数说明：
无。
#### 备注：
仅在x86环境可用。
#### 样例：
 @import "./image/image085.png"
图3.26 mount命令结果
#### 配置：
当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。当LW_CFG_MAX_VOLUMES> 0)且LW_CFG_MOUNT_EN>0时，系统同时支持的卷的数量>0并且系统需要使用mount工具时，该命令将会被包含。
#### 输出定义：
无。
###  msgq 
#### 命令格式：
```shell
msgq  	message queue handle
```
#### 命令说明：
显示消息队列的信息。
#### 常用选项：
无。
#### 使用说明：
该命令用来查看指定消息队列的消息。 
#### 参数说明：
message queue handle：信息列队。
#### 备注：
无。
#### 样例：
tp可以查看消息队列句柄
 @import "./image/image086.png"
图3.27 msgq命令结果
#### 配置：
该命令属于系统提供的tshell命令。当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。
#### 输出定义：
消息列队的具体信息。
###  mv 
#### 命令格式：
```shell
mv  SRC file name  DST file name
```
#### 命令说明：
移到或重命名一个文件。
#### 常用选项：
无。
#### 使用说明：
该命令只有1种用法，如果两者在同一个目录下将src file重命名为dst file，不在同一目录下则将srcfile 移到到dst的目录下并命名为dst。
#### 参数说明：
SRC file name：源文件；
DST file name：目标文件。
#### 备注：
mv会监测目标文件是否存在，存在给出提示是否覆盖，若覆盖则移动，否则不移到。
#### 样例：
 @import "./image/image087.png"
图3.28 mv命令结果
#### 配置：
当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。当LW_CFG_MAX_VOLUMES> 0)，系统同时支持的卷的数量>0时，该命令将会被包含。
#### 输出定义：
无。
###  open 
#### 命令格式：
```shell
open  filename  flag
open  filename  flag  mode
```
#### 命令说明：
打开一个文件。
#### 常用选项：
无。
#### 使用说明：
该命令用来以不用权限打开或创建一个文件。
#### 参数说明：
无。
#### 备注：
flag 可选项为：
O_RDONLY   ： 00000000
O_WRONLY   ： 00000001
O_RDWR     ：  00000002
O_APPEND   ：  00000008
O_SHLOCK   ：  00000010
O_EXLOCK   ：  00000020
O_ASYNC    ：  00000040
O_CREAT    ：  00000200
O_TRUNC    ：  00000400
O_EXCL     ：  00000800
O_SYNC     ：  00002000
O_NONBLOCK ： 00004000
O_NOCTTY   ：  00008000
O_CLOEXEC   ： 00080000。
#### 样例：
  @import "./image/image088.png"
图3.29 open命令结果
#### 配置：
该命令属于系统提供的tshell命令。当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。
#### 输出定义：
无。
###  pwd 
#### 命令格式：
```shell
pwd
```
#### 命令说明：
查看当前的工作目录。
#### 常用选项：
无。
#### 使用说明：
该命令用来显示当前的工作目录。
#### 参数说明：
无。
#### 备注：
无。
#### 样例：
 @import "./image/image089.png"
图3.30 pwd命令结果
#### 配置：
当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。当LW_CFG_MAX_VOLUMES> 0)，系统同时支持的卷的数量>0时，该命令将会被包含。
#### 输出定义：
无。
###  rm 
#### 命令格式：
```shell
rm file name
```
#### 命令说明：
删除一个文件。
#### 常用选项：
无。
#### 使用说明：
该命令用来删除一个文件。 
#### 参数说明：
无。
#### 备注：
无。
#### 样例：
  @import "./image/image090.png"
图3.31 rm命令结果
#### 配置：
当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。当LW_CFG_MAX_VOLUMES> 0)，系统同时支持的卷的数量>0时，该命令将会被包含。
#### 输出定义：
无。
###  rmdir 
#### 命令格式：
```shell
rmdir directory
```
#### 命令说明：
删除一个文件夹。
#### 常用选项：
无。
#### 使用说明：
该命令只有1种用法，用来删除一个给定的文件夹。 
#### 参数说明：
无。
#### 备注：
只能删除空的文件夹，当文件夹下有文件或目录时删除失败。
#### 样例：
  @import "./image/image091.png"
图3.32 rmdir命令结果
#### 配置：
当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。当LW_CFG_MAX_VOLUMES> 0)，系统同时支持的卷的数量>0时，该命令将会被包含。
#### 输出定义：
无。
###  showmount 
#### 命令格式：
```shell
showmount
```
#### 命令说明：
查看系统中所有已经挂载的卷。
#### 常用选项：
无。
#### 使用说明：
该命令用来查看系统中所有挂载的卷。 
#### 参数说明：
无。
备注：。
无。
#### 样例：
  @import "./image/image092.png"
图3.33 showmount命令结果
#### 配置：
当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。当LW_CFG_MAX_VOLUMES> 0)且LW_CFG_MOUNT_EN>0时，系统同时支持的卷的数量>0并且系统需要使用mount工具时，该命令将会被包含。
#### 输出定义：
系统中已挂载的卷，卷标名称和位置。
###  shfile 
#### 命令格式：
```shell
shfile  shell file
```
#### 命令说明：
执行指定的shell脚本。
#### 常用选项：
无。
#### 使用说明：
该命令用来执行指定的shell脚本。 
#### 参数说明：
无。
#### 备注：
无。
#### 样例：
创建一个shell脚本文件
使用shfile运行脚本
  @import "./image/image093.png"
  @import "./image/image094.png"
图3.34 shfile命令结果
#### 配置：
当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。当LW_CFG_MAX_VOLUMES> 0)，系统同时支持的卷的数量>0时，该命令将会被包含。
#### 输出定义：
执行脚本里的内容。
###  sync 
#### 命令格式：
```shell
sync
```
#### 命令说明：
将所有系统缓存的信息写入到物理设备。
#### 常用选项：
无。
#### 使用说明：
该命令将所有系统缓存的文件、设备、磁盘信息全部写入到相应的物理设备中。
#### 参数说明：
无。
#### 备注：
无。
#### 样例：
  @import "./image/image095.png"
图3.35 sync命令结果
#### 配置：
该命令属于系统提供的tshell命令。当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。
#### 输出定义：
无。
###  tmpname 
#### 命令格式：
```shell
tmpname
```
#### 命令说明：
获得一个可以创建的临时文件名。
#### 常用选项：
无。
#### 使用说明：
该命令用来获得一个可以创建的临时文件名。
#### 参数说明：
无。
#### 备注：
无。
#### 样例：
 @import "./image/image096.png"
图3.36 tmpname命令结果
#### 配置：
当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。当LW_CFG_MAX_VOLUMES> 0)，系统同时支持的卷的数量>0时，该命令将会被包含。
#### 输出定义：
输出临时文件名。
###  touch 
#### 命令格式：
```shell
touch  file name
touch  –amc  file name
```
#### 命令说明：
创建一个普通文件。
#### 常用选项：
无。
#### 使用说明：
该命令有2种用法，直接跟文件名表示创建一个普通的文件；参数可以跟amc中的一个或多个的组合，其中a表示改变文件的访问时间，m表示改变修改时间，c表示如果文件不存在，不创建文件，若不跟参数c，跟上a或m时，如果文件不存在会先创建文件。
#### 参数说明：
file name：文件名。
#### 备注：
参数a和m功能暂时未实现。
#### 样例：
 @import "./image/image097.png"
图3.37 touch命令结果
#### 配置：
当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。当LW_CFG_MAX_VOLUMES> 0)，系统同时支持的卷的数量>0时，该命令将会被包含。
#### 输出定义：
无。
###  *umount 
#### 命令格式：
```shell
umount  mount path
```
#### 命令说明：
卸载一个卷。
#### 常用选项：
无。
#### 使用说明：
该命令用来卸载系统中已经存在的卷。
#### 参数说明：
无。
#### 备注：
仅在x86环境可用。
#### 样例：
 @import "./image/image098.png"
图3.38 umount命令结果
#### 配置：
当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。当LW_CFG_MAX_VOLUMES> 0)且LW_CFG_MOUNT_EN>0时，系统同时支持的卷的数量>0并且系统需要使用mount工具时，该命令将会被包含。
#### 输出定义：
无。
###  untar 
#### 命令格式：
```shell
untar  .tar or .tar.gz file  destination directory
```
#### 命令说明：
解包或解压缩一个tar或tar.gz文件包。
#### 常用选项：
无。
#### 使用说明：
该命令用来解压一个.tar.gz或.tar文件，可以指定解压路径。
#### 参数说明：
.tar or .tar.gz：file .tar.gz或.tar文件。
#### 备注：
无。
#### 样例：
 @import "./image/image099.png"
图3.39 untar命令结果
#### 配置：
当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。当LW_CFG_SHELL_TAR_EN>0时，使能tar工具后，该命令将会被包含。
#### 输出定义：
解压信息。
###  vi
#### 命令格式：
```shell
vi 	[filename]
```
#### 命令说明：
启动vi编辑器。
#### 常用选项：
无。
#### 使用说明：
该命令用来打开vi编辑器,可以预先指定文件名，也可以编辑好文档时输入文件名。
#### 参数说明：
无。
#### 备注：
无。
#### 样例：
 @import "./image/image100.png"
图3.39 vi命令结果
#### 配置：
该命令属于系统提供的tshell命令，当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令，则包含该命令。
#### 输出定义：
无。
###  yaffscmd 
#### 命令格式：
```shell
yaffscmd  volname  bad 
yaffscmd  volname  info 
yaffscmd  volname  markbad 
yaffscmd  volname  erase
```
#### 命令说明：
显示、设置和擦除一个卷标中的块。
#### 常用选项：
info：被标记的不良块。
#### 使用说明：
该命令有4种用法，其中
yaffscmd  volname  bad  查看卷中被标记的不良块
yaffscmd  volname  info 	查看卷中所有的块
yaffscmd  volname  markbad  标记卷中的不良块
yaffscmd  volname  erase	擦除卷中的块。
#### 参数说明：
无。
#### 备注：
无。
#### 样例：
 @import "./image/image101.png"
 @import "./image/image102.png"
图3.41 yaffscmd命令结果
#### 配置：
该当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。当(LW_CFG_MAX_VOLUMES > 0) 且 (LW_CFG_YAFFS_EN > 0)，系统同时支持多个卷并且允许VAFFS文件系统，该命令将会被包含。
#### 输出定义：
卷中所有的块的信息。
###  zlib 
#### 命令格式：
```shell	
zlib  test.zlib
```
#### 命令说明：
添加一个.gz的压缩文件。
#### 常用选项：
无。
#### 使用说明：
该命令用来添加一个.gz压缩文件，zlib的测试接口。
#### 参数说明：
无。
#### 备注：
无。
#### 样例：
 @import "./image/image103.png"
图3.42 zlib命令结果
#### 配置：
该命令属于系统提供的tshell命令。当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。
#### 输出定义：
无。
 
# 用户命令
## 简介
和文件相关的命令有：
表4-1 用户命令介绍
|命令名	|简要说明|
|---|---|
|gadd	|增加一个新的用户组
|gdel	|删除一个用户组
|group	|显示用户组的信息
|pmod	|修改用户密码
|uadd	|添加用户
|udel	|删除用户
|umod	|设置用户的模式
|user	|显示用户信息或为用户生成密码
## 命令解析
### gadd 
#### 命令格式：
```shell
gadd group_name gid
```
#### 命令说明：
增加一个新的用户组
#### 常用选项：
无。
#### 使用说明：
该命令用来增加一个用户组并为该组分配一个gid。
#### 参数说明：
group_name gid：用户名和序号。
#### 备注：
无。
#### 样例：
 @import "./image/image104.png"
图4.1 gadd命令结果
#### 配置：
当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。当(LW_CFG_SHELL_USER_EN > 0)，使能了shell用户管理工具，该命令将会被包含。
#### 输出定义：
无。
### gdel 
#### 命令格式：
```shell
gdel group_nam
```
#### 命令说明：
删除一个用户组。
#### 常用选项：
无。
#### 使用说明：
该命令用来删除一个用户组。
#### 参数说明：
group_nam：用户名。
#### 备注：
只能删除没有用户的空用户组，当用户组不为空时删除失败。
#### 样例：
 @import "./image/image105.png"
图4.2 gdel命令结果
#### 配置：
当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。当(LW_CFG_SHELL_USER_EN > 0)，使能了shell用户管理工具，该命令将会被包含。
#### 输出定义：
无。
### group 
#### 命令格式：
```shell
group
```
#### 命令说明：
显示用户组的信息。
#### 常用选项：
无。
#### 使用说明：
该命令用来显示所有用户组的信息。
#### 参数说明：
无。
#### 备注：
无。
#### 样例：
 @import "./image/image106.png"
图4.3 group命令结果
#### 配置：
当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。当(LW_CFG_SHELL_USER_EN > 0)，使能了shell用户管理工具，该命令将会被包含。
#### 输出定义：
所有用户的组别，权限，组内成员。
### pmod 
#### 命令格式：
```shell	
pmod name old_password new_password
```
#### 命令说明：
修改用户密码。
#### 常用选项：
无。
#### 使用说明：
该命令用来修改用户的密码。 
#### 参数说明：
无。
#### 备注：
以root用户登录，才可以修改密码。
#### 样例：
 @import "./image/image107.png"
图4.4 pmod命令结果
#### 配置：
当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。当(LW_CFG_SHELL_USER_EN > 0)，使能了shell用户管理工具，该命令将会被包含。
#### 输出定义：
无。
### uadd 
#### 命令格式：
```shell
uadd  name  password  enable[0 / 1]  uid  gid  comment  homedir
```
#### 命令说明：
添加用户。
#### 常用选项：
无。
#### 使用说明：
该命令用来添加一个用户。
#### 参数说明：
uadd  用户名  密码  是否启用（0/1） （编号） 组  位置。
#### 备注：
用户id不能重复，添加用户时必须将其添加到一个存在的用户组。
#### 样例：
 @import "./image/image108.png"
图4.5 uadd命令结果
#### 配置：
当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。当(LW_CFG_SHELL_USER_EN > 0)，使能了shell用户管理工具，该命令将会被包含。
#### 输出定义：
无。
### udel 
#### 命令格式：
```shell
udel  username
```
#### 命令说明：
删除用户。
#### 常用选项：
无。
#### 使用说明：
该命令用来删除一个用户。
#### 参数说明：
无。
#### 备注：
无。
#### 样例：
 @import "./image/image109.png"
图4.1 udel命令结果
#### 配置：
当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。当(LW_CFG_SHELL_USER_EN > 0)，使能了shell用户管理工具，该命令将会被包含。
#### 输出定义：
无。
### umod 
#### 命令格式：
```shell
umod  name  enable[0 / 1]  comment  homedir
```
#### 命令说明：
设置用户的模式。
#### 常用选项：
无。
#### 使用说明：
该命令用来设置用户的使能情况、用户描述和用户目录。
#### 参数说明：
无。
#### 备注：
无。
#### 样例：
 @import "./image/image110.png"
图4.7 umod命令结果
#### 配置：
当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。当(LW_CFG_SHELL_USER_EN > 0)，使能了shell用户管理工具，该命令将会被包含。
#### 输出定义：
无。
### user 
#### 命令格式：
```shell
user
user  genpass
```
#### 命令说明：
显示用户信息或为用户生成密码。
#### 常用选项：
user。
#### 使用说明：
该命令有2种用法，无参数时，显示所有用户的用户名、使能情况、用户id和所属的组id；当跟上参数genpass时，为用户生成一个密码。
#### 参数说明：
无。
#### 备注：
无。
#### 样例：
  @import "./image/image111.png"
图4.8 user命令结果
#### 配置：
当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。当LW_CFG_SHELL_USER_EN > 0，使用shell用户管理工具，该命令将会被包含。
#### 输出定义：
所有用户的状态用户名，是否启用等。
 
# 网络命令
## 简介
和网络相关的命令有：
表5-1 网络命令介绍

|命令名	|简要说明|
|---|---|
|aodvs|显示aodvs路由表
|arp|添加、删除或查看ARP表
|ftpdpath|查看或设置ftp服务器初始化路径
|ftpds|显示ftp服务器信息
|hosttable|查看、添加或删除主机地址映射
|ifonfig|显示或配置网络配置信息
|ifdown|禁用一个网络接口
|ifrouter|设置默认路由接口
|ifup|启用一个网络接口
|ipv6|设置或显示ipv6
|nat|启动、关闭或设置NAT虚拟网络地址服务
|natalias|添加或删除NAT别名
|natmap|添加或删除NAT映射|
|nats|查看当前NAT虚拟地址服务状态
|nbname|显示或设置本机的NetBIOS的名字
|netstat|查看网络状态
|npfattach|在指定网络接口上使能网络数据包过滤器
|npfdetach|在指定网络接口上禁能网络数据包过滤器
|npfruleadd|添加一条网络数据包滤波器规则
|npfruledel|删除一条网络数据包滤波器规则
|npfs|查看网络数据包过滤器状态
|ping|Ping命令
|ping6|IPv6 Ping命令
|route|添加、删除、修改或查看系统路由表
|tftp|使用tftp命令接收或者发送一个文件
|tftpdpath|查看或设置tftp服务器本地路径
|vlan|显示、设置和删除net接口
|vpnclose|删除一个虚拟网络接口
|vpnopen|创建一个虚拟网络接口
## 命令解析
### aodvs 
#### 命令格式：
```shell
aodvs
```
#### 命令说明：
显示aodv路由表
#### 常用选项：
无。
#### 使用说明：
该命令用来显示aodvs路由表。
#### 参数说明：
无。
#### 备注：
无。
#### 样例：
  @import "./image/image112.png"
图5.1 aodvs命令结果
#### 配置：
当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。当LW_CFG_NET_EN> 0)，允许提供网络功能时，该命令将会被包含。
#### 输出定义：
无。
### arp 
#### 命令格式：
```shell	
arp -a 
arp -s inet_address  physical_address 
arp -d inet_address
```
#### 命令说明：
添加、删除或查看ARP表。
#### 常用选项：
–a：查看arp表；
-s：增加地址到arp；
-d：删除存在arp表中的地址。
#### 使用说明：
该命令有3种用法，其中arp –a 用于查看ARP表；arp -s inet_address  physical_address 用于增加一个地址到ARP表中；arp -d inet_address 用于删除一个存在ARP表中的地址。
#### 参数说明：
无。
#### 备注：
arp表信息存放在/proc/net/arp文件下。
#### 样例：
  @import "./image/image113.png"
图5.2 arp命令结果
#### 配置：
当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。当LW_CFG_NET_EN> 0，允许提供网络功能时，该命令将会被包含。
#### 输出定义：
face：网卡名称。
type：ip类型。
### ftpdpath 
#### 命令格式：
```shell
ftpdpath
ftpdpath  new path
```
#### 命令说明：
查看或设置ftp服务器初始化路径。
#### 常用选项：
ftpdpath。
#### 使用说明：
该命令有2种用法，无参数时，显示ftp服务器的初始化路径；当跟上参数genpass时，设置ftp的路径。
#### 参数说明：
无。
#### 备注：
无。
#### 样例：
  @import "./image/image114.png"
图5.3 ftpdpath命令结果
#### 配置：
当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。当(LW_CFG_NET_EN > 0) 和(LW_CFG_NET_FTPD_EN > 0)，允许提供网络功能且使能了ftp服务器，该命令将会被包含。
#### 输出定义：
显示ftp路径。
### ftpds 
#### 命令格式：
```shell
ftpds
```
#### 命令说明：
显示ftp服务器信息。
#### 常用选项：
无。
#### 使用说明：
该命令用来显示tfp服务器的信息。
#### 参数说明：
无。
#### 备注：
无。
#### 样例：
  @import "./image/image115.png"
图5.4 ftpds命令结果
#### 配置：
当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。当(LW_CFG_NET_EN > 0) 和(LW_CFG_NET_FTPD_EN > 0)，允许提供网络功能且使能了ftp服务器，该命令将会被包含。
#### 输出定义：
无。
### hosttable 
#### 命令格式：
```shell
hosttable
hosttable  -s  host  addr
hosttable  -d  host
```
#### 命令说明：
查看、添加或删除主机地址映射。
#### 常用选项：
-s：添加地址映射；
-d：删除地址映射。
#### 使用说明：
该命令有3种用法，无参数是显示主机的地址映射关系；参数-s表示添加地址映射关系，后面跟主机名和地址；参数-d用于删除地址映射关系，后面跟主机名。
#### 参数说明：
-s表示添加地址映射关系，后面跟主机名和地址。
#### 备注：
无。
#### 样例：
  @import "./image/image116.png"
图5.5 hosttable命令结果
#### 配置：
当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。当LW_CFG_NET_EN> 0)，允许提供网络功能时，该命令将会被包含。
#### 输出定义：
无。
### ifconfig 
#### 命令格式：
```shell
ifconfig 
ifconfig  netifname
ifconfig  netifname  inet  address
ifconfig  netifname  netmask  address
ifconfig  netifname  gateway  address
ifconfig  dns 	0  address
```
#### 命令说明：
显示或配置网络配置信息。
#### 常用选项：
无。
#### 使用说明：
该命令有5种用法：
1、无参数是显示所有的网络配置的信息；
2、后面跟网口的名字显示指定的网口的配置信息；
3、inet用于配置ip地址，netmask用于配置子网掩码，gateway配置默认网关；
4、dns 0用于配置首选dns服务器；
5、dns1用于配置备用的dns服务器。
#### 参数说明：
无。
#### 备注：
无。
#### 样例：
  @import "./image/image117.png"
图5.6 ifconfig命令结果
#### 配置：
当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。当LW_CFG_NET_EN> 0)，允许提供网络功能时，该命令将会被包含。
#### 输出定义：
显示当前网络配置信息。
### ifdown 
#### 命令格式：
```shell
ifdown  netifname
```
#### 命令说明：
禁用一个网络接口。
#### 常用选项：
无。
#### 使用说明：
该命令用来禁用指定的网络端口。
#### 参数说明：
netifname：网络端口名。
#### 备注：
无。
#### 样例：
  @import "./image/image118.png"
图5.7 ifdown命令结果
#### 配置：
当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。当LW_CFG_NET_EN> 0)，允许提供网络功能时，该命令将会被包含。
#### 输出定义：
无。
### ifrouter 
#### 命令格式：
```shell 
ifconfig  netifname
```
#### 命令说明：
设置默认路由接口。
#### 常用选项：
无。
#### 使用说明：
该命令用来设置默认的路由接口。
#### 参数说明：
无。
#### 备注：
该命令已经被route替换。
#### 样例：
  @import "./image/image119.png"
图5.8 ifrouter命令结果
#### 配置：
当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。当LW_CFG_NET_EN> 0)，允许提供网络功能时，该命令将会被包含。
#### 输出定义：
无。
### ifup 
#### 命令格式：
```shell
ifup  netifname
ifup  netifname 	–dhcp
ifup  netifname  -nodhcp
```
#### 命令说明：
启用一个网络接口。
#### 常用选项：
ifup  netifname。
#### 使用说明：
该命令有3种用法， 其中只跟网络接口，用于启用一个网络；后面跟参数-dhcp，指明使用dhcp客服得到网络地址（自动获取ip地址）；后面跟参数-nodhcp，表面不使用dhcp获得ip地址。
#### 参数说明：
无。
#### 备注：
1.ifup netifname启用网络接口，关于是否使用dhcp依赖于上次是否使用了dhcp。
2.dhcp的使用需要配置LWIP_DHCP > 0， 是否允许dhcp协议。
#### 样例：
  @import "./image/image120.png"
图5.9 ifup命令结果
#### 配置：
当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。当LW_CFG_NET_EN> 0)，允许提供网络功能时，该命令将会被包含。
#### 输出定义：
无。
###  ipv6-
#### 命令格式：
```shell 
ipv6  address  ifname  address%prefixlen
ipv6  noaddress  ifname  address%prefixlen
```
#### 命令说明：
设置或显示ipv6。
#### 常用选项：
address：添加地址；
noaddress：删除地址。
#### 使用说明：
该命令有2种用法。后面跟参数address，给ifname设置ipv6的地址；后面跟参数noaddress，删除接口中此个ivp6地址。
#### 参数说明：
无。
#### 备注：
无。
#### 样例：
 @import "./image/image121.png"
图5.10 ipv6命令结果
#### 配置：
当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。当LW_CFG_NET_EN> 0)，允许提供网络功能时，该命令将会被包含。
#### 输出定义：
无。
###  nat
#### 命令格式：
```shell	
nat  stop
nat  LAN netif  WAN netif
```
#### 命令说明：
启动、关闭或设置NAT虚拟网络地址服务。
#### 常用选项：
stop：关闭nat虚拟网络地址服务。
#### 使用说明：
该命令有 2种用法，后面跟参数stop时，关闭NAT虚拟网络地址服务；后面跟参数LAN netif  WAN netif 用来启动NAT虚拟网络地址服务。
#### 参数说明：
无。
#### 备注：
无。
#### 样例：
 @import "./image/image122.png"
图5.11 nat命令结果
#### 配置：
当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。当LW_CFG_NET_EN> 0且LW_CFG_NET_NAT_EN>0，允许提供网络功能并且使用了nat服务，该命令将会被包含。
#### 输出定义：
nat工作状态。
###  natalias 
#### 命令格式：
```shell
natalias  add  alias  LAN start  LAN end
natalias  del  alias
```
#### 命令说明：
添加或删除NAT别名。
#### 常用选项：
add：添加别名；
del：删除别名。
#### 使用说明：
该命令有两种用法。跟参数add时用来给LAN start 到LAN end网段添加别名；跟参数del用来删除一个NAT别名。
#### 参数说明：
LAN start：LAN起始端；
LAN end：LAN结束端。
#### 备注：
无。
#### 样例：
 @import "./image/image123.png"
图5.12 natalias命令结果
#### 配置：
当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。当LW_CFG_NET_EN> 0且LW_CFG_NET_NAT_EN>0，允许提供网络功能并且使用了nat服务，该命令将会被包含。
#### 输出定义：
无。
###  natmap 
#### 命令格式：
```shell
natmap  add  WAN port  LAN port  LAN IP  protocol
natmap  del  WAN port  LAN port  LAN IP  protocol
```
#### 命令说明：
添加或删除NAT映射。
#### 常用选项：
无。
#### 使用说明：
该命令有两中用法，跟参数add用来添加一个NAT映射；跟参数del用来删除一个NAT映射。协议有tcp和udp两种。
#### 参数说明：
无。
#### 备注：
无。
#### 样例：
 @import "./image/image124.png"
图5.13 natmap命令结果
#### 配置：
当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。当LW_CFG_NET_EN> 0且LW_CFG_NET_NAT_EN>0，允许提供网络功能并且使用了nat服务，该命令将会被包含。
#### 输出定义：
无。
###  nats 
#### 命令格式：
```shell
nats
```
#### 命令说明：
查看当前NAT虚拟地址服务状态。
#### 常用选项：
无。
#### 使用说明：
该命令查看当前NAT虚拟地址服务的状态。
#### 参数说明：
无。
#### 备注：
nats的状态在/proc/net/nat/info文件中。
#### 样例：
 @import "./image/image125.png"
图5.14 nats命令结果
#### 配置：
当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。当LW_CFG_NET_EN> 0且LW_CFG_NET_NAT_EN>0，允许提供网络功能并且使用了nat服务，该命令将会被包含。
#### 输出定义：
无。
###  nbname 
#### 命令格式：
```shell
nbname
nbname  hostname
```
#### 命令说明：
显示或设置本机的NetBIOS的名字。
#### 常用选项：
无。
#### 使用说明：
该命令有2种用法，不跟参数用于显示本机的NetBIOS的名字；跟参数设置NetBIOIS的名字。
#### 参数说明：
hostname：设置的netbios名字。
#### 备注：
无。
#### 样例：
 @import "./image/image126.png"
图5.15 nbname命令结果
#### 配置：
当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。当LW_CFG_NET_EN> 0且LW_CFG_NET_NETBIOS_EN>0，允许提供网络功能并且使能简易netbios名字服务，该命令将会被包含。
#### 输出定义：
显示netbios名称。
###  netstat 
#### 命令格式：
```shell
netstat
netstat  -wtux –A  -i 
netstat  -hrigsapl
```
#### 命令说明：
查看网络状态。
#### 常用选项：
无。
#### 使用说明：
该命令有3种用法，无参数是显示全部的套接字信息；带参数时显示相应的信息。
-h, --help            显示帮助信息
-r, --route            显示路由表信息
-i, --interface         显示网络接口信息
-g, --groups          显示组播表情况
-s, --statistics         显示统计信息
-a, --all              显示全部的套接字信息
-p, --packet          显示数据包套接字信息
-l, --listening       	显示接收到的服务器套接字信息
-w, --raw             显示原始套接字信息
-t, --tcp              显示tcp套接字信息
-u, --udp             显示udp套接字信息
-x, --unix             显示unix套接字信息
-A `<net type>`      	列出该网络类型连线中的相关地址。有inet、 inet6、unix
数字1、2、3         表示，1表示unix、2表示inet、3表示inet6
#### 参数说明：
无。
#### 备注：
无。
#### 样例：
 @import "./image/image127.png"
图5.16 natstat命令结果
#### 配置：
当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。当LW_CFG_NET_EN> 0，允许提供网络功能时，该命令将会被包含。
#### 输出定义：
显示当前网络状态。
###  npfattach 
#### 命令格式：
```shell
npfattach  etifname
```
#### 命令说明：
在指定网络接口上使能网络数据包过滤器。
#### 常用选项：
无。
#### 使用说明：
该命令将指定的网络接口上的网络数据包过滤器使能。
#### 参数说明：
无。
#### 备注：
无。
#### 样例：
 @import "./image/image128.png"
图5.17 npfattach命令结果
#### 配置：
当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。当LW_CFG_NET_EN> 0且LW_CFG_NET_NPF_EN >0，允许提供网络功能并且使能NPF服务，该命令将会被包含。
#### 输出定义：
无。
###  npfdetach 
#### 命令格式：
```shell
npfdetach  netifname
```
#### 命令说明：
在指定网络接口上禁能网络数据包过滤器。
#### 常用选项：
无。
#### 使用说明：
该命令有1种用法，将指定网络接口上的网络数据包过滤器禁用。
#### 参数说明：
netifname：网络接口名。
#### 备注：
无。
#### 样例：
 @import "./image/image129.png"
图5.18 npfdetach命令结果
#### 配置：
当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。当LW_CFG_NET_EN> 0且LW_CFG_NET_NPF_EN >0，允许提供网络功能并且使能NPF服务，该命令将会被包含。
#### 输出定义：
无。
###  npfruleadd 
#### 命令格式：
```shell
npfruleadd  netifname  mac  ??：??：??：??：??：?? 
npfruleadd  netifname  ip    ???.???.???.???  ???.???.???.??? 
npfruleadd  netifname  udp  ???.???.???.???  ???.???.???.???  iports  iporte
npfruleadd  netifname  tcp   ???.???.???.???  ???.???.???.???  iports iporte
```
#### 命令说明：
添加一条网络数据包滤波器规则。
#### 常用选项：
无。
#### 使用说明：
该命令用来添加一条网络数据包滤波器规则。其中规则类型可以有mac、ip、udp、tcp。添加mac规则时参数是禁止通行的mac地址数组；添加ip规则时，参数是禁止通行的ip起始与结束地址；当添加udp或tcp规则时，参数是禁止通行的ip起始与结束地址、禁止通行的本地起始与结束端口号。
#### 参数说明：
无。
#### 备注：
无。
#### 样例：
 @import "./image/image130.png"
图5.19 npfruleadd命令结果
#### 配置：
当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。当LW_CFG_NET_EN> 0且LW_CFG_NET_NPF_EN >0，允许提供网络功能并且使能NPF服务，该命令将会被包含。
#### 输出定义：
无。
###  npfruledel 
#### 命令格式：
```shell
npfruledel  netifname  rule sequence num
```
#### 命令说明：
删除一条网络数据包滤波器规则。
#### 常用选项：
无。
#### 使用说明：
该命令用来删除一条网络数据包滤波器规则。
#### 参数说明：
无。
#### 备注：
无。
#### 样例：
  @import "./image/image131.png"
图5.20 npfruledel命令结果
#### 配置：
当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。当LW_CFG_NET_EN> 0且LW_CFG_NET_NPF_EN >0，允许提供网络功能并且使能NPF服务，该命令将会被包含。
#### 输出定义：
无。
###  npfs 
#### 命令格式：
```shell
nbfs
```
#### 命令说明：
查看网络数据包过滤器状态。
#### 常用选项：
无。
#### 使用说明：
该命令用来查看网络数据包过滤器的状态。
#### 参数说明：
无。
#### 备注：
网络数据包过滤器的状态保存在/proc/net/netfilter下。
#### 样例：
  @import "./image/image132.png"
图5.21 npfs命令结果
#### 配置：
当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。当LW_CFG_NET_EN> 0且LW_CFG_NET_NPF_EN >0，允许提供网络功能并且使能NPF服务，该命令将会被包含。
#### 输出定义：
无。
###  ping 
#### 命令格式：
```shell
ping  ip/hostname [-l datalen] [-n times] [-i ttl] [-w timeout]
```
#### 命令说明：
Ping命令。
#### 常用选项：
无。
#### 使用说明：
向指定的ip发送数据，测试网络的通断。使用-l参数指明发生数据包的大小；使用-n参数指明发送数据包的大小；-i参数设置ttl的值，最大为255；-w指定超时时间。其中默认的数据包大小为32byte，发送次数为4，TTL为255，超时时间为3000。
#### 参数说明：
无。
#### 备注：
无。
#### 样例：
  @import "./image/image133.png"
图5.22 ping命令结果
#### 配置：
当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。当LW_CFG_NET_EN> 0且LW_CFG_NET_PING_EN>0，允许提供网络功能并且需要ping工具时，该命令将会被包含。
#### 输出定义：
无。
###  ping6 
#### 命令格式：
```shell
ping6  ip(v6)/hostname [-l datalen] [-n times] [-w timeout] [-I interface]
```
#### 命令说明：
IPv6 Ping命令。
#### 常用选项：
无。
#### 使用说明：
向指定的ip（ipv6）发送数据，测试网络的通断。使用-l参数指明发生数据包的大小；使用-n参数指明发送数据包的大小；-I设置网络接口名；-w指定超时时间。其中默认的数据包大小为32byte，发送次数为4，超时时间为3000。
#### 参数说明：
无。
#### 备注：
无。
#### 样例：
  @import "./image/image134.png"
图5.23 ping6命令结果
#### 配置：
当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。当LW_CFG_NET_EN> 0且LW_CFG_NET_PING_EN>0，允许提供网络功能并且需要ping工具时，该命令将会被包含。
#### 输出定义：
无。
###  route
#### 命令格式：
```shell
route
route  add|change  -host | -net  ipaddr  gateway  if/dev  name
route  add|change  default  if|dev  name
route  del  ipaddr
```
#### 命令说明：
添加、删除、修改或查看系统路由表。
#### 常用选项：
add	：用来增加一个路由信息；
change：改变现有的路由信息；
del：删除现有的路由信息；
-host：目标地址是一个主机；
-net：目标地址是一个网络；
if：为可以访问的目标接口指定接口索引；
dev：指定目标接口索引为输入的name；
default：操作默认的路由。
#### 使用说明：
该命令只有4种用法，格式1无参数用于显示路由表。格式2用于修改或增加一个路由表信息；格式3用于修改或增加默认的路由信息；格式4用于删除一条路由信息。
查看路由表输出信息：
Destination：网络目的地址，列出了路由器连接的所有的网段。
Gateway：网关，一旦路由器确定它要把这个数据包转发到哪一个目的网络，路由器就要查看网关列表。网关表告诉路由器这个数据包应该转发到哪一个IP地址才能达到目的网络。
Mask：网络掩码，提供这个网段本身的子网掩码，而不是连接到这个网段的网卡的子网掩码。这基本上能够让路由器确定目的网络的地址类。
Flag：路由标志，标记当前网络节点的状态。
U Up表示此路由当前为启动状态
H Host，表示此网关为一主机
G Gateway，表示此网关为一路由器
R Reinstate Route，使用动态路由重新初始化的路由
D Dynamically,此路由是动态性地写入
M Modified，此路由是由路由守护程序或导向器动态修改! 表示此路由当前为关闭状态
Interface：接口索引，告诉路由器哪一个网卡连接到了合适的目的网络。
#### 参数说明：
	ipaddr：ip	地址；
	gateway：掩码；
name：接口索引的名字。
#### 备注：
无。
#### 样例：
  @import "./image/image135.png"
图5.24 route命令结果
#### 配置：
当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。当LW_CFG_NET_EN> 0，允许提供网络功能时，该命令将会被包含。
#### 输出定义：
无。
###  tftp 
#### 命令格式：
```shell
tftp  -i  Host  get | put  Source  Destination
```
#### 命令说明：
使用tftp命令接收或者发送一个文件。
#### 常用选项：
get：接受；
put：发送。
#### 使用说明：
该命令用来发送或接受一个文件。
#### 参数说明：
无。
#### 备注：
无。
#### 样例：
  @import "./image/image136.png"
图5.25 tftp命令结果
#### 配置：
当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。当LW_CFG_NET_EN> 0且LW_CFG_NET_TFTP_EN >0，允许提供网络功能并且使能了tftp服务，该命令将会被包含。
#### 输出定义：
无。
###  tftpdpath 
#### 命令格式：
```shell
tftpdpath
tftpdpath 	newpath
```
#### 命令说明：
查看或设置tftp服务器本地路径。
#### 常用选项：
无。
#### 使用说明：
该命令有2种用法，不跟参数用于查看当前的tftp服务器的本地路径；加newpath设置tftp 服务器的本地路径。
#### 参数说明：
无。
#### 备注：
无。
#### 样例：
  @import "./image/image137.png"
图5.26 tftpdpath命令结果
#### 配置：
当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。当LW_CFG_NET_EN> 0且LW_CFG_NET_NETBIOS_EN>0，允许提供网络功能并且使能简易netbios名字服务，该命令将会被包含。
#### 输出定义：
无。
###  vlan-
#### 命令格式：
```shell
vlan
vlan set  netifanme
	vlan clear  netifaname
```
#### 命令说明：
显示、设置和删除net接口的VLAN ID。
#### 常用选项：
无。
#### 使用说明：
该命令有3种用法，无参数时显示net接口；set选项设置VLAN ID；clear 清除响应接口的VLAN ID。
#### 参数说明：
无。
#### 备注：
无。
#### 样例：
  @import "./image/image138.png"
图5.27 vlan命令结果
#### 配置：
当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。当LW_CFG_NET_EN> 0且LW_CFG_NET_VLAN_EN >0，允许提供网络功能并且使能VLAN工具，该命令将会被包含。
#### 输出定义：
无。
# 时间命令
## 简介
表6-1 时间命令介绍
|命令名|	简要说明|
|---|---|
|date	|显示或设置系统当前时间|
|hwclock|	显示或同步操作系统与硬件RTC时钟|
|times|	显示utc或local时间|
|tzsync	|与环境变量TZ的时区同步|
## 命令解析
### date 
#### 命令格式：
```shell
date
date  -s  time | date
```
#### 命令说明：
显示或设置系统当前时间。
#### 常用选项：
-s 设置时间参数，但每次只能设置其中的一个。
#### 使用说明：
该命令有2种用法，无参数是显示当前系统时间；参数-s用于设置时间或日期，但每次只能设置其中的一个。 
#### 参数说明：
无。
#### 备注：
无。
#### 样例：
  @import "./image/image139.png"
图6.1 date命令结果
#### 配置：
该命令属于系统提供的tshell命令，当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令，则包含该命令。
#### 输出定义：
无。
### hwclock 
#### 命令格式：
```shell
hwclock  --show
hwclock  --hctosys
hwclock  --systohc
```
#### 命令说明：
显示或同步操作系统与硬件RTC时钟。
#### 常用选项：
--show：显示硬件RTC时钟；
--hctosys：同步RTC时钟到操作系统时钟；
--systohc：同步操作系统的时钟到RTC时钟。
#### 使用说明：
该命令有3种用法，跟参数—show显示硬件RTC时钟；跟参数—hctosys 同步RTC时钟到操作系统时钟；跟参数—systohc 同步操作系统的时钟到RTC时钟。
#### 参数说明：
无。
#### 备注：
跟参数时要输入两个-。
#### 样例：
  @import "./image/image140.png"
图6.2 hwclock命令结果
#### 配置：
当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。当LW_CFG_RTC_EN > 0从### 版后开始支持此命令，该命令将会被包含。
#### 输出定义：
无。
### times 
#### 命令格式：
```shell
times 
times –utc
```
#### 命令说明：
显示utc或local时间。
#### 常用选项：
-utc：显示格林威治时间。
#### 使用说明：
该命令有2种用法，不跟参数显示本地时间，跟参数-utc显示格林威治时间。 
#### 参数说明：
无。
#### 备注：
无。
#### 样例：
   @import "./image/image141.png"
图6.3 times命令结果
#### 配置：
该命令属于系统提供的tshell命令，当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令，则包含该命令。
#### 输出定义：
无。
### tzsync 
#### 命令格式：
```shell
tzsync
```
#### 命令说明：
与环境变量TZ的时区同步。
#### 常用选项：
无。
#### 使用说明：
该命令将时间与环境变量TZ的时区同步。 
#### 参数说明：
无。
#### 备注：
如果环境变量TZ的时区改变，则相应的时间也随之改变。
#### 样例：
改变环境变量TZ
装载环境变量，同步前后，查看时间
   @import "./image/image142.png"
   @import "./image/image143.png"
图6.4 tzsync命令结果
#### 配置：
该命令属于系统提供的tshell命令，当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令，则包含该命令。
#### 输出定义：
无。
 
# 动态装载命令
## 简介
表7-1 动态装载命令介绍
|命令名|	简要说明|
|---|---|
|debug|	调试一个进程|
|dlconfig	|配置动态链接器工作参数
|leakchk	|内存泄漏检查
|leakchkstart|	启动内存泄漏跟踪器
|leakchkstop	|关闭内存泄漏跟踪器
|lsmod|	查看系统装载的所有内核模块信息
|modulegcov	|生成内核模块代码文件（*.gcda）
|modulereg	|注册一个模块
|modules	|查看系统装载的所有内核模块与进程动态链接库信息
|modulestat|	查看一个内核模块或动态链接库文件信息
|moduleunreg|	卸载一个模块
## 命令解析
### debug 
#### 命令格式：
```shell
debug  connect options  program  argments
debug  --attach  connect options  program  argments
```
#### 命令说明：
调试一个进程。
#### 常用选项：
无。
#### 使用说明：
该命令用于调试一个进程。后面的参数依次是连接方式、工程名、参数列表。其中连接方式可以有网络、串口、终端、attach
#### 参数说明：
参数依次是连接方式、工程名、参数列表。
#### 备注：
无。
#### 样例：
使用网络调试
使用串口调试
使用终端调试
使用attrch方式调试
   @import "./image/image144.png"
   @import "./image/image145.png"
   @import "./image/image146.png"
   @import "./image/image147.png"
	图7.1 debug命令结果
#### 配置：
该命令属于系统提供的tshell命令，当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令，则包含该命令。
#### 输出定义：
无。
### dlconfig 
#### 命令格式：
```shell
dlconfig  share  en | dis
dlconfig  refresh  *
dlconfig  refresh
```
#### 命令说明：
配置动态链接器工作参数。
#### 常用选项：
dlconfig  share  en/dis。
#### 使用说明：
该命令有2种用法，跟选项share，使能或禁能动态链接器；跟选项refresh 清除分享数据信息，其中跟*表示只清除系统的，否则清除全部的。
#### 参数说明：
无。
#### 备注：
无。
#### 样例：
   @import "./image/image148.png"
	图7.2 dlconfig命令结果
#### 配置：
当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。当LW_CFG_MODULELOADER_EN> 0，需要提供模块装载服务，该命令将会被包含。
#### 输出定义：
无。
### leakchk 
#### 命令格式：
```shell
leakchk
```
#### 命令说明：
内存泄漏检查。
#### 常用选项：
无。
#### 使用说明：
该命令用来打印内存泄漏追踪信息。 
#### 参数说明：
无。
#### 备注：
无。
#### 样例：
   @import "./image/image149.png"
	图7.3 leakchk命令结果
#### 配置：
当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。当LW_CFG_SHELL_HEAP_TRACE_EN>0，使能 shell heap 跟踪工具，该命令将会被包含。
#### 输出定义：
打印内存泄漏追踪信息。
### leakchkstart 
#### 命令格式：
```shell
leakchkstart  max save node number  pid
```
#### 命令说明：
启动内存泄漏跟踪器。
#### 常用选项：
无。
#### 使用说明：
该命令用来启动内存泄漏跟踪器，后面的参数是最大跟踪节点数、进程id。 
#### 参数说明：
max save node number：最大跟踪节点数。
#### 备注：
最大跟踪节点数最小为1024。
进程id默认为0。
#### 样例：
   @import "./image/image150.png"
	图7.4 leakchkstart命令结果
#### 配置：
当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。当LW_CFG_SHELL_HEAP_TRACE_EN>0，使能 shell heap 跟踪工具，该命令将会被包含。
#### 输出定义：
无。
### leakchkstop 
#### 命令格式：
```shell
leakchkstop
```
#### 命令说明：
关闭内存泄漏跟踪器。
#### 常用选项：
无。
#### 使用说明：
该命令用来关闭内存泄漏跟踪器。 
#### 参数说明：
无。
#### 备注：
无。
#### 样例：
 @import "./image/image151.png"
	图7.5 leakchkstop命令结果
#### 配置：
当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。当LW_CFG_SHELL_HEAP_TRACE_EN>0，使能 shell heap 跟踪工具，该命令将会被包含。
#### 输出定义：
打印内存泄漏跟踪器状态。
### lsmod 
#### 命令格式：
```shell
lsmod
```
#### 命令说明：
查看系统装载的所有内核模块信息。
#### 常用选项：
无。
#### 使用说明：
该命令有1种用法，用来显示系统装置的所有内核模块的信息。 
#### 参数说明：
无。
#### 备注：
无。
#### 样例：
 @import "./image/image152.png"
	图7.6 lsmod命令结果
#### 配置：
当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。当LW_CFG_MODULELOADER_EN> 0，需要提供模块装载服务，该命令将会被包含。
#### 输出定义：
显示系统装置的所有内核模块的信息。
### modulegcov 
#### 命令格式：
```shell
modulegcov  kernel module handle
```
#### 命令说明：
生成内核模块代码文件（*.gcda）。
#### 常用选项：
无。
#### 使用说明：
该命令用来生成内核模块代码文件。 
#### 参数说明：
无。
#### 备注：
无。
#### 样例：
 @import "./image/image153.png"
	图7.7 modulegcov命令结果
#### 配置：
当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。当LW_CFG_MODULELOADER_EN> 0且LW_CFG_MODULELOADER_GCOV_EN>0，需要提供模块装载服务并且允许内核模块代码覆盖率分析接口，该命令将会被包含。
#### 输出定义：
添加一个内核文件。
### modulereg 
#### 命令格式：
```shell
modulereg  kernel module file *.ko
```
#### 命令说明：
注册一个模块。
#### 常用选项：
无。
#### 使用说明：
该命令有1种用法，用来注册一个模块文件。 
#### 参数说明：
无。
#### 备注：
无。
#### 样例：
 @import "./image/image154.png"
	图7.8 moulereg命令结果
#### 配置：
当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。当LW_CFG_MODULELOADER_EN> 0，需要提供模块装载服务，该命令将会被包含。
#### 输出定义：
提示模块注册是否成功。
### modules 
#### 命令格式：
```shell
modules  
modules  module name
```
#### 命令说明：
查看系统装载的所有内核模块与进程动态链接库信息。
#### 常用选项：
modules。
#### 使用说明：
该命令有1种用法，无参数是显示所有的内核模块与进程动态链接库信息；带参数查看指定的内核模块与进程动态链接库信息。 
#### 参数说明：
无。
#### 备注：
无。
#### 样例：
 @import "./image/image155.png"
	图7.9 modules命令结果
#### 配置：
该命令属于系统提供的tshell命令，当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令，则包含该命令。
#### 输出定义：
无。
###  modulestat 
#### 命令格式：
```shell
modulestat  program file
```
#### 命令说明：
查看一个内核模块或动态链接库文件信息。
#### 常用选项：
无。
#### 使用说明：
该命令用来查看一个内核模块或动态链接库文件的信息。 
#### 参数说明：
program file：内核模块或动态链接库文件。
#### 备注：
无
#### 样例：
 @import "./image/image156.png"
	图7.10 modulestat命令结果
#### 配置：
当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。当LW_CFG_MODULELOADER_EN> 0，需要提供模块装载服务，该命令将会被包含。
#### 输出定义：
打印出内核模块或动态链接库文件的信息。
###  moduleunreg 
#### 命令格式：
```shell
moduleunreg  kernel module handle
```
#### 命令说明：
卸载一个模块。
#### 常用选项：
无。
#### 使用说明：
该命令有1种用法，用来卸载一个模块。 
#### 参数说明：
kernel module handle：驱动编号。
#### 备注：
无。
#### 样例：
 @import "./image/image157.png"
	图7.11 moduleunreg命令结果
#### 配置：
当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。当LW_CFG_MODULELOADER_EN> 0，需要提供模块装载服务，该命令将会被包含。
#### 输出定义：
提示模块是否卸载成功。
 
# 其他命令
## 简介
表8-1 其他命令介绍
|命令名|	简要说明|
|---|---|
|args|	显示输入的全部参数，并且以空格为分隔符
|crypt|	对数据进行加密
|perfrefresh|	更新性能信息统计
|perfs|	显示性能统计的信息
|perfstart|	启动性能分析工具
|perfstop|	停止性能分析工具
|xmodemr|	使用xmodem协议接收一个文件
|xmodems|	使用xmodem协议发送一个文件
## 命令解析
### args 
#### 命令格式：
```shell 
args  [any argument...]
```
#### 命令说明：
显示输入的全部参数，并且以空格为分隔符。
#### 常用选项：
无。
#### 使用说明：
该命令将输入的参数以一定的格式显示出来。
#### 参数说明：
无。
#### 备注：
无。
#### 样例：
 @import "./image/image158.png"
图8.1 args命令结果
#### 配置：
该命令属于系统提供的tshell命令。当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。
#### 输出定义：
打印输入的参数。
### crypt 
#### 命令格式：
```shell
crypt  key  salt
```
#### 命令说明：
对数据进行加密。
#### 常用选项：
无。
#### 使用说明：
该命令将输入的参数以键值对的格式显示出来。其中key是要加密的明文，salt指定用来加密的密钥。
#### 参数说明：
key：是要加密的明文。
salt：指定用来加密的密钥。
#### 备注：
无。
#### 样例：
 @import "./image/image159.png"
图8.2 crypt命令结果
#### 配置：
当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。当LW_CFG_SHELL_PASS_CRYPT_EN>0，用户密码相关的支持，该命令将会被包含。
#### 输出定义：
无。
### xmodemr 
#### 命令格式：
```shell 
xmodemr  file path
```
#### 命令说明：
使用xmodem协议接收一个文件.
#### 常用选项：
无。
#### 使用说明：
该命令使用xmodem协议从远程接受一个文件。
#### 参数说明：
无。
#### 备注：
无。
#### 样例：
 @import "./image/image160.png"
图8.3 xmodemr命令结果
#### 配置：
该命令属于系统提供的tshell命令。当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。
#### 输出定义：
无。
### xmodems 
#### 命令格式：
```shell
xmodems  file path
```
#### 命令说明：
使用xmodem协议发送一个文件.
#### 常用选项：
无。
#### 使用说明：
该命令用来使用xmodem协议发送给远程一个文件。
#### 参数说明：
无。
#### 备注：
无。
#### 样例：
 @import "./image/image161.png"
图8.4 xmodems命令结果
#### 配置：
该命令属于系统提供的tshell命令。当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。
#### 输出定义：
无。
 
# 添加与配置
## 介绍
本章节将介绍关于怎样去添加或配置shell命名。
## 配置
配置某个shell命令
HYSylixOS的Shell命令支持可裁剪。如果需要裁剪某个命令，只需要将具体命令的配置宏置位0，就可以裁剪该命令。
以uadd命令为例，查看uadd命令介绍：
uadd：添加用户。
#### 命令格式：
```shell
	uadd  name  password  enable[0 / 1]  uid  gid  comment  homedir 
```
#### 使用说明：
	该命令用来添加一个用户。
#### 备注：				
用户id不能重复，添加用户时必须将其添加到一个存在的用户组。

#### 配置： 
当LW_CFG_SHELL_EN>0时，会允许操作系统提供tshell命令。 
当(LW_CFG_SHELL_USER_EN > 0)，使能了shell用户管理工具，该命令将会被包含。        
配置LW_CFG_SHELL_USER_EN为0，裁剪uadd命令。找到shell_cfg.h文件，将LW_CFG_SHELL_USER_EN置0，重新编译base和bsp文件，然后重新启动虚拟机。
（shell_cfg.h配置文件的路径base/libsylixos/SylixOS/config/shell/shell_cfg.h）
使用help查看是否有uadd命令：
 @import "./image/image162.png"
图9.1 查看命令
查看可知uadd命令的帮助文件和该命令都被裁剪了。
注意：配置shell命令应配置最小项宏，减小配置的范围。上例中配置了uadd命令，但相关的命令，例udel、umod、gadd、gdel等相关的命令也被裁剪了。
## 添加
HYSylixOS系统shell命令支持用户添加自己的命令，加方式为：
在ttintShellSysCmd.c中写对应的接口函数，然后在__tshellSysCmdInit添加shell命令。这里将用到三个系统接口。
分别是：
LW_API  ONG API_TShellKeywordAdd (
CPCHAR  pcKeyword, PCOMMAND_START_ROUTINE  pfuncCommand);
LW_API  ULONG  API_TShellFormatAdd (CPCHAR  pcKeyword, CPCHAR  pcFormat);
LW_API  ULONG  API_TShellHelpAdd (CPCHAR  pcKeyword, CPCHAR  pcFormat);
其中API_TShellKeywordAdd用来添加shell命令，并将该shell命令与相应的处理函数对应起来。
API_TShellFormatAdd函数用来添加相应shell命令的使用格式。
TShellHelpAdd用来添加响应shell命令的说明信息，介绍该命令要完成的相应功能。
例：添加一个hello的shell命令。
1.	在ttinyShellSysCmd.c文件中添加__tshellCostomCmdHello函数
```c
static INT   __tshellCosttomCmdHello(INT  iArgC, PCHAR ppcArgV[])
{
	if(iArgC > 2)
	{
		fprintf(stderr, "argument error.\n");
		return(-ERROR_TSHELL_EPARAM);
	}
	if(iArgC == 1)
	{
		printf("hello SylixOS\n";)
	}
	if(iArgC == 2)
	{
		printf("hello %s\n",ppcArgV[1]);
	}
		return 0;
}
```
2.	在__tshellSysCmdInit函数中添加代码
```c
API_TShellKeywordAdd("hello",__tshellCosttomCmdHello);
API_TShellFormatAdd("hello","[massage]");
API_TShellHelpAdd("hello","show hello sylixOS !\n");
```
3.	重新编译base工程和bsp工程。然后启动虚拟机测试是否添加成功
 @import "./image/image163.png"
图9.2 命令查看
