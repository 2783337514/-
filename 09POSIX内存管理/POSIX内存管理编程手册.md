# 概述
  
POSIX标准内存管理相关的函数在功能和内部行为上与HYSylixOS变长内存管理完全相同。本文档描述了HYSylixOS支持的POSIX标准内存管理编程接口。  
  
# 内存管理编程接口
  
## mem_findmax ：查找系统内存最大空闲区域
  
### 函数原型：
  
```c  
int mem_findmax   
(   
    void   
);  
```  
  
### 描述：
  
该接口用来获取当前系统内存最长空闲区域的大小。  
  
### 参数：
  
无。  
  
### 返回值：
  
返回系统内存最长连续空闲区域大小。  
  
### 错误码：
  
无。  
  
### 样例：
  
```c  
#include<stdio.h>  
#include<gjbext.h>   
int main (int argc, char**argv)   
{   
    int maxmem = 0;   
    // ……   
    maxmem = mem_findmax(); /* 取当前系统内存最长空闲区域的大小*/   
    // ……   
    return (0);   
}  
```  
  
## mem_getinfo ：获取系统内存信息
  
### 函数原型：
  
```c  
int mem_getinfo   
(   
    struct meminfo*info   
);  
```  
  
### 描述：
  
该接口用来获取系统当前内存信息（只有在系统线程中使用）。  
  
### 参数：
  
- info ：存放内存信息的指针。  
  
### 返回值：
  
成功返回0；失败返回-1，并置相应的错误码。  
  
### 错误码：  
  
- EINVAL ：参数无效。  
- EACCES ：权限不足（非系统线程）。  
  
### 样例：
  
```c  
#include<gjbext.h>   
void*Thread1(void*arg)   
{   
    int ret = 0;   
    struct meminfo info;   
    ret = mem_getinfo(&info); /* 获取系统当前内存信息*/   
    if(ret != 0)   
    {   
        printf("*func* mem_getinfo errorn");   
        return (void*)-1;   
    }   
    mem_show();   
    return NULL;   
}  
```  
  
## mem_show ：显示系统内存信息
  
### 函数原型：
  
```c  
void mem_show   
(   
    void   
);  
```  
  
### 描述：
  
该接口用来显示系统当前内存信息（只有在系统线程中使用）。  
  
### 参数：
  
无。  
  
### 返回值：
  
无。  
  
### 错误码：
  
无。  
  
### 样例：
  
```c  
#include<gjbext.h>   
void*Thread1(void*arg)   
{   
    int ret = 0;   
    struct meminfo info;   
    ret = mem_getinfo(&info);   
    if(ret != 0)   
    {   
        printf("*func* mem_getinfo errorn");   
        return (void*)-1;   
    }   
    mem_show(); /* 显示系统当前内存信息*/   
    return NULL;   
}  
```  
  
## heap_mem_init :初始化系统内存管理
  
### 函数原型：
  
```c  
int heap_mem_init   
(   
    int flag   
);  
```  
  
### 描述：
  
该接口用来初始化系统内存管理。  
  
### 参数：  
  
- flag ：系统内存管理标识。取值有：  
  
|flag|说明|  
|---|---|  
|FIRST_FIT_ALLOCATION|0|  
|BUDDY_ALLOCATION|1|  
  
### 返回值：
  
函数返回0。  
  
### 错误码：
  
无。  
  
### 样例：
  
```c  
```  
  
## mpart_module_init ：设置内存管理模块允许的最大的分区数
  
### 函数原型：
  
```c  
int mpart_module_init   
(   
    int max_parts   
);  
```  
  
### 描述：
  
该接口用来初始化内存管理模块，设置允许最大的分区数。  
  
### 参数：
  
- max_parts 最大分块数。不允许超过系统默认最大分块数40。  
  
### 返回值：
  
函数返回0，失败返回相应的错误码。  
  
### 错误码：
  
无。  
  
### 样例：
  
```c  
```  
  
## mpart_create ：创建可变长内存分区
  
### 函数原型：
  
