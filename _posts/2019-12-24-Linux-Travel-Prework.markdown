---
layout:     post
title:      "Linux Travel"
subtitle:   "Prework"
date:       2019-12-24
author:     "Dylan"
header-img: "img/post-assassin_odyssey.jpg"
catalog: true
tags:
    - OS
    - Study diary
---


## LEBench使用

> [LEBench链接](https://github.com/LinuxPerfStudy/LEBench) : `git clone https://github.com/LinuxPerfStudy/LEBench.git`

> [ExperimentSetup链接](https://github.com/LinuxPerfStudy/ExperimentSetup)

**LEBench对于不同发行版下的相同内核跑分也不一样, eg:ubuntu16lts就似乎要比ubuntu14lts慢**

**可以选择被测内核，另外调内核时不需要切换只需要安装**

1、Init (若编译有问题可尝试此法)

Install the following packages: gcc　make

不是c99标准的修改 LEBench\TEST_DIR\makefile :

`$(CC) -std=c99 -o OS_Eval $(libs) OS_Eval.c`

2、加环境变量

eg: `sudo gedit /etc/profile` 中加入 `export LEBENCH_DIR=/home/usrname/LEBench/`

`sudo reboot`

3、LEBench目录下, `python get_kern.py`

4、第一次运行是切换内核, LEBench目录下, `sudo -E python run.py >> ./LEBench.out 2>&1`


## GitKernelBuild

>([Reference](https://wiki.ubuntu.com/KernelTeam/GitKernelBuild)) 


#### Init

**初始环境不完整参考[apt-get update失败解决方法](#jump1)**

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

**需要不少硬盘空间，我用的50G**

**Newer kernels can be compiled on Ubuntu 16 without a problem; some older kernels need to be compiled using Ubuntu 14 for libc compatibility**

**实测Ubuntu16LTS支持4.5.7、4.15、4.16, Ubuntu14LTS支持3.0.101、3.2.102、4.0.9、4.4.0-142、4.5.7**

1、Download the kernel codebase: 

```
git clone https://mirrors.tuna.tsinghua.edu.cn/git/linux-stable.git
git clone git://git.kernel.org/pub/scm/linux/kernel/git/stable/linux.git
……
```

We originally downloaded the kernels from [here](https://kernel.ubuntu.com/~kernel-ppa/mainline/).
However, some of the kernels are no longer hosted. We backed up the kernels [here](https://github.com/LinuxPerfStudy/ExperimentSetup/tree/master/default_ubuntu_kernels) and kernel configurations [here](https://github.com/LinuxPerfStudy/ExperimentSetup/tree/master/default_ubuntu_kernel_configs). 

2、`cd linux-stable`

3、checkout the version of choice

eg: `git checkout v4.0.1`

4、Copy the kernel config file from your existing system to the kernel tree: 

``cp /boot/config-`uname -r` .config``

5、Bring the config file up to date: 

`make oldconfig` or `yes '' | make oldconfig`

6、(optional) make any kernel config changes: `make menuconfig`

7、(如果不是第一次更换内核) `make clean`

8、(To apply a patch) `git apply <name>.patch`

9、(particularly) You need to backport patches to the 3.0.7 and 3.1.7 kernels to fix compatibility issues that prevent booting. The patches can be found here(https://github.com/LinuxPerfStudy/ExperimentSetup/tree/master/boot_patches).

10、build the linux-image and linux-header: (大概要用2h?)

``make -j `getconf _NPROCESSORS_ONLN` deb-pkg LOCALVERSION=-custom`` custom可以换成任意英文或者数字

11、`cd ..`

12、install the custom kernel:

`sudo dpkg -i *.deb`

13、(磁盘里有多个内核时) 解释见[grub设置启动项](https://blog.csdn.net/king_cpp_py/article/details/80308032)

**启动错系统只能重启; 如果进入了memtest，按esc会重启**

方法一: 通过图形界面的启动菜单: 重启后, 按住shift即可进入启动菜单选择界面

---

方法二: 修改grub:

查menu序号: `sudo gedit /boot/grub/grub.cfg` or `grep menuentry /boot/grub/grub.cfg`

修改grub引导 `sudo gedit /etc/default/grub` 

```
GRUB_DEFAULT=x          #只选菜单
GRUB_DEFAULT="x>y"      #选菜单和子菜单
GRUB_HIDDEN_TIMEOUT=-1  #设置启动菜单选择界面
```

重新生成grub: `sudo update-grub`

`sudo reboot`

14、`sudo reboot`

---

use shell, after checkout run this:

```
cp /boot/config-`uname -r` .config
yes '' | make oldconfig
make clean
make -j `getconf _NPROCESSORS_ONLN` deb-pkg LOCALVERSION=-custom
```

then goto step 11


## 配置问题


#### Ubuntu环境变量配置

见[链接](https://blog.csdn.net/netwalk/article/details/9455893)


#### VMware打开虚拟机没反应

管理员态运行cmd：netsh winsock reset


#### <span id="jump1">sudo apt-get update 失败</span>

1、`sudo gedit /etc/apt/sources.list`, 插入中科大的源:

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

2、修改DNS: `sudo vi /etc/resolvconf/resolv.conf.d/base`(文件默认是空), 插入：

```
nameserver 8.8.8.8
nameserver 8.8.4.4
```

`sudo resolvconf -u`

`sudo gedit /etc/resolv.conf` (此文件重启会重置) 同样插入

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

写文件mail.rc, 随便存个位置：

```
set from=NAME@qq.com                       
set smtp=smtp.qq.com:587                
set smtp-auth-user=NAME       
set smtp-auth-password=xxx          
set smtp-auth=login
```

**NAME替换成为登录的账号，xxx替换为授权码，注意是授权码，而不是登录密码**

发邮件命令:

```
export MAILRC=<path>/mail.rc
echo 'Test' | mail -v -s 'Title' sendto@xx.com
```