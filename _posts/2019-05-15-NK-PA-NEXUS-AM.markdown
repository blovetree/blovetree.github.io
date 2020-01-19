---
layout:     post
title:      "NK-PA-NEXUS-AM"
subtitle:   "NEXUS-AM"
date:       2019-05-15
author:     "Dylan"
header-img: "img/post-bg-unix-linux.jpg"
catalog: true
tags:
    - PA
---




## 前言

NEXUS-AM代码注解



## 链接 

[NK-PA-NEMU](https://blovetree.github.io/2019/05/15/NK-PA-NEMU/)

[NK-PA-NEXUS-AM](https://blovetree.github.io/2019/05/15/NK-PA-NEXUS-AM/)

[NK-PA-NANOS-LITE](https://blovetree.github.io/2019/05/15/NK-PA-NANOS-LITE/)

[NK-PA-NAVY-APPS](https://blovetree.github.io/2019/05/15/NK-PA-NAVY-APPS/)




#### am/am.h

两个枚举，分别枚举了基本按键和一般事件。均是用宏完成名字的拼接

```
enum {
  _KEY_NONE = 0,
  _KEYS(_KEY_NAME)
};
```
```
enum {
  _EVENT_NULL = 0,
  _EVENTS(_EVENT_NAME)
};
```

---

结构体 | 描述
- | -
_Area | 进程的虚拟地址空间，包含两个指向地址空间头尾的指针
_Event | 事件结构，包含事件类型和原因类型
_Screen | 屏幕信息，包含长和宽
_Protect | 进程保护模式下的信息，包含虚拟地址空间和页目录地址的信息




### x86-nemu/include/


#### arch.h

定义了页大小和内存大小

定义了陷阱帧结构体，包含保存现场所需的信息。注意结构体中变量的顺序，如果你在这debug很久，可以尝试反一下顺序

定义了访问陷阱帧中eax、ebx、ecx、edx的宏

```
#define SYSCALL_ARG1(r) r->eax
#define SYSCALL_ARG2(r) r->ebx
#define SYSCALL_ARG3(r) r->ecx
#define SYSCALL_ARG4(r) r->edx
```


#### x86.h

提供了几个地址转换的函数

```
#define PDX(va)     (((uint32_t)(va) >> PDXSHFT) & 0x3ff)
#define PTX(va)     (((uint32_t)(va) >> PTXSHFT) & 0x3ff)
#define OFF(va)     ((uint32_t)(va) & 0xfff)
```

定义了结构体GateDesc对应门描述符，保存异常处理函数地址，本实验主要用其中的offset段和p位

另外在这个文件中定义了读写crx、idt寄存器的函数，（还有in、out函数可以看看）




### x86-nemu/src/


#### asye.c

定义了idt

`static GateDesc idt[NR_IRQ];`

---

函数 | 描述
- | -
irq_handle | 异常处理的事件分发函数，会调用处理函数，并返回一个陷阱帧
_asye_init | 初始化idt后
_trap | 内核自陷函数，发出自陷指令


#### ioe.c

定义了screen的大小，屏幕内容存放位置

```
uint32_t* const fb = (uint32_t *)0x40000;

_Screen _screen = {
  .width  = 400,
  .height = 300,
};
```

---

函数 | 描述
- | -
_ioe_init | 初始化时间
_uptime | 获得当前运行时长
_draw_rect | 绘图
_read_key | 读键盘，默认返回0

#### pte.c

定义了系统的总页目录和页表，以及页申请、释放函数

```
static PDE kpdirs[NR_PDE] PG_ALIGN;
static PTE kptabs[PMEM_SIZE / PGSIZE] PG_ALIGN;
static void* (*palloc_f)();
static void (*pfree_f)(void*);
```

---

函数 | 描述
- | -
_pte_init | 初始化内核页目录，页表，申请、释放函数，设置cr0、cr3
_protect | 初始化进程保护模式下运行所需信息
_switch | 切换页目录
_map | 映射某进程的虚拟地址到某物理地址
_umake | 创建用户进程的现场

<!-- #### trm.c
 -->

#### trap.S

定义了中断处理函数，用来push事件标志和错误码

定义了函数asm_trap，用来push陷阱帧中寄存器相关内容，并调用中断处理事件分发函数


---