```c  
int mpart_create   
(   
    char*addr,   
    size_t size,   
    mpart_id*mid   
);  
```  
  
### 描述：
  
该接口用来创建一个内存大小可变分区。  
  
### 参数：
  
- addr ：内存基地址。  
- size ：内存区域大小。  
- mid ：分区ID。  
  
### 返回值：
  
成功返回0；失败返回-1，并置错误码。  
  
### 错误码：
  
- EINVAL ：参数无效。  
- ENOSPC ：非法操作。  
  
### 样例：
  
```c  
#include<stdio.h>  
#include<gjbext.h>  
#include<string.h>   
int main (int argc, char**argv)   
{   
    int ret = 0;   
    static char addr[1024*8];   
    mpart_id mpid;   
    memset(addr, 0, 1024* 8);   
    ret = mpart_create(addr, 1024* 8,&mpid); /* 创建一个内存大小可变分区*/   
    if(ret != 0)   
    {   
        perror("func mpart_create errorn");   
        return -1;   
    }   
    // ……   
    return (0);   
}  
```  
  
## mpart_delete ：删除可变长内存分区
  
### 函数原型：
  
```c  
int mpart_delete   
(   
    mpart_id mid   
);  
```  
  
### 描述：
  
该接口用来删除一个内存大小可变分区。  
  
### 参数：
  
- mid ：分区ID。  
  
### 返回值：
  
成功返回0；失败返回-1，并置错误码。  
  
### 错误码：  
  
- EINVAL ：参数无效。  
- EBUSY ：设备资源忙。  
  
### 样例：
  
```c  
#include<stdio.h>  
#include<gjbext.h>  
#include<string.h>   
int main (int argc, char**argv)   
{   
    int ret = 0;   
    static char addr[1024*8];   
    mpart_id mpid;   
    memset(addr, 0, 1024* 8);   
    ret = mpart_create(addr, 1024* 8,&mpid);   
    if(ret != 0)   
    {   
        perror("func mpart_create errorn");   
        return -1;   
    }   
    // ……   
    ret = mpart_delete(mpid); /* 删除一个内存大小可变分区*/   
    if(ret != 0)   
    {   
        perror("func mpart_delete errorn");   
        return -1;   
    }   
    return (0);   
}  
```  
  
## mpart_addmem ：给指定分区增加内存
  
### 函数原型：
  
```c  
int mpart_addmem   
(   
    mpart_id mid,   
    char*addr,   
    size_t size   
);  
```  
  
### 描述：
  
该接口用来向指定内存大小可变分区添加内存。  
  
### 参数：
  
- mid ：分区ID。  
- addr ：内存基址。  
- size ：内存大小。  
  
### 返回值：
  
成功返回相应的信号量句柄；失败返回MQ_FAILED (mqd_t)-1，并置错误码。  
  
### 错误码：  
  
- EINVAL ：参数无效。  
- ERROR_KERNEL_IN_ISR ：系统处于中断中。  
- ERROR_KERNEL_MEMORY ：内存地址出现错误。  
- ERROR_REGION_SIZE ：分区大小太小。  
  
### 样例：
  
```c  
#include<stdio.h>  
#include<gjbext.h>  
#include<string.h>   
int main (int argc, char**argv)   
{   
    int ret = 0;   
    static char addr[1024*8];   
    mpart_id mpid;   
    char*add = (char*)malloc(1024* 8);   
    memset(addr, 0, 1024* 8);   
    ret = mpart_create(addr, 1024* 8,&mpid);   
    if(ret != 0)   
    {   
        perror("func mpart_create errorn");   
        return -1;   
    }   
    ret = mpart_addmem(mpid, add, 1024*8); /*向内存大小可变分区添加内存*/   
    if(ret != 0)   
    {   
        perror("func mpart_addmem errorn");   
        return -1;   
    }   
    // ……   
    return 0;   
}  
```  
  
## mpart_alloc ：从内存分区申请内存
  
### 函数原型：
  
