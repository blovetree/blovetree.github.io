---
layout:     post
title:      "NK-PA-NANOS-LITE"
subtitle:   "NANOS-LITE"
date:       2019-05-15
author:     "Dylan"
header-img: "img/post-bg-unix-linux.jpg"
catalog: true
tags:
    - PA
---




## 前言

NANOS-LITE代码注解



### 链接 

[NK-PA-NEMU](https://blovetree.github.io/2019/05/15/NK-PA-NEMU/)

[NK-PA-NEXUS-AM](https://blovetree.github.io/2019/05/15/NK-PA-NEXUS-AM/)

[NK-PA-NANOS-LITE](https://blovetree.github.io/2019/05/15/NK-PA-NANOS-LITE/)

[NK-PA-NAVY-APPS](https://blovetree.github.io/2019/05/15/NK-PA-NAVY-APPS/)




## x86-nemu/include/


#### fs.h

枚举了设置偏移的方式

`enum {SEEK_SET, SEEK_CUR, SEEK_END};`

函数 | 描述
- | -
fs_open | 打开文件
fs_filesz | 获得文件大小
fs_read | 读文件
fs_write | 写文件
fs_lseek | 设置文件指针位置
fs_close | 关文件


#### memory.h

定义了几个宏，方便来操作页和地址

```
#define PGMASK          (PGSIZE - 1)    // Mask for bit ops
#define PGROUNDUP(sz)   (((sz)+PGSIZE-1) & ~PGMASK)
#define PGROUNDDOWN(a)  (((a)) & ~PGMASK)
```


#### proc.h

>定义了进程控制块，每个进程分配32k栈空间，以下内容被放在栈底部

union PCB | 描述
- | -
tf | 进程陷阱帧
as | 进程虚拟地址空间信息
cur_brk | 目前进程break位置
max_brk | 最大break位置




## x86-nemu/src/


#### device.c

>注意在写文件时字符串后面加\n，读取时底层会把\n转为\0

>注意文件读写是以字节为单位，使用4字节长的类型时尤其注意，这一点在fb_write中比较明显

通过宏声明了按键名的数组，这样在按键事件写文件时，可以直接引这个数组

```
#define NAME(key) \
  [_KEY_##key] = #key,

static const char *keyname[256] __attribute__((used)) = {
  [_KEY_NONE] = "NONE",
  _KEYS(NAME)
};
```

声明了数组dispinfo来存放显示屏的信息，对应FD_DISPINFO文件的读写

---

函数 | 描述
- | -
events_read | 读按键信息和时间信息，也可在这处理按键对应os的反应
dispinfo_read | 读屏幕信息文件
fb_write | 写屏幕，调用_draw_rect
init_device | 初始化设备，同时写屏幕信息文件

#### files.h

“磁盘上”的信息，虽然很多没法用

#### fs.c

struct Finfo | 文件结构体
- | -
name | 文件名
size | 文件大小
disk_offset | 在磁盘上的起始位置
open_offset | 文件指针位置

用枚举定义了几个基本文件的id

`enum {FD_STDIN, FD_STDOUT, FD_STDERR, FD_FB, FD_EVENTS, FD_DISPINFO, FD_NORMAL};`

数组file_table初始化了文件的信息，其中直接调用了files.h，并之后用init_fs初始化一般文件的大小

```
static Finfo file_table[] __attribute__((used)) = {
  {"stdin (note that this is not the actual stdin)", 0, 0},
  {"stdout (note that this is not the actual stdout)", 0, 0},
  {"stderr (note that this is not the actual stderr)", 0, 0},
  [FD_FB] = {"/dev/fb", 0, 0},
  [FD_EVENTS] = {"/dev/events", 0, 0},
  [FD_DISPINFO] = {"/proc/dispinfo", 128, 0},
#include "files.h"
};
```

之后实现了在fs.h中定义的文件读写相关的函数


<!-- #### initrd.S -->


#### irq.c

函数 | 描述
- | -
do_event | 中断处理函数，并在处理完后进行进程调度
init_irq | 初始化中断处理，调用_asye_init


#### loader.c
 
首先，在这里定义了默认程序入口

`#define DEFAULT_ENTRY ((void *)0x8048000)`

定义了进程加载函数loader，完成进程从“外存”到“内存”的过程


#### mian.c

这里是操作系统主函数，初始化设备和加载进程都在这。内核通过自陷完成进程切换(现在还没有内核态切换到拥护态)


#### mm.c

>这个文件定义了对“内存”的操作

>在此简易系统中页就是一直分配下去，也不会有页的释放

首先定义了pf指针，指示内存目前开辟到的位置，在init_mm中，pf被初始化到堆起始位置，具体可见/am/arch/x86-nemu/img/loader.ld

函数 | 描述
- | -
new_page | 分配页
mm_brk | 调整进程break位置，系统调用中调用此函数
init_mm | 初始化内存


#### proc.c

>这个文件定义了对进程的操作

函数 | 描述
- | -
load_prog | 加载进程
change_game | 按F12调换游戏
schedule | 进程调度


#### ramdisk.c

这里引用的磁盘上文件的起始是在/initrd.S中定义的

函数 | 描述
- | -
ramdisk_read | 读“磁盘”
ramdisk_write | 写“磁盘”
get_ramdisk_size | 获得磁盘上内容总大小


#### syscall.c

各种系统调用的实现，先通过do_syscall来分发系统调用

这里要引用_end，同样是定义在/am/arch/x86-nemu/img/loader.ld

>所以_end定义在了0x100000

#### syscall.h

枚举了各种系统调用