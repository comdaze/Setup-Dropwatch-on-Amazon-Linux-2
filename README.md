# 在Amazon Linux 2上安装Dropwatch

在Amazon Linux 2运行dropwatch工具会如下错误：
```
$ dropwatch -lkas

Unable to find NET_DM family, dropwatch can't work Cleaning up on socket creation error`
```
这个错误意味着Amazon Linux 2内核中CONFIG_NET_DROP_MONITOR模块没有编译加载。
以下以内核版本为4.14.181为例编译drop_monitor模块，并加载到内核

1.创建一个目录，下载drop_monitor.c源码：
https://elixir.bootlin.com/linux/v4.14.181/source/net/core/drop_monitor.c
`$ mkdir ～/dropcd ~/drop`

2.创建一个Makefile
```
obj-m += drop_monitor.o   all:  make -C /lib/modules/$(shell uname -r)/build M=$(PWD) modules   clean:  make -C /lib/modules/$(shell uname -r)/build M=$(PWD) clean
```
 3.编译内核模块
`$make`
如下输出：
```
make -C /lib/modules/4.14.181-142.260.amzn2.x86_64/build M=/root/drop modules 
make[1]: Entering directory `/usr/src/kernels/4.14.181-142.260.amzn2.x86_64'   CC [M]  /root/drop/drop_monitor.o   
Building modules, stage 2.   MODPOST 1 modules   CC      /root/drop/drop_monitor.mod.o   LD [M]  /root/drop/drop_monitor.ko 
make[1]: Leaving directory `/usr/src/kernels/4.14.181-142.260.amzn2.x86_64'
```
会输出如下文件
```
$ ls

drop_monitor.c  drop_monitor.ko  drop_monitor.mod.c  drop_monitor.mod.o  drop_monitor.o  Makefile  modules.order  Module.symvers`
```
4.加载内核模块：
`$ insmod drop_monitor.ko`

5.安装dropwatch
```
$ cd ～/ wget https://rpmfind.net/linux/centos/7.8.2003/os/x86_64/Packages/dropwatch-1.4-9.el7.x86_64.rpmsudo yum localinstall dropwatch-1.4-9.el7.x86_64.rpm
```
6.运行dropwatch
```
$dropwatch -lkasInitalizing kallsyms db 

dropwatch> start
```