```c  
void*mpart_alloc   
(   
    mpart_id mid,   
    size_t size   
);  
```  
  
### 描述：
  
该接口用来从指定内存大小可变分区分配内存。  
  
### 参数：
  
- mid ：分区ID。  
- size ：分配内存大小。  
  
### 返回值：
  
成功返回分配内存的首地址；失败返回NULL，并置错误码。  
  
### 错误码：
  
- EINVAL ：参数无效。  
- ERROR_KERNEL_IN_ISR ：系统处于中断中。  
- ENOMEM ：内存不足。  
- ERROR_KERNEL_HANDLE_NULL ：句柄无效。  
- ERROR_REGION_SIZE ：分区大小太小。  
  
### 样例：
  
```c  
#include<stdio.h>  
#include<gjbext.h>  
#include<string.h>   
int main (int argc, char**argv)   
{   
    int ret = 0;   
    static char addr[1024*8];   
    mpart_id mpid;   
    char*add = (char*)malloc(1024* 8);   
    char* p = NULL;   
    memset(addr, 0, 1024* 8);   
    ret = mpart_create(addr, 1024* 8,&mpid);   
    if(ret != 0)   
    {   
        perror("func mpart_create errorn");   
        return -1;   
    }   
    p = (char*)mpart_alloc(mpid, 1024* 4); /* 从内存大小可变分区分配内存*/   
    if(p == NULL)   
    {   
        perror("func mpart_alloc errorn");   
    }   
    // ……   
    return (0);   
}  
```  
  
## mpart_memalign ：从内存分区申请指定对齐大小的分存
  
### 函数原型：
  
```c  
void*mpart_memalign   
(   
    mpart_id mid,   
    size_t alignment,   
    size_t size   
);  
```  
  
### 描述：
  
该接口用来从指定内存大小可变分区对齐分配内存。  
  
### 参数：
  
- mid ：分区ID。  
- alignment ：对齐大小。  
- size ：分配内存大小。  
  
### 返回值：
  
成功返回分配内存的首地址；失败返回NULL，并置错误码。  
  
### 错误码：  
  
- EINVAL ：参数无效。  
- ERROR_KERNEL_IN_ISR ：系统处于中断中。  
- ENOMEM ：内存不足。  
- ERROR_KERNEL_HANDLE_NULL ：句柄无效。  
- ERROR_REGION_SIZE ：分区大小太小。  
- ERROR_REGION_ALIGN ：对齐关系错误。  
  
### 样例：
  
```c  
#include<stdio.h>  
#include<gjbext.h>  
#include<string.h>   
int main (int argc, char**argv)   
{   
    int ret = 0;   
    static char addr[1024*8];   
    mpart_id mpid;   
    char*add = (char*)malloc(1024* 8);   
    char* p = NULL;   
    memset(addr, 0, 1024* 8);   
    ret = mpart_create(addr, 1024* 8,&mpid);   
    if(ret != 0)   
    {   
        perror("func mpart_create errorn");   
        return -1;   
    } /**从指定内存大小可变分区对齐分配内存*/   
    p = (char*)mpart_ memalign(mpid, 4, 1024* 4);   
    if(p == NULL)   
    {   
        perror("func mpart_alloc errorn");   
    }   
    // ……   
    return (0);   
}  
  
```  
  
## mpart_realloc ：从分区重新申请指定大小的内存
  
### 函数原型：
  
```c  
void*mpart_realloc   
(   
    mpart_id mid,   
    char*addr,   
    size_t size   
);  
```  
  
### 描述：
  
该接口用来从指定内存大小可变分区中重新分配内存。  
  
### 参数：
  
- mid ：分区ID。  
- addr ：原先分配的内存地址。  
- size ：分配内存大小。  
  
### 返回值：
  
成功返回分配内存的首地址；失败返回NULL，并置错误码。  
  
### 错误码：
  
- EINVAL ：参数无效。  
- ERROR_KERNEL_IN_ISR ：系统处于中断中。  
- ERROR_KERNEL_HANDLE_NULL ：句柄无效。  
- ENOMEM ：内存不足。  
  
