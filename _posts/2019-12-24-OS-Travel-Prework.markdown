---
layout:     post
title:      "OS Travel"
subtitle:   "Prework"
date:       2019-12-24
author:     "Dylan"
header-img: "img/post-assassin_odyssey.jpg"
catalog: true
tags:
    - OS
    - Study diary
---


## Ubuntu环境变量配置

见[链接](https://blog.csdn.net/netwalk/article/details/9455893)


## LEBench配置

> [LEBench链接](https://github.com/LinuxPerfStudy/LEBench) : https://github.com/LinuxPerfStudy/LEBench

> [ExperimentSetup链接](https://github.com/LinuxPerfStudy/ExperimentSetup) : https://github.com/LinuxPerfStudy/ExperimentSetup

1、配环境(maybe unnecessary)

`sudo apt update`

`sudo apt install gcc`

`sudo apt install make`

2、加环境变量

eg: /etc/profile中加入 `export LEBENCH_DIR=/home/usrname/LEBench/`

`sudu reboot`

3、LEBench目录下，`python get_kern.py`

4、需运行2次。LEBench目录下，`sudo -E python run.py >> ./LEBench.out 2>&1`


## GitKernelBuild

>([Reference](https://wiki.ubuntu.com/KernelTeam/GitKernelBuild)) 


#### Init

**初始环境可能有问题，可以先手动安git确认是否完整，若不完整参考[apt-get update失败解决方法](#jump1)**

Install the following packages:

sudo　apt-get　install　git　build-essential　kernel-package　fakeroot　libncurses5-dev　libelf-dev　　libssl-dev　ccache　bison　flex

---

use shell(not recommend):

```
PASSWORD=

echo $PASSWORD  | sudo -S apt-get update
echo $PASSWORD  | sudo -S apt-get upgrade
echo $PASSWORD  | sudo -S apt-get install build-essential -y
echo $PASSWORD  | sudo -S apt-get install fakeroot -y
echo $PASSWORD  | sudo -S apt-get install git -y
echo $PASSWORD  | sudo -S apt-get install libncurses5-dev -y
echo $PASSWORD  | sudo -S apt-get install libelf-dev -y
echo $PASSWORD  | sudo -S apt-get install libssl-dev -y
echo $PASSWORD  | sudo -S apt-get install ccache -y
echo $PASSWORD  | sudo -S apt-get install bison -y
echo $PASSWORD  | sudo -S apt-get install flex -y
echo $PASSWORD  | sudo -S apt-get install kernel-package -y
```

#### Custom Building a Kernel

1、Download the kernel codebase: 

```
git clone https://mirrors.tuna.tsinghua.edu.cn/git/linux-stable.git
git clone git://git.kernel.org/pub/scm/linux/kernel/git/stable/linux.git
```

We originally downloaded the kernels from [here](https://kernel.ubuntu.com/~kernel-ppa/mainline/).
However, some of the kernels are no longer hosted. We backed up the kernels [here](https://github.com/LinuxPerfStudy/ExperimentSetup/tree/master/default_ubuntu_kernels) and kernel configurations [here](https://github.com/LinuxPerfStudy/ExperimentSetup/tree/master/default_ubuntu_kernel_configs). 

2、checkout the version of choice, for example: `git checkout v4.0.1`

3、``cp /boot/config-`uname -r` .config``

4、Bring the config file up to date: `make oldconfig` or `yes '' | make oldconfig`

5、(optional) make any kernel config changes: `make menuconfig`

6、(如果不是第一次更换内核) `make clean`

7、(To apply a patch) run: `git apply <name>.patch`

8、(optional) You need to backport patches to the 3.0.7 and 3.1.7 kernels to fix compatibility issues that prevent booting. The patches can be found here(https://github.com/LinuxPerfStudy/ExperimentSetup/tree/master/boot_patches).

9、(大概要用1h?)build the linux-image and linux-header: ``make -j `getconf _NPROCESSORS_ONLN` deb-pkg LOCALVERSION=-custom`` custom可以换成任意英文或者数字 (Newer kernels can be compiled on Ubuntu 16 without a problem; some older kernels need to be compiled using Ubuntu 14 for libc compatibility)

10、Change to one directory level up: `cd ..`

11、install the custom kernel, run: `sudo dpkg -i *.deb`

12、`sudo reboot`

---

use shell, after checkout run this:

```
cp /boot/config-`uname -r` .config
yes '' | make oldconfig
make clean
make -j `getconf _NPROCESSORS_ONLN` deb-pkg LOCALVERSION=-custom
```

then goto step 9


## 配置问题


#### VMware打开虚拟机没反应

管理员态运行cmd：netsh winsock reset


#### <span id="jump1">sudo apt-get update 失败</span>

`sudo gedit /etc/apt/sources.list`, 插入（中科大的源）：

```
deb http://mirrors.ustc.edu.cn/ubuntu/ xenial main restricted universe multiverse
deb http://mirrors.ustc.edu.cn/ubuntu/ xenial-security main restricted universe multiverse
deb http://mirrors.ustc.edu.cn/ubuntu/ xenial-updates main restricted universe multiverse
deb http://mirrors.ustc.edu.cn/ubuntu/ xenial-proposed main restricted universe multiverse
deb http://mirrors.ustc.edu.cn/ubuntu/ xenial-backports main restricted universe multiverse
deb-src http://mirrors.ustc.edu.cn/ubuntu/ xenial main restricted universe multiverse
deb-src http://mirrors.ustc.edu.cn/ubuntu/ xenial-security main restricted universe multiverse
deb-src http://mirrors.ustc.edu.cn/ubuntu/ xenial-updates main restricted universe multiverse
deb-src http://mirrors.ustc.edu.cn/ubuntu/ xenial-proposed main restricted universe multiverse
deb-src http://mirrors.ustc.edu.cn/ubuntu/ xenial-backports main restricted universe multiverse
```

**地址中的xenial是ubuntu系统版本的名称，只要换成自己系统版本的名称即可。查看版本名称可以运行:`lsb_release -a`，其中，Codename就是**

---

修改DNS: `sudo vi /etc/resolvconf/resolv.conf.d/base`(文件默认是空),插入：

```
nameserver 8.8.8.8
nameserver 8.8.4.4
```

`sudo resolvconf -u`

`sudo gedit /etc/resolv.conf` 同样插入,(重启会重置)

检查是否修改,`cat /etc/resolv.conf`:

```
# Dynamic resolv.conf(5) file for glibc resolver(3) generated by resolvconf(8)
# DO NOT EDIT THIS FILE BY HAND -- YOUR CHANGES WILL BE OVERWRITTEN
nameserver 8.8.8.8
nameserver 8.8.4.4
```

重启网络：`sudo /etc/init.d/networking restart`


#### sudo apt-get install 失败(未测试)

sudo apt-get --fix-broken install

sudo apt-get -f install


## Ubuntu命令发邮件

以qq邮箱发送为例，qq邮箱smtp服务器端口用587而不是另一个

写文件mail.rc：

```
set from=NAME@qq.com                       
set smtp=smtp.qq.com:587                
set smtp-auth-user=NAME       
set smtp-auth-password=xxx          
set smtp-auth=login
```

**NAME替换成为登录的账号，xxx替换为授权码，注意是授权码，而不是登录密码**

```
export MAILRC=<path>/mail.rc
echo 'Test' | mail -v -s 'Title' sendto@xx.com
```