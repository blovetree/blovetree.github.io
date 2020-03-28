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


#### 

> ([Reference]())


## C


#### #include路径

> ([Reference](https://www.cnblogs.com/amanlikethis/p/6914265.html))

Linux应用程序：`echo 'main(){}'|gcc -E -v -`

Linux内核一般在 对应的arch下 或者 /usr/include


## 宏


#### # #@ ##

> ([Reference](https://www.cnblogs.com/skyofbitbit/p/4210626.html))

在#define中，标准只定义了#和##两种操作。#用来把参数转换成字符串，##则用来连接两个前后两个参数，把它们变成一个字符串。还有一个是#@//加单引号


#### __ASSEMBLY__

> ([Reference](https://my.oschina.net/u/930588/blog/134751))

对应汇编代码


#### __stringify(x)

把参数x转换成一个字符串


## 汇编


#### EXPORT_SYMBOL

> ([Reference](https://blog.csdn.net/qq_37858386/article/details/78444168))


#### CFI_STARTPROC/CFI_ENDPROC

> ([Reference](https://blog.csdn.net/permike/article/details/41550991))


#### .MACRO

> ([Reference](https://www.cnblogs.com/Widesky/p/9006954.html))

定义一个宏


#### .align

> ([Reference](https://www.cnblogs.com/GaryEmbed/archive/2012/11/21/2780783.html))