### 样例：
  
```c  
#include<stdio.h>  
#include<gjbext.h>  
#include<string.h>   
int main (int argc, char**argv)   
{   
    int ret = 0;   
    static char addr[1024*8];   
    mpart_id mpid;   
    char*add = (char*)malloc(1024* 8);   
    char* p = NULL;   
    memset(addr, 0, 1024* 8);   
    ret = mpart_create(addr, 1024* 8,&mpid);   
    if(ret != 0)   
    {   
        perror("func mpart_create errorn");   
        return -1;   
    }   
    p = (char*)mpart_alloc(mpid, 1024* 4);   
    if(p == NULL)   
    {   
        perror("func mpart_alloc errorn");   
    }   
    // …… /** 从指定内存大小可变分区中重新分配内存*/   
    p = (char*)mpart_realloc(mpid, p, 1024* 5);   
    if(p == NULL)   
    {   
        perror("func mpart_realloc errorn");   
    }   
    // ……   
    return (0);   
}  
```  
  
## mpart_free ：释放申请的内存
  
### 函数原型：
  
```c  
int mpart_free   
(  
    mpart_id mid,   
    char*addr   
);  
```  
  
### 描述：
  
该接口用来释放从指定内存大小可变分区中分配的内存。  
  
### 参数：
  
- mid ：分区ID。  
- addr ：分配的内存地址。  
  
### 返回值：
  
成功返回0；失败返回-1，并置错误码。  
  
### 错误码：
  
- EINVAL ：参数无效。  
- ERROR_KERNEL_IN_ISR ：系统处于中断中。  
- ERROR_REGION_NULL ：控制块错误。  
- ERROR_KERNEL_HANDLE_NULL ：句柄无效。  
  
### 样例：
  
```c  
#include<stdio.h>  
#include<gjbext.h>  
#include<string.h>   
int main (int argc, char**argv)   
{   
    int ret = 0; static char addr[1024*8];   
    mpart_id mpid;   
    char*add = (char*)malloc(1024* 8);   
    char* p = NULL;   
    memset(addr, 0, 1024* 8);   
    ret = mpart_create(addr, 1024* 8,&mpid);   
    if(ret != 0)  
    {   
        perror("func mpart_create errorn");   
        return -1;   
    }   
    p = (char*)mpart_alloc(mpid, 1024* 4);   
    if(p == NULL)   
    {   
        perror("func mpart_alloc errorn");   
        return -1;  
    }   
    // ……   
    ret = mpart_free(mpid, p); /*释放从指定内存大小可变分区中分配的内存*/   
    if(ret != 0)   
    {   
        perror("func mpart_free errorn");   
    }   
    // ……   
    return (0);   
}  
```  
  
## mpart_findmaxfree ：查看分区最大空闲分段
  
### 函数原型：
  
```c  
int mpart_findmaxfree   
(   
    mpart_id mid   
);  
```  
  
### 描述：
  
该接口用来获取指定内存大小可变分区最大空闲分断。  
  
### 参数：
  
- mid ：分区ID。  
  
### 返回值：
  
成功返回该分区最大空闲分断大小；失败返回-1，并置错误码。  
  
### 错误码：
  
- ERROR_KERNEL_HANDLE_NULL ：句柄无效。  
  
### 样例：
  
```c  
#include<stdio.h>  
#include<gjbext.h>  
#include<string.h>   
int main (int argc, char**argv)   
{   
    int ret = 0;   
    static char addr[1024*8];   
    mpart_id mpid;   
    int maxfree;   
    memset(addr, 0, 1024* 8);   
    ret = mpart_create(addr, 1024* 8,&mpid);   
    if(ret != 0)   
    {   
        perror("func mpart_create errorn");   
        return -1;   
    }   
    // ……   
    /**获取指定内存大小可变分区最大空闲分断*/   
    maxfree = mpart_findmaxfree(mpid);   
    if(maxfree != -1)   
    {   
        printf("mpid max free :%dn", maxfree);   
    }   
    // ……   
    return (0);   
}  
```  
  
