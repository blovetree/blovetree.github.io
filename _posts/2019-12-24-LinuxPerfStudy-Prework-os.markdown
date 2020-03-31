---
layout:     post
title:      "LinuxPerfStudy"
subtitle:   "Prework"
date:       2019-12-24
author:     "Dylan"
header-img: "img/post-assassin_odyssey.jpg"
catalog: true
tags:
    - OS
---


## LEBench使用

> [LEBench链接](https://github.com/LinuxPerfStudy/LEBench) : `git clone https://github.com/LinuxPerfStudy/LEBench.git`

> [ExperimentSetup链接](https://github.com/LinuxPerfStudy/ExperimentSetup)

**LEBench对于不同发行版下的相同内核跑分也不一样, eg:ubuntu16lts就似乎要比ubuntu14lts慢**

**LEBench测试的是当前内核，并在测试后根据iteration和kern_list切换内核**

1、Init (若编译有问题可尝试此法)

Install the following packages: gcc　make

不是c99标准的修改 LEBench\TEST_DIR\makefile : `$(CC) -std=c99 -o OS_Eval $(libs) OS_Eval.c`

2、加环境变量

eg: `sudo gedit /etc/profile` 中加入 `export LEBENCH_DIR=/home/usrname/LEBench/`

`sudo reboot`

3、LEBench目录下, `python get_kern.py`

4、LEBench目录下, `sudo -E python run.py >> ./LEBench.out 2>&1` 第一次运行只切换内核，详细见代码。(0.5h)


## GitKernelBuild

> ([Reference](https://wiki.ubuntu.com/KernelTeam/GitKernelBuild)) 

**硬盘50-60G，2-4核，后面build有并行优化，LEBench没有**

**Newer kernels can be compiled on Ubuntu 16 without a problem; some older kernels need to be compiled using Ubuntu 14 for libc compatibility**

**实测Ubuntu16LTS支持4.5.7、4.15、4.16, Ubuntu14LTS支持3.0.101、3.2.102、4.0.9、4.4.0-142、4.5.7**


#### Init

**初始环境不完整参考[apt-get update失败解决方法](#jump1)**

Install the following packages:

sudo　apt-get　install　git　build-essential　kernel-package　fakeroot　libncurses5-dev　libelf-dev　　libssl-dev　ccache　bison　flex

---

use shell(not recommend):

```
PASSWORD=

# echo $PASSWORD  | sudo -S apt-get update
# echo $PASSWORD  | sudo -S apt-get upgrade
echo $PASSWORD  | sudo -S apt-get install -y git
echo $PASSWORD  | sudo -S apt-get install -y build-essential
echo $PASSWORD  | sudo -S apt-get install -y fakeroot
echo $PASSWORD  | sudo -S apt-get install -y libncurses5-dev
echo $PASSWORD  | sudo -S apt-get install -y libelf-dev
echo $PASSWORD  | sudo -S apt-get install -y libssl-dev
echo $PASSWORD  | sudo -S apt-get install -y ccache
echo $PASSWORD  | sudo -S apt-get install -y bison
echo $PASSWORD  | sudo -S apt-get install -y flex
echo $PASSWORD  | sudo -S apt-get install -y kernel-package
```

#### Custom Building a Kernel

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

4、copy the configuration file into the Linux codebase directory, and rename the file to `.config`

5、Bring the config file up to date. 

make oldconfig: Default all questions based on the contents of your existing ./.config file and asking about new config symbols.

`yes '' | make oldconfig`

6、(optional) make any kernel config changes: `make menuconfig`

7、(To apply a patch) `git apply <name>.patch`

8、(particularly) You need to backport patches to the 3.0.7 and 3.1.7 kernels to fix compatibility issues that prevent booting. The patches can be found here(https://github.com/LinuxPerfStudy/ExperimentSetup/tree/master/boot_patches).

9、`make clean` 有的版本在第10步会包括 make clean，有的版本没有

10、build the linux-image and linux-header: (单核2-4h?)

``make -j `getconf _NPROCESSORS_ONLN` deb-pkg LOCALVERSION=-<version>`` version换成任意英文或者数字，LEBench要求有version

11、`cd ..`

12、install the custom kernel:

`sudo dpkg -i *.deb`

---


#### Change Kernel

> 解释见[grub设置启动项](https://blog.csdn.net/king_cpp_py/article/details/80308032)

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
#x,y为序号，也可以用字串
eg:GRUB_DEFAULT="Advanced options for Ubuntu>Ubuntu, with Linux xxx #xxx为内核号、名,eg: 4.14.166-custom
```

重新生成grub: `sudo update-grub`

`sudo reboot`


#### Remove Installed Kernel

查看已安装的内核版本: `sudo dpkg --get-selections | grep linux`

根据所示版本号删除: eg: `sudo apt-get purge linux-headers-4.13.0-36 linux-image-4.13.0-36-generic`

`sudo update-grub`


## 其他问题


#### Linux的“任务管理器”

`gnome-system-monitor`


#### Ubuntu硬盘空间用量分析工具

> [Ubuntu清理硬盘空间的8个技巧](https://blog.csdn.net/m0_37407756/article/details/79903837)

服务器上用 `sudo ncdu <dir>` , 在桌面版本用 `baobab`


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

2、修改DNS:

`sudo gedit /etc/resolv.conf` (此文件重启会重置) 同样插入

```
nameserver 8.8.8.8
nameserver 8.8.4.4
```

检查是否修改,`cat /etc/resolv.conf`

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