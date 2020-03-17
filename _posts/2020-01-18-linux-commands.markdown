---
layout:     post
title:      "Linux Commands"
date:       2019-11-06
author:     "Dylan"
header-img: "img/post-linux-commands.png"
catalog: true
tags:
    - Linux
    - Commands
---


## 进程

```
jobs

fg %1   # 重启1号进程
kill %1 # 结束1号进程
```


## 打包、压缩&解包、解压

#### .tar文件

> .tar仅打包

```
tar -xvf FileName.tar         # 解包
tar -cvf FileName.tar DirName # 将DirName和其下所有文件（夹）打包
```

#### .tar.gz文件

```
tar -zxvf FileName.tar.gz               # 解压
tar -zcvf FileName.tar.gz DirName       # 将DirName和其下所有文件（夹）压缩
tar -C DesDirName -zxvf FileName.tar.gz # 解压到目标路径
```

#### .gz文件

```
gunzip FileName.gz  # 解压1
gzip -d FileName.gz # 解压2
gzip FileName       # 压缩，只能压缩文件
```

#### .zip文件

```
unzip FileName.zip          # 解压
zip FileName.zip DirName    # 将DirName本身压缩
zip -r FileName.zip DirName # 压缩，递归处理，将指定目录下的所有文件和子目录一并压缩
```

#### .rar文件

```
# mac和linux并没有自带rar，需要去下载
rar x FileName.rar      # 解压
rar a FileName.rar DirName # 压缩
```