## mpart_getinfo ：获取内存分区信息
  
### 函数原型：
  
```c  
int mpart_getinfo   
(   
    mpart_id mid,   
    struct partinfo*pi   
);  
```  
  
### 描述：
  
该接口用来获取指定内存大小可变分区信息。  
  
### 参数：
  
- mid ：分区ID。  
- pi ：存放内存信息的指针。  
  
### 返回值：
  
成功返回0；失败返回-1，并置错误码。  
  
### 错误码：  
  
- EINVAL ：参数无效。  
- ERROR_KERNEL_HANDLE_NULL ：句柄无效。  
  
### 样例：
  
```c  
#include<stdio.h>  
#include<gjbext.h>  
#include<string.h>   
int main (int argc, char**argv)   
{   
    int ret = 0;   
    static char addr[1024*8];   
    mpart_id mpid;   
    struct partinfo info;   
    memset(addr, 0, 1024* 8);   
    ret = mpart_create(addr, 1024* 8,&mpid);   
    if(ret != 0)   
    {   
        perror("func mpart_create errorn");   
        return -1;   
    }   
    // ……   
    ret = mpart_getinfo(mpid,&info); /* 获取指定内存大小可变分区信息*/   
    if(ret == 0)   
    {   
        // ……   
    }   
    // ……   
    return (0);   
}  
```  
  
## mlock ：锁定指定内存地址空间
  
### 函数原型：
  
```c  
int mlock   
(   
    const void*pvAddr,   
    size_t stLen   
);  
```  
  
### 描述：  
  
该接口用来锁定指定内存地址空间不进行换页处理。  
  
### 参数：  
  
- pvAddr ：起始地址。  
- stLen ：内存空间长度。  
  
### 返回值：
  
成功返回0；失败返回-1，并置错误码。  
  
### 错误码：
  
- EACCES ：权限不足（需要进程ID为0）。  
  
### 样例：  
  
```c  
```  
  
## munlock ：解锁指定内存地址空间
  
### 函数原型：
  
```c  
int munlock   
(   
    const void*pvAddr,   
    size_t stLen   
);  
```  
  
### 描述：  
  
该接口用来解锁指定内存地址空间，允许进行换页处理。  
  
### 参数：   
  
- pvAddr ：起始地址。  
- stLen ：内存空间长度。  
  
### 返回值：
  
成功返回0；失败返回-1，并置错误码。  
  
### 错误码：
  
- EACCES ：权限不足（需要进程ID为0）。  
  
### 样例：  
  
```c  
```  
  
## mlockall ：锁定进程空间
  
### 函数原型：
  
```c  
int mlockall  
(   
    int iFlag   
);  
```  
  
### 描述：  
  
该接口用来锁定进程空间不进行换页处理。  
  
### 参数：   
  
iFlag ：锁定选项。取值有：  
  
|iFlag|说明|  
|---|---|  
|MCL_CURRENT|仅仅当前已分配的内存会被锁定，之后分配的不会被锁定|  
|MCL_FUTURE|锁定之后分配的所有内存|  
  
### 返回值：
  
函数返回0。  
  
### 错误码：
  
无。  
  
### 样例：  
  
```c  
```  
  
## munlockall ：解锁进程空间
  
### 函数原型：
  
```c  
int munlockall   
(   
    void   
);  
```  
  
### 描述：  
  
该接口用来解锁进程空间，允许进行换页处理。  
  
### 参数：   
  
无。  
  
### 返回值：
  
函数返回0。  
  
### 错误码：
  
无。  
  
### 样例：  
  
```c  
```  
  
## mmap ：内存文件映射
  
### 函数原型：
  
```c  
void*mmap   
(   
    void*pvAddr,   
    size_t stLen,   
    int iProt,   
    int iFlag,   
    int iFd,   
    off_t off   
);  
```  
  
### 描述：  
  
该接口用来内存文件映射。  
  
### 参数：   
  
