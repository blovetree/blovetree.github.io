---
layout:     post
title:      "LinuxPerfStudy"
subtitle:   "Kernel"
date:       2020-01-21
author:     "Dylan"
header-img: "img/post-assassin_odyssey.jpg"
catalog: true
tags:
    - OS
---


## 内核调试


#### printk

> ([Reference](https://www.cnblogs.com/victor-ma/p/5332137.html))

1、用法

`cat /proc/sys/kernel/printk`：控制台的日志级别、默认消息日志级别、最小控制台日志级别和默认控制台日志级别

`printk("<0>" "str")`，其中的数字(0-7)为日志级别，数字越小级别越高，日记级别高于控制台的日志级别才会打印出来。后面的str与printf格式一致,但可打印指针名：%p裸指针，%pf函数名，%pF函数名+偏移量

2、查看结果

`dmesg`：在不刷新缓冲区的情况下获得缓冲区的内容，并将内容返回给stdout。

`sudo cat /proc/kmsg > 1.txt`：读取了缓冲区中的数据后，将缓冲区中的数据删除

3、优先级

```
#define KERN_EMERG  "<0>"
#define KERN_ALERT  "<1>"
#define KERN_CRIT   "<2>"
#define KERN_ERR    "<3>"
#define KERN_WARNING    "<4>"
#define KERN_NOTICE "<5>"
#define KERN_INFO   "<6>"
#define KERN_DEBUG  "<7>"
```


## C


#### #include路径

> ([Reference](https://www.cnblogs.com/amanlikethis/p/6914265.html))

Linux应用程序：`echo 'main(){}'|gcc -E -v -`

Linux内核一般在 对应的arch下 或者 /usr/include


#### fdget() fdput()

对fget() fput()的改进

fdget函数检查文件描述符表的引用计数count是否为1，如果是则意味着当前任务拥有文件描述符表的唯一所有权，那么fdget函数不会增加f_count。fdget函数根据是否已获取f_count在其返回值中设置一个标志，fdput函数根据这个标志使用普通的fput函数的逻辑或者什么也不做。


#### file.private_data

private_data 其实是用来保存自定义设备结构体的地址的。自定义结构体的地址被保存在private_data后，可以在read ,write 等驱动函数中被传递和调用自定义设备结构体中的成员



## 宏


#### # #@ \#\#

> ([Reference](https://www.cnblogs.com/skyofbitbit/p/4210626.html))

在#define中，标准只定义了#和##两种操作。#用来把参数转换成字符串，##则用来连接两个前后两个参数，把它们变成一个字符串。还有一个是 #@ 表示加单引号


#### __ASSEMBLY__

> ([Reference](https://my.oschina.net/u/930588/blog/134751))

对应汇编代码


#### __stringify(x)

把参数x转换成一个字符串


## 汇编


#### 反汇编

> ([Reference](https://blog.csdn.net/q2519008/article/details/82349869))

```
objdump -d print > print.s

gcc -g test.c -o test.out
objdump -S test.out > test.s
```


#### SFENCE、LFENCE、MFENCE指令

> ([Reference](https://blog.csdn.net/admiral_j/article/details/8072855))

当系统在做Memory IO操作的时候，用Index和Data间接方式访问寄存器（比如APIC 寄存器），这个时候需要加入写延时，否则，数据就会错位，因为系统硬件做流水操作，导致程序不能严格的顺序执行。而以前的延时值都是自己在实际中进行测试

sfence:在sfence指令前的写操作当必须在sfence指令后的写操作前完成。

lfence：在lfence指令前的读操作当必须在lfence指令后的读操作前完成。

mfence：在mfence指令前的读写操作当必须在mfence指令后的读写操作前完成。


#### EXPORT_SYMBOL

> ([Reference](https://blog.csdn.net/qq_37858386/article/details/78444168))


#### CFI_STARTPROC/CFI_ENDPROC

> ([Reference](https://blog.csdn.net/permike/article/details/41550991))


#### .MACRO

> ([Reference](https://www.cnblogs.com/Widesky/p/9006954.html))

定义一个宏


#### .align

> ([Reference](https://www.cnblogs.com/GaryEmbed/archive/2012/11/21/2780783.html))