- pvAddr ：起始地址 (这里必须为 NULL, 系统将自动分配内存)。  
- stLen ：映射长度 (4096的整倍数)。  
- iProt ：页面属性，不能与文件的打开模式冲突。取值有：  
  
|iProt|说明|  
|---|---|  
|PROT_READ|可读|  
|PROT_WRITE|可读写|  
|PROT_EXEC|可执行|  
|PROT_NONE|无法访问|  
  
- iFlag ：映射标志，取值有：  
  
|iFlag|说明|  
|---|---|  
|MAP_SHARED|共享映射|  
|MAP_PRIVATE|私有映射|  
|MAP_FIXED|指定地址（系统当前不支持）|  
|MAP_ANONYMOUS|没有文件名在内存中|  
  
- iFd ：文件描述符。  
- off ：文件偏移量。  
  
### 返回值：
  
成功返回分配的虚拟地址；失败返回((void*)-1)，并置相应的错误码。  
  
### 错误码：
  
- EINVAL ：参数无效。  
- ENOTSUP ：系统不支持的操作。  
- ERROR_VMM_VIRTUAL_PAGE ：虚拟页面错误。  
  
### 样例：  
  
```c  
#include<stdio.h>  
#include<mman.h>  
#include<string.h>  
#define FILENAME "/apps/mmap/file"   
int main (int argc, char**argv)   
{   
    int file;   
    int ret = 0;   
    ......   
    char* p = mmap(NULL, 1024* 4, PROT_WRITE|PROT_READ, MAP_SHARED, file, 0); /* 内存文件映射*/   
    if(p == (void*) -1)   
    {   
        perror("func mmap errorn");   
        return -1;   
    }   
    ......   
    close(file);   
    return 0;   
}  
```  
  
## mmap64 ：内存文件映射
  
### 函数原型：
  
```c  
void*mmap64   
(   
    void*pvAddr,   
    size_t stLen,   
    int iProt,   
    int iFlag,   
    int iFd,   
    off64_t off   
);  
```  
  
### 描述：  
  
该接口用来内存文件映射。  
  
### 参数：   
  
- pvAddr ：起始地址 (这里必须为 NULL, 系统将自动分配内存)。  
- stLen ：映射长度(4096的整倍数)。  
- iProt ：页面属性。取值有：  
  
|iProt|说明|  
|---|---|  
|PROT_READ|可读|  
|PROT_WRITE|可读写|  
|PROT_EXEC|可执行|  
|PROT_NONE|无法访问|  
  
- iFlag ：映射标志。取值有：  
  
|iFlag|说明|  
|---|---|  
|MAP_SHARED|共享映射|  
|MAP_PRIVATE|私有映射|  
|MAP_FIXED|指定地址（系统当前不支持）|  
|MAP_ANONYMOUS|没有文件名在内存中|  
  
- iFd ：文件描述符。  
- off ：文件偏移量。  
  
### 返回值：
  
成功返回分配的虚拟地址；失败返回((void*)-1)，并置相应的错误码。  
  
### 错误码：  
  
- EINVAL ：参数无效。  
- ENOTSUP ：系统不支持的操作。  
- ERROR_VMM_VIRTUAL_PAGE ：虚拟页面错误。  
  
### 样例：  
  
```c  
#include<stdio.h>  
#include<mman.h>  
#include<string.h>  
#define FILENAME "/apps/mmap/file"   
int main (int argc, char**argv)   
{   
    int file;   
    int ret = 0;   
    ......   
    char* p = mmap64(NULL, 1024* 4 , PROT_WRITE|PROT_READ, MAP_SHARED, file, 0); /*内存文件映射*/   
    if(p == (void*) -1)   
    {   
        perror("func mmap64 errorn");   
        return -1;   
    }   
    ......   
    return 0;   
}  
```  
  
## mremap ：重新设置映射文件大小
  
### 函数原型：
  
```c  
void*mremap   
(   
    void*pvAddr,   
    size_t stOldSize,   
    size_t stNewSize,   
    int iFlag,   
    ...   
);  
```  
  
### 描述：  
  
该接口用来重新设置内存映射文件的大小。  
  
### 参数：   
  
- pvAddr ：起始地址。  
- stOldSize ：当前映射内存区域大小。  
- stNewSize ：需要映射的内存区域大小。  
- iFlag ：映射标志。  
  
|iFlag|说明|  
|---|---|  
|MREMAP_MAYMOVE|1|  
|MREMAP_FIXED|指定地址|  
  
### 返回值：
  
成功返回重新设置的内存区域地址；失败返回((void*)-1)，并置相应的错误码。  
  
### 错误码：
  
- EINVAL ：参数无效。  
- ENOTSUP ：系统不支持的操作。  
  
### 样例：  
  
```c  
#include<stdio.h>  
#include<mman.h>  
#include<string.h>  
#define FILENAME "/apps/mmap/file"   
int main (int argc, char**argv)   
{   
    int file;   
    int ret = 0;   
    ......   
    char*q = (char*)mremap(p, 1024* 4, 1024* 16, MREMAP_MAYMOVE);   
    if(q == (void*) -1) /* 内存文件映射*/   
    {   
        perror("func mremap error");   
    }   
    // ……   
    return 0;   
}  
```  
  
## munmap ：取消文件映射
  
### 函数原型：
  
```c  
int munmap   
(   
    void*pvAddr,   
    size_t stLen   
);  
```  
  
### 描述：  
  
该接口用来取消内存文件映射。  
  
### 参数：  
  
- pvAddr ：起始地址。  
- stLen ：映射内存区域大小。  
  
### 返回值：
  
成功返回0；失败返回-1，并置相应的错误码。  
  
### 错误码：  
  
- EINVAL ：参数无效。  
  
### 样例：  
  
```c  
#include<stdio.h>  
#include<mman.h>  
#include<string.h>  
#define FILENAME "/apps/mmap/file"   
int main (int argc, char**argv)   
{   
    int file;   
    int ret = 0;   
    ......   
    ret = munmap(p, 1024* 1024); /* 取消内存文件映射*/   
    if(ret != 0)   
    {   
        perror("func munmap errorn");   
        return -1;   
    }   
    ......   
    close(file);   
    return 0;   
}  
```  
  
## msync ：同步内存文件与被映射文件
  
### 函数原型：
  
```c  
int msync   
(   
    void*pvAddr,   
    size_t stLen,   
    int iFlag   
);  
```  
  
### 描述：  
  
该接口用来将内存中映射的文件数据回显在文件中，来实现磁盘文件内容与共享内存区中的内容一致,即同步操作。  
  
### 参数：   
  
- pvAddr ：起始地址。  
- stLen ：映射内存区域大小。  
- iFlag ：回显属性。取值有：  
  
|iFlag|说明|  
|---|---|  
|MS_INVALIDATE|放弃回显|  
|MS_ASYNC|异步回显|  
|MS_SYNC|同步回显|  
  
### 返回值：
  
成功返回0；失败返回-1，并置相应的错误码。  
  
### 错误码：  
  
- EINVAL ：参数无效。  
  
### 样例：  
  
```c  
#include<stdio.h>  
#include<mman.h>  
#include<string.h>  
#define FILENAME "/apps/mmap/file"   
int main (int argc, char**argv)   
{   
    int file;   
    int ret = 0;   
    ......   
    ret = msync(p, 1024, MS_ASYNC); /* 同步内存文件与被映射文件*/   
    if(ret != 0)   
    {   
        perror("func msync errorn");   
    }   
    ......   
    close(file);   
    return 0;   
}  
```  
  
## mprotect ：设置内存映射地址段属性
  
### 函数原型：
  
```c  
int mprotect   
(   
    void*pvAddr,   
    size_t stLen,   
    int iProt   
);  
```  
  
### 描述：  
  
该接口用来设置进程内指定的地址段属性。  
  
### 参数：   
  
- pvAddr ：起始地址。  
- stLen ：长度。  
- iProt ：新的属性，取值有：  
  
|iProt|说明|  
|---|---|  
|PROT_READ|可读|  
|PROT_WRITE|可读写|  
|PROT_EXEC|可执行|  
|PROT_NONE|无法访问|  
  
### 返回值：
  
成功返回0；失败返回-1，并置相应的错误码。  
  
### 错误码：
  
- EINVAL ：参数无效。  
- ENOTSUP ：系统不支持的操作。  
- ERROR_VMM_VIRTUAL_PAGE ：虚拟页面错误。  
  
### 样例：  
  
```c  
#include<stdio.h>  
#include<mman.h>  
#include<string.h>  
#define FILENAME "/apps/mmap/file"   
int main (int argc, char**argv)   
{   
    int file;   
    int ret = 0;   
    ......   
    ret = mprotect(p, 1024, PROT_WRITE); /* 设置进程内指定的地址段属性*/   
    if(ret != 0)   
    {   
        perror("func mprotect errorn");   
        return -1;   
    }   
    ......   
    close(file);   
    return 0;   
}  
```  
  
## shm_open ：创建或打开共享内存
  
### 函数原型：
  
```c  
int shm_open   
(   
    const char*name,   
    int oflag,   
    mode_t mode   
);  
```  
  
### 描述：  
  
该接口用来创建或打开一个共享内存,成功返回一个整数的文件描述符。用mmap映射此文件描述符，不可以使用msync函数同步内存。  
  
### 参数：   
  
- name ：共享内存区的名字，默认路径在/dev/shm/下。  
- oflag ：打开标志位。  
- mode ：权限位。  
  
### 返回值：
  
成功返回文件描述符；失败返回-1，并置相应的错误码。  
  
### 错误码：  
  
- EINVAL ：参数无效。  
  
### 样例：  
  
```c  
#include<stdio.h>  
#include<mman.h>  
#include<string.h>   
int main (int argc, char**argv)   
{   
    int file;   
    int ret = 0;   
    ......   
    file = shm_open("file", O_CREAT|O_RDWR, 0777); /* 打开一个共享内存*/   
    if(file == -1)   
    {   
        perror("func shm_open errorn");   
        return -1;   
    }   
    // ……   
    return (0);   
}  
```  
  
## shm_unlink ：移除指定的共享内存
  
### 函数原型：
  
```c  
int shm_unlink   
(   
    const char* name   
);  
```  
  
### 描述：  
  
该接口用来移除一个共享内存。  
  
### 参数：   
  
- name ：共享内存区的名字，默认路径在/dev/shm/下。  
  
### 返回值：
  
成功返回0；失败返回-1，并置相应的错误码。  
  
### 错误码：
  
- EINVAL ：参数无效。  
- EFAULT ：地址错误。  
- ENOENT ：文件或设备不存在。  
- EBUSY ：设备资源忙。  
- ELOOP ：链接文件层数太多。  
- EXDEV ：交叉设备连接。  
  
### 样例：  
  
```c  
#include<stdio.h>  
#include<mman.h>  
#include<string.h>   
int main (int argc, char**argv)   
{   
    int file;   
    int ret = 0;   
    file = shm_open("file", O_CREAT|O_RDWR, 0777);   
    if(file == -1)   
    {   
        perror("func shm_open errorn");   
        return -1;   
    }   
    // ……   
    shm_unlink("file"); /* 移除一个共享内存*/   
    return (0);   
}  
```  
  
## posix_madvise ：函数建议内核
  
### 函数原型：
  
```c  
int posix_madvise   
(   
    void*addr,   
    size_t len,   
    int advise   
);  
```  
  
### 描述：  
  
从addr指定的地址开始，长度为len的范围内，该区域的用户虚拟内存应遵循特定的使用模式。  
  
### 参数：  
  
- addr ：起始地址。  
- len ：长度参数。  
- advise ：标志位。  
  
### 返回值：
  
函数返回0。  
  
### 错误码：  
  
无。  
  
### 样例：  
  
```c  
```  
  
